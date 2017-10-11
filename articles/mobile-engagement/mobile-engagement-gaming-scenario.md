---
title: "Azure Mobile Engagement-implementering för Spelappen"
description: Spel app scenario om du vill implementera Azure Mobile Engagement
services: mobile-engagement
documentationcenter: mobile
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2cafc044-4902-4058-8037-49399bf6bf7f
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0ca35a3d634db8eb5c63afacba046a35b8a3e7ed
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="implement-mobile-engagement-with-gaming-app"></a>Implementerar Mobile Engagement med Spelappen
## <a name="overview"></a>Översikt
En spel Startup har startas en ny fiske baserat role play/strategi spel app. Spelet har varit igång i sex månader. Spelet är mycket stora lyckas, och den har miljontals hämtningar och kvarhållning är mycket högt jämfört med andra uppstart spel appar. Möte kvartalsvis granska accepterar intressenter de behöver för att öka genomsnittliga intäkter per användare (ARPU). Premium i spelet paket är tillgängliga som specialerbjudanden. Dessa spel Pack kan du uppgradera utseende och prestanda på deras fiske rader och bete eller tackles i spelet. Paketet försäljning är dock mycket låg. Därför vill först analysera kundupplevelsen med ett webbanalysverktyg för och sedan för att utveckla ett uppdrag att öka försäljning med avancerade segmentering.

Baserat på de [Azure Mobile Engagement – komma igång med bästa praxis](mobile-engagement-getting-started-best-practices.md) de skapar en strategi.

## <a name="objectives-and-kpis"></a>Mål och KPI: er
Viktiga intressenter för spelet uppfylla. Komma överens om en Huvudsyftet - att öka försäljningen för premium-paket med 15%. De skapar (Business Key Performance Indicator) som mäter och enhet för detta mål

* På vilken nivå av spelet köps paketen?
* Vad är intäkter per användare, sessioner, per vecka och per månad?
* Vilka är typerna favorit inköp?

Del 1 av den [komma igång](mobile-engagement-getting-started-best-practices.md) förklarar hur du definierar mål och KPI: er. 

Med affärsverksamhet nu definierat, skapar produkten-hanteraren Mobile Engagement KPI: er för att fastställa den nya användaren trender och lagring.

* Övervaka kvarhållning och använda på följande intervall: varje dag, varje 2 dagar, varje vecka, månad och var tredje månad
* Aktiva användare
* Appklassificering i arkivet

Baserat på rekommendationer från IT-teamet har följande tekniska KPI: er lagts till i besvara följande frågor:

* Vad är Mina användare sökväg (vilken sida besökta, hur mycket tid användare spendera på den)
* Antal krascher eller buggar påträffade per session
* Vilka operativsystemversioner körs Mina användare?
* Vad är den genomsnittliga storleken på skärmen för Mina användare?
* Vilken typ av internet-anslutning har Mina användare?

För varje KPI anger Mobile produkten Manager data hon och var den finns i sitt playbook.

## <a name="engagement-program-and-integration"></a>Program för användarinteraktion och integrering
Innan du skapar ett program för avancerade användarinteraktion bör Mobile projektet Director ansvarig för projektet ha en djupgående förståelse av hur och när produkter förbrukas av användare.

Mobila projekt Director har samlat in tillräckligt med data för att förbättra sin app push notification försäljning efter tre månader. Han Lär som:

* Det första inköpet sker vanligtvis på nivån 14. Köpet är 90% av de fall nya legendariska vapen för $3.
* Användare som har gjort ett inköp fortsätta med produkten och göra mer inköp i 80% av de fallen.
* Användare som har klarat nivån 20, starta ägna mer än 10 $/ vecka.
* Tenderar användare att köpa premium-paket på nivå 16, 24 och 32.

Tack vare den här analysen Mobile projektet Director bestämmer sig för att skapa vissa push notification-sekvenser ökar i appen försäljning. Han skapar tre push-sekvenser som han kallar: Välkommen program, försäljning Program och inaktiva Program. Mer information finns i den [Playbooks](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
