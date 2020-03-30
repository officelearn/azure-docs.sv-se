---
title: Översikt över Azure Cloud Shell | Microsoft-dokument
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
ms.openlocfilehash: 513c3da8031774f5f111ee357b5a3c43e1d09d95
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75832473"
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure Cloud Shell
Azure Cloud Shell är ett interaktivt, autentiserat skal för hantering av Azure-resurser via webbläsare.
Den ger dig flexibilitet att välja den gränssnittsmiljö som passar bäst för ditt sätt att arbeta, antingen Bash eller PowerShell.

Prova från shell.azure.com genom att klicka nedan.

[![Bädda in start](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Prova från Azure-portalen med hjälp av Cloud Shell-ikonen.

![Portal lansering](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funktioner

### <a name="browser-based-shell-experience"></a>Webbläsarbaserad skalupplevelse
Cloud Shell ger åtkomst till en webbläsarbaserad kommandoradsupplevelse som skapats med Azure-hanteringsuppgifter i åtanke.
Utnyttja Cloud Shell för att arbeta obundna från en lokal dator på ett sätt som bara molnet kan tillhandahålla.

### <a name="choice-of-preferred-shell-experience"></a>Val av föredragen skalupplevelse
Användare kan välja mellan Bash eller PowerShell.
1. Välj **Cloud Shell**.

    ![Ikon för Cloud Shell](media/overview/overview-cloudshell-icon.png)

2. Välj **Bash** eller **PowerShell**.

    ![Välj antingen Bash eller PowerShell](media/overview/overview-choices.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserade och konfigurerade Azure-arbetsstation
Cloud Shell hanteras av Microsoft så det levereras med populära kommandoradsverktyg och språkstöd. Cloud Shell autentiserar också automatiskt för omedelbar åtkomst till dina resurser via Azure CLI- eller Azure PowerShell-cmdlets.

Visa hela [listan med verktyg som är installerade i Cloud Shell.](features.md#tools)

### <a name="integrated-cloud-shell-editor"></a>Integrerad Cloud Shell-redigerare
Cloud Shell erbjuder en integrerad grafisk textredigerare baserad på Monaco Editor med öppen källkod. Skapa och redigera konfigurationsfiler genom att köra `code .` för sömlös distribution via Azure CLI eller Azure PowerShell.

[Läs mer om Cloud Shell-redigeraren](using-cloud-shell-editor.md).

### <a name="integrated-with-docsmicrosoftcom"></a>Integrerad med docs.microsoft.com

Du kan använda Cloud Shell direkt från dokumentation som finns på [docs.microsoft.com](https://docs.microsoft.com). Den är integrerad i Dokumentationen för [Microsoft Learn,](https://docs.microsoft.com/learn/) [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) och [Azure CLI](https://docs.microsoft.com/cli/azure) – klicka på knappen "Prova det" i ett kodavsnitt för att öppna den uppslukande skalupplevelsen. 

### <a name="multiple-access-points"></a>Flera åtkomstpunkter
Cloud Shell är ett flexibelt verktyg som kan användas från:
* [portal.azure.com](https://portal.azure.com)
* [shell.azure.com](https://shell.azure.com)
* [Azure CLI-dokumentation](https://docs.microsoft.com/cli/azure)
* [Azure PowerShell-dokumentation](https://docs.microsoft.com/powershell/azure/overview)
* [Azure-mobilapp](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Tillägget Visual Studio Code Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ansluta ditt Microsoft Azure Files-lagringsutrymme
Cloud Shell-datorer är tillfälliga, men dina filer sparas på två sätt: genom `clouddrive`en diskavbildning och genom en monterad filresurs med namnet .  När du startar Cloud Shell för första gången uppmanas du att skapa en resursgrupp, ett lagringskonto och en Azure Files-resurs. Detta är ett engångssteg och kommer automatiskt att bifogas för alla sessioner. En enda filresurs kan mappas och kommer att användas av både Bash och PowerShell i Cloud Shell.

Läs mer om du vill veta mer om hur du monterar ett [nytt eller befintligt lagringskonto](persisting-shell-storage.md) eller om du vill veta mer om [de persistensmekanismer som används i Cloud Shell](persisting-shell-storage.md#how-cloud-shell-storage-works).

> [!NOTE]
> Azure-lagringsbrandvägg stöds inte för molnskallagringskonton.

## <a name="concepts"></a>Begrepp
* Cloud Shell körs på en tillfällig värd som tillhandahålls per session, per användare
* Cloud Shell time out efter 20 minuter utan interaktiv aktivitet
* Cloud Shell kräver att en Azure-filresurs monteras
* Cloud Shell använder samma Azure-filresurs för både Bash och PowerShell
* Cloud Shell tilldelas en dator per användarkonto
* Cloud Shell kvarstår $HOME med hjälp av en 5 GB-avbildning som finns i filresursen
* Behörigheter anges som en vanlig Linux-användare i Bash

Läs mer om funktioner i [Bash i Cloud Shell](features.md) och [PowerShell i Cloud Shell](features-powershell.md).

## <a name="pricing"></a>Prissättning
Datorn som är värd för Cloud Shell är gratis, med en förutsättning för en monterad Azure Files-resurs. Regelbundna lagringskostnader tillkommer.

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell snabbstart](quickstart.md) <br>
[PowerShell i Cloud Shell snabbstart](quickstart-powershell.md)
