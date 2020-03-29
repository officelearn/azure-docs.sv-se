---
title: Azure Cloud Shell för Windows-användare | Microsoft-dokument
description: Guide för användare som inte är bekanta med Linux-system
services: azure
documentationcenter: ''
author: maertendMSFT
manager: hemantm
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/03/2018
ms.author: damaerte
ms.openlocfilehash: 4fc4f6523eb19294cabdf6b5b910dd346a877502
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67204147"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell i Azure Cloud Shell för Windows-användare

I maj 2018 [tillkännagavs](https://azure.microsoft.com/blog/pscloudshellrefresh/) ändringar i PowerShell i Azure Cloud Shell.
PowerShell-upplevelsen i Azure Cloud Shell kör nu [PowerShell Core 6](https://github.com/powershell/powershell) i en Linux-miljö.
Med den här ändringen kan det finnas vissa skillnader i PowerShell-upplevelsen i Cloud Shell jämfört med vad som förväntas i en Windows PowerShell-upplevelse.

## <a name="file-system-case-sensitivity"></a>Känslighet för filsystemärende

Filsystemet är skiftlägesokänsligt i Windows, medan filsystemet på Linux är skiftlägeskänsligt.
Tidigare `file.txt` `FILE.txt` och ansågs vara samma fil, men nu anses de vara olika filer.
Korrekt hölje måste `tab-completing` användas i filsystemet.
PowerShell-specifika upplevelser, `tab-completing` till exempel cmdletnamn, parametrar och värden, är inte skiftlägeskänsliga.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell alias vs Linux verktyg

Vissa befintliga PowerShell-alias har samma namn som inbyggda `cat`Linux-kommandon, till exempel ,`ls`, `sort`, `sleep`, etc. I PowerShell Core 6 har alias som kolliderar med inbyggda Linux-kommandon tagits bort.
Nedan finns de vanliga alias som har tagits bort samt deras motsvarande kommandon:  

|Borttaget alias   |Motsvarande kommando   |
|---|---|
|`cat`    | `Get-Content` |
|`curl`   | `Invoke-WebRequest` |
|`diff`   | `Compare-Object` |
|`ls`     | `dir` <br> `Get-ChildItem` |
|`mv`     | `Move-Item`   |
|`rm`     | `Remove-Item` |
|`sleep`  | `Start-Sleep` |
|`sort`   | `Sort-Object` |
|`wget`   | `Invoke-WebRequest` |

## <a name="persisting-home"></a>Ihållande $HOME

Tidigare användare kunde bara spara skript och andra filer i sin Cloud Drive.
Nu är användarens $HOME katalog envisas också över sessioner.

## <a name="powershell-profile"></a>PowerShell-profil

Som standard skapas inte en användares PowerShell-profil.
Skapa en `PowerShell` katalog under `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Under `$HOME/.config/PowerShell`kan du skapa dina `profile.ps1` profilfiler `Microsoft.PowerShell_profile.ps1`- och/eller .

## <a name="whats-new-in-powershell-core-6"></a>Nyheter i PowerShell Core 6

Mer information om vad som är nytt i PowerShell Core 6 finns i [PowerShell-dokumenten](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) och blogginlägget [Komma igång med PowerShell Core.](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/)
