---
title: Azure Cloud Shell för Windows-användare | Microsoft Docs
description: Guide för användare som inte är bekant med Linux-system
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
ms.openlocfilehash: aad474195060c01a3f9d85e6f9037b568b0c16ad
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630394"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell i Azure Cloud Shell för Windows-användare

I maj 2018 har [meddelade](https://azure.microsoft.com/blog/pscloudshellrefresh/) till PowerShell i Azure Cloud Shell.
PowerShell-miljö i Azure Cloud Shell nu körs [PowerShell Core 6](https://github.com/powershell/powershell) i en Linux-miljö.
Med den här ändringen, det kan finnas vissa skillnader i PowerShell-miljö i Cloud Shell jämfört med vad som förväntas i ett Windows PowerShell-upplevelse.

## <a name="file-system-case-sensitivity"></a>Filen system skiftlägeskänslighet

Filsystemet är skiftlägeskänslig i Windows, medan på Linux, filsystemet är skiftlägeskänsligt.
Tidigare `file.txt` och `FILE.txt` ansågs vara samma fil, men nu de anses vara olika filer.
Rätt skiftläge måste användas när `tab-completing` i filsystemet.
PowerShell-specifika upplevelser som `tab-completing` cmdlet-namnen, parametrar och värden, är inte skiftlägeskänsliga.

## <a name="windows-powershell-aliases-vs-linux-utilities"></a>Windows PowerShell-alias eller Linux-verktyg

Vissa befintliga PowerShell-alias har samma namn som inbyggda Linux-kommandon, till exempel `cat`,`ls`, `sort`, `sleep`osv. Alias som står i konflikt med inbyggda Linux-kommandon i PowerShell Core 6, har tagits bort.
Nedan visas vanliga alias som har tagits bort, samt deras motsvarande kommandon:  

|Ta bort Alias   |Motsvarande kommando   |
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

## <a name="persisting-home"></a>Bevara $HOME

Tidigare användare kan bara spara skript och andra filer i sin enhet i molnet.
Nu kan beständiga $HOME användarkatalog nu även mellan sessioner.

## <a name="powershell-profile"></a>PowerShell-profil

Som standard skapas inte en användarprofil PowerShell.
Skapa din profil genom att skapa en `PowerShell` katalogen under `$HOME/.config`.

```azurepowershell-interactive
mkdir (Split-Path $profile.CurrentUserAllHosts)
```

Under `$HOME/.config/PowerShell`, du kan skapa din profilfiler – `profile.ps1` och/eller `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Nyheter i PowerShell Core 6

Mer information om nyheter i PowerShell Core 6 refererar till den [PowerShell-dokument](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) och [komma igång med PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogginlägg.
