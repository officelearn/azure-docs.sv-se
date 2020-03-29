---
title: Populära scenarier för att använda Azure DevTest Labs
description: Den här artikeln innehåller de primära scenarierna för att använda Azure DevTest Labs och två allmänna sökvägar för att börja använda tjänsten i din organisation.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60773816"
---
# <a name="popular-scenarios-for-using-azure-devtest-labs"></a>Populära scenarier för att använda Azure DevTest Labs
Beroende på behoven hos ett företag kan DevTest Labs konfigureras för att uppfylla olika krav.  I den här artikeln beskrivs de populära scenarierna. Varje scenario täcker fördelar som medförs genom att använda DevTest Labs och resurser som ska användas för att implementera dessa scenarier.  

- Stationära utvecklare
- Testmiljöer
- Träningspass, praktiska övningar och hackathons
- Sandboxade utredningar
- Klassrumslabb

## <a name="developer-desktops"></a>Stationära utvecklare
Utvecklare har ofta olika krav på utvecklingsmaskiner för olika projekt. Med DevTest Labs kan utvecklare få åtkomst till virtuella datorer på begäran som är konfigurerade för att passa deras vanligaste scenarier. DevTest Labs ger följande fördelar:

- Organisationer kan tillhandahålla gemensamma utvecklingsdatorer som säkerställer konsekvens mellan teamen.
- Utvecklare kan snabbt etablera sina utvecklingsdatorer på begäran eller göra anspråk på [en befintlig befintlig konfigurerad dator](devtest-lab-add-claimable-vm.md).
- Utvecklare kan etablera resurser på ett självbetjäningssätt utan att behöva behörighet på prenumerationsnivå.
- IT eller administratörer kan [fördefiniera nätverkstopologin](devtest-lab-configure-vnet.md) och utvecklare kan direkt använda den på ett enkelt och intuitivt sätt utan att kräva någon särskild åtkomst.
- Utvecklare kan enkelt [anpassa](devtest-lab-add-vm.md#add-an-existing-artifact-to-a-vm) sina utvecklingsmaskiner efter behov.
- Administratörer kan kontrollera kostnaderna genom att se till att:
    - Utvecklare [kan inte få fler virtuella datorer](devtest-lab-set-lab-policy.md#set-virtual-machines-per-user) än de behöver för utveckling
    - [Virtuella datorer stängs av](devtest-lab-set-lab-policy.md#set-auto-shutdown) när de inte används
    - Tillåter endast [en delmängd av vm-instansstorlekar](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) för de specifika labben
    - [Hantera kostnadsmål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Mer information finns i [Använda Azure DevTest Labs för utvecklare](devtest-lab-developer-lab.md). 

## <a name="test-environments"></a>Testmiljöer
Att skapa och hantera testmiljöer i ett företag kan kräva en betydande ansträngning. Med DevTest Labs kan testmiljöer enkelt skapas, uppdateras eller dupliceras. Det gör det möjligt för team att komma åt en fullt konfigurerad miljö när det behövs. I det här fallet ger DevTest Labs följande fördelar:

- Organisationer kan tillhandahålla vanliga testmiljöer som säkerställer konsekvens mellan teamen.
- Testare kan testa den senaste versionen av sitt program genom att snabbt etablera Windows- och Linux-miljöer med hjälp av återanvändbara mallar.
- Administratörer kan ansluta labbet till Azure DevOps för att aktivera DevOps-scenarier
- Labbägare kan kontrollera kostnaderna genom att se till att:
    - [Virtuella datorer i miljöer stängs av](devtest-lab-set-lab-policy.md#set-auto-shutdown) när de inte används
    - Tillåter endast [en delmängd av vm-instansstorlekar för](devtest-lab-set-lab-policy.md#set-allowed-virtual-machine-sizes) de specifika labben
    - [Hantera kostnadsmål och meddelanden](devtest-lab-configure-cost-management.md) för varje labb.

Mer information finns i [Använda Azure DevTest Labs för VM- och PaaS-testmiljöer](devtest-lab-test-env.md).

## <a name="sandboxed-investigations"></a>Sandboxade utredningar
Utvecklare undersöker ofta olika tekniker eller infrastrukturdesign. Som standard skapas alla miljöer som skapas med DevTest Labs i en egen resursgrupp. DevTest Labs-användaren får bara läsbehörighet till dessa resurser. Men för utvecklare som behöver mer kontroll kan en lab-wide-inställning uppdateras för att ge [deltagarrättigheter](https://azure.microsoft.com/updates/azure-devtest-labs-view-and-set-access-rights-to-an-environment-rg/) till den ursprungliga DevTest Labs-användaren för varje miljö de skapar.  Med DevTest Labs kan utvecklare automatiskt ges bidragsbehörighet till miljöer som de skapar i labbet.  Det här scenariot gör det möjligt för utvecklare att lägga till och/eller ändra Azure-resurser som de behöver för sina utvecklings- eller testmiljöer. [Med sidan Kostnad per resurs](devtest-lab-configure-cost-management.md#view-cost-by-resource) kan labbägare spåra kostnaden för varje miljö som används för undersökningar.

Mer information finns i [Ange åtkomsträttigheter till en miljöresursgrupp](https://aka.ms/dtl-sandbox).

## <a name="trainings-hands-on-labs-and-hackathons"></a>Utbildningar, praktiska övningar och hackathons 
Ett labb i Azure DevTest Labs fungerar som en bra behållare för tillfälliga aktiviteter som workshops, praktiska övningar, utbildningar eller hackathons.  Med tjänsten kan du skapa ett labb där du kan tillhandahålla anpassade mallar som varje praktikant kan använda för att skapa identiska och isolerade miljöer för utbildning. I det här fallet ger DevTest Labs följande fördelar:

- [Principer](devtest-lab-set-lab-policy.md) säkerställer att praktikanter bara får det antal resurser, till exempel virtuella datorer, som de behöver.
- Förkonfigurerade och skapade maskiner [begärs](devtest-lab-add-claimable-vm.md) med en åtgärd från praktikanten.
- Labb delas med praktikanter genom att komma åt [URL för labbet](devtest-lab-faq.md#how-do-i-share-a-direct-link-to-my-lab).
- [Förfallodatum](devtest-lab-add-vm.md#steps-to-add-a-vm-to-a-lab-in-azure-devtest-labs) på virtuella datorer säkerställer att datorer tas bort när de inte längre behövs.
- Det är enkelt att [ta bort ett labb](devtest-lab-delete-lab-vm.md#delete-a-lab) och alla relaterade [resurser](devtest-lab-faq.md#how-do-i-automate-the-process-of-deleting-all-the-vms-in-my-lab) när utbildningen är över.

Mer information finns i [Använda Azure DevTest Labs för utbildning](devtest-lab-training-lab.md).  

## <a name="proof-of-concept-vs-scaled-deployment"></a>Proof of concept vs skalad distribution
När du bestämmer dig för att utforska DevTest Labs finns det två allmänna vägar framåt: Proof of Concept vs Scaled Deployment.  

En **skalad distribution** består av veckor/månader för granskning och planering med avsikt att distribuera DevTest Labs till hela företaget som har hundratals eller tusentals utvecklare.

Ett **proof of concept-distribution** fokuserar på en koncentrerad insats från ett enda team för att fastställa organisationsvärde. Även om det kan vara frestande att tänka på en skalad distribution, tenderar metoden att misslyckas oftare än proof of concept-alternativet. Därför rekommenderar vi att du börjar i liten skala, lär dig från det första teamet, upprepar samma metod med två till tre ytterligare team och sedan planerar för en skalad distribution baserat på den kunskap som vunnits. För ett framgångsrikt proof of concept rekommenderar vi att du väljer ett eller två team och identifierar deras scenarier (dev environment vs testmiljöer), dokumenterar deras aktuella användningsfall och distribuerar DevTest Labs.

## <a name="next-steps"></a>Nästa steg
Läs följande artiklar:

- [DevTest Labs-koncept](devtest-lab-concepts.md)
- [Vanliga frågor och svar om DevTest Labs](devtest-lab-faq.md)

