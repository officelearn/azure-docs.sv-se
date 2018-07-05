---
title: Hantera enkel inloggning och token-anpassning med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du hanterar enkel inloggning och token-anpassning med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 811fb8b2de59c9d324ab4acb8b0f51b4cec80aee
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441805"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Hantera enkel inloggning och token-anpassning med anpassade principer
Anpassade principer får du samma kontroll över din token, session och enkel inloggning (SSO) konfigurationer genom inbyggda principer.  För att se vad varje inställning gör det, finns i dokumentationen [här](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Livslängd och anspråk konfiguration
Om du vill ändra inställningarna på din tokenlivslängder, du måste lägga till en `<ClaimsProviders>` elementet i filen förlitande part för den princip du vill påverka.  Den `<ClaimsProviders>` element är underordnad den `<TrustFrameworkPolicy>`.  Inuti måste du placera den information som påverkar din livslängd för token.  XML-koden ser ut som i följande exempel:

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

**Komma åt tokenlivslängder** -livslängd för åtkomst-token kan ändras genom att ändra värdet mellan den `<Item>` med nyckel = ”token_lifetime_secs” på några sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Livslängd för uppdateringstoken ID** -ID. livslängd för uppdateringstoken kan ändras genom att ändra värdet mellan den `<Item>` med nyckel = ”id_token_lifetime_secs” på några sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Livslängd för uppdateringstoken** -livslängden för uppdateringstoken kan ändras genom att ändra värdet mellan den `<Item>` med nyckel = ”refresh_token_lifetime_secs” på några sekunder.  Standardvärdet i inbyggda är 1209600 sekunder (14 dagar).

**Token livslängd för skjutfönster** – om du vill ange en livslängd för skjutfönster för att uppdateringstoken, ändra värdet mellan `<Item>` med nyckel = ”rolling_refresh_token_lifetime_secs” på några sekunder.  Standardvärdet i inbyggda är 7776000 (90 dagar).  Om du inte vill tillämpa en livslängd för skjutfönster, ersätter du raden med:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Utfärdaren (iss) anspråk** – om du vill ändra utfärdare (iss)-anspråk, ändra värdet mellan den `<Item>` med nyckel = ”IssuanceClaimPattern”.  Lämpliga värden är `AuthorityAndTenantGuid` och `AuthorityWithTfp`.

**Inställningen anspråk som representerar princip-ID** -alternativ för det här värdet är TFP (förtroendeprincipen för framework) och ACR (autentisering kontext referens).  
Vi rekommenderar att du anger detta till TFP, gör du genom att kontrollera att den `<Item>` med Key = ”AuthenticationContextReferenceClaimPattern” finns och att värdet är `None`.
I din `<OutputClaims>` objektet, Lägg till det här elementet:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
För ACR, ta bort den `<Item>` med nyckel = ”AuthenticationContextReferenceClaimPattern”.

**Anspråk för ämne (sub)** – det här alternativet är som standard att objekt-ID om du vill växla den till `Not Supported`, gör du följande:

Ersätt den här raden 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
med den här raden:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Sessionsbeteende och enkel inloggning

Om du vill ändra dina sessionsbeteende och SSO-konfigurationer, du måste lägga till en `<UserJourneyBehaviors>` element inuti den `<RelyingParty>` element.  Den `<UserJourneyBehaviors>` elementet måste visas direkt efter den `<DefaultUserJourney>`.  Inuti din `<UserJourneyBehavors>` elementet bör se ut så här:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Enkel inloggning (SSO) konfiguration** – om du vill ändra enkel inloggning-konfigurationen måste du ändra värdet för `<SingleSignOn>`.  Lämpliga värden är `Tenant`, `Application`, `Policy` och `Disabled`. 

**Webbappssession (minuter)** – du ändrar webbappen sessionens livstid, måste du ändra värdet för den `<SessionExpiryInSeconds>` element.  Standardvärdet i inbyggda principer är 86400 sekunder (1 440 minuter).

**Web app sessionstimeout** – du ändrar webbappssession, måste du ändra värdet för `<SessionExpiryType>`.  Lämpliga värden är `Absolute` och `Rolling`.
