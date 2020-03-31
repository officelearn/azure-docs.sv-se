---
title: Stöd för begränsningar och information om API-format
titleSuffix: Azure API Management
description: Information om kända problem och begränsningar för Open API-, WSDL- och WADL-format stöder i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/02/2020
ms.author: apimpm
ms.openlocfilehash: 61d43addfdf9008cb7aa8a073dcf3bb702cb55f1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76513379"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importbegränsningar och kända problem

## <a name="about-this-list"></a>Om den här listan

När du importerar ett API kan du stöta på vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan utföra importen. Den här artikeln dokumenterar dessa begränsningar, ordnade efter API:s importformat. Den beskriver också hur OpenAPI export fungerar.

## <a name="openapiswagger-import-limitations"></a><a name="open-api"> </a>Importbegränsningar för OpenAPI/Swagger

Om du får fel när du importerar OpenAPI-dokumentet kontrollerar du att du har validerat det i förväg. Du kan göra det antingen med hjälp av designern i Azure-portalen (Design - Front End - OpenAPI Specification Editor) eller med ett verktyg från tredje part som <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="general"></a><a name="open-api-general"> </a>Allmänt

-   Obligatoriska parametrar över både sökväg och fråga måste ha unika namn. (I OpenAPI behöver ett parameternamn bara vara unikt på en plats, till exempel sökväg, fråga, huvud. I API Management tillåter vi dock att åtgärder diskrimineras av både sökvägs- och frågeparametrar (som OpenAPI inte stöder). Därför kräver vi att parameternamnen ska vara unika i hela URL-mallen.)
-   `\$ref`pekarna inte kan referera till externa filer.
-   `x-ms-paths`och `x-servers` är de enda tillägg som stöds.
-   Anpassade tillägg ignoreras vid import och sparas eller bevaras inte för export.
-   `Recursion`- API Management stöder inte definitioner som definieras rekursivt (till exempel scheman som refererar till sig själva).
-   Källfils-URL (om tillgängligt) tillämpas på relativa serveradresser.
-   Säkerhetsdefinitioner ignoreras.
-   Infogade schemadefinitioner för API-åtgärder stöds inte. Schemadefinitioner definieras i API-scopet och kan refereras i API-åtgärder begäran eller svar scope.
-   En definierad URL-parameter måste ingå i URL-mallen.
-   `Produces`nyckelordet, som beskriver MIME-typer som returneras av ett API, stöds inte. 

### <a name="openapi-version-2"></a><a name="open-api-v2"> </a>OpenAPI version 2

-   Endast JSON-format stöds.

### <a name="openapi-version-3"></a><a name="open-api-v3"> </a>OpenAPI version 3

-   Om `servers` många anges försöker API Management att välja den första HTTPs-URL:en. Om det inte finns några HTTPs-url:er – den första HTTP-URL:en. Om det inte finns några HTTP-url:er är server-URL:en tom.
-   `Examples`stöds inte, men `example` är.

## <a name="openapi-import-update-and-export-mechanisms"></a>OpenAPI-import-, uppdaterings- och exportmekanismer

### <a name="add-new-api-via-openapi-import"></a>Lägg till nytt API via OpenAPI-import

För varje åtgärd som finns i OpenAPI-dokumentet skapas en ny åtgärd `operationId` `summary` med Azure-resursnamn och visningsnamn inställt på respektive. `operationId`normaliseras enligt de regler som beskrivs nedan. `summary`värdet importeras som det är och dess längd är begränsad till 300 tecken.

Om `operationId` inte anges (det vill än `null`inte finns, eller är tomt) genereras Azure-resursnamnsvärde genom `get-foo`att http-metod och sökvägsmall kombineras, till exempel .

Om `summary` anges inte (det vill än `null`inte finns, `display name` eller är `operationId`tomt) anges värdet till . Om `operationId` inget anges genereras visningsnamnsvärdet genom att http-metod- och `Get - /foo`sökvägsmallen kombineras, till exempel .

### <a name="update-an-existing-api-via-openapi-import"></a>Uppdatera ett befintligt API via OpenAPI-import

Under importen ändras befintligt API för att matcha API som beskrivs i OpenAPI-dokumentet. Varje åtgärd i OpenAPI-dokumentet matchas med `operationId` befintlig åtgärd genom att jämföra dess värde med Azure-resursnamnet för befintlig åtgärd.

Om en matchning hittas uppdateras den befintliga åtgärdens egenskaper "på plats".

Om en matchning inte hittas skapas en ny åtgärd med hjälp av de regler som beskrivs i avsnittet ovan. För varje ny åtgärd försöker importen kopiera principer från en befintlig åtgärd med samma HTTP-metod och sökvägsmall.

Alla befintliga omatchade åtgärder tas bort.

Så här gör du importen mer förutsägbar:

- Se till `operationId` att ange egenskap för varje operation.
- Avstå från `operationId` att ändra efter första importen.
- Ändra `operationId` aldrig och HTTP-metod eller sökvägsmall samtidigt.

### <a name="export-api-as-openapi"></a>Exportera API som OpenAPI

För varje åtgärd exporteras dess Azure-resursnamn som ett `operationId`och visningsnamnet exporteras som ett `summary`.
Normaliseringsregler för operationId

- Konvertera till gemener.
- Ersätt varje sekvens av icke-alfanumeriska tecken med `GET-/foo/{bar}?buzz={quix}` ett enda `get-foo-bar-buzz-quix-`streck, till exempel, kommer att omvandlas till .
- Trim streck på båda sidor, till exempel, `get-foo-bar-buzz-quix-` kommer att bli`get-foo-bar-buzz-quix`
- Trunkera för att passa 76 tecken, fyra tecken mindre än maxgränsen för ett resursnamn.
- Använd återstående fyra tecken för ett suffix för deduplicering, om det behövs, i form av `-1, -2, ..., -999`.


## <a name="wsdl"></a><a name="wsdl"> </a>WSDL

WSDL-filer används för att skapa SOAP-vidare- och SOAP-TO-REST-API:er.

-   **SOAP-bindningar** -Endast SOAP-bindningar av stil "dokument" och "bokstavlig" kodning stöds. Det finns inget stöd för "rpc" stil eller SOAP-kodning.
-   **WSDL:Import** - Det här attributet stöds inte. Kunder bör sammanfoga importen till ett dokument.
-   **Meddelanden med flera delar** – Den här typen av meddelanden stöds inte.
-   **WCF wsHttpBinding** - SOAP-tjänster som skapats med Windows Communication Foundation bör använda grundläggandeHttpBinding - wsHttpBinding stöds inte.
-   **MTOM** - Tjänster som använder MTOM <em>kan</em> fungera. Officiellt stöd erbjuds inte just nu.
-   **Rekursion** - Typer som definieras rekursivt (till exempel refererar till en matris av sig själva) stöds inte av APIM.
-   **Flera namnområden** - Flera namnområden kan användas i ett schema, men endast målnamnområdet kan användas för att definiera meddelandedelar. Andra namnområden än målet, som används för att definiera andra indata- eller utdataelement, bevaras inte. Även om ett sådant WSDL-dokument kan importeras, kommer alla meddelandedelar att ha målnamnområdet för WSDL vid export.
-   **Matriser** - SOAP-to-REST-omvandling stöder endast raderade matriser som visas i exemplet nedan:

```xml
    <complexType name="arrayTypeName">
        <sequence>
            <element name="arrayElementValue" type="arrayElementType" minOccurs="0" maxOccurs="unbounded"/>
        </sequence>
    </complexType>
    <complexType name="typeName">
        <sequence>
            <element name="element1" type="someTypeName" minOccurs="1" maxOccurs="1"/>
            <element name="element2" type="someOtherTypeName" minOccurs="0" maxOccurs="1" nillable="true"/>
            <element name="arrayElement" type="arrayTypeName" minOccurs="1" maxOccurs="1"/>
        </sequence>
    </complexType>
```

## <a name="wadl"></a><a name="wadl"> </a>WADL

För närvarande finns det inga kända WADL-importproblem.
