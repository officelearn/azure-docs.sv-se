---
title: Azure API Management autentiseringsprinciper | Microsoft Docs
description: "Läs mer om autentiseringsprinciper som är tillgängliga för användning i Azure API Management."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/09/2017
ms.author: apimpm
ms.openlocfilehash: 2907c1e4e39f975b4dc4e9382d7258c5d56dbbdc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="api-management-authentication-policies"></a>Principer för autentisering av API-hantering
Det här avsnittet innehåller en referens för följande API Management-principer. Mer information om att lägga till och konfigurera principer finns [principer i API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AuthenticationPolicies"></a>Principer för autentisering  
  
-   [Autentisera med grundläggande](api-management-authentication-policies.md#Basic) -autentisering med en serverdelstjänst som använder grundläggande autentisering.  
  
-   [Autentisera med klientcertifikatet](api-management-authentication-policies.md#ClientCertificate) -autentisering med en serverdelstjänst som använder klientcertifikat.  
  
##  <a name="Basic"></a>Autentisera med Basic  
 Använd den `authentication-basic` princip för att autentisera med en serverdelstjänst som använder grundläggande autentisering. Den här principen anger effektivt HTTP Authorization-huvud till värdet som motsvarar de autentiseringsuppgifter som anges i principen.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<authentication-basic username="username" password="password" />  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<authentication-basic username="testuser" password="testpassword" />  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|grundläggande autentisering|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|användarnamn|Anger användarnamnet för grundläggande autentiseringsuppgifter.|Ja|Saknas|  
|lösenord|Anger lösenordet för autentiseringsuppgifterna för grundläggande.|Ja|Saknas|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
  
-   **Princip för scope:** API  
  
##  <a name="ClientCertificate"></a>Autentisera med klientcertifikatet  
 Använd den `authentication-certificate` princip för att autentisera med en serverdelstjänst som använder klientcertifikat. Certifikatet måste vara [installerats i API Management](http://go.microsoft.com/fwlink/?LinkID=511599) första och identifieras av dess tumavtryck.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<authentication-certificate thumbprint="thumbprint" />  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<authentication-certificate thumbprint="....." />  
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|certifikat för serverautentisering|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|tumavtrycket|Tumavtryck för klientcertifikatet.|Ja|Saknas|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Avsnitt i princip:** inkommande  
  
-   **Princip för scope:** API  
  

## <a name="next-steps"></a>Nästa steg
Arbeta med principer för mer information finns i [principer i API Management](api-management-howto-policies.md).  