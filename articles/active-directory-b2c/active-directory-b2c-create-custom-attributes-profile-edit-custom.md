---
title: "Azure Active Directory B2C: Lägga till egna attribut i anpassade principer och använda i profilen redigera | Microsoft Docs"
description: "En genomgång av använder tilläggsegenskaper, anpassade attribut och inkludera dem i användargränssnittet"
services: active-directory-b2c
documentationcenter: 
author: rojasja
manager: krassk
editor: 
ms.assetid: 
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 08/04/2017
ms.author: joroja
ms.openlocfilehash: f3e4eb6fedf850dbb827fd2a10593249d2f17ef1
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="azure-active-directory-b2c-creating-and-using-custom-attributes-in-a-custom-profile-edit-policy"></a>Azure Active Directory B2C: Skapa och använda anpassade attribut i en anpassad profil redigera principen

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I den här artikeln, skapa ett anpassat attribut i Azure AD B2C-katalogen och använda den här nya attributet som ett anpassat anspråk i profilen Redigera användare transporten.

## <a name="prerequisites"></a>Krav

Utför stegen i artikeln [komma igång med principer för anpassade](active-directory-b2c-get-started-custom.md).

## <a name="use-custom-attributes-to-collect-information-about-your-customers-in-azure-active-directory-b2c-using-custom-policies"></a>Använd anpassade attribut för att samla in information om dina kunder i Azure Active Directory B2C anpassade principer
Din Azure Active Directory (AD Azure) B2C-katalog som levereras med en inbyggd uppsättning attribut: namn, efternamn, ort, postnummer, userPrincipalName får osv.  Du behöver ofta skapa egna attribut.  Exempel:
* Ett kund-riktade program måste bevara ett attribut, till exempel ”LoyaltyNumber”.
* En identitetsleverantör har unika användar-ID som måste till exempel ”uniqueUserGUID” ”.
* En anpassad användare resa måste bevara tillståndet för användaren, till exempel ”migrationStatus”.

Med Azure AD B2C kan du utöka uppsättningen attribut som lagras för varje användarkonto. Du kan också läsa och skriva dessa attribut med hjälp av den [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md).

Egenskaper för webbtjänsttillägg utöka schemat för användarobjekt i katalogen.  Villkoren utökade egenskapen, det anpassade attributet och anpassade anspråk refererar till samma sak i kontexten för den här artikeln och namnet varierar beroende på kontext (program, objekt, princip).

Egenskaper för webbtjänsttillägg kan endast registreras på ett programobjekt trots att de kan innehålla data för en användare. Egenskapen är kopplad till programmet. Programobjektet måste beviljas skrivbehörighet för att registrera en egenskap för tillägget. 100 tilläggsegenskaper (över alla typer och alla program) kan skrivas till något enskilt objekt. Egenskaper för webbtjänsttillägg läggs till i katalogen måltypen och blir omedelbart tillgängligt i Azure AD B2C directory-klient.
Om programmet raderas tas de tilläggsegenskaper tillsammans med alla data i dem för alla användare också bort. Om en egenskap för tillägget tas bort av programmet tas bort den på katalogobjekt målet och de värden som tas bort.

Egenskaper för webbtjänsttillägg finns bara i kontexten för ett program som är registrerade i klienten. Objekt-id för programmet måste inkluderas i TechnicalProfile som använder den.

>[!NOTE]
>Azure AD B2C-katalogen innehåller vanligtvis ett webbprogram med namnet `b2c-extensions-app`.  Det här programmet används främst av b2c inbyggda principer för anpassade anspråk som skapats via Azure-portalen.  Det här programmet rekommenderas för att registrera tillägg för b2c anpassade principer endast för avancerade användare.  Instruktioner för detta finns i avsnittet nästa steg i den här artikeln.


## <a name="creating-a-new-application-to-store-the-extension-properties"></a>Skapa ett nytt program för att lagra egenskaper för webbtjänsttillägg

1. Öppna en webbläsarsession och navigera till den [Azure-portalen](https://portal.azure.com) och logga in med administratörsbehörighet för B2C-katalog som du vill konfigurera.
1. Klicka på **Azure Active Directory** på den vänstra navigeringsmenyn. Du kan behöva hitta genom att välja fler tjänster >.
1. Välj **App registreringar** och på **nya appregistrering**
1. Ange följande rekommenderade poster:
  * Ange ett namn för webbprogrammet: **WebApp-GraphAPI-DirectoryExtensions**
  * Programtyp: webb-app/API
  * URL:https://{tenantName}.onmicrosoft.com/WebApp-GraphAPI-DirectoryExtensions inloggning
1. Välj ** Skapa. Åtgärden lyckades visas i den **meddelanden**
1. Välj det nyligen skapade webbprogrammet: **WebApp-GraphAPI-DirectoryExtensions**
1. Välj inställningar: **nödvändiga behörigheter**
1. Välj API **Windows Azure Active Directory**
1. Markera programbehörigheter: **läsning och skrivning katalogdata**, och **spara**
1. Välj **bevilja behörigheter** och bekräfta **Ja**.
1. Kopiera till Urklipp och spara följande identifierare från WebApp-GraphAPI-DirectoryExtensions > Inställningar > Egenskaper >
*  **Program-ID** . Exempel:`103ee0e6-f92d-4183-b576-8c3739027780`
* **Objekt-ID**. Exempel:`80d8296a-da0a-49ee-b6ab-fd232aa45201`



## <a name="modifying-your-custom-policy-to-add-the-applicationobjectid"></a>Ändra en anpassad princip för att lägga till ApplicationObjectId

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
>Den <TechnicalProfile Id="AAD-Common"> kallas för ”gemensamma” eftersom dess element ingår i och återanvändas i alla de Azure Active Directory TechnicalProfiles med hjälp av element:`<IncludeTechnicalProfile ReferenceId="AAD-Common" />`

>[!NOTE]
>När TechnicalProfile skriver för första gången till den nyligen skapade utökade egenskapen, kan det uppstå ett enstaka fel.  Den utökade egenskapen skapas första gången den används.  

## <a name="using-the-new-extension-property--custom-attribute-in-a-user-journey"></a>Med hjälp av egenskapen extension / anpassade attribut i en användare resa


1. Öppna filen förlitande Party(RP) som beskriver principen Redigera användare resa.  Om du startar kan det vara tillrådligt att hämta din redan konfigurerade version av filen RP PolicyEdit direkt från avsnittet anpassad princip för Azure B2C i Azure-portalen.  Du kan också öppna XML-filen från lagringsmappen.
2. Lägg till ett anpassat anspråk `loyaltyId`.  Genom att lägga till ett anpassat anspråk i den `<RelyingParty>` element den skickas som en parameter för UserJourney TechnicalProfiles och ingår i token för programmet.
```xml
<RelyingParty>
   <DefaultUserJourney ReferenceId="ProfileEdit" />
   <TechnicalProfile Id="PolicyProfile">
     <DisplayName>PolicyProfile</DisplayName>
     <Protocol Name="OpenIdConnect" />
     <OutputClaims>
       <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub"/>
       <OutputClaim ClaimTypeReferenceId="city" />

       <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />

     </OutputClaims>
     <SubjectNamingInfo ClaimType="sub" />
   </TechnicalProfile>
 </RelyingParty>
 ```
3. Lägg till en definition av anspråk i princip tilläggsfilen `TrustFrameworkExtensions.xml` inuti den `<ClaimsSchema>` element som visas.
```xml
<ClaimsSchema>
        <ClaimType Id="extension_loyaltyId">
            <DisplayName>Loyalty Identification Tag</DisplayName>
            <DataType>string</DataType>
            <UserHelpText>Your loyalty number from your membership card</UserHelpText>
            <UserInputType>TextBox</UserInputType>
        </ClaimType>
</ClaimsSchema>
```
4. Lägg till samma anspråk definition till den grundläggande principfilen `TrustFrameworkBase.xml`.  
>Lägga till en `ClaimType` definition i både bas- och tilläggsfilen är normalt inte behövs, men eftersom nästa steg lägger till extension_loyaltyId TechnicalProfiles i filen bas, princip verifieraren avvisar överföringen av grundläggande filen utan att den.
>Det kan vara praktiskt att spåra körningen av användaren transporten med namnet ”ProfileEdit” i filen TrustFrameworkBase.xml.  Sök efter användare transporten med samma namn i redigeringsprogrammet och Observera att Orchestration steg 5 anropar TechnicalProfileReferenceID = ”SelfAsserted ProfileUpdate”.  Söka och inspektera den här TechnicalProfile för att bekanta dig med den.
5. Lägg till loyaltyId som inkommande och utgående anspråk i TechnicalProfile ”SelfAsserted ProfileUpdate”
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

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <InputClaim ClaimTypeReferenceId="givenName" />
            <InputClaim ClaimTypeReferenceId="surname" />
            <InputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </InputClaims>
          <OutputClaims>
            <!-- Required claims -->
            <OutputClaim ClaimTypeReferenceId="executed-SelfAsserted-Input" DefaultValue="true" />

            <!-- Optional claims. These claims are collected from the user and can be modified. Any claim added here should be updated in the
                 ValidationTechnicalProfile referenced below so it can be written to directory after being updateed by the user, i.e. AAD-UserWriteProfileUsingObjectId. -->
            <OutputClaim ClaimTypeReferenceId="givenName" />
            <OutputClaim ClaimTypeReferenceId="surname" />
            <OutputClaim ClaimTypeReferenceId="extension_loyaltyId"/>
          </OutputClaims>
          <ValidationTechnicalProfiles>
            <ValidationTechnicalProfile ReferenceId="AAD-UserWriteProfileUsingObjectId" />
          </ValidationTechnicalProfiles>
        </TechnicalProfile>
```
6. Lägga till anspråk i TechnicalProfile ”AAD-UserWriteProfileUsingObjectId” att bevara värdet för anspråket i tilläggsegenskapen för den aktuella användaren i katalogen.
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
            <!-- Required claims -->
            <PersistedClaim ClaimTypeReferenceId="objectId" />

            <!-- Optional claims -->
            <PersistedClaim ClaimTypeReferenceId="givenName" />
            <PersistedClaim ClaimTypeReferenceId="surname" />
            <PersistedClaim ClaimTypeReferenceId="extension_loyaltyId" />

          </PersistedClaims>
          <IncludeTechnicalProfile ReferenceId="AAD-Common" />
        </TechnicalProfile>
```
7. Lägga till anspråk i TechnicalProfile ”AAD-UserReadUsingObjectId” att läsa värdet för attributet för anknytning varje gång en användare loggar in. Hittills har TechnicalProfiles ändrats i flödet för lokala konton.  Om det nya attributet önskade i flödet för social/federerat konto, måste en annan uppsättning TechnicalProfiles ändras. Se nästa steg.

```xml
<!-- The following technical profile is used to read data after user authenticates. -->
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
         <!-- Optional claims -->
         <OutputClaim ClaimTypeReferenceId="signInNames.emailAddress" />
         <OutputClaim ClaimTypeReferenceId="displayName" />
         <OutputClaim ClaimTypeReferenceId="otherMails" />
         <OutputClaim ClaimTypeReferenceId="givenName" />
         <OutputClaim ClaimTypeReferenceId="surname" />
         <OutputClaim ClaimTypeReferenceId="extension_loyaltyId" />
       </OutputClaims>
       <IncludeTechnicalProfile ReferenceId="AAD-Common" />
     </TechnicalProfile>
```


>[!IMPORTANT]
>Elementet IncludeTechnicalProfile lägger till alla element i AAD-gemensamma denna TechnicalProfile.

## <a name="test-the-custom-policy-using-run-now"></a>Testa den anpassade principen med hjälp av ”kör nu”
1. Öppna den **Azure AD B2C bladet** och gå till **identitet upplevelse Framework > anpassade principer**.
1. Välj den anpassade principen som du överfört och klicka på den **kör nu** knappen.
1. Du ska kunna logga med en e-postadress.

Token id skickas tillbaka till ditt program innehåller egenskapen extension som ett anpassat anspråk som föregås av extension_loyaltyId. Visa exempel.

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

### <a name="add-the-new-claim-to-the-flows-for-social-account-logins-by-changing-the-technicalprofiles-listed-below-these-two-technicalprofiles-are-used-by-socialfederated-account-logins-to-write-and-read-the-user-data-using-the-alternativesecurityid-as-the-locator-of-the-user-object"></a>Lägga till nya anspråk till flödena för sociala inloggningsnamn genom att ändra TechnicalProfiles nedan. Dessa två TechnicalProfiles som används av sociala/federerad inloggningsnamn att skriva och läsa informationen med hjälp av alternativeSecurityId som lokaliserare för användarobjektet.
```xml
  <TechnicalProfile Id="AAD-UserWriteUsingAlternativeSecurityId">

  <TechnicalProfile Id="AAD-UserReadUsingAlternativeSecurityId">
```

Med hjälp av samma tilläggsattribut mellan inbyggda och anpassade principer.
När du lägger till tilläggsattribut (aka anpassade attribut) via portalen upplevelsen attributen registreras med hjälp av den ** b2c-tillägg-app som finns i varje b2c-klient.  Du använder dessa tilläggsattribut i en egen princip:
1. Gå till under din b2c-klient i portal.azure.com **Azure Active Directory** och välj **App registreringar**
2. Hitta din **b2c-tillägg-app** och markera den
3. Under 'Essentials-post i **program-ID** och **objekt-ID**
4. Inkludera dem i din AAD-gemensamma tekniska Profilmetadata som på följande sätt:

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

Om du vill behålla konsekvens med portalen upplevelsen, skapa dessa attribut med hjälp av portalen UI *innan* du använder dem i din anpassade principer.  När du skapar ett attribut ”ActivationStatus” i portalen måste du referera till den på följande sätt:

```
extension_ActivationStatus in the custom policy
extension_<app-guid>_ActivationStatus via the Graph API.
```


## <a name="reference"></a>Referens

* En **tekniska profil (TP)** är en elementtyp som kan betraktas som en *funktionen* som definierar en slutpunkt namn, dess metadata, dess protokoll och information utbyten av anspråk som identitet upplevelse Framework ska utföra.  När detta *funktionen* anropas i en orchestration-steg eller från en annan TechnicalProfile, InputClaims och OutputClaims tillhandahålls som parametrar av anroparen.


* Fullständig behandling på tilläggsegenskaper finns i artikeln [DIRECTORY-SCHEMAUTÖKNINGAR | GRAPH API-BEGREPP](https://msdn.microsoft.com/Library/Azure/Ad/Graph/howto/azure-ad-graph-api-directory-schema-extensions)

>[!NOTE]
>Tilläggsattribut i Graph API är namngivna med konventionen `extension_ApplicationObjectID_attributename`. Anpassade principer som refererar till tillägg attribut som extension_attributename, vilket utesluter ApplicationObjectId i XML
