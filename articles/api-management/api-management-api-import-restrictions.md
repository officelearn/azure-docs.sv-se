---
title: Begränsningar och kända problem i Azure API Management API import | Microsoft Docs
description: Information om kända problem och begränsningar för import till Azure API Management med hjälp av de öppna API, WSDL eller WADL format.
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
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: ab4bc4024248675c6325159b5507add1274addc9
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/05/2018
---
# <a name="api-import-restrictions-and-known-issues"></a>API-begränsningar för import och kända problem
## <a name="about-this-list"></a>Om den här listan
När du importerar en API kan du stöta på vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. Den här artikeln dokument, ordnade efter Importformat för API: et.

## <a name="open-api"> </a>Öppna API/Swagger
Om felmeddelanden importera dokumentet öppna API: et, se till att du har validerat den - antingen med hjälp av designern i Azure-portalen (Design - frontend - öppna API specifikation-redigeraren), eller en tredjeparts-verktyget som <a href="http://www.swagger.io">Swagger Editor</a>.

* Endast JSON-format för OpenAPI stöds.
* Scheman som refereras till med hjälp av **$ref** egenskaper får inte innehålla andra **$ref** egenskaper.
* **$ref** pekare kan inte referera till externa filer.
* **x-ms-sökvägar** och **x servrar** är de enda tillägg som stöds.
* Anpassade tillägg ignoreras för import och sparas eller bevaras för export.

> [!IMPORTANT]
> Se den här [dokument](https://blogs.msdn.microsoft.com/apimanagement/2018/03/28/important-changes-to-openapi-import-and-export/) viktig information och tips som rör OpenAPI import.

## <a name="wsdl"> </a>WSDL
WSDL-filer används för att generera API: er för SOAP-direkt eller fungera som serverdelen av ett SOAP-REST-API.

* **WSDL: import** -APIM stöder för närvarande inte API: er med det här attributet. Kunder bör samman importerade element till ett dokument.
* **Meddelanden med flera delar** APIM stöder för närvarande inte dessa typer av meddelanden.
* **WCF-wsHttpBinding** SOAP-tjänster som skapats med Windows Communication Foundation ska använda basicHttpBinding - wsHttpBinding stöds inte.
* **MTOM** tjänster som använder MTOM <em>kan</em> fungerar. Officiell stöd erbjuds inte just nu.
* **Rekursion** typer som är definierade rekursivt (till exempel finns i en matris med själva) stöds inte av APIM.

## <a name="wadl"> </a>WADL
Det finns inga kända problem i WADL import.
