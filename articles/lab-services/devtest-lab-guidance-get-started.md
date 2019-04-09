---
title: Populära scenarier för att använda Azure DevTest Labs
description: Den här artikeln innehåller de primära scenarierna med Azure DevTest Labs och två allmänna sökvägar för att börja använda tjänsten i din organisation.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/01/2019
ms.author: spelluru
ms.reviewer: christianreddington,anthdela,juselph
ms.openlocfilehash: 8736ba4c24ac4c8f8d84345028d1cadfdef38697
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59272398"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populära scenarier för att använda Azure DevTest Labs
Beroende på behoven hos företaget, kan DevTest Labs konfigureras för att uppfylla olika krav.  Den här artikeln beskrivs de populära scenarierna. Varje scenario beskriver fördelar som gav oss genom att använda DevTest Labs och resurser du använder för att implementera dessa scenarier.  

- Developer-skrivbord
- Miljöer för testning
- Utbildningssessioner, praktiska labbövningar och då hackathon-evenemang
- I begränsat läge undersökningar
- Klassrumslabb

## <a name="developer-desktops"></a>Developer-skrivbord
Utvecklare har ofta olika krav för utveckling av datorer för olika projekt. Med DevTest Labs kan utvecklare har tillgång till virtuella datorer på begäran som har konfigurerats så att den passar deras vanligaste scenarierna. DevTest Labs ger följande fördelar:

- Organisationer kan tillhandahålla vanliga utveckling datorer att säkerställa konsekvens mellan olika grupper.
- Utvecklare kan snabbt etablera sina utveckling datorer på begäran eller [gör anspråk på en befintlig förkonfigurerade dator](devtest-lab-add-claimable-vm.md).
- Utvecklare kan etablera resurser på ett sätt som självbetjäning utan att behöva prenumerationsnivå behörigheter.
- IT eller administratörer kan [fördefiniera nätverkstopologin](devtest-lab-configure-vnet.md) och utvecklare kan använda direkt det på ett enkelt och intuitivt sätt utan att någon särskild behörighet.
- Utvecklare kan enkelt [anpassa](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) deras utveckling datorer efter behov.
- Administratörer kan styra kostnaderna genom att säkerställa att:
    - Utvecklare [kan inte hämta fler virtuella datorer](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) än de behöver för utveckling
    - [Virtuella datorer är avstängda](devtest-lab-set-lab-policy.md#set-auto-shutdown) när den inte används
    - Endast [så att en delmängd av Virtuella datorstorlekar](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) för specifika labb
    - [Hantera kostnaden mål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Läs mer, se [Använd Azure DevTest Labs för utvecklare](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Miljöer för testning
Skapa och hantera miljöer för testning i ett företag kan kräva mycket ansträngning. Med DevTest Labs kan testmiljöer enkelt skapas, uppdateras, eller en dubblett. Det kan grupper åtkomst till en fullständigt konfigurerad miljö när det behövs. I det här scenariot ger DevTest Labs följande fördelar:

- Organisationer kan tillhandahålla vanliga testmiljöer för att säkerställa konsekvens mellan olika grupper.
- Testare kan testa den senaste versionen av sitt program genom att snabbt etablera Windows och Linux-miljöer med återanvändningsbara mallar.
- Administratörer kan ansluta labbet till Azure DevOps för att aktivera DevOps-scenarier
- Labbägare kan styra kostnaderna genom att säkerställa att:
    - [Virtuella datorer i miljöer är stänga](devtest-lab-set-lab-policy.md#set-auto-shutdown) när den inte används
    - Endast [så att en delmängd av Virtuella datorstorlekar för](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) specifika labs
    - [Hantera kostnaden mål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Mer information finns i [Använd Azure DevTest Labs för virtuella datorer och PaaS testmiljöer](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>I begränsat läge undersökningar
Utvecklare undersöka ofta olika tekniker eller infrastrukturdesign. Som standard skapas alla miljöer som skapats med DevTest Labs i sin egen resursgrupp. DevTest Labs-användare får bara läsbehörighet till dessa resurser. Men för utvecklare som behöver mer kontroll, en inställning för hela labb kan uppdateras för att ge [bidragsgivarbehörighet](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) till den ursprungliga DevTest Labs-användaren för varje miljö som de skapar.  Med DevTest Labs kan utvecklare få behörigheten deltagare automatiskt till miljöer som de skapar i labbet.  Det här scenariot gör att utvecklare kan lägga till och/eller ändra Azure-resurser som de behöver för sina miljöer för utveckling eller testning. Den [kostnad per resurs](devtest-lab-configure-cost-management.md#view-cost-by-resource) sidan kan Labbägare att spåra kostnaden för varje miljö som ska användas för undersökningar.

Mer information finns i [Set åtkomsträttigheter till en resursgrupp för miljön](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Utbildningar, praktiska labbövningar och då hackathon-evenemang 
Ett labb i Azure DevTest Labs fungerar som en bra behållare för tillfälliga aktiviteter som workshops, praktiska labbövningar, utbildningar eller då hackathon-evenemang.  Tjänsten kan du skapa ett labb där du kan ange anpassade mallar som varje praktikanten kan använda för att skapa identiska och isolerade miljöer för utbildning. I det här scenariot ger DevTest Labs följande fördelar:

- [Principer](devtest-lab-set-lab-policy.md) eleverna får endast antalet resurser, till exempel virtuella datorer som de behöver.
- Datorer som redan konfigurerats och skapats [ägs](devtest-lab-add-claimable-vm.md) med en enda åtgärd från äga rum.
- Labs delas med eleverna genom att gå till [URL: en för labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Förfallodatum för](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) på virtuella datorer ser du till att datorerna tas bort när de inte längre behövs.
- Det är enkelt att [ta bort ett labb](devtest-lab-delete-lab-vm.md#delete-a-lab) och alla [relaterade resurser](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) när utbildningen är över.

Mer information finns i [Använd Azure DevTest Labs för träning](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Konceptbevis kontra skalade distribution
När du vill utforska DevTest Labs, finns det två allmänna framåt: Bevis på koncept vs skala distributionen.  

En **skalas distribution** består av veckor eller månader för att granska och planera med syftet för DevTest Labs till hela företaget med hundratals eller tusentals utvecklare.

En **funktionstest av** distribution fokuserar på en topp arbete från en grupp för att fastställa organisationens-värde. Det kan vara lockande att tänka på en skalade distribution, tenderar metoden misslyckas oftare än proof of concept-alternativet. Därför rekommenderar vi att du börja i liten skala, Lär dig från första-teamet, upprepa samma sak med två till tre fler lag och sedan planera för en skalade distribution baserad på den kunskap som. En lyckad konceptbevis rekommenderar vi att du väljer en eller två grupper, och identifiera deras scenarier (dev miljö vs testmiljöer), dokumentera sina aktuella användningsfall och distribuerar DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [DevTest Labs-koncept](devtest-lab-concepts.md)
- [Vanliga frågor om DevTest Labs](devtest-lab-faq.md)

