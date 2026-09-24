# GraphQL Lab (CineGraph) — находки

Методичка: `graphql/GraphQL_Lab_Plan.html`. Битых якорей в оглавлении нет.
README, карточка и методичка согласованы: NestJS + Apollo Server (code-first), Prisma 7 + `@prisma/adapter-pg`,
PostgreSQL 17, Redis 7, DataLoader, bcryptjs.

## 🟢 Актуальность — лучшая среди лаб
- Prisma 7 описана правильно: `prisma.config.ts`, генератор `prisma-client` с выводом в проект, driver adapters,
  `migrate dev` больше не запускает generate/seed. Есть блок «ПРО ВЕРСИИ» с объяснением.
- Apollo Server 5 + `@as-integrations/express5` — верно (интеграции вынесены в отдельные пакеты).
- GraphiQL вместо удалённого playground — верно.
- Предупреждение про смену пути данных в PostgreSQL 18 — полезно.

## 🟡 Мелочи
- «NestJS 11/12» — хорошо, что с запасом; проверь при старте, какую версию реально поставит `nest new`.
- `redis:7-alpine` — актуален Redis 8 (см. `fixes/redis.md`); для подписок разницы нет.
- В этой лабе Prisma 7, в NestJS-лабе Prisma 6 — это **намеренно** (в NestJS это объяснено), но в корневом README
  стоит одной фразой сказать, чтобы не выглядело рассинхроном.
- Самостоятельность («ни от одной лабы не зависит») подтверждается текстом — ссылок на другие лабы в методичке нет.


---

## ✍️ Варианты решений

Оставь в каждом вопросе **один** вариант (остальные удали или поставь `[x]` у нужного).
Потом я прочитаю этот файл и перепишу лабу ровно по выбранному. ⭐ — моя рекомендация.

### 1. Prisma 7 здесь, Prisma 6 в NestJS
- [x] ⭐ A — одна фраза в корневом README: разные версии намеренно (NestJS зафиксирована на 6, GraphQL показывает 7)
- [ ] B — не трогать

### 2. Redis 7
- [x] ⭐ A — как решишь в `site.md` (вопрос 3)
- [ ] B — отдельно: оставить 7

---

## 📖 Вычитка методички

### Часть 1 (разделы 1–3)
- — сверил: оглавление (13 разделов + 3 сессии шагов) против всех `<h2 id="sec-N">`/`<div class="step" id="step-X-Y">` — все ссылки TOC ведут на существующие якоря, нумерация шагов (1.1–1.5, 2.1–2.4, 3.1–3.3, 4.1–4.4, 5.1–5.4, 6.1–6.6) не пересекается и не дублируется в пределах видимого текста; нумерация терминов-сносок (¹…³⁵) идёт по порядку появления без пропусков/повторов и все `href="#term-N"` резолвятся; таблица nullability ([Review]/[Review!]/[Review]!/[Review!]!) соответствует спецификации GraphQL; итоговая SDL-схема (Movie/Person/Credit/CastCredit/DirectorCredit/Review/User/ReviewConnection/Query/Mutation/Subscription) сверена с примером запроса из 2.1 (поля/типы/аргументы совпадают: movie(id: ID!), director: Person!, cast: [CastCredit!]!, reviews(first, after)) и с таблицей «модуль NestJS → корневые поля» (MoviesModule/ReviewsModule/AuthModule/UsersModule владеют ровно теми полями, что перечислены в Query/Mutation/Subscription); сценарий в разделе 6 (кол-во SQL на шаг) пересчитан вручную и совпадает с логикой батчинга из раздела 5 (шаг 2 — 7 SQL = 4 базовых + 3 на отзывы/авторов; шаг 8 — LIMIT first+1=6 согласно «трюку» из раздела 8); диаграммы (parse→validate→execute, N+1, архитектура) не противоречат сопровождающему тексту. Латиницы вместо кириллицы и сырых бэктиков не найдено (grep по исходному HTML). Расхождений не найдено.

### Часть 2 (разделы 4–9, шаг 1.1)
- — сверил: таблица стека (раздел 4) против команд `npm i` в шаге 1.1 — все пакеты из таблицы (GraphQL-триада, Prisma+adapter-pg, dataloader, class-validator/transformer, @nestjs/jwt+bcryptjs, graphql-subscriptions/graphql-redis-subscriptions/ioredis, graphql-query-complexity) присутствуют в командах установки; дерево файлов проекта (раздел 4) против структуры модулей и имён классов, упомянутых в «Под капотом» (MoviesResolver/MovieReviewsResolver ↔ movies.resolver.ts/movie-reviews.resolver.ts, LoadersFactory ↔ loaders.factory.ts) — совпадает; `.gitignore`-строка `src/generated/` в шаге 1.1 согласована с комментарием «ГЕНЕРИРУЕТСЯ, в .gitignore» у `generated/prisma/`, а `schema.gql` (коммитится) не подпадает под игнор; раздел 8 (offset vs cursor, LIMIT first+1, защита от тяжёлых запросов) сверен со схемой из раздела 3 (Query.movies — offset/limit, Movie.reviews — first/after) и со сценарием раздела 6 — противоречий нет. Раздел 9 проверен только до конца шага 1.1 (граница части); проверены анкоры `#step-1-1`/`#step-1-2` и корректность bash-команд (перенос строки через `\`, `--skip-git`, `echo >> .gitignore`). Расхождений не найдено.
### Часть 5 (разделы Шаг 4.2–5.4)
- — сверено: анкоры/заголовки `step-4-2..step-5-4` в HTML совпадают с оглавлением и с `<span class="num">`; ссылки из TOC (`href="#step-X-Y"`) ведут на верные id, повисших якорей нет.
- — сверено: `Loaders`/`LoadersFactory` (src/loaders/loaders.factory.ts из части 4) — добавление `reviewStats` в шаге 4.2 не конфликтует с уже существующими `person/movie/user/castByMovie/castByPerson/moviesByDirector/reviewsByAuthor`; `reviewsByAuthor`, используемый в шаге 5.2 (`loaders.reviewsByAuthor.load`), действительно объявлен в 4.1.
- — сверено: `Movie`/`Person`/`Review`/`User` (модели из частей 3–4) не содержат полей `averageRating`/`reviewCount`/`email`(@Field)/`reviews` — они добавляются через `@ResolveField` в отдельных резолверах (`MovieReviewsResolver`, `UsersResolver`), конфликтов декларации полей нет.
- — сверено: `AddReviewInput`/`UpdateReviewInput`/`Review` — типы `rating`/`text`/`movieId` совпадают с `model Review` (prisma/schema.prisma, часть 3), включая `@@unique([movieId, authorId])` → корректно обрабатывается как `ALREADY_REVIEWED` (P2002) в `ReviewsService.add`.
- — сверено: `GqlContext`/`AuthUser` (common/context.ts, часть 4) — поля `req?/user/loaders` совпадают с тем, что кладёт `context()` в шаге 4.3; `GqlAuthGuard`/`RolesGuard`/`CurrentUser`/`Roles` используют `GqlExecutionContext.create(context).getContext<GqlContext>()` согласованно.
- — сверено: `MoviesResolver.movies`/`MoviesResolver.movie` (часть 4) — `movies` без `nullable` (→ `[Movie!]!`), `movie` с `{ nullable: true }` — согласуется с примерами Эксперимента 2 в шаге 5.1 (`movies` роняет весь `data`, `movie(id)` гасит ошибку на себе).
- — сверено: `process.env.LAB_FAIL_DIRECTOR_OF_MOVIE` (рычаг из part 4, резолвер `director`) корректно используется в Эксперименте 2 шага 5.1.
- — сверено: ссылки «см. раздел 7» (шаги 5.1, 5.2) указывают на раздел «7. Ошибки и nullability» — совпадает с оглавлением.
- — остальное: пройден код шагов 4.2–5.4 целиком (DataLoader reviewStats, AuthService/AuthResolver/AuthModule, ReviewsService/ReviewsResolver, users.resolver email/reviews, CreateMovieInput/createMovie, Credit/CastCredit/DirectorCredit/SearchResult, курсорная пагинация Reviews) на технические ошибки, противоречия в именах файлов/классов/полей и опечатки — расхождений не найдено.

### Часть 6 (Шаг 5.1(частично, см. выше)–6.1 начало)
- — сверено: `formatGqlError` (src/graphql/format-error.ts) — использование `unwrapResolverError`, `GraphQLFormattedError`, `formatError`/`includeStacktraceInErrorResponses` как опций Apollo Server через `GraphQLModule.forRootAsync` — валидные, реально существующие API.
- — сверено: примеры кодов ошибок (`BAD_USER_INPUT`, `UNAUTHENTICATED`, `FORBIDDEN`, `NOT_FOUND`, `ALREADY_REVIEWED`, `EMAIL_TAKEN`) в `common/errors.ts` (введён в шаге 4.3, часть 5) используются далее без новых незаявленных кодов.
- — сверено: `Credit`/`CastCredit`/`DirectorCredit`/`SearchResult` (шаг 5.3) — `CreditResolver` заменяет удаляемый `cast-credit.resolver.ts` (из части 4); поля `movie`/`person` резолвятся через существующие `loaders.movie`/`loaders.person`.
- — сверено: `ReviewConnection`/`ReviewEdge`/`PageInfo`/`encodeCursor`/`decodeCursor` (шаг 5.4) — индекс `@@index([movieId, id])` в `model Review` (часть 3) соответствует запросу `where: { movieId, id: { lt } }, orderBy: { id: 'desc' }`.
- — сверено: переход в шаг 6.1 (`PubSubModule`, `REVIEW_ADDED`, публикация события в `ReviewsService.add`) — текст обрывается на границе файла (продолжение в части 7, `reviews.resolver.ts` Subscription) — это следствие нарезки на части, не ошибка методички.
- — остальное: пройден код шага 5.1 (продолжение) и начала 6.1 целиком (formatError, PubSubModule) на технические ошибки, противоречия имён/полей и опечатки — расхождений не найдено.
