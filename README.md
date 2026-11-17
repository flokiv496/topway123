# CarPlay Dark — тема для Topway TS105 (Android 14)

Тема сделана в том же формате, что ваша T733: это APK **без кода** (ресурсы + JSON-конфиги),
который читает лаунчер по ключу `launcher.variety.theme.plugin`.

## Что внутри
| Что | Где |
|---|---|
| Манифест (ключ темы `sfp_cp105_1`) | `app/src/main/AndroidManifest.xml` |
| Раскладка экрана (левая панель + 3 страницы) | `app/src/main/res/layout/` |
| Виджет музыки (большой) | `assets/layout-1280x652/media/media_config.json`, `media_view_config.json` |
| Мини-плеер в левой панели | `media_config_2.json`, `media_view_config_2.json` |
| Порядок страниц (свайп) | `assets/layout-1280x652/theme_config.json` |
| Кнопки, прогресс-бар, фоны | `res/drawable/`, `res/mipmap-mdpi/` |

Страницы: **1** — сетка приложений 4×2, **2** — большой Bluetooth-плеер, **3** — окно приложения (навигация).
Левая панель всегда на месте: часы, мини-плеер, док из 3 приложений.

## Сборка APK
Нужны Android Studio (Koala или новее, JDK 17) и интернет для скачивания Gradle-зависимостей.
1. `File → Open` → папка `CarPlayTheme_TS105`, дождаться Gradle Sync.
2. `Build → Build Bundle(s) / APK(s) → Build APK(s)`.
3. Готово: `app/build/outputs/apk/debug/app-debug.apk` (подписан debug-ключом).

Из консоли (нужен установленный Gradle 8.7+):
`gradle assembleDebug`

Релизная подпись (по желанию):
```
keytool -genkey -v -keystore my.jks -alias theme -keyalg RSA -keysize 2048 -validity 10000
apksigner sign --ks my.jks --out CarPlay_signed.apk app/build/outputs/apk/release/app-release-unsigned.apk
```

## Установка на магнитолу
- флешка: скопировать APK и поставить через файловый менеджер, или
- `adb connect <IP магнитолы>` → `adb install -r app-debug.apk`.
Иконки у темы нет — она появляется в настройках лаунчера, в разделе выбора тем.

## Если что-то не так
- **Разрешение экрана.** Папка `layout-1280x652` рассчитана на экран как у T733. Проверьте: `adb shell wm size`.
  Если другое (например 1280x720) — переименуйте папку в `layout-<ШxВ>` и пересчитайте числа
  (в JSON и в `res/layout/*.xml`, там везде `px`). Копия настроек в корне `assets/` — запасной вариант.
- **Свайп в другую сторону.** Порядок страниц — массив `config` в `theme_config.json`.
  Поменяйте местами записи `theme_config_1` и `theme_config_2`, и виджет музыки окажется с другой стороны.
- **Нет исполнителя.** В известных мне настройках лаунчера есть только `tv_media_name` (название трека),
  отдельного поля исполнителя я в вашей теме не нашёл, поэтому его в конфиге нет.
- **Размеры шрифтов и отступы** подбираются на устройстве: правьте `text_size`, `width`, `gravity` в `media_view_config*.json`.
- Свой шрифт: положите файл в `assets/fonts/` и добавьте в нужный элемент `"typeface": "fonts/Имя.ttf"`.

## Сборка через GitHub
1. Создайте репозиторий на github.com и загрузите в него содержимое папки проекта (включая скрытую папку `.github`).
2. Вкладка **Actions** → workflow **Build APK** → **Run workflow** (или он запустится сам после push).
3. По завершении откройте запуск и скачайте артефакт **CarPlay-theme-apk** — внутри `app-debug.apk`.
