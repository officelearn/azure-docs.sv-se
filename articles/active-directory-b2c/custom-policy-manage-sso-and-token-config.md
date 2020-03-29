---
title: Hantera SSO- och tokenanpassning med hjälp av anpassade principer
titleSuffix: Azure AD B2C
description: Lär dig mer om hur du hanterar SSO- och tokenanpassning med hjälp av anpassade principer i Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ff9f57af92c50c0df6f628113bd9490ca83e1310
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189301"
---
# <a name="manage-sso-and-token-customization-using-custom-policies-in-azure-active-directory-b2c"></a>Hantera SSO- och tokenanpassning med hjälp av anpassade principer i Azure Active Directory B2C

Den här artikeln innehåller information om hur du kan hantera dina token-, sessions- och enkel inloggningskonfigurationer (SSO) med hjälp av [anpassade principer](custom-policy-overview.md) i Azure Active Directory B2C (Azure AD B2C).

## <a name="token-lifetimes-and-claims-configuration"></a>Token livstidar och anspråk konfiguration

Om du vill ändra inställningarna för tokenlivstiderna lägger du till ett [ClaimsProviders-element](claimsproviders.md) i den förlitande partfilen för den princip som du vill påverka.  Elementet **ClaimsProviders** är underordnadt elementet [TrustFrameworkPolicy.](trustframeworkpolicy.md)

Infoga elementet ClaimsProviders mellan Elementet BasePolicy och Elementet RelyingParty i den förlitande part-filen.

Inuti måste du lägga den information som påverkar din token livstid. XML-koden ser ut så här:

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

Följande värden anges i föregående exempel:

- **Åtkomsttokens livstid -** Livstidsvärdet för åtkomsttoken anges med **token_lifetime_secs** metadataobjekt. Standardvärdet är 3600 sekunder (60 minuter).
- **ID token lifetime** - ID-tokenlivsvärdet anges med **id_token_lifetime_secs** metadataobjektet. Standardvärdet är 3600 sekunder (60 minuter).
- **Uppdatera tokenlivstid** - Livstidsvärdet för uppdateringstoken anges med **refresh_token_lifetime_secs** metadataobjektet. Standardvärdet är 1209600 sekunder (14 dagar).
- **Uppdatera livslängden** för skjutfönstret för token – Om du vill ställa in en livslängd för ett glidande fönster till din uppdateringstoken anger du värdet **för rolling_refresh_token_lifetime_secs** metadataobjektet. Standardvärdet är 7776000 (90 dagar). Om du inte vill framtvinga en glidande `<Item Key="allow_infinite_rolling_refresh_token">True</Item>`fönsterlivslängd ersätter du objektet med .
- **Emittent (iss) anspråk** - Emittenten (iss) anspråk ställs in med **IssuanceClaimPattern** metadata objekt. De tillämpliga `AuthorityAndTenantGuid` värdena är och `AuthorityWithTfp`.
- **Ange anspråk som representerar princip-ID** `TFP` - Alternativen för `ACR` att ange det här värdet är (förtroenderamsprincip) och (autentiseringskontextreferens). `TFP`är det rekommenderade värdet. Ange **AuthenticationContextReferenceClaimPattern** med `None`värdet .

    Lägg till det här elementet i elementet **ClaimsSchema:**

    ```XML
    <ClaimType Id="trustFrameworkPolicy">
      <DisplayName>Trust framework policy name</DisplayName>
      <DataType>string</DataType>
    </ClaimType>
    ```

    Lägg till det här elementet i **elementet OutputClaims:**

    ```XML
    <OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
    ```

    För ACR tar du bort objektet **AuthenticationContextReferenceClaimPattern.**

- **Ämnesanspråk (under) anspråk** - Det här alternativet är objekt-ID som standard, om du vill växla den här inställningen till `Not Supported`ersätter du den här raden:

    ```XML
    <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
    ```

    med denna rad:

    ```XML
    <OutputClaim ClaimTypeReferenceId="sub" />
    ```

## <a name="session-behavior-and-sso"></a>Sessionsbeteende och SSO

Om du vill ändra sessionsbeteendet och SSO-konfigurationerna lägger du till ett **UserJourneyBehaviors-element** i elementet [RelyingParty.](relyingparty.md)  Elementet **UserJourneyBehaviors** måste omedelbart följa **DefaultUserJourney**. Insidan av ditt **UserJourneyBehavors-element** ska se ut så här:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```

Följande värden konfigureras i föregående exempel:

- **Enkel inloggning (SSO)** - Enkel inloggning är konfigurerad med **SingleSignOn**. De tillämpliga `Tenant`värdena `Policy`är `Suppressed`, `Application`, och .
- **Timeout för webbappsession** – Timeout för webbappsessionen anges med elementet **SessionExpiryType.** De tillämpliga `Absolute` värdena är och `Rolling`.
- **Livstid för webbappsession** – Livstiden för webbappsessionen ställs in med elementet **SessionExpiryInSeconds.** Standardvärdet är 86400 sekunder (1 440 minuter).
