---
title: 'Azure Active Directory B2C: Hantera enkel inloggning och token anpassning med anpassade principer | Microsoft Docs'
description: 
services: active-directory-b2c
documentationcenter: 
author: sama
ms.assetid: eec4d418-453f-4755-8b30-5ed997841b56
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 05/02/2017
ms.author: sama
ms.openlocfilehash: 8f5703d15766f221517cd89352d41685652d32d6
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-manage-sso-and-token-customization-with-custom-policies"></a>Azure Active Directory B2C: Hantera enkel inloggning och token anpassning med anpassade principer
Anpassade principer ger dig samma kontroll över din token, session och enkel inloggning (SSO) konfigurationer som via inbyggda principer.  Om du vill veta vad varje inställning gör finns i dokumentationen för [här](#active-directory-b2c-token-session-sso).

## <a name="token-lifetimes-and-claims-configuration"></a>Konfiguration av token livslängd och anspråk
Om du vill ändra inställningarna för din token livslängd, måste du lägga till en `<ClaimsProviders>` element i filen förlitande part för den princip du vill påverka.  Den `<ClaimsProviders>` element är underordnad den `<TrustFrameworkPolicy>`.  Inuti måste du placera den information som påverkar din token livslängd.  XML-filen ser ut så här:

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

**Åtkomst-token livslängd** livslängd för åtkomst-token kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Livslängd för token ID** ID livslängd för token kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”id_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 3 600 sekunder (60 minuter).

**Uppdatera livslängd för token** livslängd för token uppdatering kan ändras genom att ändra värdet i den `<Item>` med nyckel = ”refresh_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 1209600 sekunder (14 dagar).

**Uppdatera livslängd för token glidande fönstret** om du vill ange en glidande fönstret livslängd till din uppdateringstoken, ändra värdet i `<Item>` med nyckel = ”rolling_refresh_token_lifetime_secs” i sekunder.  Standardvärdet i inbyggda är 7776000 (90 dagar).  Om du inte vill enfore ett glidande fönstret livslängd, ersätter den här raden med:
```XML
<Item Key="allow_infinite_rolling_refresh_token">True</Item>
```

**Utfärdaren (iss) anspråk** om du vill ändra utfärdaren (iss)-anspråk, ändra värdet i den `<Item>` med nyckel = ”IssuanceClaimPattern”.  Lämpliga värden är `AuthorityAndTenantGuid` och `AuthorityWithTfp`.

**Inställningen anspråk som representerar princip-ID** alternativen för det här värdet är TFP (förtroendeprincipen för framework) och ACR (authentication kontexten referens).  
Vi rekommenderar att du TFP, för att göra detta, se till att den `<Item>` med nyckel = ”AuthenticationContextReferenceClaimPattern” finns och värdet är `None`.
I din `<OutputClaims>` objektet, lägga till det här elementet:
```XML
<OutputClaim ClaimTypeReferenceId="trustFrameworkPolicy" Required="true" DefaultValue="{policy}" />
```
För ACR, ta bort den `<Item>` med nyckel = ”AuthenticationContextReferenceClaimPattern”.

**Ämne (under) anspråk** det här alternativet är som standard objekt-ID om du vill växla detta `Not Supported`, gör du följande:

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
**Enkel inloggning (SSO) konfiguration** om du vill ändra konfigurationen för enkel inloggning måste du ändra värdet för `<SingleSignOn>`.  Lämpliga värden är `Tenant`, `Application`, `Policy` och `Disabled`. 

**Webbprogrammet session-livstid (minuter)** att ändra den webbappen sessioners livstid, måste du ändra värdet för den `<SessionExpiryInSeconds>` element.  Standardvärdet i inbyggda principerna är 86400 sekunder (1 440 minuter).

**Tidsgräns för Web app** om du vill ändra tidsgränsen för sessionen web app måste du ändra värdet för `<SessionExpiryType>`.  Lämpliga värden är `Absolute` och `Rolling`.
