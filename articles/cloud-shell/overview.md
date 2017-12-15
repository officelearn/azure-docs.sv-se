---
title: "Översikt över Azure Cloud Shell | Microsoft Docs"
description: "Översikt över Azure-molnet Shell."
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: juluk
ms.openlocfilehash: 129b43db85a0962005352e0f1e6ad2ad3be2c7d5
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure-molnet Shell
Azure Cloud-gränssnittet är en interaktiv, webbläsare-tillgängliga shell för att hantera Azure-resurser.
Den ger dig möjlighet att välja shell-upplevelse som bäst passar arbetet.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Försök från shell.azure.com med den här knappen.

[![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure-molnet Shell")](https://shell.azure.com)

Försök från Azure-portalen med hjälp av molnet Shell-ikonen.

![Starta företagsportal](media/overview/portal-launch-icon.png)

## <a name="features"></a>Funktioner
### <a name="browser-based-shell-experience"></a>Shell-webbläsarbaserad upplevelse
Molnet Shell ger åtkomst till ett webbaserat kommandoradsverktyget erfarenheterna med Azure-hanteringsuppgifter i åtanke.
Utnyttjar molnet Shell ska fungera gå fritt från en lokal dator på ett sätt endast molnet kan ge.

### <a name="choice-of-preferred-shell-experience"></a>Valet av prioriterade shell-upplevelse
Linux-användare kan använda Bash molnet Shell, medan Windows-användare kan använda PowerShell i molnet Shell (förhandsgranskning) shell listrutan.

![Bash i molnet Shell](media/overview/overview-bash-pic.png)

![PowerShell i molnet Shell (förhandsgranskning)](media/overview/overview-ps-pic.png)

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserad och konfigurerat Azure arbetsstation
Molnet Shell hanteras av Microsoft så att den innehåller populära kommandoradsverktyg och språkstöd. Molnet Shell autentiserar också säkert automatiskt för omedelbar åtkomst till resurser via Azure CLI 2.0 eller Azure PowerShell-cmdlets.

Visa fullständig verktygsuppsättning listan för den [Bash upplevelse](features.md#tools) och [PowerShell (förhandsgranskning)-upplevelse.](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Flera åtkomstpunkter
Moln-gränssnittet är ett flexibelt verktyg som kan användas från:
* [Portal.Azure.com](https://portal.azure.com)
* [Shell.Azure.com](https://shell.azure.com)
* [Azure CLI 2.0 ”försök”-dokumentation](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Azure mobilappar](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Tillägget för VS kod Azure-konto](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-microsoft-azure-files-storage"></a>Ansluta din Microsoft Azure File storage
Molnet Shell datorer är tillfälliga och kräver en resurs för Azure-filer som ska monteras som `clouddrive` att spara dina filer.

Första startas för molnet Shell uppmanas du för att skapa en resurs dela grupp, storage-konto och Azure-filer för din räkning. Detta är ett enstaka steg och bifogas automatiskt för alla sessioner. En enda filresurs kan mappas och kommer att användas av både Bash och PowerShell i molnet Shell (förhandsversion).

#### <a name="create-new-storage"></a>Skapa nya lagringsenheter
![](media/overview/basic-storage.png)

Ett lokalt redundant lagringskonto (LRS) och Azure-filer resursen kan skapas för din räkning. Resursen Azure-filer ska användas för både Bash och PowerShell miljöer om du väljer att använda båda. Vanliga lagringskostnader gäller.

Tre resurser kommer att skapas för din räkning:
1. En resursgrupp med namnet:`cloud-shell-storage-<region>`
2. Lagringskontonamnet:`cs<uniqueGuid>`
3. Filresurs med namnet:`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash i molnet Shell skapar även en standardbild 5 GB disk för att bevara `$Home`. Alla filer i katalogen $Home, till exempel SSH-nycklar finns kvar i din användare diskavbildning som lagras i den monterade Azure-filresursen. Tillämpa metodtips när du sparar filer i din $Home katalog och monterade Azure-filresursen.

#### <a name="use-existing-resources"></a>Använda befintliga resurser
![](media/overview/advanced-storage.png)

Ett avancerat alternativ har angetts för att associera befintliga resurser till molnet Shell.
Klicka på ”Visa avancerade inställningar” i Kommandotolken lagring installationen att visa ytterligare alternativ.

> [!Note]
> Nedrullningsbara listorna filtreras för före molnet Shell regionen och LRS/GRS storage-konton.

[Lär dig om molnet Shell lagring, uppdatera Azure-filresurser och ladda upp/hämta filer.](persisting-shell-storage.md)

## <a name="concepts"></a>Koncept
* Molnet Shell körs på en tillfällig värd på en per session, per användare
* Molnet Shell timeout efter 20 minuter utan interaktiva aktivitet
* Molnet Shell kräver en Azure-filresurs som ska monteras
* Molnet Shell använder samma Azure-filresursen för både Bash och PowerShell
* Molnet Shell tilldelas en dator per konto
* Bash kvarstår $Home med hjälp av en 5 GB-avbildning som lagras i filresursen
* Behörigheter har angetts som en vanlig Linux-användare i Bash

Mer information om funktioner i [Bash i molnet Shell](features.md) och [PowerShell i molnet Shell (förhandsgranskning)](features-powershell.md).

## <a name="pricing"></a>Prissättning
Den dator som är värd molnet Shell är ledigt, med ett krav för en monterad resurs i Azure-filer. Vanliga lagringskostnader gäller.

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Snabbstart molnet Shell (förhandsgranskning)](quickstart-powershell.md)