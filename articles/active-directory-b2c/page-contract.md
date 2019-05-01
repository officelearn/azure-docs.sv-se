---
title: Välj en sida-kontrakt - Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du väljer ett sida-kontrakt i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 4cd29df19179f07fd9b61a2f484b1d49cc05c4cf
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64570579"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Välj en sida kontrakt i Azure Active Directory B2C med anpassade principer

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

Du kan aktivera JavaScript-kod på klientsidan i din Azure Active Directory (Azure AD) B2C-principer, oavsett om du använder användarflöden eller anpassade principer. Om du vill aktivera JavaScript för dina program, måste du lägga till ett element till din [anpassad princip](active-directory-b2c-overview-custom.md), Välj ett sida-kontrakt och använda [b2clogin.com](b2clogin.md) i dina önskemål. Ett sida-kontrakt är ett nätverk med element som tillhandahåller Azure AD B2C och det innehåll som du anger. Den här artikeln beskrivs hur du väljer ett sida-kontrakt i Azure AD B2C genom att konfigurera i en anpassad princip.

> [!NOTE]
> Om du vill aktivera JavaScript för användarflöden [JavaScript och sidan kontrakt-versioner i Azure Active Directory B2C](user-flow-javascript-overview.md).

## <a name="replace-datauri-values"></a>Ersätt DataUri värden

Du kan ha i dina anpassade principer [ContentDefinitions](contentdefinitions.md) som definierar HTML-mallarna som används i användarresan. Den **ContentDefinition** innehåller en **DataUri** som refererar till sidelement som tillhandahålls av Azure AD B2C. Den **LoadUri** är den relativa sökvägen till HTML och CSS-innehåll som du anger.

```XML
<ContentDefinition Id="api.idpselections">
  <LoadUri>~/tenant/default/idpSelector.cshtml</LoadUri>
  <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
  <DataUri>urn:com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0</DataUri>
  <Metadata>
    <Item Key="DisplayName">Idp selection page</Item>
    <Item Key="language.intro">Sign in</Item>
  </Metadata>
</ContentDefinition>
```

För att välja ett sida-kontrakt, du ändrar den **DataUri** värdena i din [ContentDefinitions](contentdefinitions.md) i dina principer. Genom att växla från gammalt **DataUri** värden till de nya värdena du väljer ett paket som inte kan ändras. Fördelen med att använda det här paketet är att du vet den inte ändra och orsaka oväntade resultat på sidan. 

Om du vill konfigurera ett sida-kontrakt, Använd följande tabell för att hitta **DataUri** värden. 

| Gammalt DataUri värde | Nytt DataUri värde |
| ----------------- | ----------------- |
| urn: com:microsoft:aad:b2c:elements:idpselection:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:providerselection:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssd:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssd:1.0.0 | 
| urn: com:microsoft:aad:b2c:elements:claimsconsent:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:claimsconsent:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:multifactor:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:multifactor:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:selfasserted:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:selfasserted:1.1.0 | 
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:unifiedssp:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:unifiedssp:1.1.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.0.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.0.0 |
| urn: com:microsoft:aad:b2c:elements:globalexception:1.1.0 | urn: com:microsoft:aad:b2c:elements:contract:globalexception:1.1.0 |

## <a name="next-steps"></a>Nästa steg

Mer information om hur du kan anpassa användargränssnittet i dina program i [anpassa användargränssnittet i ditt program med en anpassad princip i Azure Active Directory B2C](active-directory-b2c-ui-customization-custom.md).



