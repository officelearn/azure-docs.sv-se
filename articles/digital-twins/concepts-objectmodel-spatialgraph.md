---
title: Förstå Digital Twins objektmodeller och rumsliga intelligence graph | Microsoft Docs
description: Använd Azure Digital Twins för att skapa relationer mellan personer, platser och enheter
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: 912a3ed558f8fabfcad517aeb7b7e864cf8f359e
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2018
ms.locfileid: "53436933"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Förstå Digital Twins objektmodeller och rumsliga intelligence graph

Azure Digital Twins är en Azure IoT-tjänst som driver omfattande virtuella framställningar av fysiska miljöer och tillhörande enheter, sensorer och personer. Det förbättrar utvecklingen genom att ordna domänspecifika begrepp i användbara modeller. Modeller är sedan belägna inom ett spatial intelligence-diagram. Dessa begrepp modell troget relationer och interaktioner mellan människor, blanksteg och enheter.

Digitala Twins objektmodeller beskrivs domänspecifika begrepp, kategorier och egenskaper. Modeller är fördefinierade av användare som vill anpassa lösning för deras specifika behov. Tillsammans, de fördefinierade digitala Twins objektmodeller utgör en _ontology_. En smart byggnad ontology beskriver regioner, platser, golv, kontor, zoner, konferensrum och fokus rum. En energi grid ontology beskriver olika kraftverk, delstationer, resurser och kunder. Olika scenarier och behov kan anpassas med digitala Twins objektmodeller och ontologi.

Med digitala Twins objektmodeller och en ontology på plats kan du fylla i en _spatial graph_. Spatial diagram är virtuella representationer av många relationerna mellan blanksteg, enheter och människor som är relevanta för en IoT-lösning. Det här diagrammet visar ett exempel på ett spatial diagram som använder en smart byggnad ontology.

![Digitala Twins spatial graph byggnad][1]

<a id="model"></a>

Spatial diagrammet innehåller blanksteg, enheter, sensorer och användare. Var och en länkas samman på ett sätt som den verkliga världen-modeller. I det här exemplet har platsen 43 fyra golv, var och en med många olika områden. Användare som är associerade med deras arbetsstationer och få åtkomst till delar av diagrammet. En administratör har behörighet att göra ändringar i spatial diagrammet, medan en besökare har behörighet att endast visa vissa data för att skapa.

## <a name="digital-twins-object-models"></a>Digitala Twins objektmodeller

Digitala Twins objektmodeller stöder dessa huvudkategorier av objekt:

- **Blanksteg** är virtuella eller fysiska platser, till exempel `Tenant`, `Customer`, `Region`, och `Venue`.
- **Enheter** är virtuella eller fysiska delar av utrustning, till exempel `AwesomeCompany Device` och `Raspberry Pi 3`.
- **Sensorer** är objekt som identifiera händelser, till exempel `AwesomeCompany Temperature Sensor` och `AwesomeCompany Presence Sensor`.
- **Användare** identifiera personer och deras egenskaper.

Andra typer av objekt är:

- **Resurser** är kopplade till ett blanksteg och representerar vanligtvis den Azure-resurser som ska användas av objekt i spatial diagrammet, till exempel `IoTHub`.
- **Blobbar** är kopplade till objekt (till exempel blanksteg, enheter, sensorer och användare). De används som filer med mime-typ och metadata, till exempel `maps`, `pictures`, och `manuals`.
- **Utökade typer** är utökningsbar uppräkningar som kan förbättra entiteter med specifika egenskaper, till exempel `SpaceType` och `SpaceSubtype`.
- **Ontologi** representerar en uppsättning utökade typer, till exempel `Default`, `Building`, `BACnet`, och `EnergyGrid`.
- **Egenskapen nycklar och värden** följer anpassade egenskaper för blanksteg, enheter, sensorer och användare. De kan användas tillsammans med inbyggda egenskaper, till exempel `DeltaProcessingRefreshTime` som nyckel och `10` som värde.
- **Roller** är en uppsättning behörigheter som tilldelats användare och enheter i spatial diagrammet, till exempel `Space Administrator`, `User Administrator`, och `Device Administrator`.
- **Rolltilldelningar** är associationen mellan en roll och ett objekt i spatial diagrammet. Exempelvis kan kan en användare eller ett huvudnamn för tjänsten beviljas behörighet att hantera ett blanksteg i spatial diagrammet.
- **Säkerhet viktiga butiker** security-nycklar för alla enheter i hierarkin under ett visst utrymme objekt så att enheten att kommunicera säkert med digitala Twins.
- **Användardefinierade funktioner** (UDF) Tillåt anpassningsbara sensor telemetribearbetning i spatial diagrammet. Det kan till exempel en UDF:
  - Ange ett värde för sensorn.
  - Utföra anpassad logik baserat på sensoravläsningar och ange utdata till ett blanksteg.
  - Koppla metadata till ett blanksteg.
  - Skicka meddelanden när fördefinierade villkor är uppfyllda. För närvarande kan UDF: er skrivas i JavaScript.
- **Matchers** är objekt som bestämmer vilka UDF: er körs för en viss telemetri-meddelande.
- **Slutpunkter** är platser där telemetrimeddelanden och Digital Twins händelser kan vidarebefordras, till exempel `Event Hub`, `Service Bus`, och `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Diagram för spatial intelligens

Spatial graph är hierarkiska diagrammet av blanksteg, enheter och människor som definierats i digitala Twins object model. Spatial graph har stöd för arv, filtrering, passerar, skalbarhet och utökningsbarhet. Du kan hantera och interagera med dina spatial diagram med en uppsättning REST API: er.

Om du distribuerar en Digital Twins-tjänst i din prenumeration blir global administratör för rotnoden. Du är sedan automatiskt beviljas fullständig åtkomst till hela strukturen. Etablera blanksteg i grafen med hjälp av utrymme-API. Tillhandahålla tjänster med hjälp av enhets-API och sensorer med hjälp av Sensor-API. [Verktyg med öppen källkod](https://github.com/Azure-Samples/digital-twins-samples-csharp) också är tillgängliga för att etablera graph gruppvis.

**Graph arv**. Arv gäller behörigheter och egenskaper som fallande från en överordnad nod till alla noder under den. När en roll tilldelas till en användare på en viss nod, har användaren behörighet för den rollen att noden och varje nod under den. Varje Egenskapsnyckeln och utökad typ som definierats för en viss nod ärvs av alla noder under noden.

**Graph filtrering**. Filtrering används för att begränsa begäran resultat. Du kan filtrera efter ID, namn, typer, undertyper, överordnade utrymme och associerade blanksteg. Du kan även kan filtrera efter sensor datatyper, egenskapen nycklar och värden, *passerar*, *minLevel*, *maxLevel*, och andra parametrar för OData-filter.

**Diagram som passerar**. Du kan bläddra i spatial grafen via dess djupa och breda. För djup, bläddra i grafen uppifrån och ned eller nedifrån och upp med hjälp av parametrar *passerar*, *minLevel*, och *maxLevel*. Bläddra i grafen för att få noder på samma nivå direkt anslutna till en överordnad utrymme eller någon av dess underordnade för bredd. När du frågar ett objekt kan du få alla relaterade objekt som har relationer till objektet med hjälp av den *innehåller* parametern få API: er.

**Skalbarhet för Graph**. Digitala Twins garanterar graph skalbarhet, så att den kan hantera verkliga arbetsbelastningar. Digitala Twins kan användas för att representera stora portföljer fastigheter, infrastruktur, enheter, sensorer, telemetri och mer.

**Graph utökningsbarhet**. Anpassa de underliggande digitala Twins objektmodellerna med nya typer och ontologi med hjälp av utökningsbarhet. Dina digitala Twins data kan också utökas med extensible egenskaper och värden.

### <a name="spatial-intelligence-graph-management-apis"></a>Spatial intelligence graph API: er Management

När du har distribuerat digitala Twins från den [Azure-portalen](https://portal.azure.com), [Swagger](https://swagger.io/tools/swagger-ui/) URL: en för Management-API: er genereras automatiskt. Den visas i Azure-portalen i den **översikt** avsnitt med följande format.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Namn | Ersätt med |
| --- | --- |
| YOUR_INSTANCE_NAME | Namnet på Digital Twins-instansen |
| YOUR_LOCATION | Den serverregion som instansen finns i |

 Det fullständiga URL-formatet som visas i den här avbildningen.

![Digitala Twins portal Management-API][2]

Mer information om hur du använder spatial intelligence diagram på förhandstitt Azure Digital Twins Management API: er.

> [!TIP]
> En Swagger-förhandstitt tillhandahålls för att demonstrera API-funktionen.
> Den finns på [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Läs mer om [hur du använder Swagger](how-to-use-swagger.md).

Alla API-anrop måste autentiseras med hjälp av [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). API: er följer [riktlinjerna för Microsofts REST API-konventioner](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). Stöd för de flesta av de API: er som returnerar samlingar [OData](http://www.odata.org/getting-started/basic-tutorial/#queryData) system frågealternativ.

## <a name="next-steps"></a>Nästa steg

- Läs om enhetsanslutning och hur du skickar telemetrimeddelanden till Digital Twins [Azure Digital Twins enhetens anslutning och telemetriingång](concepts-device-ingress.md).

- Mer information om hantering av API-begränsningar och begränsningar, läsa [Azure Digital Twins API management och begränsningar](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
