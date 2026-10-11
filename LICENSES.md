# Лицензии и происхождение

Subnautica Together распространяется по GPL-3.0-only, полный текст в `LICENSE`. Автор: Subnautica Together contributors, 2026. В исходниках нет игровых файлов Subnautica, защищённой графики или изменённых DLL Nitrox. Ocean UI создан геометрическими WPF-элементами.

Nitrox — GPL-3.0, Copyright Nitrox contributors: https://github.com/SubnauticaNitrox/Nitrox. Приложение загружает неизменённый официальный релиз отдельно после установки. Его `LICENSE.txt` остаётся в каталоге Nitrox. Bootstrap bridge вызывает публичные `NitroxEntryPatch.Apply/Remove` из официальной DLL; имена, конфигурация и IPC проверены по tag 1.8.1.0 (`aec1f6e4`). Код адаптера, протокол взаимодействия и исходники bridge поставляются в этом репозитории. Изменённых GPL-компонентов upstream нет. При дальнейшем распространении собственного бинарного лаунчера предоставляйте соответствующие полные исходники, скрипты сборки и этот текст GPL.

Microsoft .NET/WPF и Microsoft.Extensions.*: MIT; self-contained runtime содержит уведомления Microsoft `ThirdPartyNotices.txt` и `LICENSE.txt` там, где они включены SDK. Исходники: https://github.com/dotnet/runtime, https://github.com/dotnet/wpf, https://github.com/dotnet/extensions.

LiteNetLib 1.2.0: MIT, Copyright Ruslan Pyrch (RevenantX). https://github.com/RevenantX/LiteNetLib. Полный текст: `licenses/LiteNetLib-MIT.txt`.

xUnit, xUnit VS runner и Microsoft.NET.Test.Sdk используются только в тестах (Apache-2.0 / MIT соответственно); они не включаются в установщик. Tailscale не перераспространяется, адаптер вызывает уже установленный официальный CLI и требует собственный аккаунт пользователя.

Установщик создан Inno Setup; инструменты компиляции не входят в приложение. Цифровая подпись приложения и установщика не настроена. Контрольные суммы сборок не следует путать с цифровой подписью издателя.
