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
ms.date: 02/08/2017
ms.author: apipm
ms.openlocfilehash: 4cb6ad53b59b81f906a85027f4ff988bbb78706a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="api-import-restrictions-and-known-issues"></a>API-begränsningar för import och kända problem
## <a name="about-this-list"></a>Om den här listan
Medan görs allt för att säkerställa att importera din API i Azure API Management är som sömlös och problemfri som möjligt, vi göra ibland införa begränsningar eller identifiera problem som måste åtgärdas innan du kan importera. Den här artikeln beskrivs dessa organiseras av importformatet API.

## <a name="open-api"></a>Öppna API/Swagger
I allmänhet om felmeddelanden importerar öppna API-dokument, kontrollera du har validerat den - antingen med hjälp av designern i den nya Azure-portalen (Design - frontend - öppna API specifikation-redigeraren), eller med en 3 part verktyget som <a href="http://www.swagger.io">Swagger Redigeraren</a>.

* **Värdnamn** vi kräver ett host name-attribut.
* **Basera sökvägen** vi kräver att attributet bassökväg.
* **Scheman** vi kräver en matris för schemat. 

## <a name="wsdl"></a>WSDL
WSDL-filer används för att generera API: er för SOAP-direkt eller fungera som serverdelen av ett SOAP-REST-API.

* **WSDL: import** vi för närvarande stöder inte API: er med det här attributet. Kunder bör samman importerade element till ett dokument.
* **Meddelanden med flera delar** stöds inte för närvarande.
* **WCF-wsHttpBinding** SOAP-tjänster som skapats med Windows Communication Foundation ska använda basicHttpBinding - wsHttpBinding stöds inte.
* **MTOM** tjänster som använder MTOM <em>kan</em> fungerar. Officiell stöd erbjuds inte just nu.
* **Rekursion** typer som är definierade rekursivt (t.ex. finns i en matris med själva) stöds inte.

## <a name="wadl"></a>WADL
Det finns inga kända problem i WADL import.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md
