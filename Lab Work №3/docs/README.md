## Диаграмма контейнеров
![C2new](https://github.com/user-attachments/assets/792f3f17-9e61-43d2-865b-f0eb432b8529)

## Диаграмма компонентов для сервиса обходов
![C3 bypass](https://github.com/user-attachments/assets/113eb7d3-24c4-4d2b-8dad-409c74fe2f7c)

## Диаграмма последовательности для совершения обходов
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

