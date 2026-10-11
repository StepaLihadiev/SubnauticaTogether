# Сборка Windows

Нужен .NET SDK 10.0.302 или совместимый 10.0 feature band и официальный Inno Setup 6. Проекты имеют фиксированные версии NuGet, target `net10.0-windows` (relay-сервисы `net10.0`).

```powershell
./scripts/build.ps1
./scripts/test.ps1
./scripts/publish.ps1
./scripts/smoke-test.ps1
./installer/build-installer.ps1
```

`publish.ps1` создаёт `artifacts/release/portable/SubnauticaTogether.exe` и отдельный self-contained `bridge/SubnauticaTogether.Bridge.exe`. Не переносите один exe без остальных файлов каталога. `build-installer.ps1` собирает `artifacts/release/SubnauticaTogetherSetup.exe`; каталог программы включает runtime .NET 10. Nitrox требует отдельный .NET 9: он устанавливается launcher автоматически из официальной Microsoft release metadata, SHA-512 архива и SHA-256 распакованных файлов проверяются. Версия реально проверенного runtime — 9.0.20. При будущей смене runtime upstream адаптер нужно пересмотреть; не заменяйте framework через major roll-forward без испытаний.

В installer нет Subnautica или Nitrox. Inno регистрирует per-user uninstall, Start menu shortcut, опциональный desktop shortcut и `subnautica-together://` в HKCU. Подпись издателя отсутствует. PublishTrimmed отключён: reflection bridge и WPF не должны ломаться от trimming.

Сборку проверяли на Windows 11 x64 с установленным SDK; clean Windows 10 без SDK пока не испытан отдельной VM. Self-contained smoke выполняется с опубликованным runtime.
