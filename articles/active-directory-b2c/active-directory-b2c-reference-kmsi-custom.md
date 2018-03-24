---
title: 'Azure Active Directory B2C: KMSI | Microsoft Docs'
description: Ett avsnitt som visar hur du ställer in 'keep mig inloggad'
services: active-directory-b2c
documentationcenter: ''
author: davidmu1
manager: mtillman
editor: ''
ms.service: active-directory-b2c
ms.workload: identity
ms.topic: article
ms.date: 09/05/2016
ms.author: davidmu
ms.openlocfilehash: 073ba8eef7f2f42d1c308fb20d3bfdbfc8d321b7
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="azure-active-directory-b2c-enable-keep-me-signed-in-kmsi"></a>Azure Active Directory B2C: Aktivera ”jag vill förbli inloggad (KMSI)”  
[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Azure AD B2C kan nu webb- och interna program genom att aktivera funktionen ”jag vill förbli inloggad (KMSI)'. Den här funktionen ger åtkomst till återgår användare till programmet utan att be användaren att ange användarnamn och lösenord. Åtkomst har återkallats när användaren loggar. 

Vi rekommenderar mot användare med det här alternativet på offentliga datorer. 

![bild](images/kmsi.PNG)


## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C-klient som är konfigurerad för att tillåta lokala kontot sign-upp/inloggning, enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

## <a name="how-to-enable-kmsi"></a>Så här aktiverar du KMSI

Gör följande ändringar i din förtroendeprincipen framework tillägg.

## <a name="adding-a-content-definition-element"></a>Lägga till ett element i innehåll 

Den `BuildingBlocks` nod i tillägget-filen måste innehålla en `ContentDefinitions` element. 

1. I den `ContentDefinitions` avsnittet, definiera en ny `ContentDefinition` med ID `api.signuporsigninwithkmsi`.
2. Din nya `ContentDefinition` måste innehålla en `LoadUri`, `RecoveryUri` och `DataUri` på följande sätt.
3. Datauri`urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` är en förstå identifierare för datorn som tar fram en KMSI kryssruta i sidorna för inloggning. Kontrollera att du inte ändrar det här värdet. 

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



## <a name="add-a--local-account-sign-in-claims-provider"></a>Lägg till ett lokalt konto inloggning anspråksprovider 

Kan du definiera lokala konto logga in som en anspråksprovider för den `<ClaimsProvider>` nod i tilläggsfilen i principen:

1. Öppna tilläggsfilen (TrustFrameworkExtensions.xml) från arbetskatalogen. 
2. Hitta de `<ClaimsProviders>` avsnitt. Om det inte finns lägger du till den under rotnoden.
3. Startpaket från [komma igång](active-directory-b2c-get-started-custom.md) medföljer en logga lokalt konto in anspråksleverantör. 
4. Om inte, lägger du till en ny `<ClaimsProvider>` nod på följande sätt:

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

### <a name="add-the-application-ids-to-your-custom-policy"></a>Lägg till program-ID: N i en anpassad princip

Lägg till program-ID i tilläggsfilen (`TrustFrameworkExtensions.xml`):

1. Hitta element i tilläggsfilen (TrustFrameworkExtensions.xml) `<TechnicalProfile Id="login-NonInteractive">` och `<TechnicalProfile Id="login-NonInteractive-PasswordChange">`

2. Ersätt alla förekomster av `IdentityExperienceFrameworkAppId` med program-ID för identitet upplevelse Framework-program som beskrivs i [komma igång](active-directory-b2c-get-started-custom.md). Här är ett exempel:

   ```
   <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
   ```

3. Ersätt alla förekomster av `ProxyIdentityExperienceFrameworkAppId` med program-ID för Proxy identitet upplevelse Framework-program som beskrivs i [komma igång](active-directory-b2c-get-started-custom.md).

4. Spara tilläggsfilen.

## <a name="create-a-kmsi-in-enabled-user-journey"></a>Skapa en KMSI i aktiverad användare resa

Du måste nu lägga till lokala konto logga anspråksprovider till användaren-transporten. 

1. Öppna filen grundläggande av principen (till exempel TrustFrameworkBase.xml).
2. Hitta de `<UserJourneys>` element och kopiera hela `<UserJourney>` nod som innehåller `Id="SignUpOrSignIn"`.
3. Öppna tilläggsfilen (till exempel TrustFrameworkExtensions.xml) och Sök efter den `<UserJourneys>` element. Om elementet inte finns, kan du lägga till en.
4. Klistra in hela `<UserJourney>` nod som du kopierade som underordnad till den `<UserJourneys>` element.
5. Byt namn på ID för den nya användare resan (t.ex, Byt namn på det `SignUpOrSignInWithKmsi`).
6. Slutligen i `OrchestrationStep 1` ändra den `ContentDefinitionReferenceId` till `api.signuporsigninwithkmsi` , definied i de föregående stegen. Detta gör att kryssrutan i användaren transporten. 
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

Därefter uppdaterar du filen förlitande part (RP) som initierar transporten användare som du skapade:

1. Gör en kopia av SignUpOrSignIn.xml i arbetskatalogen. Sedan, byta namn på den (till exempel SignUpOrSignInWithKmsi.xml).

2. Öppna ny fil och uppdatera den `PolicyId` attribut för `<TrustFrameworkPolicy>` med ett unikt värde. Detta är namnet på principen (till exempel SignUpOrSignInWithKmsi).

3. Ändra den `ReferenceId` attribut i `<DefaultUserJourney>` så att den matchar den `Id` transporten för nya användare som du skapade (till exempel SignUpOrSignInWithKmsi).

4. KMSI har konfigurerats i `UserJourneyBehaviors`. 

5. **`KeepAliveInDays`** Anger hur länge användaren är inloggad. I följande exempel KMSI sessionen automatiskt upphör att gälla efter 14 dagar oavsett hur ofta användaren utför tyst autentisering.

   Ange `KeepAliveInDays` värdet 0 inaktiverar KMSI funktioner. Det här värdet är 0 som standard

6. Om **`SessionExpiryType`** är *rullande*, och sedan KMSI sessionen utökas med 14 dagar varje gång användaren utför tyst autentisering.  Om *rullande* har markerats, rekommenderar vi att du hålla minsta antalet dagar. 

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

7. Spara ändringarna och sedan ladda upp filen.

8. Om du vill testa den anpassade principen som du överfört i Azure portal, gå till principbladet och klicka sedan på **kör nu**.


## <a name="link-to-sample-policy"></a>Länkar till exempel policy

Du kan hitta exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








