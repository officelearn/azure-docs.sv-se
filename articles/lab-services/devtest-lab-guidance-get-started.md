---
title: Kom igång med Azure DevTest Labs
description: Den här artikeln innehåller primära scnearios med Azure DevTest Labs och två allmänna sökvägar för att börja använda tjänsten i din organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: spelluru
ms.openlocfilehash: 87baef8ddb5b5d8fc979ba5afb9f9b13cb4fc2ef
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52876544"
---
# <a name="get-started-with-using-azure-devtest-labs"></a>Kom igång med Azure DevTest Labs
När du vill utforska DevTest Labs, det finns två allmänna framåt – Proof of Concept vs skala distributionen. 

En **skalas distribution** består av veckor eller månader för att granska och planera med syftet för DevTest Labs till hela företaget med hundratals eller tusentals utvecklare. 

En **bevis på koncept distribution** fokuserar på en topp arbete från en grupp för att fastställa organisationens-värde. Det kan vara lockande att tänka på en skalade distribution, tenderar metoden misslyckas oftare än proof of concept-alternativet. Därför rekommenderar vi att du börja i liten skala, Lär dig från första-teamet, upprepa samma sak med två till tre fler lag och sedan planera för en skalade distribution baserad på den kunskap som. En lyckad konceptbevis rekommenderar vi att du väljer en eller två grupper, och identifiera deras scenarier (dev miljö vs testmiljöer), dokumentera sina aktuella användningsfall och distribuerar DevTest Labs. 

## <a name="scenarios"></a>Scenarier
Det finns tre huvudsakliga scenarier för implementering av Labb: 

- Developer-skrivbord
- Miljöer för testning
- Labs/utbildning/hackathon

## <a name="developer-desktops"></a>Developer-skrivbord
Utvecklare har ofta olika krav för utveckling av datorer för olika projekt. Med DevTest Labs kan utvecklare har tillgång till virtuella datorer på begäran som är förkonfigurerade för att passa de vanligaste scenarierna. DevTest Labs ger följande fördelar:

- Organisationer kan tillhandahålla vanliga utvecklings- och testmiljöer för att säkerställa konsekvens mellan olika grupper
- Utvecklare kan snabbt etablera sina utveckling datorer på begäran
- Utvecklare kan etablera resurser på ett sätt som självbetjäning utan att behöva prenumerationsnivå behörigheter
- IT eller administratörer kan förväg definiera nätverkets topologi och utvecklare kan direkt kan utnyttja den på ett enkelt och intuitivt sätt utan att någon särskild åtkomst
- Utvecklare kan enkelt anpassa sina utveckling-datorer efter behov
- Administratörer kan styra kostnaderna genom att säkerställa att:
    - Utvecklare kan inte hämta flera virtuella datorer än de behöver för utveckling
    - Virtuella datorer är avstängda när inte är i användning
    - Endast tillåta en delmängd av Virtuella datorstorlekar för specifika arbetsbelastningar

## <a name="test-environments"></a>Miljöer för testning
Skapa och hantera miljöer för testning i ett företag kan kräva mycket ansträngning. Med DevTest Labs kan testmiljöer enkelt skapas, uppdateras, eller en dubblett, vilket gör att team för att komma åt en fullständigt konfigurerad miljö när det behövs. I det här scenariot ger DevTest Labs följande fördelar:

- Testare kan testa den senaste versionen av sitt program genom att snabbt etablera Windows och Linux-miljöer med återanvändningsbara mallar och artefakter.
- Testare kan skala upp sina belastningstester genom att etablera flera testagenter
- Administratörer kan ansluta labbet till Azure DevOps för att aktivera DevOps-scenarier
- Administratörer kan styra kostnaderna genom att säkerställa att:
    - Testare kan inte hämta flera virtuella datorer än de behöver
    - Virtuella datorer är avstängda när inte är i användning
    - Endast tillåta en delmängd av Virtuella datorstorlekar för specifika arbetsbelastningar

## <a name="labs-workshops-trainings-and-hackathons"></a>Labs, workshops, utbildningar och då hackathon-evenemang  
Ett labb i Azure DevTest Labs fungerar som en bra behållare för tillfälliga aktiviteter som workshops, praktiska labbövningar, utbildning eller då hackathon-evenemang. Tjänsten kan du skapa ett labb där du kan ange anpassade mallar som varje praktikanten kan använda för att skapa identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildning miljöer är tillgängliga för varje ges endast när de behöver dem och innehåller tillräckligt med resurser – till exempel virtuella datorer – krävs för utbildning. Slutligen kan du enkelt dela labbet med eleverna som de kan komma åt med ett klick. När klassen har avslutat, är det enkelt att ta bort labbet och alla relaterade resurser.


## <a name="next-steps"></a>Nästa steg
Se nästa artikel i den här serien: [skala upp distributionen DevTest Labs](devtest-lab-guidance-scale.md)
