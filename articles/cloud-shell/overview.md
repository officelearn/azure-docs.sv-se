---
title: Översikt över Azure Cloud Shell | Microsoft Docs
description: Översikt över Azure Cloudshell.
services: ''
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: juluk
ms.openlocfilehash: 9588bebdc827760f0e0d3e2aadccbff5f24723f1
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2018
ms.locfileid: "39258933"
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure Cloudshell
Azure Cloud Shell är en interaktiv, tillgängligt via webbläsaren gränssnitt för att hantera Azure-resurser.
Det ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Testa från shell.azure.com genom att klicka nedan.

[![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloudshell")](https://shell.azure.com)

Försök från Azure-portalen med hjälp av Cloud Shell-ikonen.

![Portalen Start](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funktioner
### <a name="browser-based-shell-experience"></a>Webbläsarbaserad skalupplevelse
Cloudshell kan åtkomst till en webbläsarbaserad kommandoradsmiljö som skapats med Azure-hanteringsuppgifter i åtanke.
Utnyttja molnet gränssnittet för att arbeta gå fritt från en lokal dator på ett sätt endast molnet kan ge.

### <a name="choice-of-preferred-shell-experience"></a>Val av önskad skalupplevelse
Linux-användare kan använda Bash i Cloud Shell, även om Windows-användare kan använda PowerShell i Cloud Shell (förhandsversion) i listrutan shell.

![Bash i Cloudshell](media/overview/overview-bash-pic.png)

![PowerShell i Cloudshell (förhandsversion)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserade och konfigurerade Azure arbetsstation
Cloudshell hanteras av Microsoft, så det levereras med populära kommandoradsverktyg och språk som stöds. Cloudshell är också på ett säkert sätt autentiserar automatiskt för omedelbar åtkomst till dina resurser via Azure CLI 2.0 eller Azure PowerShell-cmdletar.

Visa hela [lista över verktyg som installerats i Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrerad Cloud Shell-redigeraren
Cloudshell tillhandahåller en integrerad grafiska textredigerare baserat på öppen källkod Monaco redigeraren. Helt enkelt skapa och redigera konfigurationsfiler genom att köra `code .` för sömlös distribution via Azure CLI 2.0 eller Azure PowerShell.

[Läs mer om Cloud Shell-redigeraren](using-cloud-shell-editor.md).

### <a name="multiple-access-points"></a>Flera anslutningsplatser
Cloudshell är ett flexibelt verktyg som kan användas från:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [”Prova”-dokumentation för Azure CLI 2.0](https://docs.microsoft.com/cli/azure?view=azure-cli-latest)
* [Azure-mobilappen](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS Code Azure-kontotillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ansluta din Microsoft Azure Files-lagring
Cloud Shell datorer är tillfälliga och kräver en Azure Files-resurs som ska monteras som `clouddrive` för bevarande av filer.

Vid första start Cloud Shell uppmanas du för att skapa en resurs dela gruppen, storage-konto och Azure Files för din räkning. Detta är ett enstaka steg och bifogas automatiskt för alla sessioner. En enda filresurs kan mappas och används av både Bash och PowerShell i Cloud Shell (förhandsversion).

#### <a name="create-new-storage"></a>Skapa ny lagring
![](media/overview/basic-storage.png)

Ett konto för lokalt redundant lagring (LRS) och Azure Files-resurs kan skapas för din räkning. Om du väljer att använda båda används Azure Files-resurs för både Bash och PowerShell-miljöer. Vanliga storage kan tillkomma.

Tre resurser kommer att skapas åt dig:
1. Resursgrupp med namnet: `cloud-shell-storage-<region>`
2. Storage-konto med namnet: `cs<uniqueGuid>`
3. Filresurs med namnet: `cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash i Cloud Shell skapar även en standardbild 5 GB-disk för att bevara `$Home`. Alla filer i katalogen $Home som SSH-nycklar finns kvar i din diskavbildning för användare som lagras i monterade Azure-filresursen. Gäller bästa praxis när du sparar filer i din $Home katalog och monterade Azure-filresurs.

#### <a name="use-existing-resources"></a>Använda befintliga resurser
![](media/overview/advanced-storage.png)

Ett avancerat alternativ har angetts för att koppla befintliga resurser till Cloud Shell.
Klicka på ”Visa avancerade inställningar” i Kommandotolken storage installationen att visa ytterligare alternativ.

> [!Note]
> Listrutor filtreras för förtilldelad Cloud Shell-region och LRS/GRS/ZRS-lagringskonton.

[Läs mer om Cloud Shell-lagring, uppdatering av Azure-filresurser och ladda upp/ned filer.](persisting-shell-storage.md)

## <a name="concepts"></a>Begrepp
* Cloudshell som körs på en tillfällig värd på en per session, per användare
* Cloudshell gör timeout när 20 minuter utan interaktiva aktivitet
* Cloudshell kräver en Azure-filresurs som ska monteras
* Cloudshell använder samma Azure-filresurs för både Bash och PowerShell
* Cloudshell tilldelas en dator per användarkonto
* Cloudshell kvarstår $Home med en avbildning för 5 GB som lagras i filresursen
* Behörigheter har angetts som en vanlig Linux-användare i Bash

Läs mer om funktionerna i [Bash i Cloud Shell](features.md) och [PowerShell i Cloud Shell (förhandsversion)](features-powershell.md).

## <a name="pricing"></a>Prissättning
Den datorn som är värd för Cloud Shell är kostnadsfri, med ett krav för en monterad Azure Files-resurs. Vanliga storage kan tillkomma.

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Cloud Shell (förhandsversion) Snabbstart](quickstart-powershell.md)