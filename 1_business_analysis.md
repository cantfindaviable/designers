# 1.  Бизнес-анализ (Business Understanding)
Сервис генерации изображений на основе текстовых описаний для помощи дизайнерам и маркетологам в создании визуального контента, соответствующего корпоративным стандартам и бренд-буку.

**Проблема** при создании дизайна исходит из ограниченного времени на проработку прототипов, которые необходимо показывать заказчику, и на поиск референсов, редактирование/сокращение текста из технического задания, выделение ключевых слов для дизайна, отрисовку паттернов.

Техническое задание, с которым заказчик приходит к маркетологу или дизайнеру включает в себя следующие пункты: 
- информация о проекте/продукте/услуги
- цель рекламы: увеличить продажи/повысить узнаваемость/привлечь новых клиентов
- целевая аудитория рекламы: пол/возраст/география/интересы/поведение
- боли целевой аудитории – какую потребность закрывает продукт
- ключевое сообщение/уникальное торговое предложение
- тип рекламы
- канал распространения
- креативные требования: стиль и тон коммуникации, элементы дизайна (лого, цветовая палитра, стиль визуализации, шрифты...), примеры вдохновения
- целевые метрики: охват, конверсия, клики
- организационные моменты: ответственные лица, бюджет, сроки

После проработки требований с заказчиком и разработки брифа проводятся креативные сессии, создаются мудборды, скетчи, текст рекламы, выбирается концепция исходя из технического задания, создаются прототипы и их варианты согласуются с заказчиком. Весь процесс итеративный, может занимать много времени и на первых этапах возможно его оптимизировать на моменте создания прототипов по итогам сессий.

Основная **цель** сервиса — сократить время на создание графических материалов и повысить качество визуального контента.Повысить качество визуального контента, обеспечивая соответствие стандартам бренда.Упростить процесс работы дизайнеров, позволяя им сосредоточиться на креативных задачах.

Существующие решения могут закрывать потребность пользователей, но они не имеют единой точки интерфейса, имеют неудобную систему хранения запросов и результатов: midjorney, crea.ai.

Основной пользователь:
 - Дизайнеры/копирайтеры в рекламных агентствах и студиях.
 - Маркетологи и бренд-менеджеры.
 - Компании с высоким объемом визуального контента (e-commerce, медиа).

# 1.1 Текущая ситуация (Assessing current solution)
На данном этапе сервис разрабатывает один разработчик. 
Технические ресурсы: облачный сервис, хранилище в s3.

Риски:
- Не уложиться в сроки.
- Малое количество или плохое качество данных, придется создавать синтетические данные
- Непонятная система оценки качества работы модели

# 1.2 Решаемые задачи с точки зрения аналитики (Data Mining goals)

С точки зрения ML-задачи сделаем так, чтобы запрос пользователя улучшался на основании промптов.

Решение предполагает работу в двух режимах:
1. Работа в режиме маркетолога: 
  Будем улучшать запрос пользователя на основе похожих промптов и учетом дополнительных параметров, которые пользователь укажет, и направлять его на генерацию картинок. 
2. Работа в режиме дизайнера компании: 
  Улучшаем запрос дизайнера на основе примеров дизайна компании и корпоративного стиля, описанного в брендбуке.

Будем использовать RAG-pipeline для 1 режима и дообучать LoRA для 2 режима.

Для оценки модели не понимаю, какую лучше метрику использовать:
В качестве объективного критерия качества можно:
- обучить модель классификации, которая будет оценивать выходной промпт по нескольким параметрам. – тут не знаю, подскажите пожалуйста как лучше поступить, тк не понимаю как можно выделить параметры
- использовать tripled loss 

Оценка результатов без объективного критерия качества:
- A/B тестирование между сгенерированными изображениями и существующими решениями.
- Сбор отзывов от конечных пользователей о том, насколько сгенерированные изображения соответствуют их ожиданиям и требованиям.

Критерии для бизнес-оценки:
- Качество генерации (экспертное)
- Среднее время создания промпта 
- Среднее время генерации картинки

# 1.3 План проекта (Project Plan)
1. Бизнес-анализ (Business Understanding) - 2 дня
2. Анализ данных (Data Understanding) – 3 дня
  - Выгрузка данных из существующих источников:
    - изображения, которые созданы компанией, ее бренд-буки и корпоративные стандарты.
   датасеты https://huggingface.co/datasets/multimodalart/vintage-ads, https://huggingface.co/datasets/X-ART/LeX-10K
 - Сбор данных
     - Формирование искусственного датасета с промптами.
 - Исследование данных
3. Подготовка данных (Data Preparation) - 1 неделя
 - Объединение и отбор данных
 - Очистка данных
 - Версионирование данных
4. Моделирование (Modeling) - 2 недели
 - Выбор модели
   - Исследование существующих моделей генерации изображений.
   - Выбор подходящей модели на основе требований к качеству, скорости генерации и простоты использования.
 - Обучение модели
 - Оценка модели
   - Оценка качества генерируемых изображений по критериям соответствия текстовым описаниям и стандартам бренда.
   - Проведение пользовательского тестирования для получения обратной связи и доработки модели.
5. Оценка результата (Evaluation) - 1 неделя
 - Оценка результатов моделирования
   - Качество изображений
   - Общее время генерации изображений.
 - Разбор полетов
 - Принятие решения
6. Внедрение (Deployment) - 2 недели
 - Планирование развертывания
 - Разработка интерфейса
 - Запуск сервиса
 - Настройка мониторинга модели
 - Формирование документации
