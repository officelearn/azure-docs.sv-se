---
title: Sidkontroller för Azure API Management | Microsoft-dokument
description: Lär dig mer om de sidkontroller som är tillgängliga för användning i utvecklarportalmallar i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: ff608cac9786ebc0d3908ce4d88fb501e621be6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244022"
---
# <a name="azure-api-management-page-controls"></a>Sidkontroller för Azure API Management
Azure API Management innehåller följande kontroller för användning i utvecklarportalmallarna.  
  
Om du vill använda en kontroll placerar du den på önskad plats i utvecklarportalmallen. Vissa kontroller, till exempel kontrollen för [appåtgärder,](#app-actions) har parametrar, som visas i följande exempel:  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
Värdena för parametrarna skickas in som en del av datamodellen för mallen. I de flesta fall kan du helt enkelt klistra in i det medföljande exemplet för varje kontroll för att det ska fungera korrekt. Mer information om parametervärdena finns i avsnittet datamodell för varje mall där en kontroll kan användas.  

Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](https://azure.microsoft.com/documentation/articles/api-management-developer-portal-templates/)för API Management med hjälp av mallar .  

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]
  
## <a name="developer-portal-template-page-controls"></a>Sidkontroller för utvecklarportalmall  
  
-   [app-åtgärder](#app-actions)  
-   [grundläggande signin](#basic-signin)  
-   [personkontroll](#paging-control)  
-   [Leverantörer](#providers)  
-   [sökkontroll](#search-control)  
-   [Registrera dig](#sign-up)  
-   [knappen prenumerera](#subscribe-button)  
-   [prenumerationsuppst)](#subscription-cancel)  
  
##  <a name="app-actions"></a><a name="app-actions"></a>app-åtgärder  
 Kontrollen `app-actions` tillhandahåller ett användargränssnitt för att interagera med program på användarprofilsidan i utvecklarportalen.  
  
 ![app&#45;åtgärdskontroll](./media/api-management-page-controls/APIM-app-actions-control.png "APIM-kontroll för appåtgärder")  
  
### <a name="usage"></a>Användning  
  
```xml  
<app-actions params="{ appId: '{{app.id}}' }"></app-actions>  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|appId|ID för programmet.|  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `app-actions` kan användas i följande utvecklarportalmallar:  
  
-   [Program](api-management-user-profile-templates.md#Applications)  
  
##  <a name="basic-signin"></a><a name="basic-signin"></a>grundläggande signin  
 Kontrollen `basic-signin` ger en kontroll för att samla in information om användare i inloggningssidan i utvecklarportalen.  
  
 ![grundläggande&#45;signin kontroll](./media/api-management-page-controls/APIM-basic-signin-control.png "APIM grundläggande signin kontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<basic-SignIn></basic-SignIn>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `basic-signin` kan användas i följande utvecklarportalmallar:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="paging-control"></a><a name="paging-control"></a>personkontroll  
 Den `paging-control` ger växlingsfunktioner på utvecklarportalsidor som visar en lista med objekt.  
  
 ![söka kontroll](./media/api-management-page-controls/APIM-paging-control.png "APIM-växlingskontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<paging-control></paging-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `paging-control` kan användas i följande utvecklarportalmallar:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Lista över problem](api-management-issue-templates.md#IssueList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="providers"></a><a name="providers"></a>Leverantörer  
 Kontrollen `providers` ger en kontroll för val av autentiseringsleverantörer på inloggningssidan i utvecklarportalen.  
  
 ![leverantörer kontrollerar](./media/api-management-page-controls/APIM-providers-control.png "APIM-leverantörer kontrollerar")  
  
### <a name="usage"></a>Användning  
  
```xml  
<providers></providers>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `providers` kan användas i följande utvecklarportalmallar:  
  
-   [Logga in](api-management-page-templates.md#SignIn)  
  
##  <a name="search-control"></a><a name="search-control"></a>sökkontroll  
 Den `search-control` ger sökfunktioner på utvecklarportalsidor som visar en lista med objekt.  
  
 ![sökkontroll](./media/api-management-page-controls/APIM-search-control.png "APIM-sökkontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<search-control></search-control>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `search-control` kan användas i följande utvecklarportalmallar:  
  
-   [API-lista](api-management-api-templates.md#APIList)  
  
-   [Produktlista](api-management-product-templates.md#ProductList)  
  
##  <a name="sign-up"></a><a name="sign-up"></a>Registrera dig  
 Kontrollen `sign-up` ger en kontroll för att samla in användarprofilinformation på registreringssidan i utvecklarportalen.  
  
 ![signera&#45;upp kontroll](./media/api-management-page-controls/APIM-sign-up-control.png "APIM-registreringskontroll")  
  
### <a name="usage"></a>Användning  
  
```xml  
<sign-up></sign-up>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `sign-up` kan användas i följande utvecklarportalmallar:  
  
-   [Registrera sig](api-management-page-templates.md#SignUp)  
  
##  <a name="subscribe-button"></a><a name="subscribe-button"></a>knappen prenumerera  
 Den `subscribe-button` ger en kontroll för att prenumerera en användare på en produkt.  
  
 ![prenumerera&#45;knappkontroll](./media/api-management-page-controls/APIM-subscribe-button-control.png "APIM-kontroll för a-knappen i APIM")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscribe-button></subscribe-button>  
```  
  
### <a name="parameters"></a>Parametrar  
 Inga.  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `subscribe-button` kan användas i följande utvecklarportalmallar:  
  
-   [Produkt](api-management-product-templates.md#Product)  
  
##  <a name="subscription-cancel"></a><a name="subscription-cancel"></a>prenumerationsuppst)  
 Kontrollen `subscription-cancel` ger en kontroll för att avbryta en prenumeration på en produkt på användarprofilsidan i utvecklarportalen.  
  
 ![prenumeration&#45;avbryta kontrollen](./media/api-management-page-controls/APIM-subscription-cancel-control.png "APIM-kontroll för prenumerationsuppakning")  
  
### <a name="usage"></a>Användning  
  
```xml  
<subscription-cancel params="{ subscriptionId: '{{subscription.id}}', cancelUrl: '{{subscription.cancelUrl}}' }">  
</subscription-cancel>  
  
```  
  
### <a name="parameters"></a>Parametrar  
  
|Parameter|Beskrivning|  
|---------------|-----------------|  
|subscriptionId|ID:et för prenumerationen som ska avbrytas.|  
|avbrytUrl|Prenumerationen avbryter URL:en.|  
  
### <a name="developer-portal-templates"></a>Mallar för utvecklarportal  
 Kontrollen `subscription-cancel` kan användas i följande utvecklarportalmallar:  
  
-   [Produkt](api-management-product-templates.md#Product)

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med mallar finns i [Så här anpassar du utvecklarportalen](api-management-developer-portal-templates.md)för API Management med hjälp av mallar .
