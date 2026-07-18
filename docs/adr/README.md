# Architecture Decision Records (ADR)

Решения эксперимента о формулировках инструкций, процессе и артефактах. Принцип ведения — в [README.md](../../README.md#adr-как-фиксируем-изменения).

| ADR | Статус | Тема |
| --- | --- | --- |
| [001-analyst-instructions.md](001-analyst-instructions.md) | Заменён ADR-002 | Инструкция Аналитика (`analyst/INSTRUCTIONS.md`) — история |
| [002-roles-and-account-manager.md](002-roles-and-account-manager.md) | Принято | Роли, каталоги, этапы 2–3, инструкция Аккаунт-менеджера |
| [003-stage1-and-business-analyst.md](003-stage1-and-business-analyst.md) | Принято | Принятие этапа 1, инструкция BA, запросы `account-manager-request-NNN.md` |
| [004-am-contradiction-reconciliation.md](004-am-contradiction-reconciliation.md) | Принято | Сверка противоречий AM; инструкция AM обновлена |
| [005-ba-requirements-vs-decisions.md](005-ba-requirements-vs-decisions.md) | Принято | Граница REQ, эталоны, решения BA; инструкция BA обновлена |
| [006-product-manager-instructions.md](006-product-manager-instructions.md) | Принято (частично замещён ADR-009 в части выбора стека) | Инструкция PM: стек, волны задач, `REQ-… #<номер>` |
| [007-pm-task-trace-roles.md](007-pm-task-trace-roles.md) | Принято (форма ссылки в теле Issue уточнена ADR-013) | PM: роли ссылок «выполнить»/«не нарушить»; без «вайб-кодер» и v1 |
| [008-pm-verification-reaction.md](008-pm-verification-reaction.md) | Принято (превенция первого черновика заменена ADR-017) | PM: реакция на верификацию Issue; правка только после явного указания |
| [009-senior-developer-and-pm-stack.md](009-senior-developer-and-pm-stack.md) | Принято | Ведущий разработчик; PM не выбирает стек; задача Ведущему → `tech-stack.md` |
| [010-ba-verification-reaction.md](010-ba-verification-reaction.md) | Принято (превенция первого черновика для BA — ADR-011 / ADR-019) | BA: реакция на верификацию Issue; правка только после явного указания |
| [011-ba-proactive-quality-after-verification-run.md](011-ba-proactive-quality-after-verification-run.md) | Принято (внедрено ADR-019) | Прогон 17/29; смена линии BA → превентивное качество формулировок |
| [012-ba-appendix-b-storage-refs.md](012-ba-appendix-b-storage-refs.md) | Принято | BA: title Issue `REQ-NNN …`; в теле после переноса отсылки → `#N` |
| [013-pm-storage-refs.md](013-pm-storage-refs.md) | Принято | PM: title Issue `TASK-NNN …`; в теле после переноса только `#N` |
| [014-lead-dev-module-recommendations.md](014-lead-dev-module-recommendations.md) | Принято (процесс ИИ / формат `tech-stack.md` заменены ADR-015) | Ведущий: рекомендации модулей; жёсткий низкоуровневый стек; инструкция senior-developer |
| [015-lead-dev-tech-stack-handoff.md](015-lead-dev-tech-stack-handoff.md) | Принято | ИИ Ведущего: стек из репо Разработчика; краткий `tech-stack.md`; без «убрали»; рекомендации по вопросу |
| [016-pm-wave-granularity-and-task-links.md](016-pm-wave-granularity-and-task-links.md) | Принято | PM: дробить волну по умолчанию; в тексте задачи ссылки на задачи только «дополняет» |
| [017-pm-proactive-task-quality.md](017-pm-proactive-task-quality.md) | Принято | PM: превентивная проверка качества задач (4 критерия); реактивный шаг сохранён |
| [018-pm-project-docs-boundary-contracts.md](018-pm-project-docs-boundary-contracts.md) | Принято | PM: `docs/` + индекс; контракты границ; гейт до задач Разработчику; адресат — Ведущий |
| [019-ba-proactive-requirement-quality.md](019-ba-proactive-requirement-quality.md) | Принято | BA: превентивная проверка качества REQ (4 свойства) на шаге 3; те же свойства на шаге 4 |
| [020-full-issue-verification-pass.md](020-full-issue-verification-pass.md) | Принято | Полный положительный проход верификации Issue (REQ + задачи); F-010; MR-промпт отложен |
