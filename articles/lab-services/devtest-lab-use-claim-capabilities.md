---
title: Använd anspråksfunktioner i Azure DevTest Labs | Microsoft-dokument
description: Lär dig mer om olika scenarier för att använda anspråks-/unclaim-funktioner i Azure DevTest Labs
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2019
ms.author: spelluru
ms.openlocfilehash: a15148260bccadc59966c86031100f0e0332b0f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67861435"
---
# <a name="use-claim-capabilities-in-azure-devtest-labs"></a>Använda anspråksfunktioner i Azure DevTest Labs
Azure DevTest Labs-tjänsten förbättrar effektiviteten och effektiviteten hos utvecklare och testare. Den här artikeln fokuserar på möjligheten att göra anspråk på eller ta ut virtuella datorer i Azure DevTest Labs. Den listar också olika sätt att den här funktionen förbättrar användarupplevelsen. Innan du tittar på olika scenarier där den här funktionen kan användas, låt oss titta på vad **som påstås** vara och hur det fungerar.

## <a name="claimable-machines"></a>Maskiner som kan hämtas
En anspråksbar dator är en virtuell dator (VM) som skapas i ett labb utan en ägare. När datorn har begärts har användaren ett komplett utbud av alternativ för den virtuella datorn. När en användare gör anspråk på en dator görs några ändringar. Den virtuella datorn flyttas från listan **Anspråksbara virtuella datorer** till listan **Mina virtuella datorer** i Azure-portalen. 

Användaren kan ansluta till den virtuella datorn, anpassa artefakter, starta om, stoppa eller ta ut datorn. Det finns ett par sätt att göra en virtuell dator anspråksbar:

- Skapa en maskin och ta bort den så att den flyttas till den anspråksbara poolen. 
- Skapa en virtuell dator och placera den delade poolen med [avancerade inställningar](https://azure.microsoft.com/updates/azure-devtest-labs-claim-lab-vms-from-a-shared-pool/).

Det finns två fall där anspråks-/anspråksfunktionerna kan användas effektivt. Det första fallet kräver mer förtänksamhet och planering, att utformas och utföras på rätt sätt. Och den andra är mer situationsanlig. Följande är några exempel på de olika fallen.

## <a name="designed-use-of-claimable-machines"></a>Utformad användning av maskiner som kan hävdas

- **Mjukvaruutveckling / testning:** Tillåt utvecklare eller testare att vara mer produktiva genom att ha konfigurerade datorer redo och i ett outlöst tillstånd. Med en uppsättning virtuella datorer med olika konfigurationer, nödvändiga verktyg och med den senaste koden tillåter användare att göra anspråk på en virtuell dator och börja arbeta utan att behöva spendera tid att ställa in en dator. Innan de virtuella datorerna begärs etableras datorerna men avstängningsminimerar kostnaden för att ha datorer som används mindre ofta. När de virtuella datorerna behövs gör en användare helt enkelt anspråk på den virtuella datorn, som startar datorn. Unclaim alternativet är inte lika användbart i det här fallet eftersom skapa en ny virtuell dator är ofta enklare och billigare.
- **Klassrum/labb:** Ha virtuella datorer förkonfigurerade för en klass eller ett labb så att eleverna omedelbart kan ansluta till en dator med Hjälp av Azure-portalen.  När en elev har gjort anspråk på en virtuell dator säkerställer labbet att ingen kan göra anspråk på samma dator. Automatisera denna process säkerställer att det erforderliga antalet datorer med den angivna miljön är tillgängliga. Om deltagarna inte dyker upp eller är försenade kan de ej utlösta datorerna hållas tillgängliga tills sessionen är över med minimal kostnad. Alternativet unclaim är inte lika effektivt i det här scenariot eftersom den virtuella datorn är i ett okänt tillstånd när den tidigare användaren är klar.
- **Demonstrationer:** Använd maskiner för demonstrationer, där maskinerna i labbet ställs in med specifika miljöer. Den här funktionen är användbar om flera personer kan ge en demonstration samtidigt eller slumpmässiga tider, till exempel vid en konferens. Alternativet unclaim kan vara användbart i den här situationen eftersom demon inte bör ändra maskinens tillstånd, så att användarna kan returnera en virtuell dator tillbaka till den anspråksbara poolen för nästa demonstration. Med den outtagna datorn som avetablerades och medför minimal kostnad kan virtuella datorer lämnas kvar i labbet under längre tidsperioder.
- **Tillfälligt anställda/kontraktsanställda:** Tillåt användare att använda en dator. När de lämnar, returnerar de den virtuella datorn till den anspråksbara poolen utan förlust av data. Med den virtuella datorn outtagna, en annan användare kan göra anspråk på den virtuella datorn och fortsätta eller granska datorn för ytterligare information.
- **I allmänhet:** Möjligheten att ha en enda källa automatiskt konfigurera och distribuera virtuella datorer, på en viss kadens, är användbar i många olika situationer. Det finns flera olika situationer där funktionen anspråk/unclaim hjälper användarna att bli mer effektiva genom att ha en automatiserad process för att skapa de virtuella datorerna i ett outlöst tillstånd med en uppsättning konfiguration. Konfigurationerna kan innehålla olika operativsystem, språk, diskar eller [annan programvara (artefakter)](devtest-lab-artifact-author.md) beroende på dina behov. Möjligheten att göra anspråk på en virtuell dator från labbet gör det möjligt för labbanvändaren att få ett korrekt konfigurerat system utan att spendera tid eller ansträngning på att konfigurera datorn. Labbhanteraren kan använda det påstådda tillståndet för de virtuella datorerna för att förbättra antalet datorer som genereras, rensa datorer och bestämma prioriteten för konfigurationer. [Image fabriken](image-factory-create.md) är ett bra exempel på en automatiserad process för att bygga virtuella datorer och avbildningar för flera laboratorier. Skripten kan ändras för att köra någon av följande situationer med lämpliga ändringar eller användas som referens för att skapa ett anpassat system.

## <a name="situational-use-of-claimable-machines"></a>Situationsanvändning av maskiner som kan hävdas

- Använd funktionen för anspråk/anspråk som gör att användare kan skicka kontrollen över datorer från en till en annan och inte behöva veta uttryckligen vem som kommer att plocka upp maskinen härnäst.
- Utveckling, testning och felsökning av ett scenario där en specifik datorkonfiguration kan återskapa ett fel då datorn kan tas ut så att en annan utvecklare kan göra anspråk på maskinen och fortsätta arbetet. Den här funktionen är särskilt användbar eftersom fler människor arbetar på distans i olika delar av världen. 
- Gruppmedlemmar kan arbeta med en enda miljö. Du kan till exempel manuellt ställa in en komplex miljö som inte kan automatiseras eller skapa resurser som bara kan hantera ändringar för en enda indata som bilder. Tidigare har detta problem hanterats genom att ha en särskild maskin igång. Den anspråksbara funktionen är en förbättring jämfört med den manuella processen genom att ha inbyggd användaråtkomstkontroll och visuell identifiering när den är tillgänglig. När den virtuella datorn inte har etablerats avstÃ¤nteras den för att minska kostnaderna.
- Ha en datadisk som är kopplad till en virtuell dator. Varje disk upp till ~ 1 TB data gör att en stor mängd data kan skickas utan att behöva kopiera eller duplicera data. Den virtuella datorn skulle skapas ursprungligen med en ansluten disk som hade den stora mängden data.  Alla användare kan sedan göra anspråk på datorn och komma åt data. När du är klar, unclaim den virtuella datorn för att tillåta andra användare till maskinen.

Det finns några förbehåll att använda claimable maskiner, oftast runt att få tillgång till maskinen. Om datorn är domänansluten måste användaren som gör anspråk på datorn redan ha beviljats åtkomst, vanligtvis görs det genom att bevilja åtkomst till en grupp som omfattar alla användare i labbet när den virtuella datorn skapas. Om datorn inte är domänansluten måste **anaktefakten För återställning av VM-lösenord** i den offentliga databasen köras för att användaren ska kunna läggas till som administratör.  Artefakter kan tillämpas även efter att maskinen har startats eller begärts.

## <a name="next-steps"></a>Nästa steg
Se följande artikel: [Skapa och hantera anspråksbara virtuella datorer i Azure DevTest Labs](devtest-lab-add-claimable-vm.md)
