---
title: Lägga till egna attribut i anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: En genomgång av hur du använder tilläggsegenskaper, anpassade attribut och inkludera dem i användargränssnittet.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/04/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ecde4d8cd8ee454290b16b640ba05d310cf348fe
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/03/2018
ms.locfileid: "37450440"
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Skapa och använda anpassade attribut i en anpassad profil Redigera princip

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln får du skapa ett anpassat attribut i Azure AD B2C-katalogen och använda den här nya attribut som ett anpassat anspråk i användarresan för profilen redigera.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i artikeln [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Använd anpassade attribut för att samla in information om dina kunder i Azure Active Directory B2C med anpassade principer
Din Azure Active Directory (Azure AD) B2C-katalog som levereras med en inbyggd uppsättning attribut: givet namn, efternamn, stad, postnummer, userPrincipalName, osv.  Du behöver ofta skapa dina egna attribut.  Exempel:
* En kundinriktad programmet behöver för att bevara ett attribut, till exempel ”LoyaltyNumber”.
* En identitetsleverantör har ett unikt användar-ID som måste sparas, till exempel ”uniqueUserGUID” ”.
* En anpassad användarresa måste bevara tillståndet för användaren, till exempel ”migrationStatus”.

Med Azure AD B2C kan du utöka uppsättningen attribut som lagras på varje användarkonto. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Tilläggsegenskaper utöka schemat för användarobjekt i katalogen.  Allmänna tilläggsegenskap, anpassat attribut och anpassade anspråk avser samma sak i kontexten för den här artikeln och namnet varierar beroende på kontext (program, objekt, princip).

Tilläggsegenskaper kan bara registreras för ett programobjekt trots att de kan innehålla data för en användare. Egenskapen är kopplad till programmet. Programobjektet måste beviljas skrivbehörighet för att registrera en tilläggsegenskap. 100 tilläggsegenskaper (över alla typer och alla program) kan skrivas till ett enskilt objekt. Tilläggsegenskaper läggs till i målkatalogtypen och blir omedelbart tillgänglig i Azure AD B2C-directory-klient.
Om programmet raderas tas de tilläggsegenskaper tillsammans med alla data i dem för alla användare också bort. Om en tilläggsegenskap tas bort av programmet, tas den bort på katalogobjekt mål och de värden som har tagits bort.

Tilläggsegenskaper finns bara i samband med ett registrerat program i klienten. Objekt-id för programmet måste inkluderas i den tekniska som använder den.

>[!NOTE]
>Azure AD B2C-katalogen innehåller vanligen en Webbapp med namnet `b2c-extensions-app`.  Det här programmet används främst av b2c inbyggda principer för de anpassade anspråk som skapats via Azure portal.  Med hjälp av det här programmet för att registrera tillägg för anpassade principer för b2c rekommenderas endast för avancerade användare.  Instruktioner för detta finns i avsnittet nästa steg i den här artikeln.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Skapa ett nytt program för att lagra tilläggsegenskaper

1. Öppna en webbläsarsession och navigera till den [Azure-portalen](https://portal.azure.com) och logga in med administratörsbehörighet för B2C-katalog som du vill konfigurera.
2. Klicka på **Azure Active Directory** på den vänstra navigeringsmenyn. Du kan behöva hitta den genom att välja fler tjänster >.
3. Välj **appregistreringar** och klicka på **ny programregistrering**
4. Ange följande rekommenderade poster:
    * Ange ett namn för webbprogrammet: **WebApp-GraphAPI-DirectoryExtensions**
    * Programtyp: Web app/API
    * URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions inloggning
5. Välj **Skapa**.
6. Välj det nyligen skapade webbprogrammet.
7. Välj **inställningar** > **behörigheter som krävs för**.
8. Välj API **Windows Azure Active Directory**.
9. Markera programbehörigheter: **läsa och skriva katalogdata**, och välj sedan **spara**.
10. Välj **bevilja** och bekräfta **Ja**.
11. Kopiera till Urklipp och spara följande identifierare:
    * **Program-ID** . Exempel: `103ee0e6-f92d-4183-b576-8c3739027780`
    * **Objekt-ID**. Exempel: `80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Ändra en anpassad princip för att lägga till ApplicationObjectId

När du slutfört stegen i [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md), du hämtade och ändrade [filer](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/archive/master.zip) med namnet *TrustFrameworkBase.xml*,  *TrustFrameworkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, och *PasswordReset.xml*. I följande steg ska fortsätta du att göra ändringar i filerna.

1. Öppna den *TrustFrameworkBase.xml* filen och Lägg till den `Metadata` som visas i följande exempel. Infoga objekt-ID som du tidigare registrerade för den `ApplicationObjectId` värdet och det program-ID som du registrerade för den `ClientId` värde: 

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

>[!NOTE]
>När tekniska skriver för första gången till nyligen skapade tilläggsegenskapen, kan det uppstå ett enstaka fel. Tilläggsegenskapen skapas första gången den används.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Med den nya tilläggsegenskapen / anpassade attribut i en användarresa

1. Öppna den *ProfileEdit.xml* fil.
2. Lägg till ett anpassat anspråk `loyaltyId`.  Genom att inkludera anpassat anspråk i den `<RelyingParty>` element, den ingår i token för programmet.
    
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

3. Öppna den *TrustFrameworkExtensions.xml* filen och Lägg till den`<ClaimsSchema>` elementet och dess underordnade element till den `BuildingBlocks` element:

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

4. Lägga till samma `ClaimType` definitionen för att *TrustFrameworkBase.xml*. Att lägga till en `ClaimType` definitionen i både bas- och tilläggsfilen är normalt inte behövs, men eftersom lägger till nästa steg i `extension_loyaltyId` till TechnicalProfiles i filen grundläggande princip verifieraren avvisar överföringen av grundläggande filen utan den. Det kan vara praktiskt att spåra körningen av användarresa med namnet ”ProfileEdit” i den *TrustFrameworkBase.xml* fil.  Sök efter användarresa med samma namn i redigeringsprogrammet och notera att Orchestration steg 5 anropar TechnicalProfileReferenceID = ”SelfAsserted ProfileUpdate”.  Sök och granska den här tekniska att bekanta dig med flödet.

5. Öppna den *TrustFrameworkBase.xml* filen och Lägg till `loyaltyId` som en ingående och utgående anspråksuppsättningar i tekniska ”SelfAsserted ProfileUpdate”:

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

6. I den *TrustFrameworkBase.xml* Lägg till den `loyaltyId` anspråk som ska tekniska ”AAD-UserWriteProfileUsingObjectId” att spara värdet för anspråket i tilläggsegenskapen för den aktuella användaren i katalogen:

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

7. I den *TrustFrameworkBase.xml* Lägg till den `loyaltyId` anspråk som ska tekniska ”AAD-UserReadUsingObjectId” att läsa värdet för attributet anknytning varje gång en användare loggar in. Än så länge har TechnicalProfiles ändrats i flödet för lokala konton.  Om det nya attributet är det önskade i flödet för en social/federerad konto, måste en annan uppsättning TechnicalProfiles ändras. Se nästa steg.

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

1. Öppna den **Azure AD B2C-bladet** och gå till **Identitetsramverk > anpassade principer**.
1. Välj den anpassade principen som du laddat upp och klicka på den **kör nu** knappen.
1. Du ska kunna registrera dig med en e-postadress.

Id-token som skickas tillbaka till ditt program innehåller den nya tilläggsegenskapen som ett anpassat anspråk som föregås av extension_loyaltyId. Se exempel.

```json
{
  "exp": 1493585187,
  "nbf": 1493581587,
  "ver": "1.0",
  "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Lägg till nytt anspråk till flöden för socialt konto inloggningar genom att ändra TechnicalProfiles som anges nedan. Dessa två TechnicalProfiles som används av sociala/federerad inloggningsnamn att skriva och läsa informationen med alternativeSecurityId som positionerare för användarobjektet.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Använd samma tilläggsattribut mellan inbyggda och anpassade principer.
När du lägger till tilläggsattribut (även kallat anpassade attribut) via en Portal attributen registreras med hjälp av den ** b2c-extensions-app som finns i varje b2c-klienten.  Du använder dessa tilläggsattribut i en egen princip:
1. Gå till din b2c-klient Portal.Azure.com **Azure Active Directory** och välj **appregistreringar**
2. Hitta din **b2c-extensions-app** och välj den
3. Under ”Essentials” post i **program-ID** och **objekt-ID**
4. Lägga till dem i din AAD-gemensamma tekniska profilens metadata som på följande sätt:

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

Skapa dessa attribut med hjälp av portalens användargränssnitt för att hålla konsekvens med portalmiljön *innan* du använda dem i dina anpassade principer.  När du skapar ett attribut ”ActivationStatus” i portalen, måste du referera till den på följande sätt:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referens

* En **tekniska profil (TP)** är en elementtyp av som kan betraktas som en *funktionen* som definierar en slutpunkt namn, dess metadata, dess protokoll och detaljer utbyten av anspråk som identitet Upplevelse Framework ska utföra.  När detta *funktionen* kallas i orchestration-steg eller från en annan TechnicalProfile, InputClaims och OutputClaims tillhandahålls som parametrar av anroparen.


* Fullständig behandling på tilläggsegenskaper finns i artikeln [DIRECTORY-SCHEMAUTÖKNINGAR | GRAPH API-BEGREPP](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Tilläggsattribut i Graph API namnges med konventionen `extension_ApplicationObjectID_attributename`. Anpassade principer som refererar till tillägg attribut som extension_attributename, så om du utesluter ApplicationObjectId i XML
