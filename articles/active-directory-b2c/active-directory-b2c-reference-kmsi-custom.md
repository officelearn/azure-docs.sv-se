---
title: Håll mig inloggad i Azure Active Directory B2C
description: Lär dig att konfigurera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 29cdf5e7723113b4673945bf5db3158680a44b79
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70147045"
---
# <a name="enable-keep-me-signed-in-kmsi-in-azure-active-directory-b2c"></a>Aktivera Håll mig inloggad (KMSI avgör) i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Du kan aktivera funktionen Håll mig inloggad (KMSI avgör) för användare av dina webb program och interna program som har lokala konton i din Azure Active Directory B2C (Azure AD B2C)-katalogen. Den här funktionen beviljar åtkomst till användare som återgår till programmet utan att be dem att ange sitt användar namn och lösen ord på annat sätt. Den här åtkomsten återkallas när en användare loggar ut.

Användarna bör inte aktivera det här alternativet på offentliga datorer.

![Exempel på registrerings inloggnings sida med kryss rutan Håll mig inloggad](./media/active-directory-b2c-reference-kmsi-custom/kmsi.PNG)

## <a name="prerequisites"></a>Förutsättningar

En Azure AD B2C klient som är konfigurerad för att tillåta inloggning på lokalt konto. KMSI avgör stöds inte för externa identitets leverantörs konton.

Om du inte har en klient organisation kan du skapa en med hjälp av stegen [i själv studie kursen: Skapa en Azure Active Directory B2C-klientorganisation](tutorial-create-tenant.md).

## <a name="add-a-content-definition-element"></a>Lägg till ett innehålls definitions element

Lägg till ett **ContentDefinitions** -element under **BuildingBlocks** -elementet i tilläggs filen.

1. Lägg till ett **ContentDefinition** -element med en identifierare för `api.signuporsigninwithkmsi`i **ContentDefinitions** -elementet.
2. Lägg till **LoadUri**-, **RecoveryUri**-och **DataUri** -elementen under **ContentDefinition** -elementet. Värdet för DataUri-elementet är en identifierare för maskin varu identifiering som visar kryss rutan KMSI avgör på inloggnings sidorna. `urn:com:microsoft:aad:b2c:elements:unifiedssp:1.1.0` Värdet får inte ändras.

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

## <a name="add-a-sign-in-claims-provider-for-a-local-account"></a>Lägg till en provider för inloggnings anspråk för ett lokalt konto

Du kan definiera lokal konto inloggning som en anspråks leverantör med hjälp av **ClaimsProvider** -elementet i tilläggs filen för principen:

1. Öppna filen *TrustFrameworkExtensions. XML* från din arbets katalog.
2. Hitta **ClaimsProviders** -elementet. Om den inte finns lägger du till den under rot elementet. [Start paketet](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) innehåller en provider för inloggnings anspråk för lokalt konto.
3. Lägg till ett **ClaimsProvider** -element med **DisplayName** och **TechnicalProfile** som visas i följande exempel:

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

### <a name="add-the-application-identifiers-to-your-custom-policy"></a>Lägg till program identifierare i den anpassade principen

Lägg till program identifierarna i filen *TrustFrameworkExtensions. XML* .

1. I filen *TrustFrameworkExtensions. XML* letar du upp **TechnicalProfile** - `login-NonInteractive` elementet med identifieraren och **TechnicalProfile** -elementet med en identifierare `login-NonInteractive-PasswordChange` och ersätter alla värden för `IdentityExperienceFrameworkAppId` med program identifieraren för Identity Experience Framework-programmet enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).

    ```XML
    <Item Key="client_id">8322dedc-cbf4-43bc-8bb6-141d16f0f489</Item>
    ```

2. Ersätt alla värden för `ProxyIdentityExperienceFrameworkAppId` med program-ID: t för Framework-programmet för proxy-programmiljö enligt beskrivningen i [komma igång](active-directory-b2c-get-started-custom.md).
3. Spara tilläggs filen.

## <a name="create-a-kmsi-enabled-user-journey"></a>Skapa en KMSI avgör-aktiverad användar resa

Lägg till providern för inloggnings anspråk för ett lokalt konto till din användar resa.

1. Öppna bas filen för din princip. Till exempel *TrustFrameworkBase. XML*.
2. Hitta **UserJourneys** -elementet och kopiera hela innehållet i elementet **UserJourney** som använder identifieraren `SignUpOrSignIn`.
3. Öppna tilläggs filen. Till exempel *TrustFrameworkExtensions. XML* och hitta **UserJourneys** -elementet. Om elementet inte finns lägger du till ett.
4. Klistra in hela **UserJourney** -elementet som du kopierade som ett underordnat objekt till **UserJourneys** -elementet.
5. Ändra värdet för identifieraren för den nya användar resan. Till exempel `SignUpOrSignInWithKmsi`.
6. Slutligen ändrar du värdet för **ContentDefinitionReferenceId** till `api.signuporsigninwithkmsi`i det första Orchestration-steget. Inställningen för det här värdet aktiverar kryss rutan i användar resan.
7. Spara och överför tilläggs filen och se till att alla verifieringar lyckas.

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

## <a name="create-a-relying-party-file"></a>Skapa en förlitande parts fil

Uppdatera den förlitande parten (RP) som initierar användar resan som du har skapat.

1. Gör en kopia av *SignUpOrSignIn. XML-* filen i din arbets katalog och byt sedan namn på den. Till exempel *SignUpOrSignInWithKmsi. XML*.
2. Öppna den nya filen och uppdatera attributet **PolicyId** för **TrustFrameworkPolicy** med ett unikt värde. Detta är namnet på principen. Till exempel `SignUpOrSignInWithKmsi`.
3. Ändra attributet **ReferenceId** för elementet **DefaultUserJourney** så att det matchar identifieraren för den nya användar resan som du skapade. Till exempel `SignUpOrSignInWithKmsi`.

    KMSI avgör konfigureras med hjälp av **UserJourneyBehaviors** -elementet med **SingleSignOn**, **SessionExpiryType**och **SessionExpiryInSeconds** som dess första underordnade element. Attributet **KeepAliveInDays** styr hur länge användaren är inloggad. I följande exempel upphör KMSI avgör-sessionen automatiskt att gälla efter `7` dagar, oavsett hur ofta användaren utför tyst autentisering. Ställer in värdet **KeepAliveInDays** så `0` att KMSI avgör-funktionen stängs av. Som standard är `0`det här värdet. Om värdet för **SessionExpiryType** är `Rolling`, utökas KMSI avgör-sessionen med `7` dagar varje gång användaren utför tyst autentisering.  Om `Rolling` väljs bör du behålla antalet dagar till minst.

    Värdet för **SessionExpiryInSeconds** representerar förfallo tiden för en SSO-session. Detta används internt av Azure AD B2C för att kontrol lera om sessionen för KMSI avgör har upphört att gälla eller inte. Värdet för **KeepAliveInDays** avgör värdet för Expires/max-ålder för SSO-cookien i webbläsaren. Till skillnad från **SessionExpiryInSeconds**används **KeepAliveInDays** för att förhindra att webbläsaren rensar cookien när den stängs. En användare kan bara logga in om SSO-sessionens cookie finns, som styrs av **KeepAliveInDays**och inte har upphört att gälla, vilket styrs av **SessionExpiryInSeconds**.

    Om en användare inte aktiverar **Håll mig** inloggad på registrerings-och inloggnings sidan, förfaller en session när den tid som anges av **SessionExpiryInSeconds** har passerat eller webbläsaren har stängts. Om en användare gör det möjligt att **låta mig vara**inloggad, åsidosätter värdet för **KeepAliveInDays** värdet för **SessionExpiryInSeconds** och anger förfallo tiden för sessionen. Även om användarna stänger webbläsaren och öppnar den igen, kan de fortfarande logga in så länge de är inom **KeepAliveInDays**. Vi rekommenderar att du anger värdet för **SessionExpiryInSeconds** till en kort period (1200 sekunder), medan värdet för **KeepAliveInDays** kan anges till en relativt lång period (7 dagar), vilket visas i följande exempel:

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

4. Spara ändringarna och ladda upp filen.
5. Testa den anpassade principen som du laddade upp genom att gå till sidan princip i Azure Portal och sedan välja **Kör nu**.

Du kan hitta exempel principen [här](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/keep%20me%20signed%20in).
