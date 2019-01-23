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
ms.date: 09/04/2018
ms.author: juluk
ms.openlocfilehash: 1ad85a57762ae4fba30e1699b38eaa52d45ab26e
ms.sourcegitcommit: 9b6492fdcac18aa872ed771192a420d1d9551a33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54447215"
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure Cloudshell
Azure Cloud Shell är en interaktiv, tillgängligt via webbläsaren gränssnitt för att hantera Azure-resurser.
Det ger dig flexibilitet att välja den skalupplevelse som passar bäst för ditt sätt att arbeta.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Testa från shell.azure.com genom att klicka nedan.

[![Bädda in Start](https://shell.azure.com/images/launchcloudshell.png "starta Azure Cloud Shell")](https://shell.azure.com)

Försök från Azure-portalen med hjälp av Cloud Shell-ikonen.

![Portalen Start](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funktioner

### <a name="browser-based-shell-experience"></a>Webbläsarbaserad skalupplevelse
Cloudshell kan åtkomst till en webbläsarbaserad kommandoradsmiljö som skapats med Azure-hanteringsuppgifter i åtanke.
Utnyttja molnet gränssnittet för att arbeta gå fritt från en lokal dator på ett sätt endast molnet kan ge.

### <a name="choice-of-preferred-shell-experience"></a>Val av önskad skalupplevelse
Användare kan välja mellan Bash eller PowerShell i listrutan shell.

![Bash i Cloudshell](media/overview/overview-bash-pic.png)

![PowerShell i Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserade och konfigurerade Azure arbetsstation
Cloudshell hanteras av Microsoft, så det levereras med populära kommandoradsverktyg och språk som stöds. Cloudshell är också på ett säkert sätt autentiserar automatiskt för omedelbar åtkomst till dina resurser via Azure CLI eller Azure PowerShell-cmdletar.

Visa hela [lista över verktyg som installerats i Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrerad Cloud Shell-redigeraren
Cloudshell tillhandahåller en integrerad grafiska textredigerare baserat på öppen källkod Monaco redigeraren. Helt enkelt skapa och redigera konfigurationsfiler genom att köra `code .` för sömlös distribution via Azure CLI 2.0 eller Azure PowerShell.

[Läs mer om Cloud Shell-redigeraren](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrerad med docs.microsoft.com

Du kan använda Cloud Shell direkt från dokumentation som finns på [docs.microsoft.com](https://docs.microsoft.com). Det är integrerat i [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) och [Azure CLI-dokumentationen](https://docs.microsoft.com/cli/azure) -Klicka på knappen ”prova” i ett kodfragment för att öppna uppslukande-gränssnittet upplevelse. 

### <a name="multiple-access-points"></a>Flera anslutningsplatser
Cloudshell är ett flexibelt verktyg som kan användas från:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure)
* [Dokumentation om Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-mobilappen](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [VS Code Azure-kontotillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ansluta din Microsoft Azure Files-lagring
Cloud Shell datorer är tillfälliga och kräver en ny eller befintlig Azure Files-resurs som ska monteras som `clouddrive` för bevarande av filer.

Vid första start Cloud Shell uppmanas du för att skapa en resurs dela gruppen, storage-konto och Azure Files för din räkning. Detta är ett enstaka steg och bifogas automatiskt för alla sessioner. En enda filresurs kan mappas och används av både Bash och PowerShell i Cloud Shell.

Läs mer om du vill veta hur du monterar en [nytt eller befintligt lagringskonto](persisting-shell-storage.md).

## <a name="concepts"></a>Begrepp
* Cloudshell som körs på en tillfällig värd på en per session, per användare
* Cloudshell gör timeout när 20 minuter utan interaktiva aktivitet
* Cloudshell kräver en Azure-filresurs som ska monteras
* Cloudshell använder samma Azure-filresurs för både Bash och PowerShell
* Cloudshell tilldelas en dator per användarkonto
* Cloudshell kvarstår $HOME med en avbildning för 5 GB som lagras i filresursen
* Behörigheter har angetts som en vanlig Linux-användare i Bash

Läs mer om funktionerna i [Bash i Cloud Shell](features.md) och [PowerShell i Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Prissättning
Den datorn som är värd för Cloud Shell är kostnadsfri, med ett krav för en monterad Azure Files-resurs. Vanliga storage kan tillkomma.

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md)
