---
title: Hantera enkel inloggning och token anpassning med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du hanterar enkel inloggning och token anpassning med anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: c0f5be7fd77ae195b66f8a8fb052ab8573d48171
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55856367"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Hantera enkel inloggning och token anpassning med anpassade principer i Azure Active Directory B2C

Den här artikeln innehåller information om hur du kan hantera din token, session och enkel inloggning (SSO) konfigurationer med [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="token-lifetimes-and-claims-configuration"></a>Livslängd och anspråk konfiguration

Om du vill ändra inställningarna på din tokenlivslängder du lägger till en [ClaimsProviders](claimsproviders.md) elementet i filen förlitande part för den princip du vill påverka.  Den **ClaimsProviders** element är underordnad den [TrustFrameworkPolicy](trustframeworkpolicy.md) element. Inuti måste du placera den information som påverkar din livslängd för token. XML-koden ser ut som i följande exempel:

```XML
<ClaimsProviders>
   <ClaimsProvider>
      <DisplayName>Token Issuer</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="JwtIssuer">
            <Metadata>
               <Item Key="token_lifetime_secs">3600</Item>
               <Item Key="id_token_lifetime_secs">3600</Item>
               <Item Key="refresh_token_lifetime_secs">1209600</Item>
               <Item Key="rolling_refresh_token_lifetime_secs">7776000</Item>
               <Item Key="IssuanceClaimPattern">AuthorityAndTenantGuid</Item>
               <Item Key="AuthenticationContextReferenceClaimPattern">None</Item>
            </Metadata>
         </TechnicalProfile>
      </TechnicalProfiles>
   </ClaimsProvider>
</ClaimsProviders>
```

Följande värden har angetts i föregående exempel:

- **Komma åt tokenlivslängder** – åtkomst livslängd för token värdet med **token_lifetime_secs** metadata-objekt. Standardvärdet är 3 600 sekunder (60 minuter).
- **Livslängd för uppdateringstoken ID** – ID livslängd för token värdet med den **id_token_lifetime_secs** metadata-objekt. Standardvärdet är 3 600 sekunder (60 minuter).
- **Livslängd för uppdateringstoken** – den livslängd för uppdateringstoken värdet med den **refresh_token_lifetime_secs** metadata-objekt. Standardvärdet är 1209600 sekunder (14 dagar).
- **Token livslängd för skjutfönster** – om du vill ange en livslängd för skjutfönster för att uppdateringstoken, ange värdet för **rolling_refresh_token_lifetime_secs** metadata-objekt. Standardvärdet är 7776000 (90 dagar). Om du inte vill tillämpa en livslängd för skjutfönster, ersätter du objektet med `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`.
- **Utfärdaren (iss) anspråk** -anspråk The utfärdare (iss) anges med den **IssuanceClaimPattern** metadata-objekt. Lämpliga värden är `AuthorityAndTenantGuid` och `AuthorityWithTfp`.
- **Inställningen anspråk som representerar princip-ID** -alternativ för det här värdet är `TFP` (litar framework princip) och `ACR` (autentisering kontext referens). `TFP` är det rekommenderade värdet. Ange **AuthenticationContextReferenceClaimPattern** med värdet för `None`. 

    I den **ClaimsSchema** element, lägga till det här elementet: 
    
    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```
    
    I din **OutputClaims** element, lägga till det här elementet:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    För ACR, ta bort den **AuthenticationContextReferenceClaimPattern** objekt.

- **Anspråk för ämne (sub)** – det här alternativet som standard objekt-ID om du vill växla inställningen till `Not Supported`, ersätter den här raden: 

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```
    
    med den här raden:
    
    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Sessionsbeteende och enkel inloggning

Om du vill ändra dina sessionsbeteende och konfigurationer för enkel inloggning måste du lägga till en **UserJourneyBehaviors** element inuti den [RelyingParty](relyingparty.md) element.  Den **UserJourneyBehaviors** elementet måste visas direkt efter den **DefaultUserJourney**. Inuti din **UserJourneyBehavors** elementet bör se ut så här:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Följande värden konfigureras i det förra exemplet:

- **Enkel inloggning (SSO)** -enkel inloggning har konfigurerats med den **SingleSignOn**. Lämpliga värden är `Tenant`, `Application`, `Policy`, och `Suppressed`. 
- **Webbappssession (minuter)** – app webbsessionen livslängd anges med den **SessionExpiryInSeconds** element. Standardvärdet är 86400 sekunder (1 440 minuter).
- **Web app sessionstimeout** – app webbsessionen timeouten anges med den **SessionExpiryType** element. Lämpliga värden är `Absolute` och `Rolling`.
