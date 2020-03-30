---
title: Förstå objektmodeller och spatial intelligence-graf - Azure Digital Twins | Microsoft-dokument
description: Använda Azure Digital Twins för att modellera relationer mellan personer, platser och enheter
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265212"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Förstå Digital Twins objektmodeller och spatial intelligens graf

Azure Digital Twins är en Azure IoT-tjänst som driver omfattande virtuella representationer av fysiska miljöer och associerade enheter, sensorer och personer. Det förbättrar utvecklingen genom att organisera domänspecifika begrepp i användbara modeller. Modellerna är sedan belägna i en rumslig intelligens graf. Sådana begrepp modellerar troget relationer och interaktioner mellan människor, utrymmen och enheter.

Digital Twins-objektmodeller beskriver domänspecifika begrepp, kategorier och egenskaper. Modeller är fördefinierade av användare som vill skräddarsy lösningen efter sina specifika behov. Tillsammans utgör dessa fördefinierade Digital Twins-objektmodeller en _ontologi._ En smart byggnads ontologi beskriver regioner, lokaler, golv, kontor, zoner, konferensrum och fokusrum. En energinäts ontologi beskriver olika kraftverk, transformatorstationer, energiresurser och kunder. Med Digital Twins objektmodeller och ontologier kan olika scenarier och behov anpassas.

Med Digital Twins objektmodeller och en ontologi på plats kan du fylla i ett _rumsligt diagram_. Rumsliga diagram är virtuella representationer av de många relationerna mellan mellanslag, enheter och personer som är relevanta för en IoT-lösning. Det här diagrammet visar ett exempel på ett rumsligt diagram som använder en smart byggnads ontologi.

[![Digital Twins rumsliga graf byggnad](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

Den rumsliga grafen samlar utrymmen, enheter, sensorer och användare. Var och en är sammanlänkade på ett sätt som modeller den verkliga världen. I detta urval har plats 43 fyra våningar, alla med många olika områden. Användare är associerade med sina arbetsstationer och får tillgång till delar av diagrammet. En administratör har rätt att göra ändringar i det rumsliga diagrammet, medan en besökare har behörighet att bara visa vissa byggdata.

## <a name="digital-twins-object-models"></a>Digital Twins objektmodeller

Digital Twins objektmodeller stöder dessa huvudkategorier av objekt:

- **Utrymmen** är virtuella eller fysiska `Tenant`platser, `Region`till `Venue`exempel , `Customer`, och .
- **Enheter** är virtuella eller fysiska delar `AwesomeCompany Device` av `Raspberry Pi 3`utrustning, till exempel, och .
- **Sensorer** är objekt som identifierar `AwesomeCompany Temperature Sensor` händelser, till exempel, och `AwesomeCompany Presence Sensor`.
- **Användare** identifierar passagerare och deras egenskaper.

Andra kategorier av objekt är:

- **Resurser** är kopplade till ett utrymme och representerar vanligtvis Azure-resurser som `IoTHub`ska användas av objekt i det rumsliga diagrammet, till exempel .
- **Blobbar** är kopplade till objekt (till exempel blanksteg, enheter, sensorer och användare). De används som filer med mime-typ och `maps` `pictures`metadata, `manuals`till exempel , och .
- **Utökade typer** är utökningsbara uppräkningar som förstärker enheter med specifika `SpaceType` `SpaceSubtype`egenskaper, till exempel och .
- **Ontologier** representerar en uppsättning utökade `Default`typer, `BACnet`till `EnergyGrid`exempel , `Building`, och .
- **Egenskapsnycklar och värden** är anpassade egenskaper för utrymmen, enheter, sensorer och användare. De kan användas tillsammans med inbyggda egenskaper, `DeltaProcessingRefreshTime` till `10` exempel som nyckel och som värde.
- **Roller** är behörighetsuppsättningar som tilldelats användare och enheter `Space Administrator`i `User Administrator`det `Device Administrator`rumsliga diagrammet, till exempel , och .
- **Rolltilldelningar** är kopplingen mellan en roll och ett objekt i det rumsliga diagrammet. En användare eller ett tjänsthuvudnamn kan till exempel beviljas behörighet att hantera ett utrymme i det rumsliga diagrammet.
- **Säkerhetsnyckellager** tillhandahåller säkerhetsnycklarna för alla enheter i hierarkin under ett givet utrymmesobjekt så att enheten kan kommunicera säkert med Digitala tvillingar.
- **Användardefinierade funktioner** (UDFs) tillåter anpassningsbar sensortelemetribearbetning i det rumsliga diagrammet. En UDF kan till exempel:
  - Ange ett sensorvärde.
  - Utför anpassad logik baserat på sensoravläsningar och ställ in utdata på ett blanksteg.
  - Koppla metadata till ett utrymme.
  - Skicka meddelanden när fördefinierade villkor är uppfyllda. För närvarande kan UDF:er skrivas i JavaScript.
- **Matchningar** är objekt som avgör vilka UDF:er som körs för ett visst telemetrimeddelande.
- **Slutpunkter** är de platser där telemetrimeddelanden och Digital Twins-händelser kan `Event Hub` `Service Bus`dirigeras, till exempel , och `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Diagram för spatial intelligens

Spatial graf är det hierarkiska diagrammet över blanksteg, enheter och personer som definieras i objektmodellen Digital Twins. Den rumsliga grafen stöder arv, filtrering, travers, skalbarhet och utökningsbarhet. Du kan hantera och interagera med ditt rumsliga diagram med en samling REST-API:er.

Om du distribuerar en Digital Twins-tjänst i din prenumeration blir du global administratör för rotnoden. Du får sedan automatiskt fullständig åtkomst till hela strukturen. Etablera blanksteg i diagrammet med hjälp av space API. Tillhandahålla tjänster med hjälp av enhets-API:et och sensorer med hjälp av sensor-API:et. [Verktyg med öppen källkod](https://github.com/Azure-Samples/digital-twins-samples-csharp) är också tillgängliga för att etablera diagrammet i bulk.

**Grafarv**. Arv gäller för de behörigheter och egenskaper som härstammar från en överordnad nod till alla noder under den. När en roll till exempel tilldelas en användare på en viss nod har användaren den rollens behörighet till den angivna noden och varje nod under den. Varje egenskapsnyckel och utökad typ som definierats för en viss nod ärvs av alla noder under noden.

**Graffiltrering**. Filtrering används för att begränsa resultatet av begäranden. Du kan filtrera efter ID:n, namn, typer, undertyper, överordnat utrymme och associerade blanksteg. Du kan också filtrera efter sensordatatyper, egenskapsnycklar och värden, *gå igenom,* *minLevel,* *maxLevel*och andra OData-filterparametrar.

**Graf som korsar**. Du kan korsa den rumsliga grafen genom dess djup och bredd. För djup, gå igenom diagrammet uppifrån och ned eller nedifrån och upp med hjälp av parametrarna *genom att korsa,* *minLevel*och *maxLevel*. Gå igenom diagrammet för att få syskonnoder direkt kopplade till ett överordnat utrymme eller en av dess underordnade för bredd. När du frågar ett objekt kan du hämta alla relaterade objekt som har relationer till objektet med hjälp av parametern *includes* i GET-API:erna.

**Grafskalbarhet**. Digitala tvillingar garanterar grafskalbarhet, så att den kan hantera dina verkliga arbetsbelastningar. Digital Twins kan användas för att representera stora portföljer av fastigheter, infrastruktur, enheter, sensorer, telemetri och mycket mer.

**Graf utökningsbarhet**. Använd utökningsbarhet för att anpassa underliggande Digital Twins-objektmodeller med nya typer och ontologier. Dina Digital Twins-data kan också berikas med utökningsbara egenskaper och värden.

### <a name="spatial-intelligence-graph-management-apis"></a>Api:er för hantering av spatial intelligens

När du har distribuerat Digitala tvillingar från [Azure-portalen](https://portal.azure.com)genereras [Swagger-URL:en](https://swagger.io/tools/swagger-ui/) för hanterings-API:erna automatiskt. Den visas i Azure-portalen i avsnittet **Översikt** med följande format.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Namn | Ersätt med |
| --- | --- |
| YOUR_INSTANCE_NAME | Namnet på Digital Twins-instansen |
| YOUR_LOCATION | Den serverregion som instansen finns i |

 Det fullständiga URL-formatet visas i den här bilden.

[![Api för hantering av Digitala tvillingar portal](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Mer information om hur du använder spatial intelligence-grafer finns i Azure Digital Twins Management API:er för förhandsversion.

> [!TIP]
> En Swagger-förhandstitt tillhandahålls för att demonstrera API-funktionsuppsättningen.
> Den finns på [docs.westcentralus.azuresmartspaces.net/management/swagger.](https://docs.westcentralus.azuresmartspaces.net/management/swagger)

Läs mer om [hur du använder Swagger](how-to-use-swagger.md).

Alla API-anrop måste autentiseras med [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). API:erna följer [konventionerna för Microsoft REST API Guidelines](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). De flesta API:er [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) som returnerar samlingar stöder OData-systemfrågaalternativ.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om enhetsanslutning och hur du skickar telemetrimeddelanden till Digital Twins läser du [Azure Digital Twins-enhetsanslutning och telemetriinträngning](concepts-device-ingress.md).

- Om du vill veta mer om begränsningar och begränsningar för hanterings-API läser du [Azure Digital Twins API-hantering och begränsningar](concepts-service-limits.md).
