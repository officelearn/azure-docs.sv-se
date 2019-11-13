---
title: 'Navigera bland API: erna – Azure Digitals flätar | Microsoft Docs'
description: 'Lär dig mer om vanliga mönster för att skicka frågor till Azure Digitals hanterings-API: er.'
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 10/27/2019
ms.openlocfilehash: 135c760919f094f7b2d242ad37cc1cadb614e36e
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/13/2019
ms.locfileid: "74009992"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Använda Azure Digitals hanterings-API: er

API: erna för Azure Digitals sammanflätade hantering ger kraftfulla funktioner för IoT-appar. Den här artikeln visar hur du navigerar i API-strukturen.  

## <a name="api-summary"></a>API-sammanfattning

I följande lista visas komponenterna i de digitala dubbla API: erna.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): dessa API: er interagerar med de fysiska platserna i installationen. Dessa hjälper dig att skapa, ta bort och hantera de digitala mappningarna för dina fysiska platser i form av ett [spatial diagram](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): dessa API: er interagerar med enheterna i installationen. Dessa enheter kan hantera en eller flera sensorer. En enhet kan till exempel vara din telefon eller en Raspberry Pi-sensor pod, eller en Lora-Gateway, och så vidare.

* [/sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): dessa API: er hjälper dig att kommunicera med sensorer som är kopplade till dina enheter och dina fysiska platser. Sensorer-posten och skickar omgivande värden som sedan kan användas för att manipulera din rums miljö.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): dessa API: er hjälper dig att konfigurera resurser, t. ex. en IoT-hubb, för Digitals dubbla instanser.

* [/types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): dessa API: er gör att du kan koppla utökade typer till dina digitala dubbla objekt, så att du kan lägga till särskilda egenskaper för dessa objekt. De här typerna möjliggör enkel filtrering och gruppering av objekt i användar gränssnittet och de anpassade funktioner som bearbetar dina telemetridata. Exempel på utökade typer är *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType*, *SpaceBlobType*, *SpaceResourceType*och så vidare.

* [/Ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): dessa API: er hjälper dig att hantera Ontologies, som är samlingar av utökade typer. Ontologies tillhandahåller namn för objekt typer per det fysiska utrymme som de representerar. *BACnet* -Ontology innehåller till exempel vissa namn för *sensor typer*, *data typer*, *datasubtypes*och *dataunittypes*. Ontologies hanteras och skapas av tjänsten. Användare kan läsa in och ta bort Ontologies. När en Ontology har lästs in aktive ras alla dess associerade typ namn och de är klara att tillhandahållas i det spatiala diagrammet. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): du kan använda dessa API: er för att skapa anpassade egenskaper för dina *Spaces*, *enheter*, *användare*och *sensorer*. De här egenskaperna skapas som nyckel/värde-par. Du kan definiera data typen för dessa egenskaper genom att ange deras *PrimitiveDataType*. Du kan till exempel definiera en egenskap med namnet *BasicTemperatureDeltaProcessingRefreshTime* av typen *uint* för sensorer och sedan tilldela ett värde för den här egenskapen för var och en av dina sensorer. Du kan också lägga till begränsningar för dessa värden när du skapar egenskapen, till exempel *minsta* och *högsta* intervall, samt tillåtna värden som *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): dessa API: er låter dig ange de villkor som du vill utvärdera från dina inkommande enhets data. Mer information finns i [den här artikeln](concepts-user-defined-functions.md#matchers) . 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): dessa API: er gör att du kan skapa, ta bort eller uppdatera en anpassad funktion som ska köras när villkoren som definieras av *matchningarna* sker, för att bearbeta data som kommer från din installation. Se [den här artikeln](concepts-user-defined-functions.md#user-defined-functions) för mer information om dessa anpassade funktioner, även kallade *användardefinierade funktioner*. 

* [/endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): dessa API: er gör att du kan skapa slut punkter så att din digitala enhets lösning kan kommunicera med andra Azure-tjänster för data lagring och analys. Mer information finns i [den här artikeln](concepts-events-routing.md) . 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): dessa API: er låter dig hantera säkerhets nyckel lager för dina Spaces. Dessa butiker kan innehålla en samling säkerhets nycklar och gör att du enkelt kan hämta de senaste giltiga nycklarna.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): dessa API: er gör att du kan koppla användare till dina Spaces för att hitta dessa personer när det behövs. 

* [/system](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): dessa API: er gör att du kan hantera systemomfattande inställningar, till exempel standard typer av utrymmen och sensorer. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): dessa API: er gör att du kan koppla roller till entiteter som användar-ID, användardefinierat funktions-ID osv. Varje roll tilldelning innehåller ID: t för entiteten som ska associeras, entitetstypen, ID: t för den roll som ska associeras, klient-ID och en sökväg som definierar den övre gränsen för den resurs som entiteten har åtkomst till med den associationen. Mer information finns i [den här artikeln](security-role-based-access-control.md) .


## <a name="api-navigation"></a>API-navigering

De digitala dubbla API: erna stöder filtrering och navigering i hela det spatiala diagrammet med följande parametrar:

- **spaceId**: API: n filtrerar resultaten med angivet UTRYMMES-ID. Dessutom är den booleska flaggan **useParentSpace** tillämplig på [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) -API: erna, vilket indikerar att det tilldelade Utrymmes-ID: t refererar till det överordnade utrymmet i stället för det aktuella utrymmet. 

- **minLevel** och **maxLevel**: rot utrymmen anses vara på nivå 1. Blank steg med överordnat utrymme på nivå *n* finns på nivå *n + 1*. Med de här värdena inställda kan du filtrera resultaten på vissa nivåer. Detta är inkluderade värden när de anges. Enheter, sensorer och andra objekt anses vara på samma nivå som närmaste utrymme. Om du vill hämta alla objekt på en angiven nivå anger du både **minLevel** och **maxLevel** till samma värde.

- **minRelative** och **maxRelative**: när dessa filter anges är motsvarande nivå i förhållande till nivån för angivet Utrymmes-ID:
   - Relativ nivå *0* är på samma nivå som angivet UTRYMMES-ID.
   - Relativ nivå *1* representerar blank steg på samma nivå som underordnade till angivet UTRYMMES-ID. Relativ nivå *n* representerar blank steg som är lägre än det angivna utrymmet med *n* nivåer.
   - Relativ nivå *– 1* representerar blank steg på samma nivå som det överordnade utrymmet för det angivna utrymmet.

- **Bläddra**: gör att du kan bläddra i endera riktning från ett visst UTRYMMES-ID, enligt följande värden.
   - **Inget**: det här standardvärdet filtreras till angivet UTRYMMES-ID.
   - **Ned**: det här filtret med angivet UTRYMMES-ID och dess underordnade. 
   - **Upp**: detta filter med angivet UTRYMMES-ID och dess överordnade objekt. 
   - **Span**: detta filtrerar en vågrät del av den spatiala grafen på samma nivå som angivet UTRYMMES-ID. Detta kräver att antingen **minRelative** eller **maxRelative** har angetts till true. 


### <a name="examples"></a>Exempel

I följande lista visas några exempel på navigering genom [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) -API: er. Observera att plats hållaren `YOUR_MANAGEMENT_API_URL` refererar till URI för digitala dubbla API: er i formatet `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, där `YOUR_INSTANCE_NAME` är namnet på din Azure Digital-instansen och `YOUR_LOCATION` är den region där instansen finns.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` returnerar alla enheter som är kopplade till rot utrymmen.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` returnerar alla enheter som är kopplade till utrymmen med nivåerna 2, 3 eller 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` returnerar alla enheter som är direkt anslutna till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` returnerar alla enheter som är kopplade till mySpaceId eller en av dess underordnade.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` returnerar alla enheter som är kopplade till underordnade mySpaceId, förutom mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` returnerar alla enheter som är kopplade till omedelbara underordnade till mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` returnerar alla enheter som är kopplade till någon av de överordnade mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` returnerar alla enheter som är kopplade till underordnade mySpaceId som är på nivån mindre än eller lika med 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` returnerar alla enheter som är kopplade till utrymmen på samma nivå som mySpaceId.


## <a name="odata-support"></a>OData-stöd

De flesta API: er som returnerar samlingar, t. ex. ett GET-anrop på/Spaces, stöder följande delmängd av alternativen för allmän [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) system-fråga:  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** – om du tänker Visa hela samlingen bör du begära den som en hel uppsättning i ett enda samtal och sedan utföra växling i ditt program. 

> [!NOTE]
> Vissa OData-alternativ (till exempel frågealternativ **$Count**, **$Expand**och **$search**) stöds inte för närvarande.

### <a name="examples"></a>Exempel

I följande lista beskrivs flera frågor med giltig OData-syntax:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Nästa steg

Om du vill veta några vanliga API-frågeuttryck läser [du så här frågar du Azure Digitals dubbla API: er för vanliga uppgifter](./how-to-query-common-apis.md).

Om du vill veta mer om dina API-slutpunkter läser [du så här använder du digitala dubbla Swagger](./how-to-use-swagger.md).

Om du vill granska OData-syntaxen och tillgängliga jämförelse operatorer läser du [OData-jämförelse operatörer i Azure kognitiv sökning](../search/search-query-odata-comparison-operators.md).
