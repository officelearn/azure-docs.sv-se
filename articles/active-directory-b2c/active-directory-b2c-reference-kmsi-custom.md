---
title: Jag vill förbli inloggad i Azure Active Directory B2C | Microsoft Docs
description: Ett avsnitt som visar hur du ställer in ”Håll mig inloggad”.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/05/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 6bad6e1f2b204f76b075652a9d3f27367a8de49f
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441325"
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Aktivera ”Håll mig inloggad (KMSI)”  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C kan nu dina webb- och interna program att aktivera ”Håll mig inloggad (KMSI)”-funktioner. Den här funktionen ger åtkomst till returnera användare till program utan där du uppmanas att ange användarnamn och lösenord. Återkalla den här åtkomsten när användaren loggar. 

Vi rekommenderar mot användare Markera det här alternativet på offentliga datorer. 

![bild](images/kmsi.PNG)


## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C-klient som konfigurerats för att tillåta lokala konto registrerings-registreringen/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Så här aktiverar du KMSI

Gör följande ändringar i din förtroendeprincipen framework-tillägg.

## <a name="adding-a-content-definition-element"></a>Att lägga till ett element i innehållsdefinition 

Den `BuildingBlocks` noden används filen måste innehålla en `ContentDefinitions` element. 

1. I den `ContentDefinitions` avsnittet, definiera en ny `ContentDefinition` med ID `api.signuporsigninwithkmsi`.
2. Din nya `ContentDefinition` måste innehålla en `LoadUri`, `RecoveryUri` och `DataUri` på följande sätt.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` är en dator att förstå identifierare som öppnar en KMSI kryssruta i sidorna för inloggning. Kontrollera att du inte ändrar det här värdet. 

```XML
  <BuildingBlocks>
    <ContentDefinitions>
      <ContentDefinition Id="api.signuporsigninwithkmsi">
        <LoadUri>~/tenant/default/unified.cshtml</LoadUri>
        <RecoveryUri>~/common/default_page_error.html</RecoveryUri>
        <DataUri>urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0</DataUri>
        <Metadata>
          <Item Key="DisplayName">Signin and Signup</Item>
        </Metadata>
      </ContentDefinition>
    </ContentDefinitions>
  </BuildingBlocks>                       
```



## <a name="add-a--local-account-sign-in-claims-provider"></a>Lägg till ett lokalt konto logga in anspråksprovider 

Du kan definiera inloggning från lokalt konto som en anspråksprovider till den `<ClaimsProvider>` nod i tilläggsfilen i din princip:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från din arbetskatalog. 
2. Hitta den `<ClaimsProviders>` avsnittet. Om det inte finns, lägger du till det under rotnoden.
3. Startpaket från [komma igång](active-directory-b2c-get-started-custom.md) levereras med en inloggning lokalt konto från anspråksleverantören. 
4. Om inte, lägger du till en ny `<ClaimsProvider>` noden på följande sätt:

```XML
<ClaimsProviders>
    <ClaimsProvider>
      <DisplayName>Local Account SignIn</DisplayName>
      <TechnicalProfiles>
         <TechnicalProfile Id="login-NonInteractive">
           <Metadata>
            <Item Key="client_id">ProxyIdentityExperienceFrameworkAppId</Item>
            <Item Key="IdTokenAudience">IdentityExperienceFrameworkAppId</Item>
           </Metadata>
            <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
            <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
           </InputClaims>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
 </ClaimsProviders>
```

### <a name="add-the-application-ids-to-your-custom-policy"></a>Lägg till program-ID: N i en egen princip

Lägg till program-ID i tilläggsfilen (`TrustFrameworkExtensions.xml`):

1. Hitta elementet i tilläggsfilen (TrustFrameworkExtensions.xml) `<TechnicalProfile Id="login-NonInteractive">` och `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Ersätt alla förekomster av `IdentityExperienceFrameworkAppId` med program-ID för programmet för Identitetsupplevelse enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md). Här är ett exempel:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Ersätt alla förekomster av `ProxyIdentityExperienceFrameworkAppId` med program-ID för Proxy Identitetsramverk programmet enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

4. Spara tilläggsfilen.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Skapa en KMSI i aktiverade användarresa

Du måste nu lägga till inloggning från lokalt konto anspråksprovider till din användarresa. 

1. Öppna filen grundläggande av din princip (till exempel TrustFrameworkBase.xml).
2. Hitta den `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och hitta den `<UserJourneys>` element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
5. Byt namn på ID för den nya användarresan (till exempel byta namn på som `SignUpOrSignInWithKmsi`).
6. Slutligen i `OrchestrationStep 1` ändra den `ContentDefinitionReferenceId` till `api.signuporsigninwithkmsi` , definied i tidigare steg. På så sätt kan kryssrutan i användarresan. 
7. Du är klar ändra tilläggsfilen. Spara och ladda upp den här filen. Se till att alla verifieringar lyckas.

```XML
<UserJourneys>
    <UserJourney Id="SignUpOrSignInWithKmsi">
      <OrchestrationSteps>
        <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsigninwithkmsi">
          <ClaimsProviderSelections>
            <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange" />
          </ClaimsProviderSelections>
          <ClaimsExchanges>
            <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="2" Type="ClaimsExchange">
          <Preconditions>
            <Precondition Type="ClaimsExist" ExecuteActionsIf="true">
              <Value>objectId</Value>
              <Action>SkipThisOrchestrationStep</Action>
            </Precondition>
          </Preconditions>
          <ClaimsExchanges>
            <ClaimsExchange Id="SignUpWithLogonEmailExchange" TechnicalProfileReferenceId="LocalAccountSignUpWithLogonEmail" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <!-- This step reads any user attributes that we may not have received when in the token. -->
        <OrchestrationStep Order="3" Type="ClaimsExchange">
          <ClaimsExchanges>
            <ClaimsExchange Id="AADUserReadWithObjectId" TechnicalProfileReferenceId="AAD-UserReadUsingObjectId" />
          </ClaimsExchanges>
        </OrchestrationStep>
        <OrchestrationStep Order="4" Type="SendClaims" CpimIssuerTechnicalProfileReferenceId="JwtIssuer" />
      </OrchestrationSteps>
      <ClientDefinition ReferenceId="DefaultWeb" />
    </UserJourney>
  </UserJourneys>
```

## <a name="create-a-relying-party-rp-file"></a>Skapa en förlitande part (RP)-fil

Därefter uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade:

1. Göra en kopia av SignUpOrSignIn.xml i din arbetskatalog. Sedan, byta namn (till exempel SignUpOrSignInWithKmsi.xml).

2. Öppna i ny fil och uppdatera den `PolicyId` attributet för `<TrustFrameworkPolicy>` med ett unikt värde. Det här är namnet på din princip (till exempel SignUpOrSignInWithKmsi).

3. Ändra den `ReferenceId` attributet i `<DefaultUserJourney>` så att den matchar den `Id` för nya användarresa som du skapade (till exempel SignUpOrSignInWithKmsi).

4. KMSI har konfigurerats i `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** styr hur länge användaren förblir inloggad. I följande exempel KMSI session automatiskt upphör att gälla efter 14 dagar oavsett hur ofta användaren utför en tyst autentisering.

   Ange `KeepAliveInDays` värdet 0 inaktiverar KMSI funktioner. Det här värdet är 0 som standard

6. Om **`SessionExpiryType`** är *rullande*, och sedan KMSI sessionen utökas med 14 dagar varje gång användaren utför tyst autentisering.  Om *rullande* är valt, rekommenderar vi att du hålla nere antalet dagar till minimum. 

       <RelyingParty>
       <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
       <UserJourneyBehaviors>
         <SingleSignOn Scope="Tenant" KeepAliveInDays="14" />
         <SessionExpiryType>Absolute</SessionExpiryType>
         <SessionExpiryInSeconds>1200</SessionExpiryInSeconds>
       </UserJourneyBehaviors>
       <TechnicalProfile Id="PolicyProfile">
         <DisplayName>PolicyProfile</DisplayName>
         <Protocol Name="OpenIdConnect" />
         <OutputClaims>
           <OutputClaim ClaimTypeReferenceId="displayName" />
           <OutputClaim ClaimTypeReferenceId="givenName" />
           <OutputClaim ClaimTypeReferenceId="surname" />
           <OutputClaim ClaimTypeReferenceId="email" />
           <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
         </OutputClaims>
         <SubjectNamingInfo ClaimType="sub" />
       </TechnicalProfile>
       </RelyingParty>

7. Spara dina ändringar och överför sedan filen.

8. Om du vill testa den anpassade principen som du laddade upp i Azure-portalen, gå till principbladet och klicka sedan på **kör nu**.


## <a name="link-to-sample-policy"></a>Länkar till exempel policy

Du hittar exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








