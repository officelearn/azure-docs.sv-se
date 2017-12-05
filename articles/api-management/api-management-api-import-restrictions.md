---
title: "Begränsningar och kända problem i Azure API Management API import | Microsoft Docs"
description: "Information om kända problem och begränsningar för import till Azure API Management med hjälp av de öppna API, WSDL eller WADL format."
services: api-management
documentationcenter: 
author: vladvino
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/29/2017
ms.author: apipm
ms.openlocfilehash: 758babce3ed387ed4864f1934650cf701bda788f
ms.sourcegitcommit: b854df4fc66c73ba1dd141740a2b348de3e1e028
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API-begränsningar för import och kända problem
## <a name="about-this-list"></a>Om den här listan
När du importerar en API kan du stöta på vissa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. Den här artikeln dokument, ordnade efter Importformat för API: et.

## <a name="open-api"></a>Öppna API/Swagger
Om felmeddelanden importera dokumentet öppna API: et, se till att du har validerat den - antingen med hjälp av designern i Azure-portalen (Design - frontend - öppna API specifikation-redigeraren), eller en tredjeparts-verktyget som <a href="http://www.swagger.io">Swagger Editor</a>.

* **Värdnamn** APIM kräver ett host name-attribut.
* **Basera sökvägen** APIM kräver ett bassökväg-attribut.
* **Scheman** APIM kräver en matris för schemat. 

## <a name="wsdl"></a>WSDL
WSDL-filer används för att generera API: er för SOAP-direkt eller fungera som serverdelen av ett SOAP-REST-API.

* **WSDL: import** -APIM stöder för närvarande inte API: er med det här attributet. Kunder bör samman importerade element till ett dokument.
* **Meddelanden med flera delar** APIM stöder för närvarande inte dessa typer av meddelanden.
* **WCF-wsHttpBinding** SOAP-tjänster som skapats med Windows Communication Foundation ska använda basicHttpBinding - wsHttpBinding stöds inte.
* **MTOM** tjänster som använder MTOM <em>kan</em> fungerar. Officiell stöd erbjuds inte just nu.
* **Rekursion** typer som är definierade rekursivt (till exempel finns i en matris med själva) stöds inte av APIM.

## <a name="wadl"></a>WADL
Det finns inga kända problem i WADL import.
