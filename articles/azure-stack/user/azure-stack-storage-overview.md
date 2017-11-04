---
title: "Introduktion till stacken för Azure storage"
description: "Lär dig mer om Azure-stacken lagring"
services: azure-stack
documentationcenter: 
author: xiaofmao
manager: 
editor: 
ms.assetid: 092aba28-04bc-44c0-90e1-e79d82f4ff42
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 9/25/2017
ms.author: xiaofmao
ms.openlocfilehash: 8777aa486a627cf8b2d8ba443e115638354d10da
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-stack-storage"></a>Introduktion till stacken för Azure storage

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

## <a name="overview"></a>Översikt
Azure-stacken Storage är en uppsättning lagring molntjänster inklusive Blobbar, tabeller och köer som är konsekventa med Azure Storage-tjänster.

## <a name="azure-stack-storage-services"></a>Azure Stack-och lagringstjänster
Azure Stack storage tillhandahåller följande tre tjänster:

* **Blob Storage** 

    BLOB storage lagrar Ostrukturerade objektdata. En blobb kan bestå av vilka slags textdata eller binära data som helst, till exempel ett dokument, en mediefil eller ett installationsprogram.
* **Table Storage** 

    Table storage lagrar strukturerade datauppsättningar. Table Storage är en nyckel- och attributdatabas för NoSQL som möjliggör snabb utveckling och snabb tillgång till stora mängder data.
* **Queue Storage** 

    Queue storage erbjuder tillförlitlig meddelandehantering för arbetsflödesbearbetning och för kommunikation mellan komponenter i molntjänster.

Ett Azure-stacken storage-konto är ett säkert konto som ger dig åtkomst till tjänster i Azure Storage i stacken. Ditt lagringskonto tillhandahåller den unika namnrymden för dina lagringsresurser. Följande diagram visar relationerna mellan Stack för Azure storage-resurser i ett lagringskonto:

![Översikt över Azure Storage-stacken](media/azure-stack-storage-overview/AzureStackStorageOverview.png)


### <a name="blob-storage"></a>Blob Storage

Blob storage erbjuder en effektiv och skalbar lösning för användare med stora mängder Ostrukturerade objektdata för lagring i molnet. Du kan använda Blob Storage för att lagra innehåll som:

* Dokument
* Sociala data, till exempel foton, videor, musik och bloggar
* Säkerhetskopior av filer, datorer, databaser och enheter
* Bilder och text för webbprogram
* Konfigurationsdata för molnprogram
* Stordata, till exempel loggar och andra stora datauppsättningar

Blobbar ordnas i behållare. Behållare är också ett praktiskt sätt att tilldela säkerhetsprinciper till grupper med objekt. Ett lagringskonto kan innehålla valfritt antal behållare och en behållare kan innehålla valfritt antal blobbar, upp till högst storage-konto.

BLOB storage erbjuder tre typer av blobbar: 
* **Blockblobbar** 

    Blockblobbar är optimerade för direktuppspelning och lagring av molnobjekt och är ett bra alternativ för att lagra dokument, mediefiler, säkerhetskopior osv.
* **Tilläggsblobbar** 

    Tilläggsblobbar påminner om blockblobbar, men är optimerade för tilläggsåtgärder. En tilläggsblobb kan bara uppdateras genom att ett nytt block läggs till i slutet. Tilläggsblobbar är praktiska i scenarier som loggning, där nya data bara behöver skrivas till slutet av blobben.
* **Sidblobbar** 

    Sidblobbar är optimerade för att representera IaaS-diskar och stöd för slumpmässiga skrivningar som är upp till 1 TB i storlek. En virtuell dator i Azure-stacken kopplade IaaS disken är en VHD som lagras som en sidblobb.


### <a name="table-storage"></a>Table Storage
Moderna program behöver ofta datalager med bättre skalbarhet och flexibilitet än tidigare versioner av den programvara som krävs. Table Storage erbjuder mycket skalbar lagring med hög tillgänglighet, så att ditt program kan anpassas automatiskt efter användarens behov. Table Storage är Microsofts nyckel- och attributdatabas för NoSQL. Tjänsten har en schemalös design, vilket skiljer den från traditionella relationsdatabaser. Ett schemalöst datalager gör det enkelt att anpassa data i takt med att programmets behov förändras. Table Storage är enkelt att använda, vilket gör det lätt för utvecklare att snabbt skapa program.

Table Storage är en nyckel- och attributdatabas, vilket innebär att varje värde i en tabell lagras med ett typbestämt egenskapsnamn. Egenskapsnamnet kan användas för att filtrera och ange urvalskriterier. En uppsättning egenskaper och deras värden utgör en entitet. Eftersom Table Storage är schemalöst kan två entiteter i samma tabell innehålla olika samlingar egenskaper, och dessa egenskaper kan höra till olika typer.

Du kan använda Table Storage för att lagra flexibla datauppsättningar, till exempel användardata för webbprogram, adressböcker, enhetsinformation och andra typer av metadata som din tjänst kräver. NoSQL-databaser som Table Storage erbjuder ett populärt alternativ till traditionella relationsdatabaser för dagens Internetbaserade program.

Ett lagringskonto kan innehålla valfritt antal tabeller och en tabell kan innehålla valfritt antal entiteter, upp till lagringskontots kapacitetsgräns.

### <a name="queue-storage"></a>Queue Storage
När program utformas för skalning är programkomponenterna ofta fristående, så att de kan skalas oberoende av varandra. Queue Storage är en tillförlitlig meddelandelösning för asynkron kommunikation mellan programkomponenter, oavsett om de körs i molnet, på skrivbordet, på en lokal server eller på en mobil enhet. Queue Storage har också stöd för hantering av asynkrona åtgärder och utveckling av processarbetsflöden.

Ett lagringskonto kan innehålla valfritt antal köer och en kö kan innehålla valfritt antal meddelanden, upp till lagringskontots kapacitetsgräns. Enskilda meddelanden kan vara upp till 64 kB stora.

## <a name="next-steps"></a>Nästa steg
* [Konsekvent Azure storage: skillnader och överväganden](azure-stack-acs-differences.md)

* Mer information om Azure Storage finns [introduktion till Microsoft Azure Storage](../../storage/common/storage-introduction.md)

