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
ms.date: 11/16/2017
ms.author: juluk
ms.openlocfilehash: 08ab3b38e4c1fbeb1fac67c5d1b6f6749f7a0a3e
ms.sourcegitcommit: 7d107bb9768b7f32ec5d93ae6ede40899cbaa894
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="overview-of-azure-cloud-shell"></a>Översikt över Azure-molnet Shell
Azure Cloud-gränssnittet är en interaktiv, webbläsare-tillgängliga shell för att hantera Azure-resurser.
Den ger dig möjlighet att välja shell-upplevelse som bäst passar arbetet.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

Starta via Azure-portalen från ikonen molnet Shell:

![Starta företagsportal](media/overview/portal-launch-icon.png)

Använda Bash eller PowerShell shell selector listrutan:

![Bash i molnet Shell](media/overview/overview-bash-pic.png)

![PowerShell i molnet Shell (förhandsgranskning)](media/overview/overview-ps-pic.png)

## <a name="features"></a>Funktioner
### <a name="browser-based-shell-experience"></a>Shell-webbläsarbaserad upplevelse
Molnet Shell ger åtkomst till ett webbaserat kommandoradsverktyget erfarenheterna med Azure-hanteringsuppgifter i åtanke.
Utnyttjar molnet Shell ska fungera gå fritt från en lokal dator på ett sätt endast molnet kan ge.

### <a name="choice-of-preferred-shell-experience"></a>Valet av prioriterade shell-upplevelse
Med Azure Cloud Shell kan du välja den skalupplevelse som passar bäst för ditt sätt att arbeta.
Linux-användare kan välja Bash molnet Shell, medan Windows-användare kan välja PowerShell i molnet Shell (förhandsversion).

### <a name="authenticated-and-configured-azure-workstation"></a>Autentiserad och konfigurerat Azure arbetsstation
Molnet Shell kommer hanteras av Microsoft så att den har förinstallerats med populära kommandoradsverktyg och språkstöd så att du kan arbeta snabbare. Dessutom autentiserar moln-gränssnittet på ett säkert sätt automatiskt för omedelbar åtkomst till resurser via Azure CLI 2.0 eller Azure PowerShell-cmdlets.

Visa fullständig verktygsuppsättning listan för den [Bash upplevelse](features.md#tools) och [PowerShell (förhandsgranskning)-upplevelse.](features-powershell.md#tools)

### <a name="multiple-access-points"></a>Flera åtkomstpunkter
Förutom molnet skal som är tillgängliga från Azure-portalen, kan den också komma åt från:
* [Azure CLI 2.0 ”försök”-dokumentation](https://docs.microsoft.com/cli/azure/overview?view=azure-cli-latest)
* [Azure mobilappar](https://azure.microsoft.com/features/azure-portal/mobile-app/)
* [Visual Studio Code-tillägg](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account)

### <a name="connect-your-azure-files-storage"></a>Ansluta din Azure File storage
Molnet Shell datorer är tillfälliga och därför kräver en resurs för Azure-filer som ska monteras som `clouddrive` att bevara $Home-katalogen.
Första start molnet Shell uppmanas du för att skapa en resurs dela grupp, storage-konto och filen på å dina vägnar. Detta är ett enstaka steg och bifogas automatiskt för alla sessioner. En enda filresurs kan mappas och kommer att användas av både Bash och PowerShell i molnet Shell (förhandsversion).

#### <a name="create-new-storage"></a>Skapa nya lagringsenheter
![](media/overview/basic-storage.png)

Ett lokalt redundant lagringskonto (LRS) och Azure-filer resursen kan skapas för din räkning. Resursen Azure-filer ska användas för både Bash och PowerShell miljöer om du väljer att använda båda. Vanliga lagringskostnader gäller.

Tre resurser kommer att skapas för din räkning:
1. En resursgrupp med namnet:`cloud-shell-storage-<region>`
2. Lagringskontonamnet:`cs<uniqueGuid>`
3. Filresurs med namnet:`cs-<user>-<domain>-com-<uniqueGuid>`

> [!Note]
> Bash i molnet Shell skapar även en standardbild 5 GB disk för att bevara `$Home`. Alla filer i katalogen $Home, till exempel SSH-nycklar finns kvar i din användare diskavbildning som lagras i monterade filresursen. Tillämpa metodtips när du sparar filer i din $Home katalog och monterade filresurs.

#### <a name="use-existing-resources"></a>Använda befintliga resurser
![](media/overview/advanced-storage.png)

Ett avancerat alternativ har angetts för att associera befintliga resurser till molnet Shell.
Klicka på ”Visa avancerade inställningar” i Kommandotolken lagring installationen att visa ytterligare alternativ.
Nedrullningsbara listorna filtreras för molntjänster Shell regionen och lokalt/globalt-redundant storage-konton.

[Lär dig om molnet Shell lagring, uppdatera filresurser och ladda upp/hämta filer.](persisting-shell-storage.md)

## <a name="concepts"></a>Koncept
* Molnet Shell körs på en tillfällig värd på en per session, per användare
* Molnet Shell timeout efter 20 minuter utan interaktiva aktivitet
* Molnet Shell kräver en filresurs som ska monteras
* Molnet Shell använder samma filresurs för både Bash och PowerShell
* Molnet Shell tilldelas en dator per konto
* Behörigheter har angetts som en vanlig Linux-användare i Bash

Mer information om funktioner i [Bash i molnet Shell](features.md) och [PowerShell i molnet Shell (förhandsgranskning)](features-powershell.md).

## <a name="examples"></a>Exempel
* Använda skript för att automatisera hanteringsuppgifter för Azure
* Hantera Azure-resurser via Azure-portalen och Azure kommandoradsverktyg samtidigt
* Testkör Azure CLI 2.0 eller Azure PowerShell-cmdlets

Prova att använda de här exemplen i Snabbstart för [Bash i molnet Shell](quickstart.md) och [PowerShell i molnet Shell (förhandsgranskning)](quickstart-powershell.md).

## <a name="pricing"></a>Prissättning
Den dator som är värd molnet Shell är ledigt, med ett krav för en monterad resurs i Azure-filer. Vanliga lagringskostnader gäller.

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell-Snabbstart](quickstart.md) <br>
[PowerShell i Snabbstart molnet Shell (förhandsgranskning)](quickstart-powershell.md)