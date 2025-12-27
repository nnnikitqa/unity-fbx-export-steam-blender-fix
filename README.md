# Unity не запускает Blender из Steam на macOS (fix через symlink)

Если в Unity импортируешь `.blend` (или используешь пакет FBX Export) и получаешь ошибку:

```

Blender could not be launched.
Make sure that Blender is installed correctly and in your PATH.

````

На macOS это часто происходит, когда Blender установлен через Steam и лежит в пути с пробелами:

`~/Library/Application Support/Steam/...`

Unity не может нормально найти/запустить Blender из этого места.

Данный фикс проверен на:
- Unity: 6000.1.17f1
- Package: FBX Export 5.1.5
- Blender: 5.0.1 (Steam)
- macOS: Apple Silicon (M1)

<img width="321" height="180" alt="изображение" src="https://github.com/user-attachments/assets/d6282dc1-a09b-4740-b92b-210d4d783e6d" />

---

## Решение (symlink Steam Blender в /Applications)

Идея простая: делаем ссылку `/Applications/Blender.app` на Steam Blender.
Ничего не копируется, Blender остается в Steam, просто появляется "короткий" путь без пробелов.

### 1) Найди, где Steam хранит Blender.app

Steam -> Blender -> Settings (шестеренка) -> Browse Local Files

Обычно Blender лежит тут:

`/Users/<username>/Library/Application Support/Steam/steamapps/common/Blender/Blender.app`

<img width="1160" height="958" alt="изображение" src="https://github.com/user-attachments/assets/16afc8f6-9654-4fd9-afc8-d954bcbcb069" />
<img width="518" height="178" alt="изображение" src="https://github.com/user-attachments/assets/a31317d2-3d1d-491b-befb-76b04ffa0630" />
<img width="413" height="202" alt="изображение" src="https://github.com/user-attachments/assets/7ae3ff4e-b9b4-48cc-98ed-db1cf938bfae" />
<img width="384" height="408" alt="изображение" src="https://github.com/user-attachments/assets/2052df11-579a-417a-9123-fb165cff18a3" />


### 2) Создай symlink в /Applications

Закрой Unity.

Открой Terminal и выполни:

```bash
sudo ln -s "/Users/<username>/Library/Application Support/Steam/steamapps/common/Blender/Blender.app" "/Applications/Blender.app"
````

Где `<username>` это твой логин в системе.

Пример (как у меня):

```bash
sudo ln -s "/Users/rimurutempest/Library/Application Support/Steam/steamapps/common/Blender/Blender.app" "/Applications/Blender.app"
```

<img width="737" height="230" alt="изображение" src="https://github.com/user-attachments/assets/9ea12382-d28d-4179-a21c-cd15c9af3f5c" />


Если попросит пароль, это пароль от macOS пользователя.

### 3) Перезапусти Unity и переимпортируй .blend

В Unity: выбери проблемный `.blend` файл -> ПКМ -> Reimport.

<img width="123" height="114" alt="изображение" src="https://github.com/user-attachments/assets/479e1474-33b5-42c0-b21e-14532e9f0468" />
<img width="355" height="167" alt="изображение" src="https://github.com/user-attachments/assets/f3090b8d-0ac6-480d-9350-e2f3f3a06a53" />
<img width="702" height="726" alt="изображение" src="https://github.com/user-attachments/assets/14ff7970-e910-4ec5-ab8b-03e3b88417b9" />


Готово: Unity начинает запускать Blender и обновлять модель из `.blend`.

---

## Проверка и полезные команды

Проверить, что ссылка создана:

```bash
ls -la "/Applications/Blender.app"
```

Удалить ссылку (если нужно откатить):

```bash
sudo rm "/Applications/Blender.app"
```

---

## Почему так происходит

Steam ставит Blender в папку `Application Support` (в пути есть пробелы).
Unity иногда не умеет корректно работать с таким путем и пишет, что Blender не запускается.
Symlink дает Unity простой путь без пробелов: `/Applications/Blender.app`.

---

## FAQ

### Будут ли "часики" Steam тикать?

Если запускать Blender вручную из Steam - да.
Unity при импорте запускает Blender в фоне, и это обычно не считается как "игровое время" в Steam. Что несомненно балдежно)

### Нужно ли ставить Blender отдельно в /Applications?

Нет. Этот фикс просто делает ссылку на Steam Blender. Так что можно расслабить булки)

---

Если помогло, можешь сделать issue/PR с уточнениями под другие версии Unity/Blender. Глянем что там, но заранее предупреждаю, что может не прийти уведомление(
