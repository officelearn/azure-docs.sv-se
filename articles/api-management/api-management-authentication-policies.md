---
title: Principer för Azure API Management-autentisering | Microsoft Docs
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
ms.date: 06/12/2020
ms.author: apimpm
ms.openlocfilehash: 8a92540ff2c57ff5c1aa827237a7341aecc1592b
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789267"
---
# <a name="api-management-authentication-policies"></a>Principer för API Management-autentisering
Det här avsnittet innehåller en referens för följande API Managements principer. Information om hur du lägger till och konfigurerar principer finns [i principer i API Management](https://go.microsoft.com/fwlink/?LinkID=398186).

##  <a name="authentication-policies"></a><a name="AuthenticationPolicies"></a>Autentiseringsprinciper

-   [Autentisera med Basic](api-management-authentication-policies.md#Basic) -autentisera med en backend-tjänst med grundläggande autentisering.

-   [Autentisera med klient certifikat](api-management-authentication-policies.md#ClientCertificate) – autentisera med en backend-tjänst med hjälp av klient certifikat.

-   [Autentisera med hanterad identitet](api-management-authentication-policies.md#ManagedIdentity) – autentisera med den [hanterade identiteten](../active-directory/managed-identities-azure-resources/overview.md) för den API Management tjänsten.

##  <a name="authenticate-with-basic"></a><a name="Basic"></a>Autentisera med Basic
 Använd `authentication-basic` principen för att autentisera med en backend-tjänst med grundläggande autentisering. Den här principen anger att HTTP-Authorization-huvudet ska vara det värde som motsvarar de autentiseringsuppgifter som anges i principen.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-basic username="username" password="password" />
```

### <a name="example"></a>Exempel

```xml
<authentication-basic username="testuser" password="testpassword" />
```

### <a name="elements"></a>Element

|Name|Beskrivning|Obligatorisk|
|----------|-----------------|--------------|
|autentisering – grundläggande|Rot element.|Yes|

### <a name="attributes"></a>Attribut

|Name|Beskrivning|Obligatorisk|Standard|
|----------|-----------------|--------------|-------------|
|användarnamn|Anger användar namnet för den grundläggande autentiseringsuppgiften.|Yes|Ej tillämpligt|
|password|Anger lösen ordet för grundläggande autentiseringsuppgifter.|Yes|Ej tillämpligt|

### <a name="usage"></a>Användning
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).

-   **Princip avsnitt:** inkommande

-   **Princip omfattningar:** alla omfattningar

##  <a name="authenticate-with-client-certificate"></a><a name="ClientCertificate"></a>Autentisera med klient certifikat
 Använd `authentication-certificate` principen för att autentisera med en backend-tjänst med hjälp av klient certifikat. Certifikatet måste [installeras i API Management](https://go.microsoft.com/fwlink/?LinkID=511599) först och identifieras av sitt tumavtryck.

### <a name="policy-statement"></a>Princip kommentar

```xml
<authentication-certificate thumbprint="thumbprint" certificate-id="resource name"/>
```

### <a name="examples"></a>Exempel

I det här exemplet identifieras klient certifikatet med tumavtrycket:

```xml
<authentication-certificate thumbprint="CA06F56B258B7A0D4F2B05470939478651151984" />
```

I det här exemplet identifieras klient certifikatet av resurs namnet:

```xml  
<authentication-certificate certificate-id="544fe9ddf3b8f30fb490d90f" />  
``` 

I det här exemplet anges klient certifikatet i principen i stället för att hämtas från det inbyggda certifikat arkivet:

```xml
<authentication-certificate body="@(context.Variables.GetValueOrDefault<byte[]>("byteCertificate"))" password="optional-certificate-password" />
```

### <a name="elements"></a>Element  
  
|Name|Beskrivning|Obligatorisk|  
|----------|-----------------|--------------|  
|autentisering-certifikat|Rot element.|Yes|  
  
### <a name="attributes"></a>Attribut  
  
|Name|Beskrivning|Obligatorisk|Standard|  
|----------|-----------------|--------------|-------------|  
|begäran|Tumavtryck för klient certifikatet.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Ej tillämpligt|
|certifikat-ID|Certifikat resursens namn.|Antingen `thumbprint` eller `certificate-id` måste finnas.|Ej tillämpligt|
|body|Klient certifikat som en byte mat ris.|No|Ej tillämpligt|
|password|Lösen ordet för klient certifikatet.|Används om certifikatet som anges i `body` är lösenordsskyddat.|Ej tillämpligt|
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar  

##  <a name="authenticate-with-managed-identity"></a><a name="ManagedIdentity"></a>Autentisera med hanterad identitet  
 Använd `authentication-managed-identity` principen för att autentisera med en backend-tjänst med hjälp av den hanterade identiteten. Den här principen använder i princip den hanterade identiteten för att hämta en åtkomsttoken från Azure Active Directory för åtkomst till den angivna resursen. När token har hämtats ställer principen in värdet för token i `Authorization` rubriken med `Bearer` schemat.

Både systemtilldelad identitet och någon av de flera användare som tilldelats identiteter kan användas för att begära token. Om `client-id` inte anges används en tilldelad identitet. Om `client-id` variabeln har angetts begärs token för den användarens tilldelade identitet från Azure Active Directory
  
### <a name="policy-statement"></a>Princip kommentar  
  
```xml  
<authentication-managed-identity resource="resource" client-id="clientid of user-assigned identity" output-token-variable-name="token-variable" ignore-error="true|false"/>  
```  
  
### <a name="example"></a>Exempel  
#### <a name="use-managed-identity-to-authenticate-with-a-backend-service"></a>Använd hanterad identitet för att autentisera med en server dels tjänst
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

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"/> <!--Your own Azure AD Application-->
```

#### <a name="use-managed-identity-and-set-header-manually"></a>Använd hanterad identitet och ange sidhuvudet manuellt

```xml
<authentication-managed-identity resource="api://Client_id_of_Backend"
   output-token-variable-name="msi-access-token" ignore-error="false" /> <!--Your own Azure AD Application-->
<set-header name="Authorization" exists-action="override">
   <value>@("Bearer " + (string)context.Variables["msi-access-token"])</value>
</set-header>
```

#### <a name="use-managed-identity-in-send-request-policy"></a>Använd hanterad identitet i skicka begär ande princip
```xml  
<send-request mode="new" timeout="20" ignore-error="false">
    <set-url>https://example.com/</set-url>
    <set-method>GET</set-method>
    <authentication-managed-identity resource="ResourceID"/>
</send-request>
```

### <a name="elements"></a>Element  
  
|Name|Beskrivning|Obligatorisk|  
|----------|-----------------|--------------|  
|autentisering-hanterad-identitet |Rot element.|Yes|  
  
### <a name="attributes"></a>Attribut  
  
|Name|Beskrivning|Obligatorisk|Standard|  
|----------|-----------------|--------------|-------------|  
|resource|Sträng. App-ID för mål webb-API (säker resurs) i Azure Active Directory.|Yes|Ej tillämpligt|
|klient-ID|Sträng. App-ID: t för den användarspecifika identiteten i Azure Active Directory.|No|systemtilldelad identitet|
|output-token-variabel-namn|Sträng. Namnet på den Sammanhangs variabel som kommer att ta emot token-värde som en objekt typ `string` . |No|Ej tillämpligt|  
|Ignorera-fel|Booleskt. Om detta är inställt på `true` , fortsätter princip pipelinen att köras även om en åtkomsttoken inte har hämtats.|No|falskt|  
  
### <a name="usage"></a>Användning  
 Den här principen kan användas i följande princip [avsnitt](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) och [områden](https://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Princip avsnitt:** inkommande  
  
-   **Princip omfattningar:** alla omfattningar

## <a name="next-steps"></a>Nästa steg
Mer information om hur du arbetar med principer finns i:

+ [Principer i API Management](api-management-howto-policies.md)
+ [Transformera API: er](transform-api.md)
+ [Princip referens](api-management-policy-reference.md) för en fullständig lista över princip satser och deras inställningar
+ [Princip exempel](policy-samples.md)
