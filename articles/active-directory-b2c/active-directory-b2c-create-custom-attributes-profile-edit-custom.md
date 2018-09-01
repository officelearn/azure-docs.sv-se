---
title: Lägga till egna attribut i anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: En genomgång av hur du använder tilläggsegenskaper och anpassade attribut och inkludera dem i användargränssnittet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5513e0ff434862ea7eee42cb94ff2a0f67f6d390
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/31/2018
ms.locfileid: "43338752"
---
# <a name="azure-active-directory-b2c-use-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Använd anpassade attribut i en anpassad profil Redigera princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln skapar du ett anpassat attribut i din Azure Active Directory (Azure AD) B2C-katalog. Du ska använda den här nya attribut som ett anpassat anspråk i användarresan för profilen redigera.

## <a name="prerequisites"></a>Förutsättningar

Följ stegen i artikeln [Azure Active Directory B2C: Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-ad-b2c-by-using-custom-policies"></a>Använd anpassade attribut för att samla in information om dina kunder i Azure AD B2C med anpassade principer
Din Azure AD B2C-katalog levereras med en inbyggd uppsättning attribut. Exempel är **Förnamn**, **efternamn**, **Stad**, **postnummer**, och **userPrincipalName**. Du behöver ofta skapa dina egna attribut som de här exemplen:
* En kundinriktad programmet behöver för att bevara för ett attribut som **LoyaltyNumber.**
* En identitetsleverantör har unika användar-ID som **uniqueUserGUID** som måste sparas.
* En anpassad användarresa måste upprätthålls i ett tillstånd för en användare som **migrationStatus**.

Azure AD B2C utökar uppsättningen attribut som lagras på varje användarkonto. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Tilläggsegenskaper utöka schemat för användarobjekt i katalogen. Villkoren *tilläggsegenskap*, *anpassat attribut*, och *anpassade anspråk* avser samma sak i kontexten för den här artikeln. Namnet varierar beroende på kontext, till exempel program, objekt eller princip.

Tilläggsegenskaper kan bara registreras för ett programobjekt trots att de kan innehålla data för en användare. Egenskapen är kopplad till programmet. Programobjektet måste ha skrivbehörighet för att registrera en tilläggsegenskap. Hundra tilläggsegenskaper, kan i alla typer och alla program, skrivas till ett enskilt objekt. Tilläggsegenskaper läggs till i målkatalogtypen och blir omedelbart tillgängliga i Azure AD B2C-directory-klient.
Om programmet raderas tas de tilläggsegenskaper tillsammans med alla data i dem för alla användare också bort. Om en tilläggsegenskap tas bort av programmet, tas den bort på mål-katalogobjekt och värdena tas bort.

Tilläggsegenskaper finns bara i samband med ett registrerat program i klienten. Objekt-ID för programmet måste inkluderas i den **TechnicalProfile** som använder den.

>[!NOTE]
>Azure AD B2C-katalogen innehåller vanligen en webbapp med namnet `b2c-extensions-app`. Det här programmet används främst av B2C inbyggda principer för de anpassade anspråk som skapats via Azure portal. Vi rekommenderar att endast avancerade användare registrerar tillägg för anpassade B2C-principer med hjälp av det här programmet.  
Instruktioner finns i den **nästa steg** i den här artikeln.


## <a name="create-a-new-application-to-store-the-extension-properties"></a>Skapa ett nytt program för att lagra tilläggsegenskaper

1. Öppna en webbläsarsession och navigera till den [Azure-portalen](https://portal.azure.com). Logga in med administratörsbehörighet för B2C-katalog som du vill konfigurera.
2. Välj **Azure Active Directory** på den vänstra navigeringsmenyn. Du kan behöva hitta den genom att välja **fler tjänster**.
3. Välj **Appregistreringar**. Välj **Ny programregistrering**.
4. Ange följande poster:
    * Ett namn för webbprogrammet: **WebApp-GraphAPI-DirectoryExtensions**.
    * Programtyp: **Web app/API**.
    * Inloggnings URL: **https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions**.
5. Välj **Skapa**.
6. Välj det nyligen skapade webbprogrammet.
7. Välj **inställningar** > **behörigheter som krävs för**.
8. Välj API **Windows Azure Active Directory**.
9. Markera det programbehörigheter: **läsa och skriva katalogdata**. Välj sedan **Spara**.
10. Välj **bevilja** och bekräfta **Ja**.
11. Kopiera följande identifierare till Urklipp och spara dem:
    * **Program-ID**. Exempel: `103ee0e6-f92d-4183-b576-8c3739027780`.
    * **Objekt-ID**. Exempel: `80d8296a-da0a-49ee-b6ab-fd232aa45201`.



## <a name="modify-your-custom-policy-to-add-the-applicationobjectid"></a>Ändra en anpassad princip för att lägga till den **ApplicationObjectId**

När du har följt stegen i [Azure Active Directory B2C: Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md), du hämtade och ändrade [exempelfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) med namnet **TrustFrameworkBase.xml**, **TrustFrameworkExtensions.xml**, **SignUpOrSignin.xml**, **ProfileEdit.xml**, och **PasswordReset.xml**. I det här steget ska göra du fler ändringar i filerna.

* Öppna den **TrustFrameworkBase.xml** filen och Lägg till den `Metadata` som visas i följande exempel. Infoga objekt-ID som du tidigare registrerade för den `ApplicationObjectId` värdet och det program-ID som du registrerade för den `ClientId` värde: 

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
> När den **TechnicalProfile** skriver för första gången till den nyligen skapade tilläggsegenskapen ett enstaka fel kan uppstå. Tilläggsegenskapen skapas första gången den används.  

## <a name="use-the-new-extension-property-or-custom-attribute-in-a-user-journey"></a>Använd den nya tilläggsegenskapen eller anpassat attribut i en användarresa

1. Öppna den **ProfileEdit.xml** fil.
2. Lägg till ett anpassat anspråk `loyaltyId`. Genom att inkludera anpassat anspråk i den `<RelyingParty>` element, den ingår i token för programmet.
    
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

3. Öppna den **TrustFrameworkExtensions.xml** filen och Lägg till den`<ClaimsSchema>` elementet och dess underordnade element till den `BuildingBlocks` element:

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

4. Lägga till samma `ClaimType` definitionen för att **TrustFrameworkBase.xml**. Det är inte nödvändigt att lägga till en `ClaimType` definition i både bas- och tillägg-filer. Dock lägga till i nästa steg i `extension_loyaltyId` till **TechnicalProfiles** i grundläggande-filen. Principen verifieraren avvisar så överföringen av grundläggande filen utan att göra den. Det kan vara praktiskt att spåra körningen av användarresa med namnet **ProfileEdit** i den **TrustFrameworkBase.xml** fil. Sök efter användarresa med samma namn i redigeringsprogrammet. Observera att Orchestration-steg 5 anropar den **TechnicalProfileReferenceID = ”SelfAsserted ProfileUpdate**. Sök och granska detta **TechnicalProfile** att bekanta dig med flödet.

5. Öppna den **TrustFrameworkBase.xml** filen och Lägg till `loyaltyId` som en ingående och utgående anspråksuppsättningar i den **TechnicalProfile SelfAsserted-ProfileUpdate**:

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

6. I den **TrustFrameworkBase.xml** Lägg till den `loyaltyId` anspråk som ska **TechnicalProfile AAD-UserWriteProfileUsingObjectId**. Det här tillägget kvarstår värdet för anspråket i tilläggsegenskapen för den aktuella användaren i katalogen:

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

7. I den **TrustFrameworkBase.xml** Lägg till den `loyaltyId` anspråk som ska **TechnicalProfile AAD-UserReadUsingObjectId** att läsa värdet för attributet anknytning varje gång en användare loggar in. Hittills den **TechnicalProfiles** har ändrats i flödet för lokala konton. Om du vill att det nya attributet i flödet för en social eller federerade-konto, en annan uppsättning **TechnicalProfiles** måste ändras. Se den **nästa steg** avsnittet.

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

1. Öppna bladet Azure AD B2C och gå till **Identitetsramverk** > **anpassade principer**.
1. Välj den anpassade principen som du överförde. Välj **kör nu**.
1. Registrera dig med hjälp av en e-postadress.

ID-token som skickas tillbaka till ditt program innehåller den nya tilläggsegenskapen som ett anpassat anspråk som föregås av **extension_loyaltyId**. Se följande exempel:

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

1. Lägg till nytt anspråk till flöden till att logga in på sociala konton genom att ändra följande **TechnicalProfiles**. Sociala och externa konton använder du dessa två **TechnicalProfiles** att logga in. De skriva och läsa användarens data med hjälp av den **alternativeSecurityId** som positionerare för användarobjektet.

  ```xml
    <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

    <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
  ```

2. Använd samma tilläggsattribut mellan inbyggda och anpassade principer. När du lägger till tillägget eller anpassad, attribut via en Portal dessa attribut är registrerat med hjälp av den **b2c-extensions-app** som finns i varje B2C-klienten. Vidta följande steg för att använda tilläggsattribut i en egen princip:

  a. Gå till din B2C-klient Portal.Azure.com **Azure Active Directory** och välj **appregistreringar**.  
  b. Hitta din **b2c-extensions-app** och markera den.  
  c. Under **Essentials**, ange den **program-ID** och **objekt-ID**.  
  d. Inkludera dem i din **AAD-gemensamma** tekniska metadata:  

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

3. Håll konsekvent med en Portal. Skapa dessa attribut med hjälp av portalens användargränssnitt innan du använder dem i dina anpassade principer. När du skapar ett attribut **ActivationStatus** i portalen, du måste referera till den på följande sätt:

  ```
  extension_ActivationStatus in the custom policy.
  extension_<app-guid>_ActivationStatus via Graph API.
  ```


## <a name="reference"></a>Referens

Mer information om tilläggsegenskaper finns i artikeln [Directory-schemautökningar | Graph API-begrepp](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions).

> [!NOTE]
> * En **TechnicalProfile** är en elementtyp eller funktionen, som definierar en slutpunkt namn, metadata och protokoll. Den **TechnicalProfile** information utbyten av anspråk som den Identitetsramverk utför. När den här funktionen anropas i orchestration-steg eller från en annan **TechnicalProfile**, **InputClaims** och **OutputClaims** anges som parametrar av anroparen .  
> * Tilläggsattribut i Graph API namnges med hjälp av konventionen `extension_ApplicationObjectID_attributename`.  
> * Anpassade principer avser tilläggsattribut som **extension_attributename**. Den här referensen utesluter den **ApplicationObjectId** i XML.
