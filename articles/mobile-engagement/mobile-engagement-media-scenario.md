---
title: Azure Mobile Engagement-implementering för Media App
description: Media app scenariot för att implementera Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 48201cc8-4e04-485c-a8dc-d6406d23f3ed
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 8e5d4585b47a4ca26b81b7168aa499f25722415a
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
---
# <a name="implement-mobile-engagement-with-media-app"></a>Implementerar Mobile Engagement med Media App
> [!IMPORTANT]
> Azure Mobile Engagement upphör på 3/31/2018. Den här sidan tas bort strax efter.
> 

## <a name="overview"></a>Översikt
John är mobila projektledare för ett stort media företag. Han startas nyligen en ny app som har en mycket hög hämtning. Han har nått sin mål för nedladdning men fortfarande hans returnera på Investment(ROI) per användare uppfyller inte sitt behov. 

John har redan identifierats av varför sin Räntabilitet är för lågt. Användare som ofta sluta använda sin app efter bara två veckor och de flesta gå aldrig tillbaka. Han vill öka kvarhållning av sin app.

När vissa inledande testning tenderar han har lärt dig när han snabbt tillkallar sina användare med push-meddelanden, de att fortsätta använda sitt appen. Användare som har inaktiv returneras ofta till appen beroende på meddelanden som han skickar dem också. John beslutar att investera i någon typ av Program för användarinteraktion för sin app som använder avancerade mål med push-meddelanden.

John har nyligen Läs den [Azure Mobile Engagement – komma igång med bästa praxis](mobile-engagement-getting-started-best-practices.md) och har valt att implementera rekommendationerna från guiden.

## <a name="objectives-and-kpis"></a>Mål och KPI: er
Viktiga intressenter för Johns appen uppfylla. Företag genereras från annonser som användarna använda sin media. John ökar sin intäkter genom att öka innehåll som används per användare. Komma överens om en Huvudsyftet: öka försäljningen från Active Directory med 25%. De skapar (Business Key Performance Indicator) som mäter och enhet för detta mål

* Antal annonsklickningar klickade per användare
* Hur många artikel sidor besökt (per användare / per session / per vecka / per månad...)
* Vad är favoritkategorier

Han har definierat sin KPI: er baserat på Johns möte med viktiga intressenter. Han följer del 1 av den [Azure Mobile Engagement – komma igång med bästa praxis](mobile-engagement-getting-started-best-practices.md). 

Han skapar sedan följande Engagement KPI: er för att säkerställa att målen uppnås:

* Övervaka kvarhållning över följande intervall: varje dag, varje vecka, varannan vecka och månad.
* Antal aktiva användare
* Appklassificering i appen lagrar

Baserat på rekommendationer från IT-teamet har följande tekniska KPI: er lagts till i besvara följande frågor:

* Vad är Mina användare sökväg (vilken sida besökta, hur många användare som tid spendera på den)
* Antal krascher eller buggar påträffade sessioner?
* Vilka operativsystemversioner körs Mina användare?
* Vad är den genomsnittliga storleken på skärmen för Mina användare?
* Vilken typ av internet-anslutningar har Mina användare?

Han klassificerar de data som krävs för varje KPI och han registrerar den i sin playbook rätt plats.

## <a name="engagement-program-and-integration"></a>Program för användarinteraktion och integrering
Nu definiera sin KPI: er som Johan har slutförts startar han fasen sin Engagement-strategi genom att definiera 4 program och sina mål: ![][1]

Sedan går John djupare med information om push-meddelanden för varje program. Push-meddelanden definieras av fem element:

1. Mål: Vad är målet för meddelandet
2. Hur målet kan nås
3. Mål: vem som ska få meddelandet?
4. Innehåll: Vad är ordval och format för meddelandet (i App/Out av App)
5. När: Vad är bästa tidpunkten att skicka push-meddelanden
   
    ![][2]

Mer information finns i den [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

Enligt del 2 av vitboken John använder målavsnittet för att definiera vilka data som han har att samla in och skriver hans taggen planera tillsammans med IT-teamet att implementera lösningen. Efter en vecka för genomförande och användargodkännande starta John slutligen sina program.

## <a name="program-results"></a>Programmet resultat
fyra månader senare John går igenom resultaten av program. Välkommen till programmet och programmet veckovisa uppfyller sina mål. Antal användare med endast en session minskar, funktionerna i appen används och dubblerat antalet anslutningar per vecka.

Den **inaktiva Program** hjälper John förstå användaren beteenden. Det verkar som 15% av inaktiva användare gå tillbaka till appen. Men de flesta av dem inte förblir aktiv mer än 1 månad. John förutser en potentiell optimering av denna sekvens med ytterligare meddelanden och expandera sitt innehåll val.

Den **identifiera programmet** inte fungerar väl. Den ökar mellan sälja men finns inte tillräckligt med att nå sina mål. John identifierar att han inte har tillräckligt med data så att relevant mål och föreslå rätt innehåll. Han stoppar programmet och fokuserar på Skicka ”redaktionell push-meddelanden” med Azure Mobile Engagement. Hans journalister har redan en CMS-lösning för att skicka push-meddelanden och de vill inte ändra.

John beslutar att nå-API: n som är en HTTP-REST-API som kan hantera Reach-kampanjer utan att använda AZME webbgränssnitt. John kan samla in data han måste och Tillåt hans skrivare för att fortsätta använda CMS-lösningen med den här metoden.

För att säkerställa att funktionen fungerar korrekt, ber John IT-teamet att vaksam om följande:

1. **Åtgärden system** : alla har sina egna regler för att administrera push-meddelanden så John bestämmer sig för att visa en lista med alla fall och kontrollerar om API: erna hantera den.
   T.ex.: Android push-system kan stor bild som inte är fallet med iOS.
2. **Tidsintervall**: John vill en API som tidsperioden och ett slut till kampanjer. Han vill bevara användare från någon störande meddelande bombing.
3. **Kategorier**: marknadsföring team förbereder mallen för varje typ av avisering. John frågar IT-teamet för att ange kategorier i API: et.

Efter vissa tester är John uppfyllda. Tack vare detta API kan journalister fortfarande skicka push-meddelanden med Azure Mobile Engagement och CMS samlar in alla beteendebaserade data för dem.

När dessa 4 först månader, resultaten avspeglar en bra övergripande prestanda och ger förtroende för John och hans board Räntabilitet per användare ökar per 15% och mobila försäljning representerar 17.5% av total försäljning en ökning av 7.5% bara fyra månader.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
