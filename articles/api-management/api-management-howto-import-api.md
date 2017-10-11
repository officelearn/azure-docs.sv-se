---
title: Importera en API i Azure API Management | Microsoft Docs
description: "Lär dig hur du importerar en API och dess åtgärder till Azure API Management."
services: api-management
documentationcenter: 
author: steved0x
manager: erikre
editor: 
ms.assetid: 40398b0a-ac2c-43f0-89e1-07e4abbf502f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: apimpm
ms.openlocfilehash: c851b88fc1067e65044266d07775717c028e75d9
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="how-to-import-the-definition-of-an-api-with-operations-in-azure-api-management"></a>Så här importerar du definitionen av ett API med åtgärder i Azure API Management
Nya API: er kan skapas i API-hantering och åtgärder läggas till manuellt eller API: et kan importeras tillsammans med operations i ett steg.

API: er och deras verksamhet kan importeras med följande format.

* WADL
* Swagger

Den här guiden visar hur skapa ett nytt API och importera sina åtgärder i ett steg. Mer information om att skapa en API manuellt och lägga till operations finns [hur du skapar API: er] [ How to create APIs] och [lägga till åtgärder i en API][How to add operations to an API].

## <a name="import-api"> </a>Importera ett API
API: er skapas och konfigureras i publisher-portalen. För att komma åt publisher-portalen klickar du på **Publisher portal** i Azure Portal för API Management-tjänsten. Om du inte har skapat en API Management-tjänstinstans än läser du [Skapa en API Management-tjänstinstans][Create an API Management service instance] i självstudiekursen [Komma igång med Azure API Management][Get started with Azure API Management].

![Utgivarportalen][api-management-management-console]

Klicka på **API: er** från den **API Management** menyn till vänster och klicka sedan på **importera API**.

![Importera API][api-management-import-apis]

Den **Import API** fönstret har tre flikar som motsvarar de tre sätt att tillhandahålla API-specifikationen.

* **Från Urklipp** kan du klistra in API-specifikationen i textrutan avsedda.
* **Från filen** kan du bläddra till och välj den fil som innehåller API-specifikationen.
* **Från URL** kan du ange URL: en för API-specifikationen.

![Importera API-format][api-management-import-api-clipboard]

När du har angett i API-specifikationen Använd alternativknapparna till höger för att ange formatet specifikation. Följande format stöds.

* WADL
* Swagger

Ange en **URL för Web API-suffix**. Detta läggs till en bas-URL för API management-tjänsten. Bas som URL: en är gemensamma för alla API: er som finns på varje instans av en API Management-tjänsten. API Management särskiljer API: er av deras suffix och därmed suffixet måste vara unikt för varje API i en specifik instans för API management-tjänsten.

När alla värden anges, klickar du på **spara** att skapa API: N och associerade åtgärder. 

> [!NOTE]
> En genomgång av import av en enkel kalkylator API i Swagger-format finns [hantera din första API i Azure API Management](api-management-get-started.md).
> 
> 

## <a name="export-api"></a> Exportera en API
Du kan exportera definitionerna av dina API: er från portalen publisher förutom Importera nya API: er. Om du vill göra det klickar du på **exportera API** från den **fliken Sammanfattning** av din **API**.

![Exportera API][api-management-export-api]

API: er kan exporteras med WADL eller Swagger. Välj önskat format, klicka på **spara**, och välj den plats där du vill spara filen.

![Exportera API-format][api-management-export-api-format]

## <a name="next-steps"> </a>Nästa steg
När en API som har skapats och åtgärder som importeras, kan du granska och konfigurera ytterligare inställningar, Lägg till API: et för en produkt och publicera den så att den är tillgänglig för utvecklare. Mer information finns i följande guider.

* [Hur du konfigurerar API-inställningar][How to configure API settings]
* [Hur du skapar och publicerar en produkt][How to create and publish a product]

[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to create APIs]: api-management-howto-create-apis.md
[How to configure API settings]: api-management-howto-create-apis.md#configure-api-settings
