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
ms.date: 07/03/2018
ms.author: damaerte
ms.openlocfilehash: 5e318a0f64033aa0c4b306e547c11e1994afa229
ms.sourcegitcommit: 0b4da003fc0063c6232f795d6b67fa8101695b61
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2018
ms.locfileid: "37861899"
---
# <a name="powershell-in-azure-cloud-shell-for-windows-users"></a>PowerShell i Azure Cloud Shell för Windows-användare

I maj 2018 har [meddelade](https://azure.microsoft.com/blog/pscloudshellrefresh/) till PowerShell i Azure Cloud Shell.  PowerShell-miljö i Azure Cloud Shell är nu PowerShell Core 6 i Linux.
Det finns vissa aspekter av PowerShell i Cloud Shell som kan skilja sig från vad som förväntas i Windows PowerShell 5.1 med den här ändringen.

## <a name="case-sensitivity"></a>Skiftlägeskänslighet

I Windows är filsystemet skiftlägeskänsliga.  I Linux är filsystemet skiftlägeskänsligt.
Detta innebär att tidigare `file.txt` och `FILE.txt` ansågs vara samma fil nu de anses vara olika filer.
Rätt skiftläge måste användas när `tab` slutförs i filsystemet.  PowerShell-specifika upplevelser som `tab` cmdlet: ar, är inte skiftlägeskänsliga. 

## <a name="windows-powershell-alias-vs-linux-utilities"></a>Windows PowerShell-alias eller Linux-verktyg

Befintliga kommandon i Linux, till exempel `ls`, `sort`, och `sleep` har företräde framför deras PowerShell-alias.  Nedan visas vanliga borttagna alias samt motsvarande kommandon:  

|Ta bort Alias   |Motsvarande kommando   |
|---|---|
|`ls`     | `dir` <br> `Get-ChildItem` |
|`sort`   | `Sort-Object` |
|`sleep`  | `Start-Sleep` |

## <a name="persisting-home-vs-homeclouddrive"></a>Spara $home vs $home\clouddrive

För användare av som bestående skript och andra filer i sin enhet i molnet, beständiga katalogen $HOME nu mellan sessioner.

## <a name="powershell-profile"></a>PowerShell-profil

Som standard skapas inte en PowerShell-profil.  Skapa din profil genom att skapa en `PowerShell` katalogen under `$HOME/.config`.  I `$HOME/.config/PowerShell`, du kan skapa din profil under namnet `Microsoft.PowerShell_profile.ps1`.

## <a name="whats-new-in-powershell-core-6"></a>Nyheter i PowerShell Core 6

Mer information om nyheter i PowerShell Core 6 refererar till den [PowerShell-dokument](https://docs.microsoft.com/powershell/scripting/whats-new/what-s-new-in-powershell-core-60?view=powershell-6) och [komma igång med PowerShell Core](https://blogs.msdn.microsoft.com/powershell/2017/06/09/getting-started-with-powershell-core-on-windows-mac-and-linux/) blogginlägget
