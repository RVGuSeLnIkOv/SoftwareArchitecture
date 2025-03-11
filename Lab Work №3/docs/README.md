## Диаграмма контейнеров
![C2new](https://github.com/user-attachments/assets/792f3f17-9e61-43d2-865b-f0eb432b8529)

## Диаграмма компонентов для сервиса обходов
![C3 bypass](https://github.com/user-attachments/assets/113eb7d3-24c4-4d2b-8dad-409c74fe2f7c)

## Диаграмма последовательности для совершения обходов
![диаграмма последовательности](https://github.com/user-attachments/assets/43dda49e-db9a-44a2-9213-fb1ef5924cd6)

## Модель БД
![диаграмма классов](https://github.com/user-attachments/assets/d9beb9ca-6413-417e-97f1-1ec25bb6a6b2)

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


