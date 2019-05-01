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
ms.openlocfilehash: c0f8da779ca656cf357c418b8766a53307643695
ms.sourcegitcommit: 37343b814fe3c95f8c10defac7b876759d6752c3
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/24/2019
ms.locfileid: "63764327"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Management-principer. Information om att lägga till och konfigurerar principer finns i [principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).  

##  <a name="AuthenticationPolicies"></a> Autentiseringsprinciper  
  
-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en serverdelstjänst som använder grundläggande autentisering.  
  
-   [Autentisera med klientcertifikatet](api-management-authentication-policies.md#ClientCertificate) -autentisera med en serverdelstjänst som använder klientcertifikat.  

-   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) -autentisera med den [hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för API Management-tjänsten.  
  
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
|password|Anger lösenordet för grundläggande autentiseringsuppgift.|Ja|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
  
-   **Princip för omfång:** API  
  
##  <a name="ClientCertificate"></a> Autentisering med klientcertifikat  
 Använd den `authentication-certificate` princip för att autentisera med en serverdelstjänst som använder klientcertifikat. Certifikatet måste vara [installerats i API Management](https://go.microsoft.com/fwlink/?LinkID=511599) första och identifieras av dess tumavtryck.  
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>  
```  
  
### <a name="examples"></a>Exempel  
  
I det här exemplet klienten identifieras certifikatet med dess tumavtryck.
```xml  
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />  
``` 
I det här exemplet identifieras klientcertifikat efter resursnamn.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|certifikat för serverautentisering|Rotelement.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|thumbprint|Tumavtryck för klientcertifikatet.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Gäller inte|  
|certifikat-id|Resursnamnet certifikat.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Gäller inte|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
  
-   **Princip för omfång:** API  

##  <a name="ManagedIdentity"></a> Autentisera med hanterad identitet  
 Använd den `authentication-managed-identity` princip för att autentisera med en serverdelstjänst som använder hanterade identiteten för API Management-tjänsten. Den här principen används effektivt hanterad identitet för att hämta en åtkomsttoken från Azure Active Directory för att komma åt den angivna resursen. 
  
### <a name="policy-statement"></a>Principframställning  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exempel  
  
```xml  
<authentication-managed-identity resource="https://graph.windows.net" output-token-variable-name="test-access-token" ignore-error="true" /> 
```  
  
### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|authentication-managed-identity |Rotelement.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Standard|  
|----------|-----------------|--------------|-------------|  
|resurs|sträng. App-ID URI för webb-mål-API (säker resurs) i Azure Active Directory.|Ja|Gäller inte|  
|output-token-variable-name|sträng. Namnet på sammanhangsvariabeln som ska ta emot token-värde som en objekttyp `string`.|Nej|Gäller inte|  
|ignore-error|Booleskt värde. Om inställd `true`, princip pipelinen fortsätter att köras även om en åtkomst-token inte hämtas.|Nej|false|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip-avsnitt:** inkommande  
  
-   **Princip-scope:** globala, produkt, API, igen  

## <a name="next-steps"></a>Nästa steg
Arbeta med principer, Läs mer:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Principreferens för](api-management-policy-reference.md) för en fullständig lista över principrapporter och deras inställningar
+ [Princip-exempel](policy-samples.md)   
