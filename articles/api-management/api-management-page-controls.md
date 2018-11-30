---
title: Azure API Management-Sidkontrollerna | Microsoft Docs
description: Läs mer om Sidkontrollerna som är tillgängliga för användning i utvecklarportalsmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/20/2017
ms.author: apimpm
ms.openlocfilehash: d87293d89e4009512494bf47f9742ea5901f909a
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/28/2018
ms.locfileid: "52445370"
---
# <a name="azure-api-management-page-controls"></a>Azure API Management kontroller
Azure API Management ger följande kontroller för användning i utvecklaren portal mallar.  
  
Om du vill använda en kontroll, placera den på önskad plats i mallen developer portal. Vissa kontroller, till exempel den [-åtgärder](#app-actions) genom har parametrar, som visas i följande exempel:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
 Värden för parametrarna skickas i som en del av datamodellen för mallen. I de flesta fall kan du helt enkelt klistra in det angivna exemplet för varje kontroll att fungera korrekt. Du kan se avsnittet data modellen för varje mall där en kontroll kan användas för mer information om parametervärden finns.  
  
 Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/).  

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Developer mall för kontroller  
  
-   [App-åtgärder](#app-actions)  
-   [Basic-inloggning](#basic-signin)  
-   [växling-kontroll](#paging-control)  
-   [Providers](#providers)  
-   [Sök-kontroll](#search-control)  
-   [Registrera dig](#sign-up)  
-   [prenumerera på knappen](#subscribe-button)  
-   [prenumeration Avbryt](#subscription-cancel)  
  
##  <a name="app-actions"></a> App-åtgärder  
 Den `app-actions` kontrollen innehåller ett användargränssnitt för att interagera med program på användarens profilsida i developer-portalen.  
  
 ![App&#45;åtgärder kontroll](./media/api-management-page-controls/APIM-app-actions-control.png "APIM-åtgärder kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|appId|Id för programmet.|  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `app-actions` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Program](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a> Basic-inloggning  
 Den `basic-signin` kontrollen innehåller en kontroll för att samla in användarinformation logga in på sidan logga in på utvecklarportalen.  
  
 ![grundläggande&#45;inloggning från kontrollen](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM basic-inloggning från kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `basic-signin` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a> växling-kontroll  
 Den `paging-control` ger växlingsfunktionen för sidor som visar en lista med objekt.  
  
 ![växling kontroll](./media/api-management-page-controls/APIM-paging-control.png "APIM sidindelning kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `paging-control` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Problemet lista](api-management-issue-templates.md#IssueList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a> Providers  
 Den `providers` kontrollen innehåller en kontroll för val av autentiseringsproviders på sidan logga in på utvecklarportalen.  
  
 ![leverantörer kontroll](./media/api-management-page-controls/APIM-providers-control.png "APIM-providers kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `providers` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a> Sök-kontroll  
 Den `search-control` ger sökfunktioner för sidor som visar en lista med objekt.  
  
 ![Sök kontroll](./media/api-management-page-controls/APIM-search-control.png "APIM sökkontrollen")  
  
### <a name="usage"></a>Användning  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `search-control` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a> Registrera dig  
 Den `sign-up` kontrollen innehåller en kontroll för att samla in information om användarprofiler i registreringssidan i developer-portalen.  
  
 ![logga&#45;upp kontrollen](./media/api-management-page-controls/APIM-sign-up-control.png "registrering APIM-kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `sign-up` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Registrera dig](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a> prenumerera på knappen  
 Den `subscribe-button` innehåller en kontroll för att prenumerera på en användare till en produkt.  
  
 ![prenumerera på&#45;knappkontroll](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM prenumerera knappen kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametrar  
 Ingen.  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `subscribe-button` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Produkten](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a> prenumeration Avbryt  
 Den `subscription-cancel` kontrollen innehåller en kontroll för att avbryta en prenumeration på en produkt i användarprofilsidan i developer-portalen.  
  
 ![prenumeration&#45;Avbryt kontroll](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-prenumeration Avbryt kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|subscriptionId|Id för att avbryta prenumerationen.|  
|cancelUrl|Prenumerationen avbryter URL: en.|  
  
### <a name="developer-portal-templates"></a>Utvecklarportalsmallar  
 Den `subscription-cancel` kontroll kan användas i följande utvecklarportalsmallar:  
  
-   [Produkten](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [hur du anpassar utvecklarportalen API Management med hjälp av mallar](api-management-developer-portal-templates.md).