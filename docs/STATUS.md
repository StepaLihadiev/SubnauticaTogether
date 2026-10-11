# Итоговый отчёт — 11 октября 2026

Windows-проект Subnautica Together 1.0.0 собран и проверен. Основной локальный сценарий подтверждён с настоящей Steam Subnautica build 83031 и неизменённым Nitrox 1.8.1.0. Подключение двух игровых клиентов не проверялось и не заявляется готовым испытанием.

## Реализовано

- WPF/MVVM интерфейс на русском: установка, одиночный запуск, создание сервера, подключение, миры, настройки, диагностика, уведомления и подтверждения.
- Поиск Steam библиотек/appmanifest, Epic JSON manifests, доступных XboxGames/Content каталогов; ручной выбор. Проверка PE/PlatformUtils, build и конфликтующих модов.
- Официальные stable GitHub releases, ETag/cache/rate-limit, HTTPS download с resume/retry/cancellation, size/SHA-256 и безопасной ZIP extraction.
- Staging, journal, повторная проверка после rename и rollback. Незнакомые версии/layout отклоняются.
- Автоматическая установка портативного .NET 9 с официальной Microsoft metadata, SHA-512 архива и SHA-256 каждого файла. Проверено 9.0.20.
- Отдельный bridge к официальным публичным bootstrap API и проверке доверия игровых DLL Nitrox; штатный Steam запуск с `--nitrox` и ArgumentList.
- Реальный Nitrox Server, IPC log/player-count, readiness по журналу + UDP handshake, save/stop, world config, отдельные данные и история процесса.
- GUID-миры, переименование, backup, restore в новый мир, импорт полного JSON-мира совпадающей версии, удаление в trash с подтверждением.
- Update/repair Nitrox с backup, сохранением прежней версии и запретом downgrade. Автоматический adapter поддерживает только 1.8.1.0. Guarded-переход метаданных старого мира подготовлен и проверен fixture-тестом; запуск binaries 1.8.0.1 и реальная миграция такого сохранения не поддержаны проведённым испытанием.
- Direct/LAN discovery, stateless ST1/URL приглашения с TTL/build/version, установленный Tailscale CLI adapter, opt-in штатного UPnP Nitrox.
- Repair, расширяемые JSON diagnostic rules, анализ локальных журналов и redaction экспорта. Логи никуда не отправляются.
- Клиент подписанного RSA-PSS launcher update manifest. Endpoint и publisher key не настроены.
- Отдельные relay/rendezvous исходники и инструкции. Relay передаёт UDP к фиксированному upstream, имеет allowlist/лимиты; публичной инфраструктуры нет.

## Артефакты

- `artifacts/release/SubnauticaTogetherSetup.exe` — настоящий Inno Setup installer, per-user, unsigned.
- `artifacts/release/portable/SubnauticaTogether.exe` — Windows x64 self-contained launcher; требуется весь portable-каталог.
- `artifacts/release/portable/bridge/SubnauticaTogether.Bridge.exe` — self-contained bootstrap bridge.
- `artifacts/release/SubnauticaTogetherSource.zip` — соответствующие полные исходники, скрипты, тесты, документация и лицензии.
- `artifacts/release/portable-sha256.json`, `artifacts/release/release-sha256.json` — контрольные суммы, не цифровая подпись.

Оригинальные игровые DLL использовались только в локальной проверке и её backup под `artifacts/official-verification`; они исключены из установщика и source archive. Исходная Assembly-CSharp.dll после проверок восстановлена побайтно; игру и тестовый сервер закрыли штатно.

## Выполненные проверки

```powershell
dotnet build SubnauticaTogether.sln -c Release
dotnet test SubnauticaTogether.sln -c Release --logger trx --results-directory artifacts/test-results/final-release-v2
./scripts/publish.ps1
./scripts/smoke-test.ps1
./installer/build-installer.ps1
./scripts/verify-installer.ps1
dotnet run --project tools/SubnauticaTogether.Verify -c Release -- --output artifacts/official-download
dotnet run --project tools/SubnauticaTogether.Verify -c Release -- --output artifacts/official-verification --archive .research/Nitrox_1.8.1.0_win_x64.zip --game-path 'E:\SteamLibrary\steamapps\common\Subnautica' --launch-game
```

Сборка: 0 ошибок, 0 предупреждений. Финальные xUnit результаты: **52 unit + 26 integration + 1 UI suite = 79 passed, 0 failed, 0 skipped**. UI suite содержит 13 отдельных smoke-проверок; они дополнительно повторены на опубликованном exe и установленной копии. Проверены uninstall, protocol registration/removal и сохранение user-data sentinel. Отчёты: `artifacts/test-results/final-release-v2/*.trx`, `artifacts/installer-verification/installer-verification.json`, `artifacts/published-smoke-*/ui-smoke.json`. Финальная визуальная проверка выявила ошибку TwoWay-привязки read-only progress; её исправили явным OneWay, smoke дополнительно проверяет Dispatcher exceptions.

Реальная интеграция: обнаружена игра на E:, официальный API/архив проверены, 173 файла Nitrox прошли hashes. `official-download` скачал 24 347 557 байт непосредственно через DownloadService. В `official-verification` создан настоящий мир, сервер сообщил о готовности, localhost handshake с Nitrox прошёл, Steam запустил Subnautica с официальным bootstrap. `nitrox-data/logs/game-20261010.log` подтвердил `Completed patching` и `Behaviours applied`. Сервер сохранил мир и остановился; создана резервная копия. Self-contained bridge отдельно проверен на Apply/Remove с официальной проверкой доверия DLL, исходный файл восстановлен и hashes совпали.

В финальных проверках ошибок нет. Найденные ранее duplicate Steam manifest keys, временная блокировка runtime rename и влияние настоящей игры на UI-fixture smoke исправлены и повторно проверены. Реальный Player.log содержал Steamworks/Steam DRM initialization сообщения, Nitrox game log — ошибку необязательного Discord hook. Эти сообщения не исправлялись; их влияние на полноценную игровую сессию остаётся предметом интерактивной проверки.

## Сетевые возможности

| Возможность | Реализация | Что подтверждено |
|---|---|---|
| LAN | Прямой Nitrox UDP + официальный LAN discovery | Реальный localhost handshake; отдельные два LAN-компьютера не проверены |
| Интернет напрямую | IP/hostname:UDP port | Валидация и transport probe; внешний второй игрок не проверен |
| UPnP | Explicit opt-in, mapping выполняет Nitrox | Конфигурация и adapter; реальный router mapping не проверен |
| Приглашение | Полный адрес внутри ST1 или deep link, TTL 24 часа | Parsing, expiry, build/version guard, copy и installer protocol; не identity/auth service |
| Прохождение NAT | Установленный официальный Tailscale | CLI adapter; аккаунт и маршрут между двумя hosts не проверены; универсального hole punching нет |
| Relay | Отдельный fixed-upstream UDP proxy | Реальный UDP round-trip в автоматическом тесте; VPS/game session не проверены |
| Rendezvous | Отдельный HTTPS API с TTL/token/лимитами | Компиляция; deployment/интеграция клиента не выполнены |

## Оставшиеся ограничения

Не проверены: синхронизация и аутентификация двух игровых клиентов, полный multiplayer gameplay, реальные LAN/internet/Tailscale/UPnP маршруты, CGNAT со второго хоста, Epic/Microsoft Store запуск, standalone Windows 10 VM без SDK, production relay нагрузка/безопасность, reconnect IPC после аварии UI. Native folder-picker smoke проверяет обработку результата через fixture seam, а не автоматические нажатия Windows-диалога. Английский перевод и полная ресурсная локализация не поставлены. Launcher update endpoint/key и цифровая подпись отсутствуют. Nitrox остаётся alpha-модом с upstream ограничениями синхронизации.

## Как пользоваться

Запустите Setup, откройте Subnautica Together, подтвердите найденную папку и нажмите «Установить мультиплеер». Затем «Создать игру» или «Подключиться». После запуска игры откройте «Мультиплеер» → «Together — друг», введите имя и подключитесь. Для друга создайте приглашение с адресом, доступным из его сети. Для backup/restore сначала остановите сервер.
