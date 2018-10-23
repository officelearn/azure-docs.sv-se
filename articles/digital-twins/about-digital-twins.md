---
title: Översikt över Azure Digital Twins | Microsoft Docs
description: Läs mer om Azure Digital Twins, en Azure IoT-lösning för spatial intelligens.
author: julieseto
ms.author: jseto
ms.date: 10/10/2018
ms.topic: overview
ms.service: digital-twins
services: digital-twins
manager: bertvanhoof
ms.custom: mvc
ms.openlocfilehash: 85b67683730c8352051b4d6b48f813576b11615b
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49322946"
---
# <a name="overview-of-azure-digital-twins"></a>Översikt över Azure Digital Twins

Azure Digital Twins är en Azure IoT-tjänst som skapar omfattande modeller av den fysiska miljön. Bland dess många funktioner finns möjligheten att skapa diagram för spatial intelligens för att modellera relationer och interaktioner mellan människor, utrymmen och enheter.

Azure Digital Twins gör det möjligt att köra frågor mot data från ett fysiskt utrymme i stället för ett stort antal skilda sensorer. Den här tjänsten hjälper dig att skapa återanvändbara, mycket skalbara, spatialt medvetna upplevelser som länkar strömmande data över hela den digitala och den fysiska världen. Oavsett om du förutser underhållsbehov för en fabrik, analyserar energikrav för ett elnätet i realtid eller optimerar användningen av tillgängligt kontorsutrymme förbättras dina appar med dessa unikt relevanta, kontextuella funktioner.

Azure Digital Twins passar alla typer av miljöer, till exempel lagerlokaler, kontor, skolor, sjukhus, banker, idrottsarenor, fabriker, parkeringsplatser, parker, smarta rutnät och städer. Tänk dig scenarier som att spåra daglig temperatur över flera regioner, övervaka drönarbanor med mycket trafik, identifiera autonoma fordon, analysera användningsnivåer för en byggnad eller hitta den mest upptagna kassan i butiken. Vad ditt verkliga affärsscenario än är går det att etablera en motsvarande digital instans via Azure Digital Twins.

Följande videoklipp går in närmare på Digital Twins:

> [!VIDEO https://www.youtube.com/embed/TvN_NxpgyzQ]

## <a name="key-capabilities"></a>De viktigaste funktionerna

De viktigaste funktionerna i Azure Digital Twins är:

### <a name="spatial-intelligence-graph"></a>Diagram för spatial intelligens

[*Diagrammet för spatial intelligens*](./concepts-objectmodel-spatialgraph.md), eller *det spatiala diagrammet*, är en virtuell representation av den fysiska miljön där du kan modellera relationerna mellan personer, platser och enheter.

Tänk dig en smart verktygsapp som hanterar flera elmätare som är anslutna i ett stadsområde. För att kunna övervaka och förutse användning av och debitering för elektricitet på rätt sätt måste tillverkaren av det smarta verktyget modellera varje enhet med kontext om den plats och den kund som ska debiteras. Med diagrammet för spatial intelligens kan du modellera de här typerna av komplexa relationer.

### <a name="digital-twin-object-models"></a>Digitala tvillingobjektmodeller

[Digitala tvillingobjektmodeller](./concepts-objectmodel-spatialgraph.md) är fördefinierade enhetsprotokoll och datascheman som justerar din lösnings domänspecifika behov för att påskynda och förenkla utvecklingen.

Till exempel skulle ett program för rumsanvändning kunna använda fördefinierade utrymmestyper såsom campus, våning, rum osv.

### <a name="multiple-and-nested-tenants"></a>Flera och kapslade klientorganisationer

Du kan skapa lösningar som skalar på ett säkert sätt och som kan återanvändas för flera klientorganisationer. Du kan även skapa flera underklientorganisationer som kan kommas åt och användas på ett isolerat och säkert sätt.

Till exempel skulle en app för utrymmesanvändning kunna konfigureras till att isolera en klientorganisations data från andra klientorganisationers data i en och samma byggnad, eller kombinera data för en enskild klientorganisation med flera byggnader.

### <a name="advanced-compute-capabilities"></a>Avancerade funktioner för databearbetning

Avancerade funktioner för databearbetning som kallas [användardefinierade funktioner](./concepts-user-defined-functions.md) gör att du kan definiera och köra anpassade funktioner mot inkommande [enhetsdata](./concepts-device-ingress.md) för att skicka signaler till fördefinierade slutpunkter. Den här funktionen förbättrar anpassning och automatisering av enhetsuppgifter.

Till exempel skulle ett smart jordbruksprogram kunna omfatta en användardefinierad funktion för att utvärdera mätningar från jordfuktssensorer samt väderprognoser och sedan skicka signaler om bevattningsbehov.

### <a name="built-in-access-control"></a>Inbyggd åtkomstkontroll

Med åtkomst-och identitetshanteringsfunktioner som [rollbaserad åtkomstkontroll](./security-role-based-access-control.md) och [Azure Active Directory](./security-authenticating-apis.md) kan du på ett säkert sätt styra personers och enheters åtkomst.

Till exempel skulle en app för anläggningshantering kunna konfigureras att tillåta att personer som använder ett rum kan ställa in temperaturen inom ett angivet intervall och att anläggningschefer kan ställa in temperaturen i vilket rum som helst till valfritt värde.

### <a name="ecosystem"></a>Ekosystem

Du kan ansluta en Azure Digital Twins-instans till många kraftfulla Azure-tjänster såsom: Azure Stream Analytics, AI- och Storage-tjänster samt Azure Maps, Microsoft Mixed Reality, Dynamics 365 eller Office 365.

Till exempel skulle ett program för en smart kontorsbyggnad kunna använda Azure Digital Twins för att representera team och enheter som finns på många våningar. När enheter strömmar livedata till den etablerade Digital Twin-instansen kan Azure Stream Analytics bearbeta dessa data för att ge viktiga, användbara insikter. Sedan kan data lagras i Azure Storage och konverteras till ett delbart filformat för distribution över hela organisationen med hjälp av Office 365.

## <a name="solutions-that-benefit-from-azure-digital-twins"></a>Lösningar som har nytta av Azure Digital Twins

Azure Digital Twins är användbart för att representera den fysiska världen och dess många relationer eftersom det förenklar IoT-modellering, databearbetning, händelsehantering och enhetsspårning. Nedan följer några av de scenarier i olika branscher som har nytta av dess användning:

* Visa fastighetsbolag användningsnivåerna för ett utrymme över tid för att få insikter om de bästa sätten att konfigurera företagets kontorsbyggnad.
* Utlösa arbetsorderärenden för en mobilapp där det går att utlösa vaktronder, schemalägga vaktmästaruppgifter och andra tjänster i butiksmiljö eller vid sportevenemang.
* Visa personer vilka rum som används i en byggnad i realtid. Hjälpa dessa personer att boka arbetsytor som passar deras behov.
* Spåra var tillgångar finns inom ett utrymme.
* Optimera laddning av elfordon genom att modellera användarpreferenser och begränsningar i elnätet.

## <a name="azure-digital-twins-in-the-context-of-other-iot-services"></a>Azure Digital Twins i kontexten för andra IoT-tjänster

Azure Digital Twins använder Azure IoT Hub för att ansluta IoT-enheter och -sensorer som håller allt uppdaterat med den fysiska världen. Följande diagram visar hur Azure Digital Twins relaterar till andra Azure IoT-tjänster:

![Azure Digital Twins är en tjänst som bygger på Azure IoT Hub](./media/overview/azure-digital-twins-in-iot-ecosystem.png)

En mer detaljerad beskrivning om resten av IoT-ekosystemet finns i [Azure IoT-tekniker och lösningar](https://docs.microsoft.com/azure/iot-fundamentals/iot-services-and-technologies).

## <a name="next-steps"></a>Nästa steg

Gå vidare till en kort demonstration av funktionerna i Azure Digital Twins:

> [!div class="nextstepaction"]
> [Snabbstart: Hitta tillgängliga rum med hjälp av Azure Digital Twins](./quickstart-view-occupancy-dotnet.md)

Gör en djupdykning i ett program för anläggningshantering med hjälp av Azure Digital Twins:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera Azure Digital Twins och konfigurera ett spatialt diagram](./tutorial-facilities-setup.md)

Läs mer om grundläggande Azure Digital Twins-begrepp:

> [!div class="nextstepaction"]
> [Förstå Digital Twins-objektmodellen och diagram för spatial intelligens](./concepts-objectmodel-spatialgraph.md)
