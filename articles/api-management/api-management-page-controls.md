---
title: Sid kontroller för Azure API Management | Microsoft Docs
description: Lär dig mer om sid kontrollerna som är tillgängliga för användning i mallar för utvecklare i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: b720d64f5b0a5f66375083b7854658aa754db150
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "70072289"
---
# <a name="azure-api-management-page-controls"></a>Sid kontroller för Azure API Management
Azure API Management tillhandahåller följande kontroller för användning i utvecklares Portal mallar.  
  
Om du vill använda en kontroll placerar du den på önskad plats i utvecklarens Portal mall. Vissa kontroller, till exempel [app-Actions-](#app-actions) kontrollen, har parametrar, som visas i följande exempel:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Värdena för parametrarna skickas in som en del av mallens data modell. I de flesta fall kan du helt enkelt klistra in det angivna exemplet för varje kontroll för att det ska fungera korrekt. För mer information om parametervärdena, kan du se avsnittet data modell för varje mall där en kontroll kan användas.  
  
 Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Mall kontroller för utvecklarverktyg  
  
-   [app-Actions](#app-actions)  
-   [Basic-signin](#basic-signin)  
-   [sid kontroll](#paging-control)  
-   [finansiär](#providers)  
-   [Sök-kontroll](#search-control)  
-   [Registrera dig](#sign-up)  
-   [Prenumerera – knapp](#subscribe-button)  
-   [prenumeration-Avbryt](#subscription-cancel)  
  
##  <a name="app-actions"></a>app-Actions  
 @No__t_0-kontrollen tillhandahåller ett användar gränssnitt för att interagera med program på sidan användar profil i Developer-portalen.  
  
 ![kontroll&#45;av app-åtgärder](./media/api-management-page-controls/APIM-app-actions-control.png "APIM app-Actions-kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|appId|Programmets ID.|  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Program](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a>Basic-signin  
 Kontrollen `basic-signin` ger en kontroll för att samla in användar inloggnings information på inloggnings sidan i Developer-portalen.  
  
 ![grundläggande&#45;inloggnings kontroll](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM Basic-signin-kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a>sid kontroll  
 @No__t_0 tillhandahåller sid indelnings funktioner på Portal sidor för utvecklare som visar en lista med objekt.  
  
 ![sid kontroll](./media/api-management-page-controls/APIM-paging-control.png "APIM sid kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Ärende lista](api-management-issue-templates.md#IssueList)  
  
-   [Produkt lista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a>finansiär  
 Kontrollen `providers` ger en kontroll för val av autentiseringsproviders på inloggnings sidan i Developer-portalen.  
  
 ![providers-kontroll](./media/api-management-page-controls/APIM-providers-control.png "APIM-providers-kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a>Sök-kontroll  
 @No__t_0 tillhandahåller Sök funktioner på Portal sidor för utvecklare som visar en lista med objekt.  
  
 ![Sök kontroll](./media/api-management-page-controls/APIM-search-control.png "APIM Sök kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Produkt lista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a>Registrera dig  
 Kontrollen `sign-up` ger en kontroll för att samla in information om användar profiler på registrerings sidan i Developer-portalen.  
  
 ![registrerings&#45;kontroll](./media/api-management-page-controls/APIM-sign-up-control.png "APIM registrerings kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Registrera dig](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a>Prenumerera – knapp  
 @No__t_0 ger en kontroll för att prenumerera på en användare till en produkt.  
  
 ![kontroll&#45;för prenumerations knapp](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM-kontroll för prenumerant")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inget.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Momsproduktbokföringsmallar](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a>prenumeration-Avbryt  
 Kontrollen `subscription-cancel` ger en kontroll för att avbryta en prenumeration på en produkt på sidan användar profil i Developer-portalen.  
  
 ![Avbryt&#45;kontroll av prenumeration](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-prenumeration-Avbryt kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|subscriptionId|ID för prenumerationen som ska avbrytas.|  
|cancelUrl|Prenumerationen avbryter URL.|  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklare  
 @No__t_0 kontrollen kan användas i följande mallar för utvecklare:  
  
-   [Momsproduktbokföringsmallar](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [anpassa API Management Developer-portalen med hjälp av mallar](api-management-developer-portal-templates.md).