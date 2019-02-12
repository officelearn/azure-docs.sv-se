---
title: Välj ett sida-kontrakt i Azure Active Directory B2C | Microsoft Docs
description: Läs mer om hur du väljer ett sida-kontrakt i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: fa48d3825b5b942497b1eabd19a1eeb7e0141058
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003150"
---
# <a name="select-a-page-contract-in-azure-active-directory-b2c-using-custom-policies"></a>Välj en sida kontrakt i Azure Active Directory B2C med anpassade principer

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera JavaScript-kod på klientsidan i din Azure Active Directory (Azure AD) B2C-principer, oavsett om du använder användarflöden eller anpassade principer. Den här artikeln beskrivs hur du väljer ett sida-kontrakt i Azure AD B2C genom att konfigurera det i en [anpassad princip](active-directory-b2c-overview-custom.md). Ett sida-kontrakt är ett nätverk med element som tillhandahåller Azure AD B2C och det innehåll som du anger. Om du tänker använda [Javascript](javascript-samples.md), måste du definiera en sida kontrakt-version för alla dina innehållsdefinitioner i en egen princip.

> [!NOTE]
> Om du vill aktivera JavaScript för användarflöden [om hur du använder JavaScript och sidan kontrakt-versioner i ett användarflöde](user-flow-javascript-overview.md).

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



