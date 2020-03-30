---
title: Företagets policy och efterlevnad i Azure DevTest Labs
description: Den här artikeln innehåller vägledning om hur du styr företagets policy och efterlevnad för Azure DevTest Labs-infrastrukturen.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/26/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 81ca2a90b1940d70e170cab3f8d18144a5d5e5a8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74560508"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Styrning av Azure DevTest Labs-infrastruktur – Företagets policy och efterlevnad
Den här artikeln innehåller vägledning om hur du styr företagets policy och efterlevnad för Azure DevTest Labs-infrastrukturen. 

## <a name="public-vs-private-artifact-repository"></a>Offentlig kontra privat artefaktdatabas

### <a name="question"></a>Fråga
När ska en organisation använda en offentlig artefaktdatabas kontra privat artefaktlagringsplats i DevTest Labs?

### <a name="answer"></a>Svar
Den [offentliga artefaktdatabasen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) innehåller en första uppsättning programpaket som oftast används. Det hjälper till med snabb distribution utan att behöva investera tid för att reproducera vanliga utvecklarverktyg och tillägg. Du kan välja att distribuera en egen privat databas. Du kan använda en offentlig och en privat databas parallellt. Du kan också välja att inaktivera den offentliga databasen. Kriterierna för att distribuera en privat databas bör styras av följande frågor och överväganden:

- Har organisationen ett krav på att ha företagslicensierad programvara som en del av deras DevTest Labs erbjudande? Om svaret är ja ska en privat databas skapas.
- Utvecklar organisationen anpassad programvara som tillhandahåller en specifik åtgärd, vilket krävs som en del av den övergripande etableringsprocessen? Om svaret är ja ska en privat databas distribueras.
- Om organisationens styrningsprincip kräver isolering och externa databaser inte är under direkt konfigurationshantering av organisationen, bör en privat artefaktlagringsplats distribueras. Som en del av den här processen kan en första kopia av den offentliga databasen kopieras och integreras med den privata databasen. Sedan kan den offentliga databasen inaktiveras så att ingen inom organisationen kan komma åt den längre. Den här metoden tvingar alla användare inom organisationen att bara ha en enda databas som är godkänd av organisationen och minimera konfigurationsdrift.

### <a name="single-repository-or-multiple-repositories"></a>En databas eller flera databaser 

### <a name="question"></a>Fråga
Ska en organisation planera för en enskild databas eller tillåta flera databaser?

### <a name="answer"></a>Svar
Som en del av organisationens övergripande strategi för styrning och konfigurationshantering rekommenderar vi att du använder en centraliserad databas. När du använder flera databaser kan de bli silor av ohanterat program under tiden. Med en central databas kan flera team använda artefakter från den här databasen för sina projekt. Det upprätthåller standardisering, säkerhet, enkel hantering och eliminerar dubblering av insatser. Som en del av centraliseringen är följande åtgärder rekommenderade metoder för långsiktig förvaltning och hållbarhet:

- Associera Azure Repos med samma Azure Active Directory-klient som Azure-prenumerationen använder för autentisering och auktorisering.
- Skapa en grupp med namnet **Alla DevTest Labs-utvecklare** i Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefaktutveckling bör placeras i den här gruppen.
- Samma Azure Active Directory-grupp kan användas för att ge åtkomst till Azure Repos-databasen och till labbet.
- I Azure Repos bör förgrening eller forking användas för att separera en in-development-databas från den primära produktionsdatabasen. Innehåll läggs bara till i huvudgrenen med en pull-begäran efter en korrekt kodgranskning. När kodregransaren har godkänt ändringen sammanfogar en leadutvecklare, som ansvarar för underhållet av huvudgrenen, den uppdaterade koden. 

## <a name="corporate-security-policies"></a>Företagets säkerhetsprinciper

### <a name="question"></a>Fråga
Hur kan en organisation se till att företagets säkerhetsprinciper finns på plats?

### <a name="answer"></a>Svar
En organisation kan uppnå det genom att göra följande åtgärder:

1. Utveckla och publicera en omfattande säkerhetspolicy. Principen beskriver reglerna för acceptabel användning som är associerade med hjälp av programvara, molntillgångar. Det definierar också vad som tydligt bryter mot policyn. 
2. Utveckla en anpassad avbildning, anpassade artefakter och en distributionsprocess som möjliggör orkestrering inom säkerhetssfären som definieras med active directory. Detta tillvägagångssätt upprätthåller företagets gräns och fastställer en gemensam uppsättning miljökontroller. Dessa kontroller mot miljön som en utvecklare kan tänka på när de utvecklar och följer en säker utvecklingslivscykel som en del av deras övergripande process. Målet är också att skapa en miljö som inte är alltför restriktiv och som kan hindra utvecklingen, utan en rimlig uppsättning kontroller. Gruppprinciperna på organisationsenheten (OU) som innehåller virtuella labbdatorer kan vara en delmängd av den totala gruppprincip som finns i produktionen. Alternativt kan de vara ytterligare en uppsättning för att på ett korrekt sätt minska eventuella identifierade risker.

## <a name="data-integrity"></a>Dataintegritet

### <a name="question"></a>Fråga
Hur kan en organisation säkerställa dataintegritet för att säkerställa att återmotsättningsutvecklare inte kan ta bort kod eller införa skadlig kod eller icke godkänd programvara?

### <a name="answer"></a>Svar
Det finns flera lager av kontroll för att minska hotet från externa konsulter, entreprenörer eller anställda som remoting in för att samarbeta i DevTest Labs. 

Som tidigare nämnts måste det första steget ha en godtagbar användningspolicy utarbetad och definierad som tydligt beskriver konsekvenserna när någon bryter mot policyn. 

Det första lagret av kontroller för fjärråtkomst är att tillämpa en fjärråtkomstprincip via en VPN-anslutning som inte är direkt ansluten till labbet. 

Det andra kontrolllagret är att använda en uppsättning grupprincipobjekt som förhindrar att kopiera och klistra in via fjärrskrivbord. En nätverkspolitik skulle kunna implementeras för att inte tillåta utgående tjänster från miljön, till exempel FTP- och RDP-tjänster, ut ur miljön. Användardefinierad routning kan tvinga all Azure-nätverkstrafik tillbaka till lokalt, men routningen kunde inte ta hänsyn till alla webbadresser som kan tillåta överföring av data om inte kontrolleras via en proxy för att skanna innehåll och sessioner. Offentliga IP-adresser kan begränsas inom det virtuella nätverket som stöder DevTest Labs för att inte tillåta överbryggning av en extern nätverksresurs.

I slutändan måste samma typ av begränsningar tillämpas i hela organisationen, vilket också måste ta hänsyn till alla möjliga metoder för flyttbara medier eller externa webbadresser som kan acceptera ett inlägg av innehåll. Rådgör med din säkerhetspersonal för att granska och implementera en säkerhetsprincip. Fler rekommendationer finns i [Microsoft Cyber Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Nästa steg
Se [Programmigrering och integration](devtest-lab-guidance-governance-application-migration-integration.md).
