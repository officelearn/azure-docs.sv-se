---
title: Azure API Management-Sidkontrollerna | Microsoft Docs
description: "Läs mer om tillgängliga för användning i developer portal mallar i Azure API Management Sidkontrollerna."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 03e0ac8d-64ff-4e9a-b029-d7be14fb31e3
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 6aa7a25a9addceee78abe027fb3a19351940464e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-api-management-page-controls"></a>Kontroller Azure API Management
Azure API Management innehåller följande kontroller för användning i developer portal mallar.  
  
 Om du vill använda en kontroll måste du placera det i önskad plats i mallen developer portal. Vissa kontroller, till exempel den [app åtgärder](#app-actions) styra har parametrar, som visas i följande exempel.  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Värden för parametrar skickas i som en del av datamodellen för mallen. I de flesta fall, du kan bara klistra in i den angivna exemplet för varje kontroll att fungera korrekt. Du kan se avsnittet data modellen för varje mall som en kontroll kan användas för mer information om parametervärden.  
  
 Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  
  
## <a name="developer-portal-template-page-controls"></a>Developer portal mallen kontroller  
  
-   [App-åtgärder](#app-actions)  
  
-   [Basic-inloggning](#basic-signin)  
  
-   [växlingsfil-kontroll](#paging-control)  
  
-   [providers](#providers)  
  
-   [Sök-kontroll](#search-control)  
  
-   [registrering](#sign-up)  
  
-   [prenumerera på knappen](#subscribe-button)  
  
-   [Avbryt prenumeration](#subscription-cancel)  
  
##  <a name="app-actions"></a>App-åtgärder  
 Den `app-actions` kontrollen innehåller ett användargränssnitt för att interagera med program på användarprofilsidan i developer-portalen.  
  
 ![App- &#45; åtgärder kontrollen](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app åtgärder kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|appId|Id för programmet.|  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `app-actions` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Program](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>Basic-inloggning  
 Den `basic-signin` kontrollen innehåller en kontroll för att samla in användare logga in informationen i inloggningssidan i developer-portalen.  
  
 ![grundläggande &#45; inloggning kontrollen](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic signin-kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `basic-signin` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>växlingsfil-kontroll  
 Den `paging-control` tillhandahåller funktioner för sidindelning på developer portal sidor som visar en lista med objekt.  
  
 ![växling kontrollen](./media/api-management-page-controls/APIM-paging-control.png "APIM sidindelning kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `paging-control` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Problemet lista](api-management-issue-templates.md#IssueList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>providers  
 Den `providers` kontrollen innehåller en kontroll för val av autentiseringsproviders i inloggningssidan i developer-portalen.  
  
 ![providers kontrollen](./media/api-management-page-controls/APIM-providers-control.png "APIM providers kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `providers` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>Sök-kontroll  
 Den `search-control` tillhandahåller funktioner för sökning på developer portal sidor som visar en lista med objekt.  
  
 ![Sök kontrollen](./media/api-management-page-controls/APIM-search-control.png "APIM sökkontrollen")  
  
### <a name="usage"></a>Användning  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `search-control` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>registrering  
 Den `sign-up` kontrollen innehåller en kontroll för att samla in användarens profilinformation i sidan registrering i developer-portalen.  
  
 ![signera &#45; upp kontrollen](./media/api-management-page-controls/APIM-sign-up-control.png "APIM anmälan kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `sign-up` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Registrera sig](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>prenumerera på knappen  
 Den `subscribe-button` innehåller en kontroll för att prenumerera på en användare för en produkt.  
  
 ![prenumerera &#45; knappkontrollen](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM prenumerera knappen kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `subscribe-button` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Produkten](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>Avbryt prenumeration  
 Den `subscription-cancel` kontrollen innehåller en kontroll för att avbryta en prenumeration på en produkt i användarprofilsidan i developer-portalen.  
  
 ![prenumerationen &#45; Avbryt kontrollen](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM Avbryt prenumeration kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|subscriptionId|Id för att avbryta prenumerationen.|  
|CancelUrl|Prenumerationen avbryta URL.|  
  
### <a name="developer-portal-templates"></a>Developer portal mallar  
 Den `subscription-cancel` kontroll kan användas i följande mallar för developer-portalen.  
  
-   [Produkten](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns [hur du anpassar API Management developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).