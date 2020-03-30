---
title: Navigera i API:erna – Azure Digital Twins | Microsoft-dokument
description: Lär dig hur du kan vanliga mönster för att fråga Azure Digital Twins management API:er.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265173"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Så här använder du Azure Digital Twins management API:er

Azure Digital Twins management API:er ger kraftfulla funktioner för dina IoT-appar. Den här artikeln visar hur du navigerar genom API-strukturen.  

## <a name="api-summary"></a>API-sammanfattning

I följande lista visas komponenterna i Digital Twins API:er.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Dessa API:er interagerar med de fysiska platserna i konfigurationen. Dessa hjälper dig att skapa, ta bort och hantera digitala mappningar av dina fysiska platser i form av ett [rumsligt diagram](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Dessa API:er interagerar med enheterna i konfigurationen. Dessa enheter kan hantera en eller flera sensorer. En enhet kan till exempel vara din telefon, en Raspberry Pi-sensorpod, eller en Lora-gateway och så vidare.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Dessa API:er hjälper dig att kommunicera med sensorerna som är associerade med dina enheter och dina fysiska platser. Sensorerna registrerar och skickar omgivande värden som sedan kan användas för att manipulera din rumsliga miljö.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Dessa API:er hjälper dig att ställa in resurser, till exempel en IoT-hubb, för din Digital Twins-instans.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Med dessa API:er kan du associera utökade typer med dina Digital Twins-objekt för att lägga till specifika egenskaper i dessa objekt. Dessa typer möjliggör enkel filtrering och gruppering av objekt i användargränssnittet och de anpassade funktioner som bearbetar telemetridata. Exempel på utökade typer är *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*och så vidare.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Dessa API: er hjälper dig att hantera ontologier, som är samlingar av utökade typer. Ontologier ger namn för objekttyper enligt det fysiska utrymme de representerar. *Bacnet-ontologin* innehåller till exempel specifika namn för *sensortyper,* *datatyper,* *datasubtyper*och *dataunittyper*. Ontologies hanteras och skapas av tjänsten. Användare kan ladda och lossa ontologier. När en ontologi läses in aktiveras alla associerade typnamn och är redo att etableras i det rumsliga diagrammet. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Du kan använda dessa API:er för att skapa anpassade egenskaper för *dina utrymmen,* *enheter,* *användare*och *sensorer*. Dessa egenskaper skapas som nyckel-/värdepar. Du kan definiera datatypen för dessa egenskaper genom att ange deras *PrimitiveDataType*. Du kan till exempel definiera en egenskap med namnet *BasicTemperatureDeltaProcessingRefreshTime* av typen *uint* för dina sensorer och sedan tilldela ett värde för den här egenskapen för var och en av sensorerna. Du kan också lägga till villkor för dessa värden när du skapar egenskapen, till exempel *Min-* och *Maxintervall,* samt tillåtna värden som *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Med dessa API:er kan du ange de villkor som du vill utvärdera från inkommande enhetsdata. Mer information finns i [den här artikeln](concepts-user-defined-functions.md#matchers). 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Med dessa API:er kan du skapa, ta bort eller uppdatera en anpassad funktion som körs när villkor som definieras av *matchningarna* inträffar, för att bearbeta data som kommer från din konfiguration. Mer information om dessa anpassade funktioner finns i *den*här [artikeln.](concepts-user-defined-functions.md#user-defined-functions) 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Med dessa API:er kan du skapa slutpunkter så att din Digital Twins-lösning kan kommunicera med andra Azure-tjänster för datalagring och dataanalys. Läs [den här artikeln](concepts-events-routing.md) för mer information. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Med dessa API:er kan du hantera säkerhetsnyckellager för dina utrymmen. Dessa butiker kan innehålla en samling säkerhetsnycklar och gör att du enkelt kan hämta de senaste giltiga nycklarna.

* [/users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Med dessa API:er kan du associera användare med dina utrymmen, för att hitta dessa personer när det behövs. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Med dessa API:er kan du hantera systemomfattande inställningar, till exempel standardtyper av blanksteg och sensorer. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Med dessa API:er kan du associera roller till entiteter som användar-ID, användardefinierat funktions-ID osv. Varje rolltilldelning innehåller ID:t för entiteten som ska associeras, entitetstypen, ID:t för den roll som ska associeras, klient-ID:t och en sökväg som definierar den övre gränsen för den resurs som entiteten kan komma åt med den associationen. Läs [den här artikeln](security-role-based-access-control.md) för mer information.


## <a name="api-navigation"></a>API-navigering

Digital Twins API:er stöder filtrering och navigering i hela diagrammet med hjälp av följande parametrar:

- **spaceId**: API:et filtrerar resultaten med det angivna rymd-ID:et. Dessutom är den booleska flaggan **useParentSpace** tillämplig på API:erna [/spaces,](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) vilket anger att det angivna utrymmes-ID:t refererar till det överordnade utrymmet i stället för det aktuella utrymmet. 

- **minLevel** och **maxLevel**: Rotutrymmen anses ligga på nivå 1. Blanksteg med överordnat utrymme på nivå *n* ligger på nivå *n+1*. Med dessa värden inställda kan du filtrera resultaten på specifika nivåer. Dessa är inkluderande värden när de ställs in. Enheter, sensorer och andra objekt anses ligga på samma nivå som närmaste utrymme. Om du vill hämta alla objekt på en viss nivå ställer du in både **minLevel** och **maxLevel** till samma värde.

- **minRelative** och **maxRelative**: När dessa filter anges är motsvarande nivå relativ till nivån för det angivna rymd-ID:a:
   - Relativ nivå *0* är samma nivå som det angivna rymd-ID:et.
   - Relativ nivå *1* representerar blanksteg på samma nivå som underordnade i det angivna rymd-ID:t. Relativ nivå *n* representerar utrymmen som är lägre än det angivna utrymmet med *n nivåer.*
   - Relativ nivå *-1* representerar blanksteg på samma nivå som det överordnade utrymmet i det angivna utrymmet.

- **travers:** Gör att du kan gå i båda riktningarna från ett visst rymd-ID, enligt följande värden.
   - **Ingen**: Det här standardvärdet filtrerar till det angivna rymd-ID:et.
   - **Ned**: Detta filtrerar efter det angivna rymd-ID:t och dess underordnade. 
   - **Upp**: Detta filtrerar efter det angivna rymd-ID:t och dess förfäder. 
   - **Span**: Detta filtrerar en vågrät del av det rumsliga diagrammet, på samma nivå som det angivna rymd-ID:et. Detta behöver antingen **minRelative** eller **maxRelative** att ställas in sant. 


### <a name="examples"></a>Exempel

Följande lista visar några exempel på navigering via API:erna [/devices.](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) Observera att `YOUR_MANAGEMENT_API_URL` platshållaren refererar till URI för Digital `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`Twins `YOUR_INSTANCE_NAME` API:er i formatet , `YOUR_LOCATION` där är namnet på din Azure Digital Twins-instans och är den region där din instans finns.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`returnerar alla enheter som är anslutna till rotutrymmen.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`returnerar alla enheter som är anslutna till utrymmen på nivåerna 2, 3 eller 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`returnerar alla enheter som är direkt anslutna till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`returnerar alla enheter som är anslutna till mySpaceId eller någon av dess underordnade.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`returnerar alla enheter som är anslutna till underordnade till mySpaceId, exklusive mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`returnerar alla enheter som är anslutna till omedelbara barn till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`returnerar alla enheter som är anslutna till en av förfäderna till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`returnerar alla enheter som är anslutna till underordnade i mySpaceId som är på en nivå som är mindre än eller lika med 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`returnerar alla enheter som är anslutna till blanksteg som är på samma nivå som mySpaceId.


## <a name="odata-support"></a>Support för OData

De flesta API:er som returnerar samlingar, till exempel ett GET-anrop på /spaces, stöder följande delmängd av de allmänna OData-systemfrågealternativen: [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData)  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - Om du tänker visa hela samlingen bör du begära den som en helhet i ett enda samtal och sedan utföra personsökning i ditt program. 

> [!NOTE]
> Vissa OData-alternativ (till exempel frågealternativ **$count**, **$expand**och **$search**) stöds för närvarande inte.

### <a name="examples"></a>Exempel

I följande lista visas flera frågor med giltig OData-syntax:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Nästa steg

Om du vill lära dig några vanliga API-frågemönster läser du Så här frågar du [Azure Digital Twins API:er för vanliga uppgifter](./how-to-query-common-apis.md).

Mer information om dina API-slutpunkter finns i [Så här använder du Digital Twins Swagger](./how-to-use-swagger.md).

Om du vill granska OData-syntax och tillgängliga jämförelseoperatorer läser du [OData-jämförelseoperatorer i Azure Cognitive Search](../search/search-query-odata-comparison-operators.md).
