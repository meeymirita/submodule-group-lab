# Group Lab: RabbitMQ, Redis, Traefik, OOP, Vue, TypeScript, Laravel, Docker

![Group Lab](https://raw.githubusercontent.com/meeymirita/works-lab/main/images/banner.png)

Сборный репозиторий с лабораторными работами. Каждая работа подключена как git submodule в отдельной папке и живёт в собственном репозитории — со своей историей коммитов, независимо от остальных. Репозиторий будет пополняться новыми работами.

Витрина всех работ и их описания — на [`index.html`](index.html) (открывается прямо в браузере или через GitHub Pages).

## Работы

Порядок — от простого к сложному, с учётом того, что лабы переиспользуют друг друга: OOP-лаба даёт фундамент, который нужен для RabbitMQ и Laravel; RabbitMQ стоит пройти до Redis (проще почувствовать разницу между брокером и Redis-примитивами) и до Laravel-лабы (она прямо ссылается на обе); Vue — до TypeScript (сессия 5 последней использует Vue). Docker и Traefik самодостаточны и не завязаны на остальные.

| № | Папка | Лаба | Сложность | Репозиторий |
|---|---|---|---|---|
| 1 | [`docker`](docker) | Docker + Bash — крепкое владение с нуля | Базовая по входу, объёмная | [docker-lab](https://github.com/meeymirita/docker-lab) |
| 2 | [`php-coffee`](php-coffee) | OOP на PHP/Laravel — Coffee Shop API | Базовая по материалу | [oop-lab](https://github.com/meeymirita/oop-lab) |
| 3 | [`traefik`](traefik) | Traefik — reverse proxy, service discovery, TLS | Низкая–средняя | [traefik-lab](https://github.com/meeymirita/traefik-lab) |
| 4 | [`rabbitmq`](rabbitmq) | RabbitMQ — Transactional Outbox, воркеры, DLQ | Высокая | [rabbitmq-lab](https://github.com/meeymirita/rabbitmq-lab) |
| 5 | [`redis`](redis) | Redis — кэш, локи, rate limit, Streams | Средняя | [redis-lab](https://github.com/meeymirita/redis-lab) |
| 6 | [`vue`](vue) | Vue 3 — Helpdesk (Router, Pinia, WebSocket, тесты) | Высокая | [vue-lab](https://github.com/meeymirita/vue-lab) |
| 7 | [`typescript`](typescript) | TypeScript 5 — Warehouse (generics, Zod, API + Vue) | Высокая | [typescript-lab](https://github.com/meeymirita/typescript-lab) |
| 8 | [`laravel`](laravel) | Laravel 13 изнутри — TaskFlow (таск-трекер с ролями) | Высокая | [laravel-lab](https://github.com/meeymirita/laravel-lab) |

> Личный прогресс (моя пометка, не часть плана репозитория): ✅ пройдено — RabbitMQ. 🔵 сейчас прохожу — OOP (`php-coffee`).

---

## 1. Docker Lab (`docker/`)

> **Сложность: базовая по входу, но объёмная.** Не требует предыдущих лаб — рассчитана на полных новичков в контейнерах; Bash даётся параллельно, ровно в том объёме, который нужен для entrypoint-скриптов.

**О чём:** Docker и Bash разобраны подробно и с нуля — то, на что в Traefik-лабе был выделен всего один вводный раздел. Только сам Docker (образы, контейнеры, Dockerfile, тома, сети, Compose) и Bash как параллельный трек.

**Стек:** Node.js (Express) + PostgreSQL, всё в Docker / Docker Compose.

**Формат:** методичка `Docker_Bash_Lab.html` — методичка готова, прохождение впереди.

**Что внутри (3 сессии):** разбор Docker с нуля (образ vs контейнер vs Dockerfile), Bash параллельным треком (shebang, переменные, циклы, `set -e -u -o pipefail`), ENTRYPOINT vs CMD, тома и сети, Docker Compose (`depends_on` + healthcheck) — пошаговая сборка маленького Node.js + PostgreSQL проекта, заканчивается явной точкой возврата к Traefik Lab.

---

## 2. OOP Lab (`php-coffee/`)

> **Сложность: базовая по материалу** (нужен только синтаксис PHP, фреймворк — с сессии 5), но именно здесь стоит не спешить, если ООП пока даётся тяжело: это фундамент, который потом всплывает во всех остальных лабах.

**О чём:** объектно-ориентированное программирование на PHP 8.4 с нуля — не абстрактно, а на маленьком API кофейни. Отдельный, ни от чего не зависящий проект (в отличие от Redis/RabbitMQ-лаб не растёт из общей системы заказов).

**Стек:** Laravel 13 (PHP 8.4) + PostgreSQL + RabbitMQ + Mailpit — брокер появляется только в последней сессии.

**Формат:** методичка `OOP_Lab_CoffeeShop.html` — не пройдена, ниже план по оглавлению. Первая сессия начинается с чистого PHP без фреймворка, чтобы увидеть ООП "без магии Laravel".

**Что внутри (5 сессий):**
- **Сессия 1** — касса на массивах (и почему это плохо) → первый объект `Money` → `abstract class Drink` + `enum` + полиморфизм → заказ с инвариантами
- **Сессия 2** — тесты для `Money`; иерархия напитков-наследников; фабрика `DrinkType` + `GET /api/menu`
- **Сессия 3** — интерфейс `Beverage`; паттерн **Decorator** для добавок (сироп, шот и т.д.); сущность `Order` + `OrderStatus`; Repository + `POST /api/orders`
- **Сессия 4** — `DiscountPolicy` + `Clock`; чекаут со стратегиями оплаты (`PaymentMethod`) + `/pay`; тесты на стратегиях; эксперимент "а если бы делали через наследование" (чтобы почувствовать разницу с композицией)
- **Сессия 5** — `EventPublisher` + событие `order.paid`; воркеры (бариста + уведомления) на RabbitMQ — та же схема, что в RabbitMQ-лабе (один topic-exchange, две очереди); сквозной тест без БД и без брокера; финал "до/после"

Проходит через: 4 принципа ООП, `abstract class` vs `interface`, наследование vs композиция, паттерны (Factory, Decorator, Strategy, Repository), SOLID — всё на одном сквозном примере.

---

## 3. Traefik Lab (`traefik/`)

> **Сложность: низкая–средняя** (инфраструктурная, не про код — backend/frontend уже даны готовыми). Нужно перед стартом: Docker Compose на уровне «поднять сервис и почитать логи»; для новичков в контейнерах есть отдельный вводный раздел 0.

**О чём:** reverse proxy и service discovery для стека из нескольких сервисов — без ручной правки конфигов при каждом деплое, через Docker-labels.

**Стек:** Traefik 3 + Docker Compose (с заметками про Podman) + Node.js API + статический frontend + PostgreSQL + Adminer.

**Формат:** методичка `Traefik_Lab_Plan.html` — не пройдена, ниже план по оглавлению. Есть отдельный раздел 0 "Введение в Docker с нуля" для тех, кто раньше не работал с контейнерами.

**Что внутри (3 сессии):**
- **Сессия 1** — каталоги и `traefik/traefik.yml`; базовый `docker-compose.yml`; первый роутер через labels на тестовом сервисе `whoami`; dashboard Traefik и его защита; заметка про rootless Podman
- **Сессия 2** — backend API; frontend с path-routing (`StripPrefix`); PostgreSQL + Adminer за прокси; масштабирование API + healthcheck; цепочка middlewares
- **Сессия 3** — TLS через `mkcert` (локально) и Let's Encrypt (staging); canary-деплой (weighted round robin); "Production Hell" — финальный сценарий без подсказок

Модель для понимания: `EntryPoint → Router → Middleware → Service` — весь курс выстроен вокруг этой цепочки.

---

## 4. RabbitMQ Lab (`rabbitmq/`)

> **Сложность: высокая.** Нужно перед стартом: уверенный Laravel/PHP (транзакции, Artisan-команды, очереди хотя бы на уровне концепции), базовые транзакции SQL, Docker Compose «запустить и посмотреть логи».

**О чём:** асинхронная обработка заказов интернет-магазина через очереди, с упором на паттерны надёжной доставки — то, что в реальных системах спасает от потери и дублирования сообщений.

**Стек:** Laravel 13 (PHP 8.4) + PostgreSQL 16 + RabbitMQ (Management UI) + Mailpit, всё в Docker Compose.

**Архитектура:** HTTP-запрос создаёт заказ и **сразу** пишет "записку" о событии в таблицу `outbox_messages` — в той же транзакции БД (паттерн **Transactional Outbox**, чтобы не потерять событие, если публикация в брокер упадёт). Отдельный процесс `outbox-relay` забирает записки и публикует их в exchange `orders.topic`. Дальше три независимых воркера (`order-worker`, `email-worker`, `analytics-worker`) разбирают свои копии сообщения из очередей: резервируют склад, шлют письмо, пишут в аналитику.

**Что пройдено (все 3 сессии):**
- Хопы 1–8: путь заказа от HTTP до БД, шаг за шагом, с точками наблюдения (`dd()`, логи, RabbitMQ UI)
- Что происходит, когда не хватает товара на складе
- **Идемпотентный consumer**: таблица `processed_messages` защищает от повторной обработки при redelivery
- Competing consumers + **prefetch** (`basic_qos`) — честное распределение нагрузки vs эффект "воркера-заложника" при большом prefetch
- Crash-тесты: `docker compose kill` (грубое убийство) и падение **после коммита, но до `ack`** — на практике поймали баг с `SIGKILL` на PID 1 в контейнере (ядро Linux его игнорирует), заменили на `exit()`
- **Retry с TTL → DLX** для писем: `email.retry.1/2/3` (10с/30с/300с) → `email.dlx` → назад в `email.queue` или в `email.dlq` после исчерпания попыток
- Читатель DLQ (`worker:failed-email`) — ручной разбор "мёртвых" сообщений
- Сравнение с нативными Laravel Queue Jobs (`$tries`/`$backoff`/`failed_jobs`) на том же RabbitMQ — чтобы почувствовать, где ручной AMQP-слой даёт то, чего нет из коробки (идемпотентность, publisher confirms, чужие consumer'ы не на Laravel)
- **Priority queues** (`x-max-priority`) с backlog — почему приоритет виден только при накопленной очереди
- **Fanout** (`lab:broadcast` / `worker:broadcast`) — широковещание всем подписчикам через `system.broadcast`, в отличие от topic-маршрутизации остального проекта

**Пример выполнения — в самом репозитории `rabbitmq-lab` (сабмодуль `rabbitmq/`):**
- рабочий код всех воркеров и команд — `laravel-app/app/Console/Commands/`
- пошаговый разбор пути заказа (хопы, точки наблюдения, что смотреть в БД/UI/логах) — [`docs/order-path-explained.md`](https://github.com/meeymirita/rabbitmq-lab/blob/main/docs/order-path-explained.md)
- ответы на все 18 вопросов для самопроверки, привязанные к коду проекта — [`docs/self-check-answers.md`](https://github.com/meeymirita/rabbitmq-lab/blob/main/docs/self-check-answers.md)
- подборка справочных материалов по темам лабы — [`docs/rabbit.md`](https://github.com/meeymirita/rabbitmq-lab/blob/main/docs/rabbit.md)

---

## 5. Redis Lab (`redis/`)

> **Сложность: средняя.** Нужно перед стартом: то же, что для RabbitMQ-лабы (Laravel, Docker), домен заказов переиспользуется. Ниже порог входа, чем в RabbitMQ — но полезно уже пройти RabbitMQ, чтобы прочувствовать разницу между брокером и Redis-примитивами (Streams — не полноценная очередь).

**О чём:** Redis как кэш, хранилище сессий, примитив синхронизации и брокер событий — одновременно, на кусочке той же системы заказов. Лаба специально показывает, где каждая из этих ролей "подводит" (что будет при рестарте без AOF, при отвале Pub/Sub-подписчика, при гонке за один и тот же лок).

**Стек:** Laravel 13 + PostgreSQL 16 + Redis 7.

**Формат:** методичка `Redis_Lab_Plan.html` (открывается в браузере, прогресс по чекбоксам сохраняется локально) — ещё не пройдена, ниже план по оглавлению.

**Что внутри (3 сессии):**
- **Сессия 1** — docker-compose и `redis.conf`, Laravel + `.env`, миграции; **Cache-Aside** для карточки товара (`ProductRepository`); сессии в Redis (`SESSION_DRIVER=redis`); `StreamPublisher` — первый producer в Redis Streams; первый consumer (happy path)
- **Сессия 2** — **distributed lock** (`SET NX PX`) в `StockReservationService`, чтобы не продать один товар дважды; **rate limiter** (sliding window); competing consumers + нагрузочный тест; crash-тест на **PEL** (Pending Entries List) и идемпотентность
- **Сессия 3** — retry через `XAUTOCLAIM`; ручной DLQ-поток; приоритет очереди через `ZSET`; Pub/Sub-дашборд в реальном времени; "Production Hell" — комплексный сценарий без подсказок

Логика подачи материала зеркалит RabbitMQ-лабу (архитектура → сборка по шагам → "под капотом" → что почитать перед следующим шагом), но через призму структур данных Redis вместо AMQP.

---

## 6. Vue Lab (`vue/`)

> **Сложность: высокая, если фронтенд — новая территория.** Нужно перед стартом: уверенный JavaScript (ES6+, async/await, деструктуризация); опыт с Vue или другими фреймворками не требуется, бэкенд на NestJS дан готовым.

**О чём:** Helpdesk (система тикетов) на Vue 3 с нуля — реактивность, компоненты, роутинг и общее состояние, каждое понятие на одном сквозном примере. Бэкенд (маленький NestJS-сервис) дан готовым в первой же сессии — писать его не нужно, только запустить.

**Стек:** Vue 3.5 + Vite + Vue Router 4 + Pinia + Vitest, бэкенд — NestJS (TypeScript). Composition API + `<script setup>` (Options API — только в теории для сравнения). Всё в Docker.

**Формат:** методичка `Vue_Lab_Helpdesk.html` — не пройдена, ниже план по оглавлению.

**Что внутри (5 сессий, порядок строгий — Pinia раньше Router, потому что guard'ам роутера нужен auth-store):**
- **Сессия 1** — стенд (`docker-compose`, скаффолды Nest и `create-vue`); бэкенд NestJS (auth, tickets, comments, history, WebSocket-gateway) — дан готовым; песочница реактивности: `ref`/`reactive`/`computed`/`watch`, директивы, `v-model`, `v-for`/`key`; `useAsync` и первый запрос к API
- **Сессия 2** — разбор списка тикетов на компоненты: `StatusBadge`, `TicketCard`, `TicketList` (props/emits, слоты); `BaseModal` (слоты, Teleport, lifecycle, template refs); тосты через `provide`/`inject`; composable `useNow`/`RelativeTime`
- **Сессия 3** — Pinia: `state`/`getters`/`actions`, `storeToRefs`, auth-стор с токеном, persist-плагин; оптимистичная смена статуса тикета с откатом при ошибке
- **Сессия 4** — Vue Router: маршруты, lazy loading, `RouterLink`, guards (`requiresAuth`, роли, redirect после логина), вложенные маршруты, query-синхронизация, 404; страница тикета с вкладками, форма создания, `onBeforeRouteLeave`
- **Сессия 5** — WebSocket (`useSocket`) с живыми обновлениями через store; канбан-доска (`TransitionGroup`, `defineAsyncComponent`, динамический компонент); тесты на Vitest (компонент, composable, store, router guard); production-сборка и деплой за прокси

Главная мысль лабы: Vue — это реактивность + компоненты + экосистема (Router — состояние адресной строки, Pinia — общее состояние), и каждое задание про то, где живёт состояние и кто его меняет.

---

## 7. TypeScript Lab (`typescript/`)

> **Сложность: высокая** — абстрактное мышление на уровне типов (generics, conditional/mapped types) непривычно после динамического PHP. Нужно перед стартом: тот же JavaScript, что для Vue-лабы; логично проходить после или параллельно с ней (сессия 5 использует Vue).

**О чём:** типизация домена складского учёта (Warehouse) с нуля — без фреймворков до последней сессии, чтобы увидеть TypeScript в чистом виде и потом узнавать его в Nest/Vue. Что типы реально ловят (перепутанные аргументы, `NaN` от строки вместо числа, `undefined` в рантайме), а что — нет.

**Стек:** TypeScript 5.6 + Node 22 + `tsx` + Vitest + Zod, в финале — Express и Vue 3 + TS. Отдельный репозиторий на npm workspaces: `packages/core`, `cli`, `api`, `web`. Всё в Docker.

**Формат:** методичка `TypeScript_Lab_Warehouse.html` — не пройдена, ниже план по оглавлению. Каждый шаг заканчивается зелёным `npm run typecheck` — это главный критерий готовности.

**Что внутри (5 сессий, порядок строгий):**
- **Сессия 1** — стенд (Docker, workspaces, `tsconfig.base`, `tsx`, Vitest); песочница: аннотации, вывод типов, примитивы/объекты, union и литералы, `type` vs `interface`, функции, `any`/`unknown`/`never`, `strict`, `as const`
- **Сессия 2** — домен склада: branded IDs, размеченное объединение `Movement`, exhaustive `switch`, `Result` вместо исключений, type predicates, `readonly` — `applyMovement` с тестами, невозможные состояния невыразимы на уровне типов
- **Сессия 3** — generics и абстракции: `Repository<T>`, `TypedEmitter<Events>`, mapped/conditional/template literal types, `satisfies` — сервис `Warehouse`, собранный из типизированных кубиков
- **Сессия 4** — CLI: `parseArgs`, команды как union из template literal types, валидация через Zod и `z.infer`, `unknown` в `catch`, `.d.ts` для JS, сборка esbuild — рабочий `wh`: `item:add`, `stock:in/out/transfer/list/low`, `import:csv`
- **Сессия 5** — сквозная типизация: `ApiContract`, generic-клиент с conditional types, Express + Zod на бэкенде, Vue 3 + TS (`defineProps`/`defineEmits` с generics, типизированный store), `vue-tsc` — один источник типов и в API, и в браузере

---

## 8. Laravel Lab (`laravel/`)

> **Сложность: высокая.** Нужно перед стартом: базовый Laravel (роутинг, контроллеры, миграции, Blade — даются ссылками на документацию, без разбора), ООП на PHP (см. `php-coffee/`) и общее представление про очереди (см. `rabbitmq/`) — лаба на них ссылается, а не объясняет заново.

**О чём:** Laravel 13 "изнутри" — не "как вызвать", а что происходит на каждом слое фреймворка (~30 компонентов `illuminate/*`, связанных через контейнер), на сквозном таск-трекере **TaskFlow** с воркспейсами, ролями и приглашениями.

**Стек:** Laravel 13 (PHP 8.4) + PostgreSQL 17 + Redis 7 + RabbitMQ 4 + Mailpit + Laravel Reverb; фронт — Vue 3 + Vite (JavaScript, только API-клиент). Всё в Docker.

**Формат:** методичка `Laravel_Lab_TaskFlow.html` — не пройдена, ниже план по оглавлению.

**Что внутри (10 сессий):**
- **Сессия 1** — стенд (Docker, Laravel 13, Sanctum/Reverb/RabbitMQ-драйвер), схема данных и миграции
- **Сессия 2** — Eloquent: связи, pivot, N+1 — разбираем боль по шагам (`hasMany`/`belongsTo`, `belongsToMany` + свой Pivot-класс, `attach`/`sync`/`toggle`, полиморфные связи, `hasManyThrough`)
- **Сессия 3** — коллекции (`groupBy`/`partition`/`reduce`/`keyBy`), API Resources (`whenLoaded`/`whenCounted`), три вида пагинации
- **Сессия 4** — HTTP-слой: Form Requests, своё middleware с параметром, обработка исключений API, полноценный CRUD задач
- **Сессия 5** — Service Container и провайдеры: `build`/`bind`/`call` изнутри, contextual binding (`when`/`needs`/`give`)
- **Сессия 6** — Auth: Sanctum SPA (cookie + CSRF), Gate и Policy, роли, приглашения по токену + минимальный Vue-фронт (логин, доска)
- **Сессия 7** — Observer (жизненный цикл модели), события и Listeners, Job (retry, `ShouldBeUnique`, `failed_jobs`) на RabbitMQ — та же схема, что в RabbitMQ-лабе
- **Сессия 8** — Mailable (markdown-письма, очередь), Notification (mail + database), Scheduler (дайджест задач)
- **Сессия 9** — `Cache::remember` + инвалидация в Observer, `Cache::lock` от гонки, RateLimiter, Broadcasting через Reverb + Echo
- **Сессия 10** — фабрики для всех моделей, feature-тесты (`RefreshDatabase`), fakes/моки (Event/Notification/Mail), финальный прогон

Лаба построена вокруг карты Laravel (`Kernel → Middleware → Router → Controller`, плюс сквозные Container/Events/Auth и менеджеры Database/Cache/Queue/Mail/Broadcasting) и проходит по каждому слою последовательно — от жизненного цикла запроса до тестов.

---

## Витрина работ (`works/`)

Отдельный сабмодуль [`works-lab`](https://github.com/meeymirita/works-lab) со стилизованными обзорными страницами каждой лабы (тёмный неоновый дизайн, тот же, что и у [`index.html`](index.html)): что внутри, стек, куда открыть методичку и репозиторий. Там же лежат превью-картинки лаб (`works/images/`), которые использует и главная страница.

Открыть можно прямо по ссылке `works/<ключ-лабы>.html`, например [`works/rabbitmq.html`](works/rabbitmq.html).

---

## Клонирование

Репозиторий использует submodule, поэтому клонировать нужно с флагом `--recurse-submodules`:

```bash
git clone --recurse-submodules https://github.com/meeymirita/submodule-group-lab.git
```

Если репозиторий уже склонирован без этого флага:

```bash
git submodule update --init --recursive
```

## Добавление новой работы

```bash
git submodule add <url-репозитория-лабы> <папка>
git commit -m "Add <название> lab"
```

## Обновление сабмодуля до последнего коммита

```bash
cd <папка-лабы>
git pull origin main
cd ..
git add <папка-лабы>
git commit -m "Update <папка-лабы> submodule"
```

## Автор

Все лабы веду и прохожу самостоятельно, попутно ведя заметки и методички по каждой теме.
