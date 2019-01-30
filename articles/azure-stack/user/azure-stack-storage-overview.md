---
title: Introduktion till Azure Stack-lagring
description: Lär dig mer om Azure Stack-lagring
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/14/2019
ms.author: mabrigg
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: d1baeb5ff32fcadaeca25244ce3167fe3fe4477a
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249722"
---
# <a name="introduction-to-azure-stack-storage"></a>Introduktion till Azure Stack-lagring

*Gäller för: Integrerade Azure Stack-system och Azure Stack Development Kit*

## <a name="overview"></a>Översikt

Azure Stack Storage är en uppsättning molnlagringstjänster med Blobbar, tabeller och köer som är förenliga med Azure Storage-tjänster.

## <a name="azure-stack-storage-services"></a>Azure Stack-och lagringstjänster

Azure Stack-storage tillhandahåller följande tre tjänster:

- **Blob Storage**

    BLOB storage lagrar Ostrukturerade objektdata. En blobb kan bestå av vilka slags textdata eller binära data som helst, till exempel ett dokument, en mediefil eller ett installationsprogram.

- **Table Storage**

    Table storage lagrar strukturerade datauppsättningar. Table Storage är en nyckel- och attributdatabas för NoSQL som möjliggör snabb utveckling och snabb tillgång till stora mängder data.

- **Queue Storage**

    Queue storage erbjuder tillförlitlig meddelandehantering för arbetsflödesbearbetning och för kommunikation mellan komponenter i molntjänster.

Ett Azure Stack-lagringskonto är ett säkert konto som ger dig åtkomst till tjänster i Azure Stack-lagring. Ditt lagringskonto tillhandahåller den unika namnrymden för dina lagringsresurser. Följande diagram visar relationerna mellan Azure Stack-lagringsresurser på ett lagringskonto:

![Översikt över Azure Stack-lagring](media/azure-stack-storage-overview/AzureStackStorageOverview.png)

### <a name="blob-storage"></a>Blob Storage

Blob-lagring erbjuder en effektiv och skalbar lösning för användare med en stor mängd Ostrukturerade objektdata att lagra i molnet. Du kan använda blob storage för att lagra innehåll som:

- Dokument
- Sociala data, till exempel foton, videor, musik och bloggar
- Säkerhetskopior av filer, datorer, databaser och enheter
- Bilder och text för webbprogram
- Konfigurationsdata för molnprogram
- Stordata, till exempel loggar och andra stora datauppsättningar

Blobar ordnas i container. Containrar är också ett praktiskt sätt att tilldela säkerhetsprinciper till grupper med objekt. Ett lagringskonto kan innehålla valfritt antal behållare och en behållare kan innehålla valfritt antal blobbar, upp till gränsen på storage-konto.

BLOB storage erbjuder tre typer av blobbar:

- **Blockblob-objekt**

    Blockblobbar är optimerade för direktuppspelning och lagring av molnobjekt och är ett bra alternativ för att lagra dokument, mediefiler, säkerhetskopior och osv.

- **Tilläggsblobbar**

    Tilläggsblobbar påminner om blockblobbar, men är optimerade för tilläggsåtgärder. En tilläggsblobb kan bara uppdateras genom att ett nytt block läggs till i slutet. Tilläggsblobbar är praktiska i scenarier som loggning, där nya data bara behöver skrivas till slutet av blobben.

- **Sidblob-objekt**

    Sidblobar är optimerade för att representera IaaS-diskar och bra stöd för slumpmässiga skrivningar som är upp till 1 TB i storlek. En virtuell dator i Azure Stack kopplat IaaS disken är en virtuell Hårddisk som lagras som en sidblobb.

### <a name="table-storage"></a>Table Storage

Moderna program behöver ofta datalager med bättre skalbarhet och flexibilitet än tidigare versioner av den programvara som krävs. Table Storage erbjuder mycket skalbar lagring med hög tillgänglighet, så att ditt program kan anpassas automatiskt efter användarens behov. Table storage är Microsofts NoSQL nyckel-/ attributlager – den har en schemalös design, vilket skiljer den från traditionella relationsdatabaser. Ett schemalöst datalager gör det enkelt att anpassa data i takt med att programmets behov förändras. Table Storage är enkelt att använda, vilket gör det lätt för utvecklare att snabbt skapa program.

Table storage är en nyckel-och attributdatabas, vilket innebär att varje värde i en tabell lagras med ett typbestämt egenskapsnamn. Egenskapsnamnet kan användas för att filtrera och ange urvalskriterier. En uppsättning egenskaper och deras värden utgör en entitet. Eftersom table storage är schemalöst kan två entiteter i samma tabell innehålla olika samlingar egenskaper och dessa egenskaper kan vara av olika typer.

Du kan använda table storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. NoSQL-databaser som table storage erbjuder ett populärt alternativ till traditionella relationsdatabaser för dagens Internetbaserade program.

Ett lagringskonto kan innehålla valfritt antal tabeller och en tabell kan innehålla valfritt antal enheter, upp till lagringskontots kapacitetsgräns.

### <a name="queue-storage"></a>Queue Storage

När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue storage erbjuder en pålitlig meddelandelösning för asynkron kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Ett lagringskonto kan innehålla valfritt antal köer och en kö kan innehålla valfritt antal meddelanden, upp till lagringskontots kapacitetsgräns. Enskilda meddelanden kan vara upp till 64 kB stora.

## <a name="next-steps"></a>Nästa steg

- [Azure-konsekvent: skillnader och överväganden](azure-stack-acs-differences.md)

- Läs mer om Azure Storage i [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)
