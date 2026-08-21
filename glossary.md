# Glossary

## Токены и представление текста

### Tokenizer

Компонент, который преобразует исходный текст в последовательность токенов и token IDs по правилам конкретного vocabulary.

### Token

Дискретная текстовая единица после токенизации: слово, часть слова, знак пунктуации, пробел или другой фрагмент.

### Token ID

Числовой идентификатор токена в vocabulary конкретного tokenizer.

### Vocabulary

Фиксированный набор токенов, которым tokenizer сопоставляет token IDs.

### Subword tokenization

Подход, при котором частые последовательности могут быть крупными токенами, а редкие слова разбиваются на несколько частей.

### BPE

Один из алгоритмов subword tokenization, объединяющий часто встречающиеся соседние последовательности.

### Embedding

Числовой вектор, получаемый из token ID и используемый нейросетью для дальнейших вычислений.

### Detokenization / tokenizer decoding

Обратное преобразование token IDs в байты/текст. Это не decode phase LLM inference.

## Inference и генерация

### Inference

Запуск уже обученной модели для обработки конкретного input и получения output. Во время обычного inference веса модели не переобучаются.

### Prefill

Фаза inference, в которой обрабатываются заранее известные input tokens и формируется состояние/KV cache.

### Decode phase

Последовательная autoregressive генерация новых output tokens после prefill.

### Generation

Процесс выбора и добавления следующих токенов до завершения ответа или достижения stop/output limit.

### Logits

Не-нормализованные оценки модели для возможных следующих токенов.

### KV cache

Кэш key/value представлений attention для уже обработанного prefix.

### Thinking tokens

Внутренние reasoning tokens, используемые reasoning-моделью и учитываемые в output billing.

### Effort
Параметр новых Claude для управления глубиной thinking/reasoning и связанным token spend. В Claude Sonnet 5 adaptive thinking включён по умолчанию, а `effort` управляет его глубиной.

`effort` не является прямой заменой `temperature`, `top_p` или `top_k`: sampling-параметры управляли выбором токенов, а `effort` управляет объёмом reasoning.


## Контекст и память

### Context

Информация, доступная модели в текущем inference.

### Context window

Максимальный токенный объём активного контекста, поддерживаемый моделью/API.

### Context rot

Практическая деградация способности надёжно использовать нужную информацию по мере роста/зашумления context.

### Lost in the Middle

Эффект, при котором информация в середине очень длинного context может извлекаться хуже, чем ближе к началу или концу.

### MRCR

Long-context benchmark для поиска и различения нескольких похожих фрагментов информации.

### Mean Match Ratio

Метрика MRCR: средняя степень совпадения ответа модели с целевым фрагментом; это не обычная accuracy полностью правильных ответов.

### Compaction

Сжатие длинной истории с сохранением наиболее важного рабочего состояния.

### /compact

Команда Claude Code для summarization текущей conversation history.

### /clear

Команда Claude Code для очистки conversation history перед новой независимой задачей.

### External state

Состояние вне активного context window: файлы, Git, БД, vector store и другие persistent storage.

## Retrieval и agentic engineering

### RAG

Retrieval-Augmented Generation: поиск релевантных фрагментов во внешнем источнике и добавление выбранных данных в active context.

### Retrieval

Выбор информации, наиболее релевантной текущему запросу или этапу задачи.

### Tool result

Результат вызова внешнего инструмента, который может быть добавлен в context.

### Subagent

Отдельный агент/вызов с более узкой задачей и собственным context.

### Context engineering

Проектирование того, какие инструкции, history, retrieval results и tool outputs доступны модели на каждом шаге.

### Signal-to-noise ratio

Соотношение полезной информации к нерелевантному или устаревшему контексту.

## Стоимость и оптимизация

### Input tokens

Токены контекста, которые модель обрабатывает как input.

### Output tokens

Токены, сгенерированные моделью; billed output reasoning-моделей может также включать thinking tokens.

### Prompt caching

Повторное использование большого неизменного prefix для снижения повторных вычислений/стоимости.

### Cache hit

Повторное использование уже закэшированного prefix.

### Batch API

Асинхронная массовая обработка запросов, подходящая для offline workloads.

### Token budget

Планируемый объём input, thinking и output с учётом context limit, стоимости и latency.

## Стохастичность и sampling

### Next Token Prediction
Autoregressive принцип: на каждом decode step модель вычисляет распределение следующего токена, выбирает токен и добавляет его в context.

### Probability distribution
Нормализованные вероятности возможных следующих токенов. Сумма вероятностей равна 1.

### Sampling
Выбор следующего токена из probability distribution с учётом вероятностей кандидатов.

### Stochasticity
Стохастичность — наличие случайности в generation, из-за которой одинаковый prompt может приводить к разным последовательностям токенов и разным ответам.


### Temperature
Устаревший для новых Claude sampling parameter, который менял резкость probability distribution. Последняя Sonnet-модель с поддержкой — **Claude Sonnet 4.6**. В Claude Sonnet 5 non-default значение `temperature` возвращает `400`.

В новых Claude `effort` управляет глубиной reasoning, а prompting — поведением и вариативностью output. `effort` не является прямым эквивалентом temperature.


### top-k
Устаревший для новых Claude sampling filter, ограничивающий candidates K наиболее вероятными токенами. Последняя Sonnet-модель с поддержкой — **Claude Sonnet 4.6**. В Claude Sonnet 5 non-default `top_k` возвращает `400`.


### top-p / nucleus sampling
Устаревший для новых Claude sampling filter по cumulative probability. Последняя Sonnet-модель с поддержкой — **Claude Sonnet 4.6**. В Claude Sonnet 5 non-default `top_p` возвращает `400`.


### Determinism
Способность системы повторять один результат для одинакового input. `T=0` повышает стабильность, но не гарантирует абсолютную идентичность output.

### Structured output
Output с формальным контрактом, например JSON Schema, пригодный для машинной валидации.

### Eval pipeline
Автоматизированная проверка model output через schemas, tests, business rules и другие validators.


## Источник по sampling-параметрам

- Anthropic, Migration guide: https://platform.claude.com/docs/en/about-claude/models/migration-guide
