# Laravel Lab (TaskFlow) — находки

Методичка: `laravel/Laravel_Lab_TaskFlow.html` (~300 КБ, 16 разделов, 10 сессий). Битых якорей в оглавлении нет.
Стек в README, карточке и методичке совпадает: Laravel 13 (PHP 8.4), PostgreSQL 17, Redis 7, RabbitMQ 4, Mailpit, Reverb, Vue 3.

## 🟡 README и методичка расходятся по зависимостям
README: «нужен… ООП на PHP (см. oop-lab)… лаба на них ссылается, а не объясняет заново».
В методичке RabbitMQ-лаба упоминается 10 раз, а **OOP-лаба — ни разу**. Либо добавить в методичку 1–2 отсылки
(например, в разделе 3 про контейнер: «Decorator/Strategy из OOP-лабы…»), либо убрать из README фразу «ссылается».

## 🟡 Пакет RabbitMQ-драйвера
Используется `vladimir-yuldashev/laravel-queue-rabbitmq`. Этот пакет обычно выпускает поддержку новой мажорной
версии Laravel с задержкой. Перед стартом стоит проверить, что есть релиз с поддержкой Laravel 13
(`composer require` сам скажет). Если нет — в методичке нужен запасной путь. (Проверить из офлайна я не могу.)

## 🟡 Номера разделов, на которые ссылается «Чистый PHP»
PHP-лаба ссылается на «раздел 5 Laravel-лабы» про Container. Здесь Container — **раздел 3** (теория) и **сессия 5**
(практика). Править лучше в PHP-лабе (см. `fixes/php.md`), здесь ничего менять не надо.

## 🟢 Версии
- «auto-discovery listeners с Laravel 11+» — верно.
- Образы: `php:8.4-cli`, `postgres:17`, `redis:7`, `rabbitmq:4-management`, `axllent/mailpit`, `node:22` — рабочие.
  Postgres здесь 17, а в Redis/NestJS/Traefik — 16 (см. `fixes/site.md`, «разнобой версий»).


---

## ✍️ Варианты решений

Оставь в каждом вопросе **один** вариант (остальные удали или поставь `[x]` у нужного).
Потом я прочитаю этот файл и перепишу лабу ровно по выбранному. ⭐ — моя рекомендация.

### 1. README говорит, что методичка «ссылается» на OOP-лабу, а в методичке ссылок нет
- [x] ⭐ A — добавить в методичку 1–2 отсылки к OOP-лабе (контейнер, паттерны Decorator/Strategy)
- [ ] B — убрать «ссылается» из README, оставить OOP-лабу просто как рекомендацию

### 2. Пакет `vladimir-yuldashev/laravel-queue-rabbitmq` и Laravel 13
- [x] ⭐ A — добавить в сессию 1 проверку «если composer ругается на версию — вот запасной путь» (свой минимальный драйвер через php-amqplib, как в RabbitMQ-лабе)
- [ ] B — оставить, проверю сам при старте

### 3. PostgreSQL 17 (в других лабах 16)
- [x] ⭐ A — как решишь в `site.md` (вопрос 1)
- [ ] B — отдельно: оставить 17

---

## 📖 Вычитка методички

### Часть 1 (разделы 1–11, теория)
- — сверено: карта компонентов (раздел 1) vs таблица «Слой/Классы/Сессия» (раздел 1) — номера сессий (1,4,5,1–3,3–4,6,7,8–9,9,10) соответствуют оглавлению и реальным заголовкам сессий 1–10; раздел 4 (Eloquent/pivot) vs раздел 11 (домен TaskFlow) — имена таблиц/колонок (workspace_user+role, task_user+assigned_at, label_task, attachments morph attachable=Task|Comment, activities morph subject) совпадают; раздел 6 (middleware-пример) vs раздел 11 (структура app/Http/Middleware/EnsureWorkspaceRole) — не противоречат; раздел 8 (очереди) vs раздел 9 (RabbitMQ-драйвер) — согласовано. Расхождений не найдено.

### Часть 2 (раздел 11 продолжение, раздел 12 — Сессия 1 целиком, Сессия 2 шаги 2.1–начало 2.2)
- **[тех] Шаг 1.2, «ОЖИДАЕМЫЙ РЕЗУЛЬТАТ»** — «16 таблиц (считая users, personal_access_tokens, jobs/failed_jobs/job_batches, notifications — их создали install:api и стандартные миграции)» → (1) `notifications` не создаётся ни `install:api`, ни стандартными миграциями Laravel — нужна отдельная `php artisan notifications:table` (в лабе она не выполнялась, а уведомления появляются только в сессии 8); (2) даже без notifications подсчёт не сходится: 11 своих таблиц (workspaces, workspace_user, projects, tasks, task_user, labels, label_task, comments, attachments, activities, invitations) + users + personal_access_tokens + jobs + failed_jobs + job_batches = 16, но тогда notifications лишний в перечислении (даёт 17); кроме того, не упомянуты реально создаваемые стандартными миграциями password_reset_tokens, sessions, cache, cache_locks (это даёт ещё +4, т.е. фактически 20 таблиц). → исправить: либо убрать notifications из перечисления и явно упомянуть password_reset_tokens/sessions/cache/cache_locks, либо пересчитать итоговое число.
- **[текст] Раздел 12, Шаг 1.2, комментарий про task_user/label_task** — «У task_user и label_task, наоборot, композитный PK» → латиница «ot» вместо кириллического «от» (опечатка подтверждена и в исходном HTML). → исправить на «наоборот».
- — остальное в части 2 проверено: docker-compose.yml (образы/порты/profiles) vs env-переменные (RABBITMQ_*, REVERB_*, DB_*) vs config/queue.php (RABBITMQ_QUEUE, ключи hosts/options) — согласованы; profiles scheduler/reverb/web помечены «включим в сессии 8/9/6» и совпадают с темами этих сессий по оглавлению; имена моделей/миграций (Workspace, Project, Task, Comment, Label, Attachment, Activity, Invitation + workspace_user/task_user/label_task) из Шага 1.2 совпадают с use в Шаге 2.1 (Model::preventLazyLoading, casts для Task); PHP-синтаксис миграций (b18.php), enum'ов Role/TaskStatus/TaskPriority (b17.php), моделей Workspace/Project/Task (b20.php) — `php -l` без ошибок. Других расхождений не найдено.

### Часть 3 (сессия 2, шаги 2.2–2.8)
- **[противоречие]/[тех] Шаг 2.4, belongsToMany** — код и тинкер прямо показывают, что `Workspace::first()->members()->getTable()` без явного имени возвращает `"user_workspace"` (Eloquent берёт единственное число обеих моделей «в алфавитном порядке»). Но абзац «Зачем» тут же утверждает: «в вашей схеме имя `workspace_user` совпадает с конвенцией лишь случайно для этой пары (`workspace < user` по алфавиту — ok)» — это неверно и противоречит только что показанному коду: по алфавиту `user` < `workspace` (u раньше w), поэтому конвенция даёт именно `user_workspace`, а не `workspace_user`, что и было продемонстрировано как раз абзацем выше как ошибка угадывания. Исправить: убрать «ok»/«совпадает» и явно сказать, что `workspace_user` НЕ соответствует конвенции (отсюда и нужен явный второй аргумент), заменить «workspace < user» на «user < workspace».
- **[тех] Шаг 2.6, «Зачем»** — опечатка `típично` (латинская í вместо кириллической «и») в фразе «форма «выбрать метки чекбоксами» — típично для sync». Исправить на «типично».
- — остальное по части 3 (модели/связи `Workspace::projects/owner`, `Project::workspace/tasks`, `Task::project/creator`, SQL-примеры N+1 (1+7×2=15, eager loading 3 запроса), имена pivot-таблиц `workspace_user`/`task_user`/`label_task` против миграций сессии 1, `Membership`-класс и `$incrementing=true` (в migration `workspace_user` есть `$t->id()`), `hasManyThrough(Task::class, Project::class)` против `tasks.project_id`/`projects.workspace_id`, `morphs('attachable')`/`morphTo()`, `morphMap`) — сверено с частью 2 (модели/миграции сессии 1–2) и логикой Eloquent, расхождений не найдено.

### Часть 4 (сессия 3 целиком: 3.1–3.3; сессия 4: 4.1–4.2, начало 4.3 без «Итог» — хвост сессии 4 физически лежит в part_05.txt, это только разбивка файлов вычитки, не методички)
- **[тех] Шаг 3.2, UserResource** — `whenPivotLoaded('workspace_user', fn () => $this->membership->role->value)` не будет работать даже для «настоящих» участников воркспейса: в шаге 2.5 связь `Workspace::members()`/`User::workspaces()` явно переименовывает pivot-аксессор через `->as('membership')`, а `whenPivotLoaded()` без второго аргумента-аксессора проверяет по умолчанию именно `$this->pivot` (это `whenPivotLoadedAs('pivot', ...)`). Поскольку аксессор `pivot` для этой связи больше не существует (переименован в `membership`), условие всегда ложно — поле `role` не появится никогда, даже если ресурс когда-то используют для списка участников воркспейса, а не только для `assignees`. Нужно `whenPivotLoadedAs('membership', 'workspace_user', ...)`.
- **[тех]/[противоречие] Шаг 4.1, StoreTaskRequest** — код: `Rule::exists('labels', 'id')->where('workspace_id', fn () => $this->route('project')->workspace_id)` — двухаргументная форма `where(column, value)`, где `value` — замыкание. `DatabaseRule::where()` не резолвит замыкание, переданное вторым аргументом (лениво выполняется только форма с ОДНИМ аргументом-замыканием: `->where(fn ($query) => ...)`, которая уходит в `using()`); переданный как значение `Closure` уйдёт в биндинг SQL как есть и приведёт к ошибке/неверному сравнению, а не подставит `workspace_id`. Это же расхождение видно и в самой методичке: блок «ПРОВЕРЬ СЕБЯ» тут же описывает этот код иначе — как `Rule::exists(...)->where(fn () => ...)` (однoаргументная форма) — то есть вопрос не соответствует показанному чуть выше коду. Исправить код на `Rule::exists('labels', 'id')->where(fn ($query) => $query->where('workspace_id', $this->route('project')->workspace_id))`.
- **[тех] Шаг 4.2, routes/api.php** — `Route::apiResource('projects.tasks', TaskController::class)->shallow()->middleware('workspace.role:member')` регистрируется ПЕРЕД `Route::delete('/tasks/{task}', ...)->middleware('workspace.role:admin')`, а комментарий утверждает, что второй маршрут «перекроет предыдущий route». В Laravel маршруты матчатся в порядке регистрации, побеждает первый подходящий — значит `DELETE /tasks/{task}` из `apiResource` (уровень доступа `member`) сработает раньше и всегда «победит», а более строгий `admin`-маршрут ниже окажется недостижим (мёртвый код). На демонстрационных curl (`viewer`→403, `admin`→204) это не видно, потому что оба случая совпадают со сравнением по `member`, но участник с ролью `member` (не admin) в реальности сможет удалить задачу, хотя по описанию должен только admin/owner. Исправить: либо зарегистрировать `Route::delete` раньше `apiResource`, либо исключить `destroy` из `apiResource` через `->except(['destroy'])`.
- **[текст] Шаг 4.1, ПРОВЕРЬ СЕБЯ** — опечатка «Что произойдёt» (латинская t вместо «т»).
- — остальное по части 4 (коллекции 3.1 — `groupBy`/`partition`/`countBy`/`keyBy`/`reduce`/`when` на моделях `Workspace`/`Task` из сессии 2; ресурсы 3.2 — `TaskResource`/`LabelResource`/`whenLoaded`/`whenCounted` против связей `creator/assignees/labels`/`withCount('comments')`; пагинация 3.3 — `paginate`/`simplePaginate`/`cursorPaginate`, HTTP-статусы, `TaskFactory`; CRUD 4.1 — `StoreTaskRequest`/`UpdateTaskRequest`/`TaskController`, коды 201/422/404; middleware 4.2 — `EnsureWorkspaceRole`, алиас, порядок `auth:sanctum`) — сверено с частями 1–3 и синтаксисом PHP 8.4/Laravel 13, расхождений не найдено.
