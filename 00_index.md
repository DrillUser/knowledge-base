# Банк вопросов — подготовка к собесу DevOps/SRE (junior+/middle)

> Рабочий индекс. Каждый крупный блок — отдельный файл (грузим только нужное). Claude редактирует карточки, ты тренируешься отвечать вслух своими словами, Claude правит и добавляет follow-ups.

## Легенда статусов
- 🔴 не знаю / поплыву
- 🟡 учу, отвечу базово
- 🟢 готов, вытяну с уточнениями

## Приоритеты по важности (интерес интервьюера × вес в стеке × моя слабость)
| Tier | Темы |
|------|------|
| 1 — критично | Kubernetes, **Istio** ⭐, **GitOps/ArgoCD** ⭐, Linux deep |
| 2 | Данные (Postgres/Patroni/PgBouncer/ClickHouse/Liquibase), Observability/SRE (SLO+Prometheus) |
| 3 | Kafka (добить), ELK/Fluent Bit, деплой-стратегии, Bash/Python/Jinja |
| 4 — освежить | Nginx, Vault, Docker/Terraform |

⭐ — любимые темы интервьюера. Он копает каждый вопрос вглубь, силён в Linux, топит за GitOps и евангелизм.

> Tier — это **важность** темы. А **порядок изучения** — от базы к продвинутому: Linux → данные → стриминг/логи → контейнеры → Kubernetes → Istio/GitOps. Каждый слой ложится на предыдущий (контейнеры держатся на namespaces/cgroups Linux, Istio — на k8s).

## Файлы банка (по блокам)
| Блок | Файл | Карточек | Статус |
|------|------|----------|--------|
| 1. Linux (база) | `01_linux.md` | 19 (LX-01…20, без 16) | в работе |
| 2. Данные (PostgreSQL/Patroni/PgBouncer/ClickHouse/Liquibase) | `02_data.md` | теория + 16 (PG×14, CH, LB) | переделан с нуля |
| 3. Стриминг и наблюдаемость (Kafka/ELK/Fluent Bit/SLO/Prometheus) | `03_streaming_observability.md` | 9 (KF×5, OB×4) | v1 готова |
| 4. Контейнеры (Docker) | `04_containers.md` | 7 (DK-01…07) | в работе |
| 5. Kubernetes | `05_kubernetes.md` | теория + 14 (K8S-01…14) | переделан (теория+карточки) |
| 6. Istio / Service Mesh ⭐ | `06_istio.md` | 7 (IS-01…07) | v1 готова |
| 7. GitOps / ArgoCD / CI-CD / деплой-стратегии ⭐ | `07_gitops_cicd.md` | 6 (GO-01…06) | v1 готова |
| 8. Отказоустойчивость / кластеризация / HA (сквозной синтез) | `08_ha.md` | 7 (HA-01…07) | v1 готова |
| 9. Сети и прокси (Nginx / HAProxy / Envoy / Traefik ↔ Istio) | `09_network_proxy.md` | 6 (NX-01…06) | v1 готова |

> «v1 готова» = заложена первая партия ключевых карточек; глубину докручиваем поблочно (правки + мок), как делали Linux.

## План на 4–6 недель (3+ ч/день) — от базы к продвинутому
- **Неделя 1 — База ОС:** Linux deep (процессы, systemd, namespaces/cgroups, память/OOM, тюнинг ядра, диагностика) + Bash.
- **Неделя 2 — Данные:** PostgreSQL, Patroni, PgBouncer, ClickHouse, Liquibase.
- **Неделя 3 — Стриминг и наблюдаемость:** Kafka + Kafka Connect, ELK/Fluent Bit, Observability/SRE (SLI/SLO/error budget, Prometheus).
- **Неделя 4 — Контейнеры и оркестрация:** Docker (namespaces/cgroups в деле), Kubernetes core.
- **Неделя 5 — Mesh, GitOps, релизы:** Istio ⭐, GitOps/ArgoCD ⭐, деплой-стратегии (blue-green / canary / rolling / shadow).
- **Неделя 6 — Синтез и прогон:** ⭐ этап «Отказоустойчивость / кластеризация / HA» (сквозная тема — Patroni, репликация Kafka, ClickHouse, k8s, Istio: кворум, split-brain, failover, RTO/RPO) + мок-интервью + освежение Nginx/Vault/Terraform.

> Сквозной этап HA вынесен отдельно специально: отказоустойчивость — это принцип, который интервьюер проверяет «насквозь» (как не теряются данные при падении ноды? что такое split-brain и как Patroni его избегает? кворум в etcd/Kafka?). Разбираем его, когда базовые кирпичи на месте.

## Формат карточки
Вопрос → ответ за 30–60 сек (что сказать сразу) → глубокий ответ → дерево follow-ups (что спросят дальше) → «руками» (опыт/сценарий) → подводные камни → статус.

## Прогресс по Блоку 1 (Linux)
- 🟢: LX-04
- 🟡: LX-01, LX-02, LX-03, LX-05, LX-06, LX-07, LX-08, LX-09, LX-11, LX-12, LX-13, LX-14, LX-15, LX-17, LX-18
- 🔴: LX-10, LX-19, LX-20

### Слабые места по итогам мок-опроса №1 (приоритет повтора)
1. **LX-06** — своп vs кэш vs heap: **anon → swap, файловый кэш → drop (reclaim)**. На первом прогоне перевёрнуто — добить до автоматизма.
2. **LX-07** — load average = R + D (не только CPU); первый шаг диагностики — отделить CPU-bound от I/O-bound (top/vmstat: us/sy/wa, r/b).
3. **LX-05** — capabilities (контейнерный root ≠ хостовый root) + механика container escape (общий kernel).
4. **LX-13** — root по UUID, а не /dev/sdX; обходной путь — загрузка предыдущего ядра из GRUB.
5. **LX-06** — swappiness для БД объяснять через **латентность/major page fault**, не консистентность.

### Прогресс по остальным блокам
Все карточки Блоков 2–8 пока 🔴 (свежие, не прогнанные), кроме помеченных как 🟡 по реальному опыту:
- 🟡 KF-01…05 (Kafka — недавняя проработка), OB-04 (ELK/Fluent Bit — руки), GO-05 (Ansible).
- Блок 4 (Docker): 🟢 DK-01, DK-02, DK-05; 🟡 DK-03, DK-04, DK-06; 🔴 DK-07.
Проставляем настоящие статусы по итогам мок-опросов.
- **HIGH:** ✅ добавлены — OverlayFS (перенесён в Docker → DK-06), SSH (LX-17), DNS (LX-18), TCP-состояния (LX-19).
- **MEDIUM:** eBPF / perf / bpftrace (advanced-трейсинг, любит интервьюер); /proc и /sys как интерфейсы ядра; типы ФС (ext4/xfs, журналирование).
- **LOW:** users/sudo/PAM; nice/renice, taskset (CPU affinity); NTP/время; rsyslog/logrotate.
- **Отдельным блоком (не Linux):** Bash + текстовая обработка (awk/sed/grep) — пойдёт в свой файл по плану (Программирование/автоматизация).
