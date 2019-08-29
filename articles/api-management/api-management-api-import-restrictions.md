---
title: Begränsningar och kända problem i Azure API Management API-import | Microsoft Docs
description: Information om kända problem och begränsningar vid import till Azure API Management med hjälp av Open API-, WSDL-eller WADL-format.
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
ms.date: 06/26/2019
ms.author: apimpm
ms.openlocfilehash: 1a1196da62b9d28280150dd1ddf1582db64a93d4
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073782"
---
# <a name="api-import-restrictions-and-known-issues"></a>API import-begränsningar och kända problem

## <a name="about-this-list"></a>Om den här listan

När du importerar ett API kan du komma över vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. I den här artikeln dokumenteras dessa, sorterade efter import formatet för API: et.

## <a name="open-api"> </a>Openapi/Swagger

Om du får fel när du importerar OpenAPI-dokumentet ser du till att du har validerat det i förväg. Du kan göra detta antingen med hjälp av designern i Azure Portal (design-frontend-OpenAPI specifikation) eller med ett verktyg från tredje part, till exempel <a href="https://editor.swagger.io">Swagger Editor</a>.

### <a name="open-api-general"> </a>Allmän

-   Obligatoriska parametrar i både sökväg och fråga måste ha unika namn. (I OpenAPI måste parameter namnet bara vara unikt inom en plats, till exempel sökväg, fråga, rubrik. Men i API Management tillåter vi att åtgärder diskrimineras av både sökväg och frågeparametrar (som OpenAPI inte stöder). Därför kräver vi att parameter namn är unika inom hela URL-mallen.)
-   referens punkter kan inte referera till externa filer.  **\$**
-   **x-MS – sökvägar** och **x-servrar** är de enda tillägg som stöds.
-   Anpassade tillägg ignoreras vid import och sparas eller bevaras inte för export.
-   Rekursion – API Management stöder inte definitioner som definierats rekursivt (till exempel scheman som refererar till sig själva).
-   Käll filens URL (om tillgänglig) används för relativa server-URL: er.
-   Säkerhets definitioner ignoreras.

### <a name="open-api-v2"> </a>Openapi version 2

-   Endast JSON-format stöds.

### <a name="open-api-v3"> </a>Openapi version 3

-   Om många **servrar** anges försöker API Management välja den första HTTPS-URL: en. Om det inte finns några HTTPs-URL: er – den första HTTP-URL: en. Om det inte finns några HTTP-URL: er måste serverns URL vara tom.
-   **Exempel** stöds inte, men det är ett **exempel** .
-   **Multipart/form-data** stöds inte.

> [!IMPORTANT]
> Det här [dokumentet](https://blogs.msdn.microsoft.com/apimanagement/2018/04/11/important-changes-to-openapi-import-and-export/) innehåller viktig information och tips om OpenAPI-import.

## <a name="wsdl"> </a>WSDL

WSDL-filer används för att skapa SOAP-vidarekoppling och SOAP-till-REST-API: er.

-   **SOAP** -bindningar – endast SOAP-bindningar av formatet "Document" och "literal" stöds. Det finns inget stöd för "RPC"-format eller SOAP-kodning.
-   **WSDL: import** -detta attribut stöds inte. Kunderna bör slå samman importen till ett dokument.
-   **Meddelanden med flera delar** – dessa typer av meddelanden stöds inte.
-   **WCF-wsHttpBinding** – SOAP-tjänster som skapats med Windows Communication Foundation ska använda BasicHttpBinding-wsHttpBinding stöds inte.
-   **MTOM** -tjänster som använder MTOM <em>kan</em> fungera. Statsstöd erbjuds inte för tillfället.
-   Rekursion – typer som definieras rekursivt (till exempel för att referera till en matris) stöds inte av APIM.
-   **Flera namn rymder** – flera namn områden kan användas i ett schema, men endast mål namn området kan användas för att definiera meddelande delar. Andra namn områden än målet som används för att definiera andra indata-eller utdata-element bevaras inte. Även om ett sådant WSDL-dokument kan importeras, kommer att ha mål namn området för WSDL i Exportera alla meddelande delar.
-   **Matriser** – SOAP-till-rest-omvandling stöder bara omslutna matriser som visas i exemplet nedan:

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

Det finns för närvarande inga kända WADL import problem.
