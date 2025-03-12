# Лабораторная работа №3
## Диаграмма контейнеров
![C2_vol3](https://github.com/user-attachments/assets/cc611c77-b9a7-45ee-a397-dc13fece5483)

## Диаграмма компонентов для сервиса обходов
![C3 bypass](https://github.com/user-attachments/assets/2cc3e679-b153-46a6-b0f8-fb7f5db39665)

## Диаграмма последовательности для совершения обходов
![диаграмма последовательности](https://github.com/user-attachments/assets/43dda49e-db9a-44a2-9213-fb1ef5924cd6)

``` plantuml
@startuml

autonumber

actor "Обходчик" as Actor
participant "Мобильное приложение" as App
participant "RoutesService" as RS
participant "ObjectControlService" as OCS
participant "DefectsService" as DS
participant "ObjectsRepository" as OR
participant "ChecklistRepository" as CR
participant "DefectRepository" as DR
participant "RoutesRepository" as RR
database "База данных" as DB

group Начало работы
    Actor -> App: Поиск назначенных обходов
    App -> RS: Запрос доступных маршутов обхода
    RS -> RR: Запрос маршрутов
    RR -> DB: Запрос маршрутов
    DB --> RR: Данные о назначенных обходах
    RR --> RS: Данные о назначенных обходах
    RS --> App: Отправка маршрута
    
    App -> OCS: Запрос информации\nоб объектах в обходах
    OCS -> OR: Запрос данных объектов
    OR -> DB: Запрос данных объектов
    DB --> OR: Данные объектов
    OR --> OCS: Данные объектов
    OCS --> App: Информация об объектах
    App --> Actor: Демонстрация доступных обходов
end

loop Для каждого объекта в обходе
    Actor -> App: Считывание NFC-метки на объекте
    App -> OCS: Запрос чек-листов для объекта
    OCS -> CR: Запрос чек-листа
    CR -> DB: Запрос чек-листа
    DB --> CR: Данные чек-листа
    CR --> OCS: Чек-лист на текущий объект
    OCS --> App: Чек-лист на текущий объект
    
    Actor -> App: Измерение показателей,\nзаполнение чек-листа и отправка данных
    App -> OCS: Передача чек-листа
    OCS -> CR: Сохранение чек-листа
    CR -> DB: Запись данных чек-листа
    DB --> CR: Подтверждение сохранения
    CR --> OCS: Подтверждение сохранения
    OCS --> Actor: Чек-лист сохранен
end

opt Если обнаружен дефект
    Actor -> App: Нажатие "Сообщить о дефекте"
    App -> DS: Сообщение о дефекте
    DS -> DR: Фиксация дефекта
    DR -> DB: Запись данных дефекта
    DB --> DR: Подтверждение сохранения
    DR --> DS: Подтверждение фиксации
    DS --> Actor: Дефект зафиксирован
end

@enduml
```

## Модель БД
![диаграмма классов](https://github.com/user-attachments/assets/d9beb9ca-6413-417e-97f1-1ec25bb6a6b2)

``` plantuml
@startuml
class Пользователь {
    +ID
    +ФИО
    +ID бригады
    +Логин
    +Email
    +Номер телефона
}
class Обход {
    +ID
    +Наименование
    +Описание
    +ID цеха
    +ID пользователя

}
class ТочкаОбхода {
    +ID
    +ID обхода
    +Id объекта
}
class Объект {
    +ID
    +Наименование
    +Широта
    +Долгота
    +Информация
    +ID чеклиста
}
class Чеклист {
    +ID
    +Наименование
}
class ПунктЧеклиста {
    +ID
    +Наименование
    +Тип данных
    +Единица измерения
    +Значение по умолчанию
}
class ПричинаДефекта {
    +ID
    +Наименование
}
class Дефект {
    +ID
    +ID причины дефекта
    +Описание
    +Фото
}
class СправочникДефектов {
    +ID
    +ID объекта
    +ID дефекта
}

Пользователь "1" -- "*" Обход
Обход "1" -- "*" ТочкаОбхода
Объект "1" -- "*" ТочкаОбхода
Объект "1" -- "*" СправочникДефектов
Дефект "1" -- "*" СправочникДефектов
ПричинаДефекта "1" -- "*" Дефект
Чеклист "1" -- "*" Объект
ПунктЧеклиста "1" -- "*" Чеклист

@enduml
```

## Реализованный код
``` java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;
import org.springframework.data.jpa.repository.JpaRepository;
import org.springframework.web.bind.annotation.*;
import javax.persistence.*;
import java.util.List;

@SpringBootApplication
public class PatrolServiceApplication {
    public static void main(String[] args) {
        SpringApplication.run(PatrolServiceApplication.class, args);
    }
}

@Entity
class User {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String fullName;
    private String login;
    private String email;
    private String phoneNumber;
    private Long teamId;
    
    // Getters and setters
}

@Entity
class Patrol {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String description;
    private Long userId;
    private Long departmentId;

    // Getters and setters
}

@Entity
class PatrolPoint {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Long patrolId;
    private Long objectId;

    // Getters and setters
}

@Entity
class ObjectEntity {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private double latitude;
    private double longitude;
    private String info;
    private Long checklistId;

    // Getters and setters
}

@Entity
class Checklist {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    // Getters and setters
}

@Entity
class ChecklistItem {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;
    private String dataType;
    private String unit;
    private String defaultValue;
    private Long checklistId;

    // Getters and setters
}

@Entity
class DefectReason {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private String name;

    // Getters and setters
}

@Entity
class Defect {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Long reasonId;
    private String description;
    private String photo;
    private Long objectId;

    // Getters and setters
}

@Entity
class DefectDirectory {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;
    private Long objectId;
    private Long defectId;

    // Getters and setters
}

interface UserRepository extends JpaRepository<User, Long> {}
interface PatrolRepository extends JpaRepository<Patrol, Long> {}
interface PatrolPointRepository extends JpaRepository<PatrolPoint, Long> {}
interface ObjectRepository extends JpaRepository<ObjectEntity, Long> {}
interface ChecklistRepository extends JpaRepository<Checklist, Long> {}
interface ChecklistItemRepository extends JpaRepository<ChecklistItem, Long> {}
interface DefectReasonRepository extends JpaRepository<DefectReason, Long> {}
interface DefectRepository extends JpaRepository<Defect, Long> {}
interface DefectDirectoryRepository extends JpaRepository<DefectDirectory, Long> {}

@RestController
@RequestMapping("/api/users")
class UserController {
    private final UserRepository userRepository;

    public UserController(UserRepository userRepository) {
        this.userRepository = userRepository;
    }

    @GetMapping
    public List<User> getAllUsers() {
        return userRepository.findAll();
    }

    @PostMapping
    public User createUser(@RequestBody User user) {
        return userRepository.save(user);
    }
}

@RestController
@RequestMapping("/api/patrols")
class PatrolController {
    private final PatrolRepository patrolRepository;

    public PatrolController(PatrolRepository patrolRepository) {
        this.patrolRepository = patrolRepository;
    }

    @GetMapping
    public List<Patrol> getAllPatrols() {
        return patrolRepository.findAll();
    }

    @PostMapping
    public Patrol createPatrol(@RequestBody Patrol patrol) {
        return patrolRepository.save(patrol);
    }
}

@RestController
@RequestMapping("/api/objects")
class ObjectController {
    private final ObjectRepository objectRepository;

    public ObjectController(ObjectRepository objectRepository) {
        this.objectRepository = objectRepository;
    }

    @GetMapping
    public List<ObjectEntity> getAllObjects() {
        return objectRepository.findAll();
    }

    @PostMapping
    public ObjectEntity createObject(@RequestBody ObjectEntity objectEntity) {
        return objectRepository.save(objectEntity);
    }
}

@RestController
@RequestMapping("/api/checklists")
class ChecklistController {
    private final ChecklistRepository checklistRepository;

    public ChecklistController(ChecklistRepository checklistRepository) {
        this.checklistRepository = checklistRepository;
    }

    @GetMapping
    public List<Checklist> getAllChecklists() {
        return checklistRepository.findAll();
    }

    @PostMapping
    public Checklist createChecklist(@RequestBody Checklist checklist) {
        return checklistRepository.save(checklist);
    }
}

@RestController
@RequestMapping("/api/defects")
class DefectController {
    private final DefectRepository defectRepository;

    public DefectController(DefectRepository defectRepository) {
        this.defectRepository = defectRepository;
    }

    @GetMapping
    public List<Defect> getAllDefects() {
        return defectRepository.findAll();
    }

    @PostMapping
    public Defect createDefect(@RequestBody Defect defect) {
        return defectRepository.save(defect);
    }
}
```

## Принципы проектирования
### KISS
Код разделен на простые и понятные сущности. Каждая сущность (например, User, Patrol, Defect) представляет собой отдельный класс с минимальной логикой. Контроллеры выполняют простые CRUD-операции, используя Spring Data JPA. Код поддерживает чёткость и читабельность, так как каждый контроллер и репозиторий отвечает за отдельную задачу.

### YAGNI
В коде отсутствуют ненужные или избыточные функции. Реализованы только те сущности и методы, которые необходимы для базовой работы API. Например, контроллеры для пользователей, обходов и дефектов. Не реализованы функции или бизнес-логики, которые могут понадобиться в будущем, но не актуальны на данный момент. Код минимален, что позволяет избежать создания лишних классов и методов.

### DRY
Использование JPA-репозиториев исключает дублирование кода для работы с БД. Все контроллеры используют одну и ту же структуру, что исключает повторение логики.

Репозитории (например, UserRepository, PatrolRepository) предоставляют универсальные методы для работы с данными, таким образом избегая дублирования кода.
Использование аннотаций @Entity, @Id, @GeneratedValue стандартизирует структуру классов и облегчает поддержку кода.

### SOLID
#### Single Responsibility Principle
Каждый класс отвечает только за одну область функциональности.

UserController управляет пользователями, PatrolController - обходами, DefectController - дефектами.

Это делает код более структурированным и простым для тестирования и расширения.

#### Open/Closed Principle
Можно добавлять новые сущности, контроллеры или репозитории без изменения существующего кода. Например, можно добавить новый контроллер для работы с отчетами или уведомлениями без изменения текущей структуры.
Пример расширяемости: добавление нового типа дефекта не требует изменений в коде других сущностей.

#### Liskov Substitution Principle (все сущности могут быть заменены своими подклассами)
Репозитории используют JpaRepository, который работает с любыми сущностями, поддерживающими интерфейс JPA. Это позволяет легко добавлять новые сущности, заменяя их в коде без изменения бизнес-логики.
Таким образом, код легко масштабируется для новых сущностей.

#### Interface Segregation Principle (интерфейсы репозиториев узкоспециализированы)
Каждый репозиторий (например, PatrolRepository, UserRepository) отвечает только за одну сущность и ее операции. Это уменьшает связанность кода и позволяет работать с каждым интерфейсом по отдельности.
Такой подход делает код более гибким и удобным для тестирования.

#### Dependency Inversion Principle
Контроллеры используют инъекцию зависимостей для получения репозиториев (UserRepository, PatrolRepository), что позволяет изменять реализацию без изменения контроллеров.
Это облегчает тестирование и замену репозиториев.

### BDUF (Big Design Up Front - Масштабное проектирование прежде всего)
В данном случае масштабное проектирование не использовалось, и это оправдано. Система спроектирована на основе текущих требований, что позволяет гибко развивать её по мере появления новых нужд.

### SoC (Separation of Concerns - Разделение ответственности)
Это принцип выполнен, поскольку каждый контроллер и репозиторий решает свою узкую задачу, а логика работы с данными отделена от бизнес-логики.

### MVP
Код ориентирован на создание базовой функциональности, которая необходима для работы системы. Поставленные задачи (управление пользователями, обходами, дефектами) выполняются, а дальнейшие улучшения можно добавлять по мере потребности.

### PoC (Proof of Concept - Доказательство концепции)
Код может быть рассмотрен как начальная версия, доказывающая работоспособность архитектуры и базового функционала. Далее проект может развиваться на основе полученного опыта.
