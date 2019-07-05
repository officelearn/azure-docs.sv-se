---
title: Begränsningar och kända problem i Azure API Management API import | Microsoft Docs
description: Information om kända problem och begränsningar för import till Azure API Management med hjälp av öppna API, WSDL eller WADL-format.
services: api-management
documentationcenter: ''
author: vladvino
manager: vlvinogr
editor: ''
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/26/2019
ms.author: apipm
ms.openlocfilehash: 7f7c37843ccaf78c7b7e6ec7a959106df45053d6
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461612"
---
# <a name="api-import-restrictions-and-known-issues"></a>API-importrestriktioner och kända problem

## <a name="about-this-list"></a>Om den här listan

När du importerar ett API, kan du stöta på vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. Den här artikeln dokument dessa, ordnade efter importformatet för API: et.

## <a name="open-api"> </a>OpenAPI/Swagger

Om du har fått fel importera OpenAPI-dokument, kontrollera att du har verifierat den i förväg. Du kan göra det antingen med hjälp av designern i Azure portal (Design - Front End - OpenAPI-Specifikationsredigerare) eller med ett verktyg från tredje part som <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="open-api-general"> </a>Allmänt

-   Obligatoriska parametrar i både sökvägen och frågan måste ha unika namn. (I OpenAPI ett parameternamn endast måste vara unika inom en plats, till exempel sökvägen, fråga, rubrik. I API Management kan vi dock tillåta åtgärder för att vara discriminated efter både sökvägen och frågan parametrar (som OpenAPI inte stöder). That's varför vi kräver att parametern som ska vara unikt inom mallen hela URL: en.)
-   **\$REF** pekare kan inte referera till de externa filerna.
-   **x-ms-sökvägar** och **x-servers** är de enda tillägg som stöds.
-   Anpassade tillägg ignoreras för import och inte sparas eller bevaras för export.
-   **Rekursion** -API Management stöder inte definitioner som definierats rekursivt (till exempel scheman som refererar till sig själva).
-   Käll-fil-URL (om tillgängligt) tillämpas på relativ URL.

### <a name="open-api-v2"> </a>OpenAPI version 2

-   JSON-format stöds.

### <a name="open-api-v3"> </a>OpenAPI version 3

-   Om många **servrar** anges, API Management kommer att försöka att välja den första HTTPs-URL. Om det inte finns några webbadresser för HTTPs - första HTTP-URL. Om det inte finns några HTTP-URL: er - serverns URL är tom.
-   **Exempel** stöds inte, men **exempel** är.
-   **Multipart/form-data** stöds inte.

> [!IMPORTANT]
> Det här [dokumentet](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) innehåller viktig information och tips om OpenAPI-import.

## <a name="wsdl"> </a>WSDL

WSDL-filerna används för att skapa SOAP-direkt och SOAP till REST API: er.

-   **SOAP-bindningar** -endast SOAP-bindningar style ”dokument” och ”literal”-kodning stöds. Det finns inget stöd för ”RPC”-format eller SOAP-kodning.
-   **Platsattribut** – det här attributet stöds inte. Kunder bör samman importen till ett dokument.
-   **Meddelanden med flera delar** -dessa typer av meddelanden stöds inte.
-   **WCF-wsHttpBinding** -SOAP-tjänster som skapats med Windows Communication Foundation bör använda basicHttpBinding – wsHttpBinding stöds inte.
-   **MTOM** - tjänster med hjälp av MTOM <em>kan</em> fungerar. Officiella support erbjuds inte just nu.
-   **Rekursion** -typer som är definierade rekursivt (till exempel finns i en matris med själva) stöds inte av APIM.
-   **Flera namnområden** – flera namnområden kan användas i ett schema, men endast målnamnområdet kan användas för att definiera meddelandedelar. Namnområden än målet som används för att definiera andra inkommande eller utgående element bevaras inte. Även om en sådan WSDL-dokumentet kan importeras i exporten har alla meddelandedelar målnamnområdet för WSDL.
-   **Matriser** – SOAP till REST-transformering stöder bara inpackad matriser visas i exemplet nedan:

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

## <a name="wadl"> </a>WADL

Det finns för närvarande inga kända problem i WADL import.
