---
title: 'Navigera Azure Digital Twins API: er | Microsoft Docs'
description: 'Lär dig hur du gemensamma mönster för frågor till Azure Digital Twins management API: er.'
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54200963"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Hur du använder Azure Digital Twins management API: er

API: er för Azure Digital Twins hantering ger kraftfulla funktioner för dina IoT-appar. Den här artikeln visar hur du navigerar i API-struktur.  

## <a name="api-summary"></a>API-sammanfattning

I följande lista visas komponenterna i den digitala Twins-API: er.

* [/ blanksteg](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Dessa API: er kan interagera med de fysiska platserna i din konfiguration. Dessa hjälper dig att skapa, ta bort och hantera de digitala mappningarna i din fysiska platser i form av en [spatial graph](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Dessa API: er interagera med enheter i din konfiguration. Enheterna kan hantera en eller flera sensorer. Till exempel kan en enhet vara din telefon eller en Raspberry Pi sensor pod eller en Lora-gateway och så vidare.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Dessa API: er hjälpa dig att kommunicera med sensorer som är associerade med dina enheter och din fysiska platser. Sensorerna registrera och skicka omgivande värden som kan användas för att manipulera miljön spatial.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Dessa API: er hjälper dig att konfigurera resurser, t.ex en IoT-hubb för din digitala Twins-instans.

* [/ skriver](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Dessa API: er kan du associera utökade typer med din digitala Twins-objekt, lägga till specifika egenskaper för de aktuella objekten. Dessa typer möjliggör enkel filtrering och gruppering av objekt i Användargränssnittet och anpassade funktioner som bearbetar dina telemetridata. Exempel på utökade typer är *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType* , *SpaceBlobType*, *SpaceResourceType*och så vidare.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Dessa API: er hjälper dig att hantera ontologi som är samlingar av utökade typer. Ontologi ange namn för objekt av typen enligt det fysiska utrymmet som de representerar. Till exempel den *BACnet* ontology innehåller specifika namn för *sensor typer*, *datatyper*, *datasubtypes*, och *dataunittypes*. Ontologi hanteras och har skapats av tjänsten. Användare kan läsa in och ta bort ontologi. När en ontology läses är alla dess associerade typnamnen aktiverad och redo att etableras i grafen spatial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Du kan använda dessa API: er för att skapa anpassade egenskaper för din *blanksteg*, *enheter*, *användare*, och *sensorer*. Dessa egenskaper skapas som nyckel/värde-par. Du kan definiera datatypen för dessa egenskaper genom att ange sina *PrimitiveDataType*. Du kan till exempel definiera en egenskap med namnet *BasicTemperatureDeltaProcessingRefreshTime* av typen *uint* för sensorer och sedan tilldela ett värde för den här egenskapen för var och en av dina sensorer. Du kan också lägga till begränsningar för dessa värden när du skapar egenskapen, till exempel *Min* och *Max* adressintervall, samt tillåtna värden som *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Dessa API: er kan du ange de villkor som du vill utvärdera från din inkommande enhetsdata. Se [i den här artikeln](concepts-user-defined-functions.md#matchers) för mer information. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Dessa API: er kan du skapa, ta bort eller uppdatera en anpassad funktion som körs när villkor definieras av den *matchers* inträffa att bearbeta data som kommer från din konfiguration. Se [i den här artikeln](concepts-user-defined-functions.md#user-defined-functions) för mer information om dessa anpassade funktioner, kallas även den *användardefinierade funktioner*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Dessa API: er kan du skapa slutpunkter så att din digitala Twins-lösning kan kommunicera med andra Azure-tjänster för lagring av data och analys. Läs [i den här artikeln](concepts-events-routing.md) för mer information. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Dessa API: er kan du hantera säkerhet viktiga butiker för dina lagringsutrymmen. Dessa lager kan innehålla en uppsättning säkerhetsnycklar och gör att du kan enkelt hämta de senaste giltiga nycklarna.

* [/ användare](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Dessa API: er kan du koppla användare till din blanksteg, att hitta dessa individer vid behov. 

* [/ system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Dessa API: er kan du hantera systemomfattande inställningar, till exempel standardtyper av blanksteg och sensorer. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Dessa API: er kan du associera roller till entiteter, till exempel användar-ID, en användardefinierad funktion ID osv. Varje rolltilldelning innehåller ID för entitet som ska associera entitetstypen, ID för rollen ska associera, klient-ID och en sökväg som definierar den övre gränsen för den resurs som entiteten kan komma åt med kopplingen. Läs [i den här artikeln](security-role-based-access-control.md) för mer information.


## <a name="api-navigation"></a>API-navigering

API: er för Digital Twins stöder filtrering och navigeringen i hela spatial diagrammet med följande parametrar:

- **spaceId**: API: et ska filtrera resultatet efter angivna utrymmes-ID. Dessutom boolesk flagga **useParentSpace** gäller för den [/blanksteg](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) API: er, vilket betyder att viss utrymmes-ID som refererar till det överordnade utrymmet i stället för utrymmet. 

- **minLevel** och **maxLevel**: Rot blanksteg anses vara på nivå 1. Blankstegen med överordnade utrymme på nivå *n* är på nivå *n + 1*. Med dessa värden, kan du filtrera resultaten på specifika nivåer. Detta är inkluderande värden när värdet. Enheter, sensorer och andra objekt anses vara på samma nivå som sin närmaste utrymme. Om du vill hämta alla objekt på en viss nivå, anger du både **minLevel** och **maxLevel** till samma värde.

- **minRelative** och **maxRelative**: När de här filtren ges är motsvarande nivå i förhållande till nivån för den angivna utrymme-ID:
   - Relativa nivå *0* som samma nivå som den angivna utrymme-ID.
   - Relativa nivå *1* representerar blanksteg på samma nivå som de underordnade objekten i den angivna utrymme-ID. Relativa nivå *n* representerar blanksteg som är lägre än det angivna utrymmet genom *n* nivåer.
   - Relativa nivå *-1* representerar blanksteg på samma nivå som det överordnade utrymmet på det angivna utrymmet.

- **Bläddra bland**: Kan du bläddra i båda riktningarna från en viss utrymmes-ID, som den anges av följande värden.
   - **Ingen**: Det här standardvärdet filter på angivna utrymmes-ID.
   - **Ned**: Detta filtrerar efter angivna utrymme-ID och dess underordnade. 
   - **Konfigurera**: Detta filtrerar efter angivna utrymme-ID och dess överordnade. 
   - **Span**: Detta filtrerar en vågrät del av spatial diagram, på samma nivå som den angivna utrymme-ID. Detta måste antingen den **minRelative** eller **maxRelative** anges som true. 


### <a name="examples"></a>Exempel

I följande lista visas några exempel på navigering via den [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) API: er. Observera att platshållaren `YOUR_MANAGEMENT_API_URL` refererar till URI: N för den digitala Twins API: er i formatet `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, där `YOUR_INSTANCE_NAME` är namnet på din digitala Twins för Azure-instans och `YOUR_LOCATION` är den region där din instans finns.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` Returnerar alla enheter som är anslutna till roten blanksteg.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` Returnerar alla enheter som är anslutna till utrymmen på nivåer 2, 3 eller 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` Returnerar alla enheter som är direkt anslutna till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` Returnerar alla enheter som är anslutna till mySpaceId eller till en av dess underordnade.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` Returnerar alla enheter som är anslutna till underordnade för mySpaceId, exklusive mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` Returnerar alla enheter som är anslutna till direkt underordnade objekten för mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` Returnerar alla enheter som är anslutna till en av överordnade för mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` Returnerar alla enheter som är anslutna till underordnade för mySpaceId som är på nivån mindre än eller lika med 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` Returnerar alla enheter som är anslutna till utrymmen som är på samma nivå som mySpaceId.


## <a name="odata-support"></a>Stöd för OData
De flesta av de API: er som returnerar samlingar, till exempel en GET-anrop på /spaces, stödjer följande delmängd av ett allmänt [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) system frågealternativ:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** -om du vill visa hela samlingen du ska begära den som en hel uppsättning i ett enda anrop och därefter utför växling i ditt program. 

Observera att andra frågealternativ, till exempel $count $Expandera $search, stöds inte.

### <a name="examples"></a>Exempel

I följande lista visas några exempel på frågor med Odata's system-frågealternativ:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Nästa steg

Vissa vanliga frågemönster API kan läsa [hur frågar Azure Digital Twins API: er för vanliga uppgifter](how-to-query-common-apis.md).


