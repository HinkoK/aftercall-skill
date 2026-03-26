# aftercall

`aftercall` — это skill для OpenClaw, который помогает разбирать созвоны, встречи, сессии, учебные звонки и голосовые заметки **после разговора**.

Он умеет:
- транскрибировать аудио и видео
- делать понятную выжимку разговора
- вытаскивать решения, next steps, open questions и ответственных
- собирать follow-up сообщение после звонка
- экспортировать результат в Telegram, Obsidian или task list
- опционально пытаться разделять говорящих (`Speaker 1`, `Speaker 2` и т.д.)

## Для чего подходит

Лучше всего skill подходит для таких сценариев:
- рабочие созвоны
- клиентские звонки
- after-call follow-up
- сессии с психологом или коучем
- study call / mentoring / обучение

## Режимы

Внутри skill есть 4 основных режима:
- `work-call`
- `therapy-session`
- `client-follow-up`
- `study-call`

## Speaker-aware transcription

Если на входе аудио, видео или сырой transcript без ролей, skill может попробовать отделить говорящих друг от друга.

Например так:
- `Speaker 1`
- `Speaker 2`
- `Speaker 3`

Если ты заранее знаешь, кто есть кто, можно сразу переименовать роли:
- `Speaker 1 = я`
- `Speaker 2 = клиент`
- `Speaker 2 = психолог`

Важно: speaker-aware режим работает по принципу **best effort**.
Лучше всего он работает, если:
- 1–3 участника
- нормальный звук
- мало перебиваний

Если запись шумная или люди часто говорят одновременно, точность разделения может падать.

## Что нужно для работы

Рекомендуется:
- OpenClaw
- skill `openai-whisper` для локальной транскрибации аудио и видео

Опционально:
- Obsidian, если хочешь экспорт в заметки
- Telegram workflow, если хочешь удобные короткие summary для чатов

## Установка — для человека

### Вариант 1: установить в текущий workspace агента

1. Скачай репозиторий `aftercall-skill` с GitHub.
2. Переименуй папку в `aftercall`, если после скачивания она называется `aftercall-skill`.
3. Положи её в папку:

```bash
<workspace>/skills/aftercall/
```

Пример:

```bash
~/.openclaw/workspace/skills/aftercall/
```

4. Начни новую сессию OpenClaw или перезапусти агент.
5. Проверь, что skill виден:

```bash
openclaw skills list
openclaw skills info aftercall
openclaw skills check
```

### Вариант 2: установить как shared skill для всех агентов

Положи папку `aftercall` сюда:

```bash
~/.openclaw/skills/aftercall/
```

Так skill будет доступен как shared skill, а не только внутри одного workspace.

## Полная установка со всеми фишками

Если хочешь, чтобы работало **всё**, а не только разбор готовых transcript-файлов:

1. Установи сам skill `aftercall`
2. Установи `openai-whisper`, чтобы агент мог транскрибировать аудио и видео

Пример установки whisper skill:

```bash
clawhub install openai-whisper
```

3. Проверь видимость skill через:

```bash
openclaw skills list
openclaw skills info aftercall
openclaw skills check
```

4. После этого можно использовать:
- `/aftercall`
- запросы вроде `Разбери этот созвон`
- transcript-анализ
- audio/video transcription
- follow-up generation
- Obsidian / Telegram / task-list export
- speaker-aware transcription (best effort)

## Что должен делать агент после установки

После установки агент должен работать так:

1. Если пользователь прислал аудио/видео без transcript — сначала сделать транскрибацию через `openai-whisper`, если он доступен.
2. Если transcript сырой или пользователь хочет понимать, кто говорил — предложить `speaker-aware transcription`.
3. Если нужен быстрый результат — не перегружать лишними шагами.
4. На выходе отдавать полезный after-call результат: summary, decisions, owners, next steps, open questions, follow-up или экспорт в нужный формат.

## Примеры запросов

- `Разбери этот созвон и собери итоги`
- `Вот запись звонка, сделай выжимку и action items`
- `Сделай aftercall по этой сессии с психологом`
- `Сделай follow-up по этому звонку`
- `Сделай transcript с Speaker 1 / Speaker 2`
- `Speaker 1 = я, Speaker 2 = клиент`
- `Из этого transcript сделай заметку для Obsidian`

## Что может быть в результате

В зависимости от задачи skill может вернуть:
- короткий recap
- что обсудили
- к чему пришли
- кто за что отвечает
- что делать дальше
- открытые вопросы
- готовый follow-up draft
- task list
- transcript с разделением по говорящим

## Структура skill

- `SKILL.md` — основная инструкция и метаданные skill
- `references/` — режимы, шаблоны вывода, форматы экспорта, speaker-labeling notes, evaluation cases
- `scripts/build_after_call_prompt.py` — helper script для сборки formatter-блока

## Как упаковать skill

```bash
python3 /opt/homebrew/lib/node_modules/openclaw/skills/skill-creator/scripts/package_skill.py /path/to/aftercall
```

На выходе получится файл `aftercall.skill`.

## Важные замечания

- Этот skill сделан именно для **разбора после созвона**, а не для live note-taking во время звонка.
- Для быстрых сценариев может быть достаточно обычного transcript без speaker-aware режима.
- Если запись грязная, noisy или с большим количеством перебиваний, skill должен явно показывать, что разделение по спикерам не полностью надёжно.
