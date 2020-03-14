---
title: Förstå objekt modeller och rums information Graph – Azure Digitals flätade | Microsoft Docs
description: Använd Azure Digitals sammanflätade för att modellera relationer mellan människor, platser och enheter
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79265212"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Förstå digitala objekt modeller och spatial Intelligence-diagram

Azure Digitals dubbla är en Azure IoT-tjänst som ger omfattande virtuella representationer av fysiska miljöer och tillhör ande enheter, sensorer och människor. Det förbättrar utvecklingen genom att organisera domänbaserade koncept i användbara modeller. Modellerna är sedan belägna i ett diagram över spatial information. Dessa begrepp förvarar i sin modell relationer och interaktioner mellan människor, utrymmen och enheter.

Digitala dubbla objekt modeller beskriver domänbaserade koncept, kategorier och egenskaper. Modeller är fördefinierade av användare som vill skräddarsy lösningen efter sina egna behov. Tillsammans utgör dessa fördefinierade digitala objekt modeller en _Ontology_. En smart byggnads Ontology beskriver regioner, platser, golv, kontor, zoner, konferens rum och fokus rum. I ett Ontology för energi raster beskrivs olika strömförsörjnings stationer, under stationer, energi resurser och kunder. Med digitala dubbla objekt modeller och Ontologies kan olika scenarier och behov anpassas.

Med digitala dubbla objekt modeller och en Ontology på plats kan du fylla ett _spatial diagram_. Spatiala diagram är virtuella representationer av många relationer mellan utrymmen, enheter och personer som är relevanta för en IoT-lösning. Det här diagrammet visar ett exempel på en spatial graf som använder en smart byggnads Ontology.

[![Digitals sammanställd spatial diagram uppbyggnad](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

I spatial grafen samlas utrymmen, enheter, sensorer och användare samman. Varje länkas samman på ett sätt som modellerar den verkliga världen. I det här exemplet har plats 43 fyra golv, var och en med många olika områden. Användare är kopplade till sina arbets stationer och tilldelas åtkomst till delar av grafen. En administratör har behörighet att göra ändringar i den spatiala grafen, medan en besökare har behörighet att bara visa vissa byggnads data.

## <a name="digital-twins-object-models"></a>Digitala dubbla objekt modeller

Digitala objekt modeller med dubbla objekt stöder dessa huvud kategorier av objekt:

- **Blank steg** är virtuella eller fysiska platser, till exempel `Tenant`, `Customer`, `Region`och `Venue`.
- **Enheter** är virtuella eller fysiska delar av utrustning, till exempel `AwesomeCompany Device` och `Raspberry Pi 3`.
- **Sensorer** är objekt som identifierar händelser, till exempel `AwesomeCompany Temperature Sensor` och `AwesomeCompany Presence Sensor`.
- **Användarna** identifierar personer och deras egenskaper.

Andra objekt kategorier är:

- **Resurser** är kopplade till ett utrymme och representerar vanligt vis Azure-resurser som ska användas av objekt i spatial graf, till exempel `IoTHub`.
- **Blobbar** är kopplade till objekt (till exempel utrymmen, enheter, sensorer och användare). De används som filer med MIME-typ och metadata, till exempel `maps`, `pictures`och `manuals`.
- **Utökade typer** är utöknings bara uppräkningar som förstärker entiteter med särskilda egenskaper, till exempel `SpaceType` och `SpaceSubtype`.
- **Ontologies** representerar en uppsättning utökade typer, till exempel `Default`, `Building`, `BACnet`och `EnergyGrid`.
- **Egenskaps nycklar och värden** är anpassade egenskaper för utrymmen, enheter, sensorer och användare. De kan användas tillsammans med inbyggda egenskaper, till exempel `DeltaProcessingRefreshTime` som nyckel och `10` som värde.
- **Roller** är uppsättningar med behörigheter som tilldelas till användare och enheter i spatial graf, till exempel `Space Administrator`, `User Administrator`och `Device Administrator`.
- **Roll tilldelningar** är associationen mellan en roll och ett objekt i det spatiala diagrammet. Till exempel kan en användare eller ett huvud namn för tjänsten beviljas behörighet att hantera ett utrymme i den spatiala diagrammet.
- **Säkerhets nyckel Arkiv** innehåller säkerhets nycklar för alla enheter i hierarkin under ett givet utrymme för att tillåta att enheten kommunicerar säkert med digitala dubbla objekt.
- Med **användardefinierade funktioner** (UDF: er) kan du bearbeta anpassningsbar telemetri för sensorer i det spatiala diagrammet. Till exempel kan en UDF:
  - Ange ett sensor värde.
  - Utför anpassad logik baserat på sensor avläsningar och ange utdata till ett blank steg.
  - Bifoga metadata till ett utrymme.
  - Skicka meddelanden när fördefinierade villkor är uppfyllda. För närvarande kan UDF: er skrivas i Java Script.
- **Matchningar** är objekt som avgör vilka UDF: er som körs för ett angivet telemetri.
- **Slut punkter** är de platser där telemetri-meddelanden och digitala dubbla händelser kan dirigeras, till exempel `Event Hub`, `Service Bus`och `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Diagram för spatial intelligens

Spatial graf är det hierarkiska diagrammet över utrymmen, enheter och personer som definierats i den digitala dubbla objekt modellen. Det spatiala diagrammet stöder arv, filtrering, bläddringskontroll, skalbarhet och utöknings barhet. Du kan hantera och interagera med din spatiala graf med en samling REST-API: er.

Om du distribuerar en Digitals dubbla tjänst i din prenumeration blir du global administratör för rotnoden. Du beviljas sedan automatiskt fullständig åtkomst till hela strukturen. Etablera utrymmen i grafen med hjälp av blank stegs-API: et. Etablera tjänster med hjälp av enhets-API och sensorer med hjälp av sensor-API: et. [Verktyg för öppen källkod](https://github.com/Azure-Samples/digital-twins-samples-csharp) är också tillgängliga för att etablera grafen i bulk.

**Diagram arv**. Arv gäller behörigheter och egenskaper som är fallande från en överordnad nod till alla noder under den. Till exempel när en roll tilldelas till en användare på en specifik nod, har användaren den rollens behörigheter till den angivna noden och varje nod under den. Varje egenskaps nyckel och utökad typ som definierats för en viss nod ärvs av alla noder under den noden.

**Diagram filtrering**. Filtrering används för att begränsa resultaten av begäran. Du kan filtrera efter ID: n, namn, typer, under typer, överordnat utrymme och associerade utrymmen. Du kan också filtrera efter sensor data typer, egenskaps nycklar och värden, gå *igenom*, *minLevel*, *maxLevel*och andra OData-filter parametrar.

**Graf-bläddra**. Du kan förflytta den spatiala grafen genom dess djup och bredd. För djup kan du förflytta diagrammet uppåt eller nedåt genom att använda parametrarna *Traversal*, *minLevel*och *maxLevel*. Bläddra i grafen för att hämta noder på samma nivå som är direkt kopplade till ett överordnat utrymme eller en av dess underordnade för bredd. När du frågar ett objekt kan du hämta alla relaterade objekt som har relationer till objektet med hjälp av parametern *includes* i get-API: erna.

**Diagram skalbarhet**. Digitala dubblare garanterar diagram skalbarhet, så att den kan hantera dina verkliga arbets belastningar. Digitala garn kan användas för att representera stora portföljer av fastighets-, infrastruktur-, enhets-, sensorer-, telemetri-och mer.

**Diagram utökning**. Använd utöknings barhet för att anpassa de underliggande digitala dubbla objekt modellerna med nya typer och Ontologies. Dina digitala dubbla data kan också vara omfattande med utöknings bara egenskaper och värden.

### <a name="spatial-intelligence-graph-management-apis"></a>API: er för grafisk hantering i spatial information

När du har distribuerat digitala dubbla från [Azure Portal](https://portal.azure.com) [genereras URL: en för](https://swagger.io/tools/swagger-ui/) hanterings-API: erna automatiskt. Den visas i Azure Portal i **översikts** avsnittet med följande format.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Namn | Ersätt med |
| --- | --- |
| YOUR_INSTANCE_NAME | Namnet på Digital Twins-instansen |
| YOUR_LOCATION | Den serverregion som instansen finns i |

 Det fullständiga URL-formatet visas i den här bilden.

[![Digitals sammanflätade Portal hanterings-API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Mer information om hur du använder spatialdata i spatial Intelligence finns i förhands granskning av Azure Digitals hanterings-API: er.

> [!TIP]
> En Swagger förhandstitt är att demonstrera API-funktionen.
> Den finns på [docs.westcentralus.azuresmartspaces.net/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Läs mer om [hur du använder Swagger](how-to-use-swagger.md).

Alla API-anrop måste autentiseras med hjälp av [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). API: erna följer [rikt linjerna för Microsoft rest Apis regler](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). De flesta API: er som returnerar samlingar har stöd för [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) system Query-alternativ.

## <a name="next-steps"></a>Nästa steg

- Om du vill veta mer om enhets anslutning och hur du skickar telemetri till digitala enheter kan du läsa mer i [Azure Digitals enhets anslutning och telemetri](concepts-device-ingress.md).

- Om du vill veta mer om hanterings-API-begränsningar och begränsningar kan du läsa [Azure Digitals dubbla API-hantering och begränsningar](concepts-service-limits.md).
