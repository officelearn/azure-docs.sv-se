---
title: Översikt över Azure Cloud Shell | Microsoft Docs
description: Översikt över Azure Cloud Shell.
services: ''
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/03/2019
ms.author: damaerte
ms.openlocfilehash: 2edb50910614b47aca5a4d8f7f02c2e3bc69b643
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75449009"
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure Cloud Shell
Azure Cloud Shell är ett interaktivt, autentiserat, webb läsar tillgängligt gränssnitt för att hantera Azure-resurser.
Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell.

Försök från shell.azure.com genom att klicka nedan.

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Försök från Azure Portal att använda Cloud Shell-ikonen.

![Portal lansering](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funktioner

### <a name="browser-based-shell-experience"></a>Webbläsarbaserad skal upplevelse
Cloud Shell ger åtkomst till en webbläsarbaserad kommando rads upplevelse som skapats med Azures hanterings uppgifter i åtanke.
Använd Cloud Shell för att arbeta lokalt från en lokal dator på ett sätt som bara molnet kan tillhandahålla.

### <a name="choice-of-preferred-shell-experience"></a>Val av önskad Shell-upplevelse
Användarna kan välja mellan bash eller PowerShell från List rutan Shell.

![Bash i Cloud Shell](media/overview/overview-bash-pic.png)

![PowerShell i Cloud Shell](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserad och konfigurerad Azure-arbetsstation
Cloud Shell hanteras av Microsoft så att det levereras med populära kommando rads verktyg och språk stöd. Cloud Shell autentiseras också automatiskt för omedelbar åtkomst till dina resurser via Azure CLI-eller Azure PowerShell-cmdletar.

Visa en fullständig [lista över verktyg som är installerade i Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrerad Cloud Shell redigerare
Cloud Shell erbjuder en integrerad grafisk text redigerare som baseras på redigeraren för öppen källkod i Monaco. Skapa och redigera bara konfigurationsfiler genom att köra `code .` för sömlös distribution via Azure CLI eller Azure PowerShell.

[Läs mer om Cloud Shell redigeraren](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrerat med docs.microsoft.com

Du kan använda Cloud Shell direkt från dokumentationen som finns på [docs.Microsoft.com](https://docs.microsoft.com). Den är integrerad i [Microsoft Learn](https://docs.microsoft.com/learn/), [Azure POWERSHELL](https://docs.microsoft.com/powershell/azure/overview) och [Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure) – Klicka på knappen "testa" i ett kodfragment för att öppna den fördjupade Shell-upplevelsen. 

### <a name="multiple-access-points"></a>Flera åtkomst punkter
Cloud Shell är ett flexibelt verktyg som kan användas från:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell dokumentation](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Tillägg för Visual Studio Code Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Anslut lagringen för Microsoft Azure filer
Cloud Shell datorer är temporära, men dina filer sparas på två sätt: via en disk avbildning och via en monterad fil resurs med namnet `clouddrive`.  När du startar Cloud Shell för första gången uppmanas du att skapa en resursgrupp, ett lagringskonto och en Azure Files-resurs. Det här är ett engångs steg och kommer automatiskt att bifogas för alla sessioner. En enda fil resurs kan mappas och kommer att användas av både bash och PowerShell i Cloud Shell.

Läs mer om hur du monterar ett [nytt eller befintligt lagrings konto](persisting-shell-storage.md) eller Lär dig mer om de [beständiga mekanismer som används i Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Azure Storage-brandväggen stöds inte för moln gränssnitts lagrings konton.

## <a name="concepts"></a>Begrepp
* Cloud Shell körs på en tillfällig värd som tillhandahålls per-session per användare
* Cloud Shell timeout efter 20 minuter utan interaktiv aktivitet
* Cloud Shell kräver att en Azure-filresurs monteras
* Cloud Shell använder samma Azure-filresurs för både bash och PowerShell
* Cloud Shell tilldelas en dator per användar konto
* Cloud Shell kvarstår $HOME att använda en 5 GB-avbildning som lagras i fil resursen
* Behörigheter anges som en vanlig Linux-användare i bash

Lär dig mer om funktioner i [bash i Cloud Shell](features.md) och [PowerShell i Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Prissättning
Datorn som är värd för Cloud Shell är kostnads fri, med en för hands version av en monterad Azure Files-resurs. Vanliga lagrings kostnader gäller.

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell snabb start](quickstart.md) <br>
[PowerShell i Cloud Shell snabb start](quickstart-powershell.md)
