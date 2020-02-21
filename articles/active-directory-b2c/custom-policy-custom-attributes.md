---
title: Lägg till egna attribut till anpassade principer
titleSuffix: Azure AD B2C
description: En genom gång av hur du använder tilläggs egenskaper och anpassade attribut och inkluderar dem i användar gränssnittet.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: c2361192a612cfd92003b1e3c36e85dbbd9090bb
ms.sourcegitcommit: 98a5a6765da081e7f294d3cb19c1357d10ca333f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2020
ms.locfileid: "77482776"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Använd anpassade attribut i en anpassad profil redigerings princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln skapar du ett anpassat attribut i Azure Active Directory B2C-katalogen (Azure AD B2C). Du kommer att använda det här nya attributet som ett anpassat anspråk i profilen redigera användar resa.

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i artikeln [Azure Active Directory B2C: kom igång med anpassade principer](custom-policy-get-started.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Använd anpassade attribut för att samla in information om dina kunder i Azure AD B2C med anpassade principer
Din Azure AD B2C katalog levereras med en inbyggd uppsättning attribut. Exempel på **namn**, efter **namn**, **stad**, **post nummer**och **userPrincipalName**. Du behöver ofta skapa egna attribut som de här exemplen:
* Ett kundriktat program måste vara beständigt för ett attribut som **LoyaltyNumber.**
* En identitetsprovider har ett unikt användar-ID, t. ex. **uniqueUserGUID** , som måste sparas.
* En anpassad användar resa måste vara beständig för ett tillstånd för en användare som **migrationStatus**.

Azure AD B2C utökar uppsättningen attribut som lagras för varje användar konto. Du kan också läsa och skriva attributen med hjälp av [Microsoft Graph-API: et](manage-user-accounts-graph-api.md).

Tilläggs egenskaper utökar schemat för användar objekt i katalogen. Villkors *tilläggets egenskap*, *anpassade attribut*och *anpassat anspråk* refererar till samma sak som i den här artikeln. Namnet varierar beroende på kontexten, t. ex. program, objekt eller princip.

Tilläggs egenskaper kan bara registreras på ett program objekt trots att de kan innehålla data för en användare. Egenskapen är kopplad till programmet. Programobjektet måste ha Skriv behörighet för att registrera en tilläggs egenskap. Ett hundra tilläggs egenskaper, för alla typer och alla program, kan skrivas till ett enda objekt. Tilläggs egenskaper läggs till i mål katalog typen och blir omedelbart tillgängliga i Azure AD B2C Directory-klienten.
Om programmet tas bort, tas även dessa tilläggs egenskaper tillsammans med alla data som finns i dem bort för alla användare. Om en tilläggs egenskap tas bort av programmet, tas den bort från mål katalogens objekt och värdena tas bort.

Tilläggs egenskaper finns bara i kontexten för ett registrerat program i klienten. Objekt-ID för programmet måste ingå i **TechnicalProfile** som använder det.

>[!NOTE]
>Azure AD B2C-katalogen innehåller vanligt vis en webbapp med namnet `b2c-extensions-app`. Det här programmet används främst av de inbyggda B2C-principerna för de anpassade anspråk som skapats via Azure Portal. Vi rekommenderar att endast avancerade användare registrerar tillägg för anpassade B2C-principer med hjälp av det här programmet.
Instruktioner finns i avsnittet **Nästa steg** i den här artikeln.

## <a name="create-a-new-application-to-store-the-extension-properties"></a>Skapa ett nytt program för att lagra tilläggs egenskaperna

1. Öppna en webbläsarsession och navigera till [Azure Portal](https://portal.azure.com). Logga in med administratörs behörigheterna för den B2C-katalog som du vill konfigurera.
2. Välj **Azure Active Directory** på den vänstra navigerings menyn. Du kan behöva hitta den genom att välja **fler tjänster**.
3. Välj **Appregistreringar**. Välj **Ny programregistrering**.
4. Ange följande poster:
    * Ett namn för webb programmet: **webapp-GraphAPI-DirectoryExtensions**.
    * Program typ: **webbapp/API**.
    * Inloggnings-URL: **https://{tenantName}. onmicrosoft. com/webapp-GraphAPI-DirectoryExtensions**.
5. Välj **Skapa**.
6. Välj det nyligen skapade webb programmet.
7. Välj **inställningar** > **nödvändiga behörigheter**.
8. Välj API **Windows-Azure Active Directory**.
9. Ange en bock i program behörigheter: **läsa och skriva katalog data**. Välj sedan **Spara**.
10. Välj **bevilja behörigheter** och bekräfta **Ja**.
11. Kopiera följande identifierare till Urklipp och spara dem:
    * **Program-ID**. Exempel: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Objekt-ID**. Exempel: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.

## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Ändra den anpassade principen för att lägga till **ApplicationObjectId**

När du följde stegen i [Azure Active Directory B2C: kom igång med anpassade principer](custom-policy-get-started.md), hämtade och ändrade [exempelfilerna](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) med namnet **TrustFrameworkBase. XML**, **TrustFrameworkExtensions. XML**, **SignUpOrSignin. XML**, **ProfileEdit. XML**och **PasswordReset original. XML**. I det här steget gör du fler ändringar av filerna.

* Öppna filen **TrustFrameworkBase. XML** och Lägg till avsnittet `Metadata` som visas i följande exempel. Infoga det objekt-ID som du tidigare har spelat in för `ApplicationObjectId`-värdet och det program-ID som du registrerade för `ClientId` svärdet:

    ```xml
    <ClaimsProviders>
      <ClaimsProvider>
        <DisplayName>Azure Active Directory</DisplayName>
        <TechnicalProfile Id="AAD-Common">
          <DisplayName>Azure Active Directory</DisplayName>
          <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />

          <!-- Provide objectId and appId before using extension properties. -->
          <Metadata>
            <Item Key="ApplicationObjectId">insert objectId here</Item>
            <Item Key="ClientId">insert appId here</Item>
          </Metadata>
          <!-- End of changes -->

          <CryptographicKeys>
            <Key Id="issuer_secret" StorageReferenceId="TokenSigningKeyContainer" />
          </CryptographicKeys>
          <IncludeInSso>false</IncludeInSso>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop" />
        </TechnicalProfile>
      </ClaimsProvider>
    </ClaimsProviders>
    ```

> [!NOTE]
> När **TechnicalProfile** skriver för första gången till egenskapen nyligen skapade tillägg kan det uppstå ett engångs fel. Tilläggs egenskapen skapas första gången den används.

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Använd den nya tilläggs egenskapen eller det anpassade attributet i en användar resa

1. Öppna filen **ProfileEdit. XML** .
2. Lägg till en anpassad anspråks `loyaltyId`. Genom att inkludera det anpassade anspråket i `<RelyingParty>`-elementet ingår det i token för programmet.

    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="ProfileEdit" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
          <OutputClaim ClaimTypeReferenceId="city" />

          <!-- Provide the custom claim identifier -->
          <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
          <!-- End of changes -->
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```

3. Öppna filen **TrustFrameworkExtensions. XML** och lägg till`<ClaimsSchema>`-elementet och dess underordnade element till `BuildingBlocks`-elementet:

    ```xml
    <BuildingBlocks>
      <ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
          <DisplayName>Loyalty Identification Tag</DisplayName>
          <DataType>string</DataType>
          <UserHelpText>Your loyalty number from your membership card</UserHelpText>
          <UserInputType>TextBox</UserInputType>
        </ClaimType>
      </ClaimsSchema>
    </BuildingBlocks>
    ```

4. Lägg till samma `ClaimType`-definition till **TrustFrameworkBase. XML**. Du behöver inte lägga till en `ClaimType`-definition i både bas-och tillägg-filerna. Nästa steg är dock att lägga till `extension_loyaltyId` i **TechnicalProfiles** i bas filen. Det innebär att princip verifieringen avvisar överföringen av bas filen utan den. Det kan vara användbart att spåra körningen av användar resan med namnet **ProfileEdit** i filen **TrustFrameworkBase. XML** . Sök efter användar resan med samma namn i redigeraren. Observera att Orchestration steg 5 anropar **TechnicalProfileReferenceID = "SelfAsserted-ProfileUpdate**. Sök efter och granska den här **TechnicalProfile** för att bekanta dig med flödet.

5. Öppna filen **TrustFrameworkBase. XML** och lägg till `loyaltyId` som ett indata-och utgående anspråk i **TechnicalProfile SelfAsserted-ProfileUpdate**:

    ```xml
    <TechnicalProfile Id="SelfAsserted-ProfileUpdate">
      <DisplayName>User ID signup</DisplayName>
      <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.SelfAssertedAttributeProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
      <Metadata>
        <Item Key="ContentDefinitionReferenceId">api.selfasserted.profileupdate</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="alternativeSecurityId" />
        <InputClaim ClaimTypeReferenceId="userPrincipalName" />
        <InputClaim ClaimTypeReferenceId="givenName" />
        <InputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
        <!-- End of changes -->

      </OutputClaims>
      <ValidationTechnicalProfiles>
        <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
      </ValidationTechnicalProfiles>
    </TechnicalProfile>
    ```

6. I filen **TrustFrameworkBase. XML** lägger du till `loyaltyId`-anspråk till **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Detta tillägg behåller värdet för anspråket i tilläggs egenskapen för den aktuella användaren i katalogen:

    ```xml
    <TechnicalProfile Id="AAD-UserWriteProfileUsingObjectId">
      <Metadata>
        <Item Key="Operation">Write</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalAlreadyExists">false</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <PersistedClaims>
        <PersistedClaim ClaimTypeReferenceId="objectId" />
        <PersistedClaim ClaimTypeReferenceId="givenName" />
        <PersistedClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </PersistedClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

7. I filen **TrustFrameworkBase. XML** lägger du till `loyaltyId`-anspråk till **TechnicalProfile AAD-UserReadUsingObjectId** för att läsa värdet för attributet Extension varje gång en användare loggar in. Hittills har **TechnicalProfiles** endast ändrats i flödet av lokala konton. Om du vill ha det nya attributet i flödet för ett socialt eller federerat konto måste du ändra en annan uppsättning **TechnicalProfiles** . Se **Nästa steg** -avsnitt.

    ```xml
    <TechnicalProfile Id="AAD-UserReadUsingObjectId">
      <Metadata>
        <Item Key="Operation">Read</Item>
        <Item Key="RaiseErrorIfClaimsPrincipalDoesNotExist">true</Item>
      </Metadata>
      <IncludeInSso>false</IncludeInSso>
      <InputClaims>
        <InputClaim ClaimTypeReferenceId="objectId" Required="true" />
      </InputClaims>
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="otherMails" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />

        <!-- Add the loyalty identifier -->
        <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
        <!-- End of changes -->

      </OutputClaims>
      <IncludeTechnicalProfile ReferenceId="AAD-Common" />
    </TechnicalProfile>
    ```

## <a name="test-the-custom-policy"></a>Testa den anpassade principen

1. Öppna bladet Azure AD B2C och gå till **identifieraren för identitets miljö** > **anpassade principer**.
1. Välj den anpassade princip som du överförde. Välj **Kör nu**.
1. Registrera dig med hjälp av en e-postadress.

ID-token som skickas tillbaka till programmet inkluderar den nya tilläggs egenskapen som ett anpassat anspråk som föregås av **extension_loyaltyId**. Se följande exempel:

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
  "sub": "a58e7c6c-7535-4074-93da-b0023fbaf3ac",
  "aud": "4e87c1dd-e5f5-4ac8-8368-bc6a98751b8b",
  "acr": "b2c_1a_trustframeworkprofileedit",
  "nonce": "defaultNonce",
  "iat": 1493581587,
  "auth_time": 1493581587,
  "extension_loyaltyId": "abc",
  "city": "Redmond"
}
```

## <a name="next-steps"></a>Nästa steg

1. Lägg till det nya anspråket till flödena för att logga in på sociala konton genom att ändra följande **TechnicalProfiles**. Sociala och federerade konton använder dessa två **TechnicalProfiles** för att logga in. De skriver och läser användar data genom att använda **alternativeSecurityId** som plats för användar objektets positionerare.

   ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
   ```

2. Använd samma attribut för tillägg mellan inbyggda och anpassade principer. När du lägger till tillägg, eller anpassade, attribut via portal miljön registreras attributen med hjälp av **B2C-Extensions-appen** som finns i varje B2C-klient. Utför följande steg för att använda attribut för tillägg i den anpassade principen:

   a. I B2C-klienten i portal.azure.com går du till **Azure Active Directory** och väljer **Appregistreringar**.
   b. Hitta din **B2C-Extensions-app** och välj den.
   c. Under **Essentials**anger du **program-ID** och **objekt-ID**.
   d. Ta med dem i dina **AAD-vanliga TechnicalProfile-** metadata:

   ```xml
      <ClaimsProviders>
        <ClaimsProvider>
          <DisplayName>Azure Active Directory</DisplayName>
          <TechnicalProfile Id="AAD-Common">
            <DisplayName>Azure Active Directory</DisplayName>
            <Protocol Name="Proprietary" Handler="Web.TPEngine.Providers.AzureActiveDirectoryProvider, Web.TPEngine, Version=1.0.0.0, Culture=neutral, PublicKeyToken=null" />
            <!-- Provide objectId and appId before using extension properties. -->
            <Metadata>
              <Item Key="ApplicationObjectId">insert objectId here</Item> <!-- This is the "Object ID" from the "b2c-extensions-app"-->
              <Item Key="ClientId">insert appId here</Item> <!--This is the "Application ID" from the "b2c-extensions-app"-->
            </Metadata>
   ```

3. Håll dig konsekvent med Portal upplevelsen. Skapa de här attributen med hjälp av Portal gränssnittet innan du använder dem i dina anpassade principer. När du skapar ett attribut **ActivationStatus** i portalen måste du referera till det enligt följande:

   ```
   extension_ActivationStatus in the custom policy.
   extension_<app-guid>_ActivationStatus via Graph API.
   ```

## <a name="reference"></a>Referens

Mer information om tilläggs egenskaper finns i artikeln [lägga till anpassade data till resurser med hjälp av tillägg](https://docs.microsoft.com/graph/extensibility-overview).

> [!NOTE]
> * En **TechnicalProfile** är en element typ, eller funktion, som definierar en slut punkts namn, metadata och protokoll. **TechnicalProfile** innehåller information om utbytet av anspråk som identitets miljöns ramverk utför. När den här funktionen anropas i ett Orchestration-steg eller från en annan **TechnicalProfile**anges **InputClaims** och **OutputClaims** som parametrar av anroparen.
> * Attribut för tillägg i Graph API namnges med hjälp av konventions `extension_ApplicationObjectID_attributename`.
> * Anpassade principer refererar till tilläggs-attribut som **extension_attributename**. Den här referensen utesluter **ApplicationObjectId** i XML.
> * Du måste ange attribut-ID i följande format **extension_attributename** när det refereras till.
