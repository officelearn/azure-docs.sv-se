---
title: "Översikt över Azure Cloud Shell (förhandsversion) | Microsoft Docs"
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
ms.date: 09/25/2017
ms.author: juluk
ms.openlocfilehash: 44d2ac2fd35621ab8cd8d7584744139ee3bab5c2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="overview-of-azure-cloud-shell-preview"></a>Översikt över Azure-molnet Shell (förhandsgranskning)
Azure Cloud-gränssnittet är en interaktiv, webbläsare-tillgängliga shell för att hantera Azure-resurser.
Den ger dig möjlighet att välja shell-upplevelse som bäst passar arbetet.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

![Bash i molnet Shell](media/overview/overview-bash-pic.png)

![PowerShell i molnet Shell](media/overview/overview-ps-pic.png)

## <a name="features"></a>Funktioner
### <a name="browser-based-shell-experience"></a>Shell-webbläsarbaserad upplevelse
Molnet Shell ger åtkomst till ett webbaserat kommandoradsverktyget erfarenheterna med Azure-hanteringsuppgifter i åtanke.
Utnyttjar molnet Shell ska fungera gå fritt från en lokal dator på ett sätt endast molnet kan ge.

### <a name="choice-of-preferred-shell-experience"></a>Valet av prioriterade shell-upplevelse
Med Azure Cloud Shell kan du välja den skalupplevelse som passar bäst för ditt sätt att arbeta.
Linux-användare kan välja Bash och Windows-användare kan välja PowerShell.

### <a name="pre-configured-azure-workstation"></a>Förkonfigurerade Azure arbetsstation
Molnet Shell finns förinstallerat med populära kommandoradsverktyg och språkstöd så att du kan arbeta snabbare.

Visa listan över fullständig verktygsuppsättning för [Bash upplevelse](features.md#tools) och [PowerShell upplevelse.](features-powershell.md#tools)

### <a name="automatic-authentication"></a>Automatisk autentisering
Molnet Shell autentiserar på ett säkert sätt automatiskt på varje session för omedelbar åtkomst till resurser via Azure CLI 2.0.

### <a name="connect-your-azure-file-storage"></a>Anslut Azure File storage
Molnet Shell datorer är tillfälliga och därför kräver en Azure-filresurs som ska monteras som `clouddrive` att bevara $Home-katalogen.
Första start molnet Shell uppmanas du för att skapa en resurs dela grupp, storage-konto och filen på å dina vägnar. Detta är ett enstaka steg och bifogas automatiskt för alla sessioner. En enda filresurs kan mappas och kommer att användas av både Bash och PowerShell i molnet Shell.

#### <a name="create-new-storage"></a>Skapa nya lagringsenheter
![](media/overview/basic-storage.png)

Ett lokalt redundant lagringskonto (LRS) och Azure-filresurs kan skapas för din räkning. Om du väljer att använda både används Azure-filresursen för Bash- och PowerShell-miljöer. Vanliga lagringskostnader gäller.

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
* Molnet Shell körs på en tillfällig virtuell dator på en per session, per användare
* Molnet Shell timeout efter 20 minuter utan interaktiva aktivitet
* Molnet Shell kan endast användas med en filresurs som ansluten
* Molnet Shell använder en samma filresurs för både Bash och PowerShell
* Molnet Shell tilldelas en dator per konto
* Behörigheter har angetts som en vanlig Linux-användare (Bash)

Mer information om funktioner i [Bash i molnet Shell](features.md) och [PowerShell i molnet Shell](features-powershell.md).

## <a name="examples"></a>Exempel
* Använda skript för att automatisera hanteringsuppgifter för Azure
* Hantera Azure-resurser via Azure-portalen och Azure kommandoradsverktyg samtidigt
* Testkör Azure CLI 2.0 eller Azure PowerShell-cmdlets

Prova att använda de här exemplen i Snabbstart för [Bash i molnet Shell](quickstart.md) och [PowerShell i molnet Shell](quickstart-powershell.md).

## <a name="pricing"></a>Prissättning
Den dator som är värd molnet Shell är ledigt, med ett krav för en monterad Azure-filresursen. Vanliga lagringskostnader gäller.

## <a name="supported-browsers"></a>Webbläsare som stöds
Molnet Shell rekommenderas för Chrome, kant och Safari.
När molnet Shell stöds för Chrome, Firefox, Safari, Internet Explorer eller Edge, regleras molnet Shell specifika webbläsarinställningar.