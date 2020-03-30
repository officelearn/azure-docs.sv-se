---
title: Autentiseringsprinciper för Azure API Management | Microsoft-dokument
description: Lär dig mer om de autentiseringsprinciper som är tillgängliga för användning i Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 061702a7-3a78-472b-a54a-f3b1e332490d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/27/2017
ms.author: apimpm
ms.openlocfilehash: 828f738ff8923dc8194e2449f5fb0be74ef45ad7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79473565"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Management-principer. Information om hur du lägger till och konfigurerar principer finns [i Principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Autentiseringsprinciper

-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) - Autentisera med en serverdtjänst med grundläggande autentisering.

-   [Autentisera med klientcertifikat](api-management-authentication-policies.md#ClientCertificate) - Autentisera med en serverd-tjänst med klientcertifikat.

-   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) - Autentisera med den [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) för API Management-tjänsten.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Autentisera med Basic
 Använd `authentication-basic` principen för att autentisera med en serverdtjänst med grundläggande autentisering. Den här principen anger effektivt HTTP-auktoriseringshuvudet till det värde som motsvarar de autentiseringsuppgifter som anges i principen.

### <a name="policy-statement"></a>Policyuttalande

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
|autentisering-basic|Rotelementet.|Ja|

### <a name="attributes"></a>Attribut

|Namn|Beskrivning|Krävs|Default|
|----------|-----------------|--------------|-------------|
|användarnamn|Anger användarnamnet för grundläggande autentiseringsuppgifter.|Ja|Ej tillämpligt|
|password|Anger lösenordet för grundläggande autentiseringsuppgifter.|Ja|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Policyavsnitt:** inkommande

-   **Principomfattningar:** alla scope

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Autentisera med klientcertifikat
 Använd `authentication-certificate` principen för att autentisera med en serverd-tjänst med klientcertifikatet. Certifikatet måste [installeras i API Management](https://go.microsoft.com/fwlink/?LinkID=511599) först och identifieras med tumavtrycket.

### <a name="policy-statement"></a>Policyuttalande

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exempel

I det här exemplet identifieras klientcertifikatet med tumavtrycket.
```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```
I det här exemplet identifieras klientcertifikatet med resursnamnet.
```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
```  

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentiseringscertifikat|Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Default|  
|----------|-----------------|--------------|-------------|  
|Stämpel|Tumavtrycket för klientcertifikatet.|Antingen `thumbprint` `certificate-id` eller måste vara närvarande.|Ej tillämpligt|  
|certifikat-ID|Namnet på certifikatresursen.|Antingen `thumbprint` `certificate-id` eller måste vara närvarande.|Ej tillämpligt|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policyavsnitt:** inkommande  
  
-   **Principomfattningar:** alla scope  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Autentisera med hanterad identitet  
 Använd `authentication-managed-identity` principen för att autentisera med en serverd-tjänst med hjälp av den hanterade identiteten för API Management-tjänsten. Den här principen använder i huvudsak den hanterade identiteten för att hämta en åtkomsttoken från Azure Active Directory för åtkomst till den angivna resursen. När token har hämtats anger principen värdet för `Authorization` token i `Bearer` huvudet med hjälp av schemat.
  
### <a name="policy-statement"></a>Policyuttalande  
  
```xml  
<authentication-managed-identity resource="resource" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exempel  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Använda hanterad identitet för att autentisera med en backend-tjänst
```xml  
<authentication-managed-identity resource="https://graph.microsoft.com"/> 
```
```xml  
<authentication-managed-identity resource="https://management.azure.com/"/> <!--Azure Resource Manager-->
```
```xml  
<authentication-managed-identity resource="https://vault.azure.net"/> <!--Azure Key Vault-->
```
```xml  
<authentication-managed-identity resource="https://servicebus.azure.net/"/> <!--Azure Service Busr-->
```
```xml  
<authentication-managed-identity resource="https://storage.azure.com/"/> <!--Azure Blob Storage-->
```
```xml  
<authentication-managed-identity resource="https://database.windows.net/"/> <!--Azure SQL-->
```
  
#### <a name="use-managed-identity-in-send-request-policy"></a>Använda hanterad identitet i principen för skicka begäran
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Element  
  
|Namn|Beskrivning|Krävs|  
|----------|-----------------|--------------|  
|autentiseringshanterad identitet |Rotelementet.|Ja|  
  
### <a name="attributes"></a>Attribut  
  
|Namn|Beskrivning|Krävs|Default|  
|----------|-----------------|--------------|-------------|  
|resource|Sträng. App-ID:t för målwebb-API:et (säker resurs) i Azure Active Directory.|Ja|Ej tillämpligt|  
|output-token-variabel-namn|Sträng. Namnet på kontextvariabeln som ska `string`ta emot tokenvärdet som objekttyp . |Inga|Ej tillämpligt|  
|ignorera-fel|Boolean. Om den `true`är inställd på fortsätter princippipelinen att köras även om en åtkomsttoken inte hämtas.|Inga|false|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande [principavsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [scope](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policyavsnitt:** inkommande  
  
-   **Principomfattningar:** alla scope

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API-hantering](api-management-howto-policies.md)
+ [Omvandla API:er](transform-api.md)
+ [Principreferens](api-management-policy-reference.md) för en fullständig lista över policyutdrag och deras inställningar
+ [Policyexempel](policy-samples.md)
