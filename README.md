# System Design социальной сети для [курса по System Design](https://balun.courses/courses/system_design)

## Функциональные требования

- **Профиль**:
    - Аутентификация
    - Возможность заполнять профиль: имя, раздел о себе, фотография, контакты
- **Публикация постов**:
    - Возможность создания постов с фото, текстом (до 400 символов), геометкой
    - Возможность удаления постов
    - Посты должны отображаться в профиле пользователя и ленте подписчиков
- **Оценки и комментарии**:
    - Возможность оценивать посты (лайки) других путешественников, даже если они на них не подписаны
    - Возможность убирать свою оценку с поста (лайк)
    - Возможность оставлять комментарии под постами других путешественников, даже если они на них не подписаны
    - Возможность удалять свои комментарии под постами
    - Возможность отвечать на комментарии других путешественников
- **Подписки**:
    - Возможность подписаться на других путешественников
    - Возможность отписаться от других путешественников
- **Поиск и просмотр популярных мест**:
    - Поиск мест для путешествий по странам и городам (по вводу названия города/страны в поиск)
    - Просмотр ленты в виде ТОПа мест (с наибольшим количеством постов/лайков), которая сформировалась в результате поиска
- **Общение**:
    - Возможность отправки личных сообщений другим пользователям
    - Возможность блокировки других пользователей в личных сообщениях
- **Лента активности**:
    - Возможность просмотреть в ленте посты тех, на кого подписан в хронологическом порядке
    - Лента должна обновляться в реальном времени при появлении новых постов


## **Нефункциональные требования:**

- DAU 10 000 000, архитектура должна позволять горизонтальное масштабирование для обработки растущей нагрузки
- Каждый пользователь в среднем:
    - Читает 50 постов в день
    - Создает 1 пост в день с фото 1 Мб + ~400 символов (~0.78 Кб) + геометка (~0.1 Кб) + служебная информация (в том числе лайки → 0) =~ 1 Мб
    - Пишет 10 сообщений в чатах (~400 символов ~0.78 Кб)
    - Пишет 5 сообщений в комментариях (~400 символов ~0.78 Кб)
    - Читает 100 сообщений в чатах и комментариях  (50 на 50)
- Аудитория СНГ
- Сезонность есть (будут наблюдаться всплески в период отпусков, новогодних праздников и в летний период)
- Необходимо обеспечить репликацию данных и их резервное копирование (для восстановления)
- Максимальное количество подписчиков 30 млн
- Доступность 99.95 (не более 4.38 часов за год)


## **Оценка нагрузки:**

- **RPS чтение =** 10 000 000 * (50 + 100) / 86 400 ~= 17361
- **RPS запись =**  10 000 000 * (1 + 10 + 5) / 86 400 ~= 1852
- **Traffic** чтения = 17361*(50 * 1 Мб пост + 100 * 300/1024^2 Мб текста)) ~= 848 050 Мб/с
- **Traffic** записи = 1852 * (1 * 1 Мб пост + (10 + 5) * 0.78/1024 Мб текста)) ~= 1873 Мб/с
- **Memory для хранения постов** **за 1 год** = 10 млн * 365 * 1 Мб ~= 3.4 Пб
- **Memory для хранения текста + гео + служебная инфа** **за 1 год** = 10 млн * 365 * 15 * 1.57 Кб ~= 0.08 Пб
- **Memory общая за 1 год** = 3 (фактор репликации, копии) * (3.4 + 0.08) = 10.28 Пб