---
title: "PowerShell i funktioner för Azure Cloud Shell (förhandsversion) | Microsoft Docs"
description: "Översikt över funktionerna i PowerShell Azure Cloud Shell"
services: Azure
documentationcenter: 
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: na
ms.topic: article
ms.date: 01/30/2018
ms.author: damaerte
ms.openlocfilehash: 5a2a5095f6418cf188c5d5d5f3b14049f889a272
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2018
---
# <a name="features--tools-for-powershell-in-azure-cloud-shell-preview"></a>Funktioner och verktyg för PowerShell Azure Cloud Shell (förhandsgranskning)

[!INCLUDE [features-introblock](../../includes/cloud-shell-features-introblock.md)]

> [!TIP]
> Funktioner och verktyg för [Bash](features.md) är också tillgänglig.

PowerShell i molnet Shell (förhandsgranskning) körs på `Windows Server 2016`.

## <a name="features"></a>Funktioner

### <a name="secure-automatic-authentication"></a>Säker automatisk autentisering

PowerShell i molnet Shell (förhandsversion) autentiserar på ett säkert sätt och automatiskt kontoåtkomst för Azure PowerShell.

### <a name="files-persistence-across-sessions"></a>Filer beständiga mellan sessioner

För att bevara filer mellan sessioner vägleder dig genom att koppla en resurs för Microsoft Azure-filer startas för första i molnet Shell.
När slutfört, koppla molnet Shell automatiskt din lagring (monterade som `$home\clouddrive`) för alla framtida sessioner.
Eftersom varje begäran för molnet Shell allokerar en temporär dator filer utanför din `$home\clouddrive` och datorns tillstånd är inte beständiga mellan sessioner.

[Mer information om hur du kopplar Azure-filresurser till molnet Shell.](persisting-shell-storage-powershell.md)

### <a name="azure-drive-azure"></a>Azure-enhet (Azure:)

PowerShell i molnet Shell (förhandsgranskning) startar du i Azure-enhet (`Azure:`).
Azure-enhet kan enkelt kan identifieras och navigering i Azure-resurser, till exempel beräkning, nätverk, lagring etc. liknande navigera i filsystemet.
Du kan fortsätta att använda vanliga [Azure PowerShell-cmdlets](https://docs.microsoft.com/powershell/azure) att hantera dessa resurser.
Ändringar som görs till Azure-resurser, antingen direkt i Azure-portalen eller via Azure PowerShell-cmdlets återspeglas direkt i Azure-enheten.

![](media/features-powershell/azure-drive.png)

#### <a name="contextual-awareness"></a>Kontextuella medvetenhet

- **Resursen målgrupp**: när inom ramen för en grupp resurssökvägen i Azure enheten (`Azure:`), resursgruppens namn skickas automatiskt till Azure PowerShell-cmdlets.

    ![](media/features-powershell/resource-group-autocomplete.png)

- **Get-AzureRmCommand**: denna cmdlet returnerar listan med kommandon som är tillämpliga i kontexten för platsen under Azure enhet (`Azure:`). Den visar exempelvis lagringsrelaterade kommandon när användaren är under`Azure:\<subscription name>\StorageAccounts`

    ![](media/features-powershell/get-azurermcommand.png)

### <a name="rich-powershell-script-editing"></a>Redigera Rich PowerShell-skript

När du använder VIM redigera PowerShell-filer (`.ps1,.psm1,.psd1`), automatiskt får syntaxmarkering och IntelliSense-stöd.
IntelliSense-stöd är implementerad via ett vim-plugin-program som interagerar med en lokal instans av [PowerShell Editor Services](https://github.com/PowerShell/PowerShellEditorServices).

> [!TIP]
> Använd `TAB` få slutförande (IntelliSense) på kommandonamn parameternamn och parametervärden (om tillämplig).

![](media/features-powershell/powershell-editing-vim.png)

### <a name="extensible-model"></a>Extensible modellen

Med hjälp av [PowerShellGet](https://docs.microsoft.com/powershell/module/powershellget), kan du enkelt installera (och uppdatera) anpassade moduler och skript från den [PowerShell-galleriet](https://www.powershellgallery.com).
När installationen är modulerna automatiskt beständiga mellan moln Shell-sessioner.

> [!TIP]
> Moduler som installeras av användarna sparas i den `$Home\CloudDrive\.pscloudshell\WindowsPowerShell` mapp. En symbolisk länk för den här mappen skapas i användarens dokument-mappen (`$home\Documents\WindowsPowerShell`).

![](media/features-powershell/powershellget-module.png)

### <a name="management-of-guest-vms"></a>Hantering av gäst-VM

Med två inbyggda kommandon - `Enter-AzureRmVM` och `Invoke-AzureRmVMCommand`, du kan fjärrhantera din virtuella Azure-datorer.
Dessa kommandon är byggda på PowerShell-fjärrkommunikation och kräver PowerShell anslutning till den virtuella Azure-datorer.

## <a name="tools"></a>Verktyg

|**Kategori**    |**Namn**                                 |
|----------------|-----------------------------------------|
|Azure-verktyg     |[Azure PowerShell (5.2.0)](https://docs.microsoft.com/powershell/azure/overview)<br> [Azure CLI (2.0.26)](https://docs.microsoft.com/cli/azure/overview)|
|Textredigerare    |vim<br> nano                             |
|Package Manager |PowerShellGet<br> PackageManagement<br> npm<br> pip |
|Källkontrollen  |git                                      |
|Databaser       |[SqlServer-modul](https://www.powershellgallery.com/packages/SqlServer)<br> [SQLCMD-verktyget](https://docs.microsoft.com/sql/tools/sqlcmd-utility)      |
|Testa verktyg      |Lära                                   |

## <a name="language-support"></a>Språkstöd

|**Språk**|**Version**|
|------------|-----------|
|.NET        |4.6        |
|Node.js     |6.10       |
|PowerShell  |5.1 och [6.0 (beta)](https://github.com/PowerShell/powershell/releases)       |
|Python      |2.7        |

## <a name="next-steps"></a>Nästa steg

[Snabbstart med PowerShell i molnet Shell (förhandsgranskning)](quickstart-powershell.md)

[Lär dig mer om Azure PowerShell](https://docs.microsoft.com/powershell/azure/)
