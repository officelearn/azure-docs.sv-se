---
title: Hantera enkel inloggning och token anpassning med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Lär dig mer om hur du hanterar enkel inloggning och token anpassning med anpassade principer.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 05/02/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 43e392979c50d340a10575898edb25b119e1410b
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/04/2018
ms.locfileid: "34712240"
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Hantera enkel inloggning och token anpassning med anpassade principer
Anpassade principer ger dig samma kontroll över din token, session och enkel inloggning (SSO) konfigurationer som via inbyggda principer.  För att se vad varje inställning finns i dokumentationen [här](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Konfiguration av token livslängd och anspråk
Om du vill ändra inställningarna för din token livslängd, måste du lägga till en `<ClaimsProviders>` element i filen förlitande part för den princip du vill påverka.  Den `<ClaimsProviders>` element är underordnad den `<TrustFrameworkPolicy>`.  Inuti måste du placera den information som påverkar din token livslängd.  XML-filen ser ut som i det här exemplet:

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

**Åtkomst-token livslängd** -livslängd för åtkomst-token kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Livslängd för token ID** -ID-livslängd för token kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”id_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Uppdatera livslängd för token** -uppdatering livslängd för token kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”refresh_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 1209600 sekunder (14 dagar).

**Uppdatera livslängd för token glidande fönstret** - om du vill ange en glidande fönstret livslängd till din uppdateringstoken, ändrar du värdet i `<Item>` med nyckel = ”rolling_refresh_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 7776000 (90 dagar).  Om du inte vill tillämpa en glidande fönstret livslängd Ersätt den här raden med:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Utfärdaren (iss) anspråk** - om du vill ändra utfärdaren (iss)-anspråk, ändra värdet i den `<Item>` med nyckel = ”IssuanceClaimPattern”.  Lämpliga värden är `AuthorityAndTenantGuid` och `AuthorityWithTfp`.

**Inställningen anspråk som representerar princip-ID** -alternativen för det här värdet är TFP (förtroendeprincipen för framework) och ACR (authentication kontexten referens).  
Vi rekommenderar att du TFP, för att göra detta, se till att den `<Item>` med nyckel = ”AuthenticationContextReferenceClaimPattern” finns och värdet är `None`.
I din `<OutputClaims>` objektet, lägga till det här elementet:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
För ACR, ta bort den `<Item>` med nyckel = ”AuthenticationContextReferenceClaimPattern”.

**Ämne (under) anspråk** -det här alternativet är som standard objekt-ID om du vill växla detta `Not Supported`, gör du följande:

Ersätt den här raden 
```XML
<OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
```
med den här raden:
```XML
<OutputClaim ClaimTypeReferenceId="sub" />
```

## <a name="session-behavior-and-sso"></a>Sessionen fungerar och enkel inloggning

Om du vill ändra din session beteende och konfigurationer för enkel inloggning måste du lägga till en `<UserJourneyBehaviors>` element inuti den `<RelyingParty>` element.  Den `<UserJourneyBehaviors>` element måste omedelbart efter den `<DefaultUserJourney>`.  För din `<UserJourneyBehavors>` element ska se ut så här:

```XML
<UserJourneyBehaviors>
   <SingleSignOn Scope="Application" />
   <SessionExpiryType>Absolute</SessionExpiryType>
   <SessionExpiryInSeconds>86400</SessionExpiryInSeconds>
</UserJourneyBehaviors>
```
**Enkel inloggning (SSO) konfiguration** – du ändrar enkel inloggning konfigurationen, måste du ändra värdet för `<SingleSignOn>`.  Lämpliga värden är `Tenant`, `Application`, `Policy` och `Disabled`. 

**Webbprogrammet session-livstid (minuter)** – du ändrar webbprogrammet sessioners livstid, måste du ändra värdet för den `<SessionExpiryInSeconds>` element.  Standardvärdet i inbyggda principerna är 86400 sekunder (1 440 minuter).

**Web app sessionstimeout** – du ändrar sessionstidsgränsen för web app måste du ändra värdet för `<SessionExpiryType>`.  Lämpliga värden är `Absolute` och `Rolling`.
