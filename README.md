# Проблема надежности/проблемы с версиями в LlamaIndex

* **Issue:** https://github.com/run-llama/llama_index/issues/16774

Из-за ужасной документации и порой грубого отсутствия оптимизации для разработчиков, о чем я подробно писал в [LangChain-ToolDocs-Problem-ru](https://github.com/hherpa/LangChain-ToolDocs-Problem-ru-), и в ожидании ответа на мой [issue](https://github.com/langchain-ai/langchain/issues/27668), мне пришлось перейти с LangChain на LLamaIndex. Однако здесь сразу возникли сложности: первые три строки [документации](https://llamahub.ai/l/llms/llama-index-llms-fireworks?from=llms) — две строки для установки библиотеки и строка `from llama_index.llms.fireworks import Fireworks` — привели меня сразу к двум ошибкам.

Если в случае с LangChain причинами создания issue было требование вручную обрабатывать 98 страниц документации, посвященной интеграции различных инструментов с LLM агентом, а также платформы API-поставщиков (вместо удобной таблицы как в других разделах документации или хотя бы более шаблонного кода как в LLama_Index), то здесь проблема оказалась в том, что ошибки возникают уже "из коробки", буквально на первых строках кода после установки.

* Меня больше интересует не столько решение конкретных ошибок, с которыми я столкнулся, сколько вероятность возникновения ошибок на стороне пользователя в будущем. Ожидается, что следуя указаниям документации, разработчик сможет без труда запустить проект. Однако наличие ошибок на столь раннем этапе использования библиотеки вызывает сомнения в ее стабильности и надежности.

**Краткое описание ошибок (полную картину вы можете наблюдать в блокноте №1-2):** я использую самую новую версию библеотеки llama-Index и llama-index-llms-fireworks, но при обращении `from llama_index.llms.fireworks import Fireworks` у меня возникают две ошибки импорта: `cannot import name 'global_handler' from 'llama_index.core' (unknown location)` и `ImportError: cannot import name 'Secret' from 'pydantic'`.

1. **[Блокнот](https://github.com/hherpa/LlamaIndex-Version-Issues-RU/blob/main/cannot_import_name_Secret_from_pydantic.ipynb) с первой ошибкой**
2. **[Блокнот](https://github.com/hherpa/LlamaIndex-Version-Issues-RU/blob/main/cannot_import_name_global_handler_from_llama_index.ipynb) с второй ошибкой**

Переустановив Jupyter и следующий набор библиотек:

```
llama-index-llms-fireworks==0.2.2
llama-cloud==0.1.4
llama-index==0.11.21
llama-index-agent-openai==0.3.4
llama-index-cli==0.3.1
llama-index-core==0.11.21
llama-index-embeddings-openai==0.2.5
llama-index-indices-managed-llama-cloud==0.4.0
llama-index-legacy==0.9.48.post3
llama-index-llms-fireworks==0.2.2
llama-index-llms-openai==0.2.16
llama-index-multi-modal-llms-openai==0.2.3
llama-index-program-openai==0.2.0
llama-index-question-gen-openai==0.2.0
llama-index-readers-file==0.2.2
llama-index-readers-llama-parse==0.3.0
llama-parse==0.5.12
```

появилась новая ошибка:

```python
-----------------------------------------------------------------------
ValueError                            Traceback (most recent call last)
Cell In[1], line 6
      1 from llama_index.llms.fireworks import Fireworks
      3 llm = Fireworks(
      4     model="accounts/fireworks/models/firefunction-v1", api_key="My api key was here"
      5 )
----> 6 resp = Fireworks().complete("Paul Graham is ")
      7 print(resp)

File ~\AppData\Roaming\Python\Python311\site-packages\llama_index\llms\fireworks\base.py:60, in Fireworks.__init__(self, model, temperature, max_tokens, additional_kwargs, max_retries, api_base, api_key, callback_manager, default_headers, system_prompt, messages_to_prompt, completion_to_prompt, pydantic_program_mode, output_parser)
     57 callback_manager = callback_manager or CallbackManager([])
     59 api_base = get_from_param_or_env("api_base", api_base, "FIREWORKS_API_BASE")
---> 60 api_key = get_from_param_or_env("api_key", api_key, "FIREWORKS_API_KEY")
     62 super().__init__(
     63     model=model,
     64     temperature=temperature,
   (...)
     76     output_parser=output_parser,
     77 )

File ~\AppData\Roaming\Python\Python311\site-packages\llama_index\core\base\llms\generic_utils.py:311, in get_from_param_or_env(key, param, env_key, default)
    309     return default
    310 else:
--> 311     raise ValueError(
    312         f"Did not find {key}, please add an environment variable"
    313         f" `{env_key}` which contains it, or pass"
    314         f"  `{key}` as a named parameter."
    315     )

ValueError: Did not find api_key, please add an environment variable FIREWORKS_API_KEY which contains it, or pass  api_key as a named parameter.
```

оказывается сам код в документации (при чем на нескольких страницах) llama_index был с ошибкой, но этого никто не замечал.
* **PR & Fix:** https://github.com/run-llama/llama_index/pull/16794
