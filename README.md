# Проблема надежности/проблемы с версиями в LlamaIndex

* https://github.com/run-llama/llama_index/issues/16774

Из-за ужасной документации и порой грубого отсутствия оптимизации для разработчиков, о чем я подробно писал в [LangChain-ToolDocs-Problem-ru](https://github.com/hherpa/LangChain-ToolDocs-Problem-ru-), и в ожидании ответа на мой [issue](https://github.com/langchain-ai/langchain/issues/27668), мне пришлось перейти с LangChain на LLamaIndex. Однако здесь сразу возникли сложности: первые три строки [документации](https://llamahub.ai/l/llms/llama-index-llms-fireworks?from=llms) — две строки для установки библиотеки и строка `from llama_index.llms.fireworks import Fireworks` — привели меня сразу к двум ошибкам.

Если в случае с LangChain причинами создания issue было требование вручную обрабатывать 98 страниц документации, посвященной интеграции различных инструментов с LLM агентом, а также платформы API-поставщиков (вместо удобной таблицы как в других разделах документации или хотя бы более шаблонного кода как в LLama_Index), то здесь проблема оказалась в том, что ошибки возникают уже "из коробки", буквально на первых строках кода после установки.

* Меня больше интересует не столько решение конкретных ошибок, с которыми я столкнулся, сколько вероятность возникновения ошибок на стороне пользователя в будущем. Ожидается, что следуя указаниям документации, разработчик сможет без труда запустить проект. Однако наличие ошибок на столь раннем этапе использования библиотеки вызывает сомнения в ее стабильности и надежности.

**Краткое описание ошибок (полную картину вы можете наблюдать в блокноте №1-2):** я использую самую новую версию библеотеки llama-Index и llama-index-llms-fireworks, но при обращении `from llama_index.llms.fireworks import Fireworks` у меня возникают две ошибки импорта: `cannot import name 'global_handler' from 'llama_index.core' (unknown location)` и `ImportError: cannot import name 'Secret' from 'pydantic'`.

1. **[Блокнот](https://github.com/hherpa/LlamaIndex-Version-Issues-RU/blob/main/cannot_import_name_Secret_from_pydantic.ipynb) с первой ошибкой**
2. **[Блокнот](https://github.com/hherpa/LlamaIndex-Version-Issues-RU/blob/main/cannot_import_name_global_handler_from_llama_index.ipynb) с второй ошибкой**
