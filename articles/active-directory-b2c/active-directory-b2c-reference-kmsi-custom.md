---
title: Jag vill förbli inloggad i Azure Active Directory B2C | Microsoft Docs
description: Lär dig hur du ställer in Håll mig registrerat i (KMSI) i Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 9002ab7396cd9beda767b4a9f81d9983ec74923d
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55163423"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivera Håll mig inloggad (KMSI) i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera Håll mig registrerat i (KMSI) funktioner för webb- och interna program i Azure Active Directory (Azure AD) B2C. Den här funktionen ger åtkomst till returnera användare till programmet utan att behöva ange sina användarnamn och lösenord. Återkalla den här åtkomsten när en användare loggar ut. 

Användare bör inte aktivera det här alternativet på offentliga datorer. 

![Aktivera ”Håll mig inloggad”](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C-klient som är konfigurerad för att tillåta lokalt konto, registrera dig och logga in. Om du inte har en klient kan du skapa en med hjälp av stegen i [självstudien: Skapa en Azure Active Directory B2C-klient](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Lägg till en innehållsdefinition-element 

Under den **BuildingBlocks** elementet i filen med din tillägg, Lägg till en **ContentDefinitions** element. 

1. Under den **ContentDefinitions** element, lägga till en **ContentDefinition** element med en identifierare för `api.signuporsigninwithkmsi`.
2. Under den **ContentDefinition** element, lägga till den **LoadUri**, **RecoveryUri**, och **DataUri** element. Den `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` värdet för den **DataUri** element är en dator att förstå identifierare som öppnar en KMSI kryssruta i sidorna för inloggning. Det här värdet får inte ändras. 

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Lägg till en inloggning anspråksprovider för ett lokalt konto  

Du kan definiera lokalt konto logga in som en anspråksprovider med hjälp av den **ClaimsProvider** elementet i tilläggsfilen av din princip:

1. Öppna den *TrustFrameworkExtensions.xml* fil från din arbetskatalog. 
2. Hitta den **ClaimsProviders** element. Om det inte finns lägger du till det under rotelementet. Den [startpaket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) innehåller ett lokalt konto logga in anspråksleverantör. 
3. Lägg till en **ClaimsProvider** element med den **DisplayName** och **TechnicalProfile** som visas i följande exempel:

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
            <InputClaims>
              <InputClaim ClaimTypeReferenceId="client_id" DefaultValue="ProxyIdentityExperienceFrameworkAppID" />
              <InputClaim ClaimTypeReferenceId="resource_id" PartnerClaimType="resource" DefaultValue="IdentityExperienceFrameworkAppID" />
            </InputClaims>
          </TechnicalProfile>
        </TechnicalProfiles>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Lägg till program-ID: n i en egen princip

Lägg till program-ID: n till den *TrustFrameworkExtensions.xml* fil.

1. I den *TrustFrameworkExtensions.xml* filen, hitta den **TechnicalProfile** element med identifierare för `login-NonInteractive` och **TechnicalProfile** element med en identifierare för `login-NonInteractive-PasswordChange` och ersätter alla värden i `IdentityExperienceFrameworkAppId` med program-ID för programmet för Identitetsupplevelse enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

    ```
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Ersätt alla värden i `ProxyIdentityExperienceFrameworkAppId` med program-ID för Proxy Identitetsramverk programmet enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
3. Spara tilläggsfilen.

## <a name="create-a-kmsi-enabled-user-journey"></a>Skapa en KMSI aktiverade användarresa

Lägg till inloggning anspråksleverantören för ett lokalt konto i din användarresa. 

1. Öppna filen bas i din princip. Till exempel *TrustFrameworkBase.xml*.
2. Hitta den **UserJourneys** element och kopiera hela innehållet i den **UserJourney** element som använder identifierare för `SignUpOrSignIn`.
3. Öppna tilläggsfilen. Till exempel *TrustFrameworkExtensions.xml* och hitta den **UserJourneys** element. Om elementet inte finns kan du lägga till en.
4. Klistra in hela **UserJourney** element som du kopierade som underordnad till den **UserJourneys** element.
5. Ändra värdet för identifieraren för den nya användarresan. Till exempel `SignUpOrSignInWithKmsi`.
6. Slutligen ändra värdet för i det första steget i orchestration **ContentDefinitionReferenceId** till `api.signuporsigninwithkmsi`. Inställningen för det här värdet aktiverar kryssrutan i användarresan. 
7. Spara och ladda upp tilläggsfilen med och se till att alla verifieringar lyckas.

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

## <a name="create-a-relying-party-file"></a>Skapa en förlitande part-fil

Uppdatera filen för förlitande part (RP) som initierar användarresa som du skapade.

1. Skapa en kopia av *SignUpOrSignIn.xml* i din arbetskatalog och Byt sedan namn. Till exempel *SignUpOrSignInWithKmsi.xml*.
2. Öppna i ny fil och uppdatera den **PolicyId** attributet för den **TrustFrameworkPolicy** med ett unikt värde. Det här är namnet på din princip. Till exempel `SignUpOrSignInWithKmsi`.
3. Ändra den **ReferenceId** attributet för den **DefaultUserJourney** elementet så att de matchar identifieraren för den nya användarresa som du skapade. Till exempel `SignUpOrSignInWithKmsi`.

    KMSI konfigureras med hjälp av den **UserJourneyBehaviors** element. Den **KeepAliveInDays** attribut styr hur länge användaren förblir inloggad. I följande exempel KMSI sessionen automatiskt upphör att gälla efter `7` dagar oavsett hur ofta användaren utför en tyst autentisering. Ange den **KeepAliveInDays** värde att `0` inaktiverar KMSI funktioner. Det här värdet är som standard `0`. Om värdet för **SessionExpiryType** är `Rolling`, KMSI sessionen utökas med `7` dagar varje gång användaren utför tyst autentisering.  Om `Rolling` är valt, bör du behålla antalet dagar till minimum. 

    Värdet för **SessionExpiryInSeconds** representerar förfallotiden för en SSO-session. Detta används internt av Azure AD B2C för att kontrollera om sessionen för KMSI har upphört att gälla eller inte. Värdet för **KeepAliveInDays** avgör förfaller/Max-Age-värdet för SSO-cookie i webbläsaren. Till skillnad från **SessionExpiryInSeconds**, **KeepAliveInDays** används för att förhindra att webbläsaren ska raderas cookien när den är stängd. En användare kan göra en obevakad logga in endast om det finns för sessions-cookie för enkel inloggning, som kontrolleras av **KeepAliveInDays**, och inte har upphört att gälla, som kontrolleras av **SessionExpiryInSeconds**. 
    
    Om en användare inte aktiverar **vill förbli inloggad** på sidan registrera dig och logga in en session upphör att gälla efter den tid som **SessionExpiryInSeconds** har passerat eller webbläsaren är stängd. Om en användare aktiverar **vill förbli inloggad**, värdet för **KeepAliveInDays** åsidosätter värdet för **SessionExpiryInSeconds** och avgör förfallotiden för sessionen. Inte ens användare att Stäng webbläsaren och öppna det igen, de kan fortfarande tyst inloggning så länge det är inom tiden av **KeepAliveInDays**. Vi rekommenderar att du ställer in värdet för **SessionExpiryInSeconds** ska vara en kort period (1200 sekunder) när värdet för **KeepAliveInDays** kan ställas in som en relativt lång tid (7 dagar), enligt den följande exempel:

    ```XML
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignInWithKmsi" />
      <UserJourneyBehaviors>
        <SingleSignOn Scope="Tenant" KeepAliveInDays="7" />
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
    ```

4. Spara dina ändringar och överför sedan filen.
5. Om du vill testa den anpassade principen som du laddade upp i Azure-portalen, gå till sidan för principer och välj sedan **kör nu**.

Du hittar exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).








