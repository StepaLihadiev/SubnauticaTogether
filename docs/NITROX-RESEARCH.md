# Проверенные upstream контракты

Источник: https://github.com/SubnauticaNitrox/Nitrox/tree/1.8.1.0, tag commit `aec1f6e4` (release assets опубликованы 7 января 2026). Последняя проверка GitHub API: 10 октября 2026; stable release 1.8.1.0.

Windows asset: `Nitrox_1.8.1.0_win_x64.zip`, 24 347 557 байт, официальный GitHub digest SHA-256 `1b9cd0f1e9f0ed9a072d18a1a548ea3b307b5f94b25559e0a58bff3c8a986adc`. Фактически скачан и проверен. Runtime configs требуют Microsoft.NETCore.App 9.0.0; Microsoft portable runtime 9.0.20 проверен на сервере. `Directory.Build.props`/embedded metadata задают GameMinimumVersion 82304. Игра определяется как первая Subnautica, Steam AppId 264710.

Контракты проверялись по:

- `Nitrox.Launcher/Models/Utils/NitroxEntryPatch.cs`: публичные Apply(string)/Remove(string), PlatformUtils.Awake, NitroxPatcher.Main.Execute.
- `Nitrox.Model/Helper/PirateDetection.cs`: TriggerOnDirectory и штатная проверка доверия steam_api64.dll.
- `NitroxPatcher/Main.cs`: NITROX_LAUNCHER_PATH или `--nitrox` для загрузки client components.
- `Nitrox.Model/Helper/NitroxUser.cs`: `--data-path`, nitrox-data, launcher path и preferred game path.
- `Nitrox.Model/Platforms/Discovery/*`: local Steam manifests, Epic manifests, environment/config и XboxGames.
- `Nitrox.Model/Platforms/Store/Steam.cs`, `EpicGames.cs`, `MSStore.cs`: платформенный запуск. Launcher использует безопасный ArgumentList; shell-конкатенация исходного MSStore adapter не переносилась.
- `Nitrox.Model/Serialization/SubnauticaServerConfig.cs`, `NitroxConfig.cs`: server.cfg и GameMode/ServerPort/MaxConnections/AutoPortForward/LANDiscoveryEnabled.
- `Nitrox.Model/Extensions/KeyValueStoreExtensions.cs`: SavesFolderDir.
- `Nitrox.Model/Serialization/ServerList.cs`: `Name|Address|Port` в файле servers.
- `Nitrox.Model/Helper/Ipc.cs`: named pipe NitroxServer_PID, UInt32 LE length + UTF-8, player-count/save-name/stop markers.
- `Nitrox.Server.Subnautica/Program.cs`, `Server.cs`, `Models/Commands/StopCommand.cs`: readiness и безопасный stop/save.
- `Models/Communication/LiteNetLibServer.cs`: UDP/LiteNetLib 1.2.0, transport key `nitrox`.
- `LANBroadcastServer.cs`/`LANBroadcastClient.cs`: NitroxLANRequest/Response и порты 1467, 8710, 16723, 3813, 9704.
- `Models/Serialization/World/WorldPersistence.cs`, `Nitrox.Model/Serialization/SaveFileVersion.cs`: Version/WorldData/PlayerData/GlobalRootData/EntityData, Major/Minor/Build/Revision и upgrade.

`--game-path` дополнительно присутствует в release notes; реальная интеграция также всегда устанавливает environment и PreferredGamePath, поскольку именно эти механизмы однозначно видны в tag source. Release launcher instantlaunch связан с development code и не используется как подтверждённый внешний autojoin API.

Отдельное подключение игрового клиента по внешнему URL не обещается. Launcher записывает адрес в официальный server list, запускает bootstrap и оставляет подключение через меню Nitrox. UPnP не доказывает внешний reachability. Статус relay deployment отделён от локального UDP-теста.

Лицензия upstream — GPL-3.0. Неизменённый архив загружается отдельно; его LICENSE.txt сохраняется. Изменённых DLL Nitrox и игровых ресурсов в распространяемой сборке нет.

11 октября дополнительно сравнен tag 1.8.0.1. Его сборки используют NitroxModel/NitroxServer и другие имена библиотек/пространств имён. Этот релиз исключён из поддерживаемых установок: автоматический adapter и bridge работают только с 1.8.1.0. Подготовленная миграция метаданных старого мира не означает поддержку запуска старых binaries.
