---
title: Förstå Digital Twins objekt-modeller och rumsliga intelligence graph | Microsoft Docs
description: Med hjälp av Azure Digital Twins till modeller av relationer mellan personer, platser och enheter
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/08/2018
ms.author: alinast
ms.openlocfilehash: 1c2068af510cb3733ce99a6ae7b40487a8c1a015
ms.sourcegitcommit: 74941e0d60dbfd5ab44395e1867b2171c4944dbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2018
ms.locfileid: "49324312"
---
# <a name="understanding-digital-twins-object-models-and-spatial-intelligence-graph"></a>Förstå Digital Twins objektmodeller och rumsliga intelligence graph

Azure Digital Twins är en Azure IoT-tjänst som driver omfattande virtuella framställningar av fysiska miljöer och tillhörande enheter, sensorer och personer. Det förbättrar utvecklingen genom att ordna domänspecifika begrepp till användbara modeller som själva belägna inom ett spatial intelligence-diagram. Dessa begrepp modell troget relationer och interaktioner mellan människor, blanksteg och enheter.

_Digitala Twins objektmodeller_ beskriver domänspecifika begrepp, kategorier och egenskaper. Modeller är fördefinierade av användare som vill anpassa lösning för deras specifika behov. Tillsammans dessa fördefinierade digitala Twins objektmodeller utgör en _Ontology_. En smart byggnad ontology skulle beskriva regioner, platser, golv, kontor, zoner, konferensrum och fokus rum. En energi grid ontology beskrivningar för olika kraftverk, delstationer, resurser och kunder. Dessa digitala Twins objektmodeller och ontologi kan du anpassa Azure Digital Twins för olika scenarier och behov.

Med _digitala Twins objektmodeller_ och _Ontology_ på plats, kan en fylla i en _Spatial Graph_. Spatial diagram är virtuella representationer av många relationerna mellan blanksteg, enheter och människor som är relevanta för en IoT-lösning. Diagrammet nedan visar ett exempel på en spatial graf med en smart byggnad ontology.

![Digitala Twins Spatial Graph byggnad][1]

<a id="model" />

Spatial diagrammet innehåller blanksteg, enheter, sensorer och användare. Var och en länkas samman på ett sätt som modeller verkligheten: jurisdiktionsort 43 har fyra golv, var och en med flera olika områden. Användarna är associerade med deras arbetsstationer och får åtkomst till delar av diagrammet.  Exempelvis kan behöver en administratör behörighet att göra ändringar i spatial diagrammet medan en besökare kan bara ha behörighet att visa vissa data för att skapa.

## <a name="digital-twins-object-models"></a>Digitala Twins objektmodeller

Digitala Twins objektmodeller stöder dessa huvudkategorier av objekt:

- **Blanksteg** är virtuella eller fysiska platser, till exempel `Tenant`, `Customer`, `Region`, `Venue`.
- **Enheter** är virtuella eller fysiska delar av utrustning, till exempel `AwesomeCompany Device`, `Raspberry Pi 3`.
- **Sensorer** är objekt som identifiera händelser, till exempel `AwesomeCompany Temperature Sensor`, `AwesomeCompany Presence Sensor`.
- **Användare** identifiera personer och deras egenskaper.

Andra typer av objekt är:

- **Resurser** är kopplade till ett blanksteg och representerar vanligtvis den Azure-resurser som ska användas av objekt i spatial diagrammet, till exempel `IoTHub`.
- **Blobbar** bifogas objekt (till exempel blanksteg, enheter, sensorer och användare) och används som filer med mime-typ och metadata, till exempel `maps`, `pictures`, `manuals`.
- **Utökade typer** är utökningsbar uppräkningar som kan förbättra entiteter med specifika egenskaper, till exempel `SpaceType`, `SpaceSubtype`.
- **Ontologi** representerar en uppsättning utökade typer, till exempel `Default`, `Building`, `BACnet`, `EnergyGrid`.
- **Egenskapen nycklar och värden** följer anpassade egenskaper för blanksteg, enheter, sensorer och användare. De kan användas utöver inbyggda egenskaper, till exempel `DeltaProcessingRefreshTime` som nyckel och `10` som värde.
- **Roller** är en uppsättning behörigheter som tilldelats användare och enheter i spatial diagrammet, till exempel `Space Administrator`, `User Administrator`, `Device Administrator`.
- **Rolltilldelningar** är associationen mellan en roll och ett objekt i spatial diagrammet, till exempel ge en användare eller en service principal behörighet att hantera ett blanksteg i spatial diagrammet.
- **Security nyckel lagrar** security-nycklar för alla enheter i hierarkin under ett visst utrymme objekt så att enheten att kommunicera säkert med digitala Twins-tjänsten.
- **Användaren användardefinierade funktioner** eller **UDF: er** Tillåt anpassningsbara sensor telemetribearbetning i spatial diagrammet. Till exempel kan en UDF ange ett värde för sensor, utföra anpassad logik baserat på sensoravläsningar och Ställ in utdatan till ett blanksteg, koppla metadata till ett blanksteg och skicka meddelanden när fördefinierade villkor är uppfyllda. För närvarande kan UDF: er skrivas i JavaScript.
- **Matchers** är objekt som bestämmer vilka UDF: er ska utföras för en viss telemetri-meddelande.
- **Slutpunkter** är platser där telemetrimeddelanden och Digital Twins händelser kan vidarebefordras, till exempel `Event Hub`, `Service Bus`, `Event Grid`.

<a id="graph" />

## <a name="spatial-intelligence-graph"></a>Spatial intelligence graph

**Spatial graph** är hierarkiska diagrammet av blanksteg, enheter, och personer som definieras i den **digitala Twins objektmodellen**. Har stöd för spatial diagrammet _arv_, _filtrering_, _passerar_, _skalbarhet_, och _utökningsbarhet_ . Användarna kan hantera och interagera med sina spatial diagram med en uppsättning REST API: er (se nedan).

Den användare som distribuerar en Digital Twins-tjänst i sin prenumeration blir global administratör för rotnoden, automatiskt ge fullständig åtkomst till hela strukturen. Den här användaren kan sedan etablera blanksteg i en graf med hjälp av den `Space` API. Enheter kan etableras med hjälp av den `Device` API, sensorer kunde etableras med hjälp av `Sensor` API, osv. Vi erbjuder också [verktyg med öppen källkod](https://github.com/Azure-Samples/digital-twins-samples-csharp) att etablera graph gruppvis.

Graph _arv_ gäller behörigheter och egenskaper som fallande från en överordnad nod till alla noder under den. När en roll tilldelas till en användare på en viss nod, kommer användaren ha behörighet för den rollen att noden och varje nod under den. Dessutom kan ärvs varje Egenskapsnyckeln och utökad typ som definierats för en viss nod av alla noder under noden.

Graph _filtrering_ gör att användarna kan begränsa begäran resultaten efter ID: N, namn, typer, undertyper, överordnade utrymme, associerade blanksteg, sensor datatyper, egenskapen nycklar och värden, bläddra, minLevel, maxLevel och andra OData-filter parametrar.

Graph _passerar_ kan användarna navigera spatial diagrammet via dess djupa och breda. För djup diagrammet kan vara slut uppifrån och ned eller nedifrån och upp med parametrar som navigering `traverse`, `minLevel`, `maxLevel`. Bredd, kan efter diagrammet för att få noder på samma nivå direkt anslutna till en överordnad utrymme eller någon av dess underordnade. När du hämtar ett objekt, du kan få alla relaterade objekt som har relationer till den objekt med hjälp av den `includes` parametern få API: er.

Azure Digital Twins garanterar graph _skalbarhet_, så att den kan hantera verkliga arbetsbelastningar. Digitala Twins kan användas för att representera stora portföljer fastigheter, infrastruktur, enheter, sensorer, telemetri och mer.

Graph _utökningsbarhet_ tillåter användare att anpassa underliggande digitala Twins objektmodeller med nya typer och ontologi. Din digitala twins data kan också utökas med extensible egenskaper och värden.

### <a name="spatial-intelligence-graph-management-apis"></a>Spatial intelligence graph API: er Management

När du har distribuerat Azure Digital Twins från den [Azure-portalen](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) URL: en för Management-API: er genereras automatiskt och visas i Azure portal **översikt** avsnitt med följande format:

```plaintext
https://yourInstanceName.yourLocation.azuresmartspaces.net/management/swagger
```

| Anpassade attributets namn | Ersätt med |
| --- | --- |
| `yourInstanceName` | Namnet på din digitala Twins för Azure-instans |
| `yourLocation` | Vilken server-region som din instans är värd för |

 Det fullständiga URL-formatet kan ses som används i bilden nedan:

![Digitala Twins Portal Management-API][2]

Mer information om hur du använder Spatial Intelligence diagram på förhandstitt Azure Digital Twins Management API: er.

> [!TIP]
> En Swagger-förhandstitt tillhandahålls för att demonstrera API-funktionen.
> Den finns på [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Läs mer om [hur du använder Swagger](how-to-use-swagger.md).

Alla API-anrop måste autentiseras med hjälp av [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). API: er följer [riktlinjerna för Microsofts REST API-konventioner](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Stöd för de flesta av de API: er som returnerar samlingar [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) system frågealternativ.

## <a name="next-steps"></a>Nästa steg

Läs om enhetsanslutning och hur du skickar telemetrimeddelanden till Azure Digital Twins service [Azure Digital Twins enhetens anslutning och telemetriingång](concepts-device-ingress.md).

Mer information om hantering av API-begränsningar och begränsningar, läsa [Azure Digital Twins API management och begränsningar](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
