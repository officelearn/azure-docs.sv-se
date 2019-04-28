---
title: Styrning i Azure DevTest Labs-infrastruktur
description: Den här artikeln innehåller riktlinjer för styrning i Azure DevTest Labs-infrastruktur.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/11/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: e02400ef940efdf42370fbdc1da75bdc7062a8ef
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62127369"
---
# <a name="governance-of-azure-devtest-labs-infrastructure---company-policy-and-compliance"></a>Styrning i Azure DevTest Labs infrastruktur - företagets principer och efterlevnad
Den här artikeln innehåller råd om reglerar företagets principer och efterlevnad för Azure DevTest Labs-infrastruktur. 

## <a name="public-vs-private-artifact-repository"></a>Offentliga och privata artefaktcentrallagret

### <a name="question"></a>Fråga
När ska en organisation använda offentliga artefaktlagringsplatsen jämfört med privata artefaktcentrallagret i DevTest Labs?

### <a name="answer"></a>Svar
Den [offentliga artefaktlagringsplatsen](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) ger en inledande uppsättning programvarupaket som används mest. Det underlättar snabb distribution utan att investera tid att återskapa vanliga utvecklingsverktyg och tillägg. Du kan välja att distribuera sina egna privata lagringsplatsen. Du kan använda en offentlig och en privat lagringsplats parallellt. Du kan också välja att inaktivera den offentliga databasen. Villkoren för att distribuera en privat lagringsplats bör styras av följande frågor och överväganden:

- Har organisationen ett krav att ha företagets licensierad programvara som en del av sitt erbjudande för DevTest Labs? Om svaret är Ja, bör privata lagringsplatsen skapas.
- Organisationen utveckla anpassade program som ger en viss åtgärd som krävs som en del av den övergripande processen för etablering? Om svaret är Ja, bör en privat lagringsplats distribueras.
- Om organisationens principer kräver isolering och externa databaser inte är direkt konfiguration hanteras av organisationen, ska en privat artefaktcentrallagret distribueras. Som en del av den här processen, en inledande kopia av den offentliga databasen kopieras och integrerad med den privata lagringsplatsen. Den offentliga databasen kan sedan inaktiveras så att ingen inom organisationen kan komma åt den längre. Den här metoden tvingar alla användare i organisationen ska ha en enda lagringsplats som är godkänd av organisationen och minimera konfigurationsförändringar.

### <a name="single-repository-or-multiple-repositories"></a>Enkel databas eller flera databaser 

### <a name="question"></a>Fråga
Bör en organisation planerar för en enda lagringsplats eller tillåta flera databaser?

### <a name="answer"></a>Svar
Som en del av din organisations övergripande styrnings- och hanteringsstrategi för konfiguration rekommenderar vi att du använder en centraliserad databas. När du använder flera databaser, kan de bli silor av ohanterade programvara över tiden. Flera team kan använda artefakter från den här lagringsplatsen för sina projekt med en central databas. Den tillämpar standardisering, säkerhet, enkel hantering och eliminerar dubbelarbete. Som en del av centralisering rekommenderas följande metoder för hantering av långsiktig och hållbarhet:

- Associera Azure-databaser med samma Azure Active Directory-klient som använder Azure-prenumerationen för autentisering och auktorisering.
- Skapa en grupp med namnet **alla DevTest Labs utvecklare** i Azure Active Directory som hanteras centralt. Alla utvecklare som bidrar till artefakt utveckling ska placeras i den här gruppen.
- Samma Azure Active Directory-grupp kan användas för att ge åtkomst till lagringsplatsen Azure-databaser och till labbet.
- I Azure-lagringsplatser, branchning eller förgreningen ska användas till en separat en i utveckling från den primära produktion-lagringsplatsen. Innehåll läggs endast till mastergrenen med en pull-begäran när du har rätt Kodgranskning. När koden granskaren godkänner ändringen, sammanfogar chefsutvecklare som ansvarar för underhåll av huvudgrenen, den uppdaterade koden. 

## <a name="corporate-security-policies"></a>Företagets säkerhetsprinciper

### <a name="question"></a>Fråga
Hur kan en organisation till att företagets säkerhetsprinciper är på plats?

### <a name="answer"></a>Svar
En organisation kan åstadkomma det genom att göra följande:

1. Utveckla och publicera en omfattande säkerhetsprincip. Principen tydliggör reglerna för godkänd användning som är associerade med den med hjälp av programvara, molntillgångar. Den definierar också vad tydligt bryter mot principen. 
2. Utveckla en anpassad avbildning, anpassade artefakter och en process som gör det möjligt orchestration inom området säkerhet som definieras med active directory. Den här metoden tillämpar företagsgräns och anger en gemensam uppsättning Miljökontroller. Dessa kontroller mot miljön utvecklare kan ha i åtanke när de utvecklar och följa en utvecklingslivscykel för säker som en del av deras övergripande processen. Målet är också att tillhandahålla en miljö som inte är mycket begränsad som kan hindra utveckling, men en rimlig uppsättning kontroller. Grupprinciper på organisationsenheten (OU) som innehåller virtuella datorer för testlabbet kan vara en delmängd av de totala grupprinciper som påträffas i produktion. De kan också vara en extra uppsättning korrekt hantera alla identifierade risker.

## <a name="data-integrity"></a>Dataintegritet

### <a name="question"></a>Fråga
Hur kan en organisation säkerställa dataintegriteten för att säkerställa att fjärrkommunikation utvecklare inte kan ta bort kod eller införa skadlig kod eller icke-godkända programvara?

### <a name="answer"></a>Svar
Det finns flera styrningslager kan minimera hotet från externa konsulter, leverantörer och anställda som är fjärrkommunikation i att samarbeta i DevTest Labs. 

Som tidigare nämnts bör måste det första steget ha en policy för acceptabel användning utformas och definieras som tydligt visar konsekvenserna när någon bryter mot principen. 

Det första lagret av kontroller för fjärråtkomst är att använda principer för fjärråtkomst via en VPN-anslutning som inte är direkt ansluten till labbet. 

Det andra lagret för kontroller är att använda en uppsättning grupprincipobjekt som kan förhindra att kopiera och klistra in via fjärrskrivbord. En princip kan implementeras för att inte tillåta utgående från miljön, till exempel FTP och RDP-tjänsterna från miljön. Användardefinierad routning kan tvinga all Azure nätverkstrafik tillbaka till den lokala, men routning kunde inte hänsyn till alla URL: er som gör överföring av data, såvida inte kontrolleras via en proxy söker igenom innehållet och sessioner. Offentliga IP-adresser kan vara begränsade i det virtuella nätverket som stöd för DevTest Labs för att inte tillåta bryggning för en resurs i externt nätverk.

Slutligen måste samma typ av begränsningar tillämpas i organisationen, vilket skulle behöva även hänsyn till alla möjliga metoder för flyttbara medier eller externa URL: er som kunde acceptera ett inlägg av innehåll. Kontakta din professionella att granska och implementera en säkerhetsprincip för säkerhet. Fler rekommendationer finns i [Microsoft Cyberhot Security](https://www.microsoft.com/security/default.aspx?&WT.srch=1&wt.mc_id=AID623240_SEM_sNYnsZDs).


## <a name="next-steps"></a>Nästa steg
Se [programmigreringen och integrering](devtest-lab-guidance-governance-application-migration-integration.md).
