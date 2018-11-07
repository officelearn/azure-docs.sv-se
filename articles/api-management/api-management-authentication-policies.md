---
title: Azure API Management-autentiseringsprinciper | Microsoft Docs
description: Läs mer om autentiseringsprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 4c4c03fffa5786bf3a50f4d2c03511f0a2de0f48
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51250959"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Autentiseringsprinciper  
  
-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en serverdelstjänst som använder grundläggande autentisering.  
  
-   [Autentisera med klientcertifikatet](api-management-authentication-policies.md#ClientCertificate) -autentisera med en serverdelstjänst som använder klientcertifikat.  
  
##  <a name="Basic"></a> Autentisera med Basic  
 Använd den `authentication-basic` princip för att autentisera med en serverdelstjänst som använder grundläggande autentisering. Den här principen anger effektivt HTTP auktoriseringsrubriken till värdet för den behörighet som angetts i principen.  
  
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
|autentisering-grundläggande|Rotelement.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|användarnamn|Anger användarnamnet för grundläggande autentiseringsuppgift.|Ja|Gäller inte|  
|lösenord|Anger lösenordet för grundläggande autentiseringsuppgift.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
  
-   **Princip-scope:** API  
  
##  <a name="ClientCertificate"></a> Autentisering med klientcertifikat  
 Använd den `authentication-certificate` princip för att autentisera med en serverdelstjänst som använder klientcertifikat. Certifikatet måste vara [installerats i API Management](https://go.microsoft.com/fwlink/?LinkID=511599) första och identifieras av dess tumavtryck.  
  
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
|certifikat för serverautentisering|Rotelement.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|tumavtryck|Tumavtryck för klientcertifikatet.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
  
-   **Princip-scope:** API  
  

## <a name="next-steps"></a>Nästa steg
Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
