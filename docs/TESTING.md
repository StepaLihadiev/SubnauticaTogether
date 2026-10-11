# Проверки и воспроизведение

```powershell
dotnet test SubnauticaTogether.sln -c Release --logger trx --results-directory artifacts/test-results
./scripts/smoke-test.ps1
dotnet run --project tools/SubnauticaTogether.Verify -c Release -- --output artifacts/official-verification --game-path 'E:\SteamLibrary\steamapps\common\Subnautica' --launch-game
```

Путь игры замените на свой. Verify хранит отдельные данные под своим output, загружает/проверяет официальный релиз и runtime, обнаруживает реальную игру, запускает настоящий сервер, проверяет готовность+UDP, запускает Steam-клиент с bootstrap, читает журналы и безопасно останавливает процессы. До patch создаёт копию Assembly-CSharp.dll; после закрытия игры восстанавливает исходный файл. При оставшейся игре резервный DLL сохранён, автоматического перезаписывания занятого файла нет. `--archive` позволяет повторно использовать официальный локальный ZIP с полной size/SHA-256 проверкой. Это явно отражается в отчёте.

Unit tests: manifest identity/escaping/повторяющиеся Steam-ключи, версия и моды, недействительные пути, settings/corruption, invite TTL/schema/version, доверенные URL, zip traversal/device names/bombs, diagnostic rules/redaction.

Integration tests: настоящий HTTP socket server с обрывом и Range resume (test routing handler направляет доверенный URL на локальный test server), cancellation и checksum rejection, GitHub 403/429/500, staged installation, повреждённый update, сбой после directory replacement, recovery journal, backup/restore/tampering, running-world guard, unsafe import, RSA signature, отдельный IPC/LiteNetLib процесс и graceful stop, занятый UDP-порт, недоступный сервер, UDP relay round-trip. Fixture server не считается Nitrox или проверкой синхронизации игры.

UI smoke запускает настоящий WPF executable в отдельном процессе, проверяет первый запуск, все шесть страниц, ошибку папки, prerequisites установки, выбор fixture-папки, создание server.cfg реальным сервисом, сохранение settings и отсутствие WPF binding errors. Шесть страниц рендерятся в PNG. Проверка выбора папки касается привязки и обработки результата; native OpenFolderDialog не нажимается автоматически. SmokeDiscovery изолирует настоящую игру и не вызывает установку в неё. В isolated run registry autostart не меняется.

Фактические результаты и ограничения: `STATUS.md`. Сценарии двух игроков, real router UPnP, CGNAT, интернет и Tailscale не подтверждаются одним локальным handshake. В реальном игровом журнале встретились сообщения Steamworks/Steam DRM initialization и недоступного Discord hook; основное патчирование Nitrox завершилось. Влияние платформенных сообщений на дальнейшую сессию требует интерактивной проверки, они не считаются исправленными.
