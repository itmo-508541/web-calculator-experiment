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
| [008-pm-verification-reaction.md](008-pm-verification-reaction.md) | Принято | PM: реакция на верификацию Issue; правка только после явного указания |
| [009-senior-developer-and-pm-stack.md](009-senior-developer-and-pm-stack.md) | Принято | Ведущий разработчик; PM не выбирает стек; задача Ведущему → `tech-stack.md` |
| [010-ba-verification-reaction.md](010-ba-verification-reaction.md) | Принято (линия «без превенции» для BA уточнена ADR-011) | BA: реакция на верификацию Issue; правка только после явного указания |
| [011-ba-proactive-quality-after-verification-run.md](011-ba-proactive-quality-after-verification-run.md) | Принято | Прогон 17/29; смена линии BA → превентивное качество формулировок |
| [012-ba-appendix-b-storage-refs.md](012-ba-appendix-b-storage-refs.md) | Принято | BA: title Issue `REQ-NNN …`; в теле после переноса отсылки → `#N` |
| [013-pm-storage-refs.md](013-pm-storage-refs.md) | Принято | PM: title Issue `TASK-NNN …`; в теле после переноса только `#N` |
| [014-lead-dev-module-recommendations.md](014-lead-dev-module-recommendations.md) | Принято (процесс ИИ / формат `tech-stack.md` заменены ADR-015) | Ведущий: рекомендации модулей; жёсткий низкоуровневый стек; инструкция senior-developer |
| [015-lead-dev-tech-stack-handoff.md](015-lead-dev-tech-stack-handoff.md) | Принято | ИИ Ведущего: стек из репо Разработчика; краткий `tech-stack.md`; без «убрали»; рекомендации по вопросу |
