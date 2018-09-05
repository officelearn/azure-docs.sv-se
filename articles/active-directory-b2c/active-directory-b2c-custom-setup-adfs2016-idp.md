---
title: Lägg till AD FS som en SAML-identitetsprovider med anpassade principer i Azure Active Directory B2C | Microsoft Docs
description: Konfigurera AD FS 2016 med SAML-protokoll och anpassade principer i Azure Active Directory B2C
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/31/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2c2e6861fda42a9e8c1aabcba303bfede47ac3c1
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669234"
---
# <a name="add-adfs-as-a-saml-identity-provider-using-custom-policies-in-azure-active-directory-b2c"></a>Lägg till AD FS som en SAML-identitetsprovider med anpassade principer i Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Den här artikeln visar hur du aktiverar inloggning för ett användarkonto i AD FS med hjälp av [anpassade principer](active-directory-b2c-overview-custom.md) i Azure Active Directory (Azure AD) B2C.

## <a name="prerequisites"></a>Förutsättningar

Utför stegen i den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) artikeln.

## <a name="add-the-adfs-account-application-key-to-azure-ad-b2c"></a>Lägg till AD FS-kontonyckel för program i Azure AD B2C

Federation med ett konto för AD FS kräver en klienthemlighet för kontot som ska förtroende Azure AD B2C åt programmet. Du behöver lagra certifikatet AD FS i din Azure AD B2C-klient. 

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Kontrollera att du använder katalogen som innehåller din Azure AD B2C-klient genom att växla till den i det övre högra hörnet i Azure-portalen. Välj **växla katalog**, och välj sedan den katalog som innehåller den klient du skapade. I de här självstudierna i *contoso* används katalogen som innehåller klient med namnet *contoso0522Tenant.onmicrosoft.com*.

    ![Växla kataloger](./media/active-directory-b2c-custom-setup-adfs2016-idp/switch-directories.png)

3. Välj **Alla tjänster** på menyn högst upp till vänster i Azure-portalen och sök efter och välj **Azure AD B2C**. Du bör nu använda din klient.
4. På sidan Översikt väljer **Identitetsramverk**.
5. Välj **Principnycklar** att visa nycklarna som är tillgängliga i din klient och klicka sedan på **Lägg till**.
6. Välj **överför** som alternativ.
7. Ange `ADFSSamlCert` för namnet. Prefixet `B2C_1A_` kan läggas till automatiskt.
8. Bläddra till och välj din .pfx-certifikatfil med privat nyckel. Det här certifikatet med den privata nyckeln ska vara samma konto som har utfärdats och används för den förlitande parten för AD FS.
9. Klicka på **skapa** och bekräfta att du har skapat den `B2C_1A_ADFSSamlCert` nyckel.

## <a name="add-a-claims-provider-in-your-extension-policy"></a>Lägg till en anspråksprovider i din princip för tillägg

Om du vill att användarna att logga in med ett konto för AD FS måste du definiera kontot som en anspråksprovider. Du kan göra detta genom att ange en slutpunkt som Azure AD B2C kommunicerar med. Slutpunkten som innehåller en uppsättning anspråk som används av Azure AD B2C för att verifiera att en viss användare har autentiserats.

Definiera ADFS som en anspråksprovider genom att lägga till **ClaimsProvider** element i din principfil för tillägget.

1. Öppna den *TrustFrameworkExtensions.xml* principfil i din arbetskatalog. Om du behöver en XML-redigerare [försök Visual Studio Code](https://code.visualstudio.com/download), vilket är ett enkelt redigeringsprogram för olika plattformar.
2. Lägg till följande XML-filen under den **ClaimsProviders** elementet och Ersätt **your AD FS domän** med ADFS-domänen namn och Ersätt värdet för den **identityProvider** utgående anspråk med din DNS-server (godtyckligt värde som anger din domän) och spara filen. 

    ```xml
    <ClaimsProvider>
      <Domain>contoso.com</Domain>
      <DisplayName>Contoso ADFS</DisplayName>
      <TechnicalProfiles>
        <TechnicalProfile Id="Contoso-SAML2">
          <DisplayName>Contoso ADFS</DisplayName>
          <Description>Login with your Contoso account</Description>
          <Protocol Name="SAML2"/>
          <Metadata>
            <Item Key="RequestsSigned">false</Item>
            <Item Key="WantsEncryptedAssertions">false</Item>
            <Item Key="PartnerEntity">https://your-ADFS-domain/federationmetadata/2007-06/federationmetadata.xml</Item>
          </Metadata>
          <CryptographicKeys>
            <Key Id="SamlAssertionSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
            <Key Id="SamlMessageSigning" StorageReferenceId="B2C_1A_ADFSSamlCert"/>
          </CryptographicKeys>
          <OutputClaims>
            <OutputClaim ClaimTypeReferenceId="socialIdpUserId" PartnerClaimType="userPrincipalName" />
            <OutputClaim ClaimTypeReferenceId="givenName" PartnerClaimType="given_name"/>
            <OutputClaim ClaimTypeReferenceId="surname" PartnerClaimType="family_name"/>
            <OutputClaim ClaimTypeReferenceId="email" PartnerClaimType="email"/>
            <OutputClaim ClaimTypeReferenceId="displayName" PartnerClaimType="name"/>
            <OutputClaim ClaimTypeReferenceId="identityProvider" DefaultValue="contoso.com" />
            <OutputClaim ClaimTypeReferenceId="authenticationSource" DefaultValue="socialIdpAuthentication"/>
          </OutputClaims>
          <OutputClaimsTransformations>
            <OutputClaimsTransformation ReferenceId="CreateRandomUPNUserName"/>
            <OutputClaimsTransformation ReferenceId="CreateUserPrincipalName"/>
            <OutputClaimsTransformation ReferenceId="CreateAlternativeSecurityId"/>
            <OutputClaimsTransformation ReferenceId="CreateSubjectClaimFromAlternativeSecurityId"/>
          </OutputClaimsTransformations>
          <UseTechnicalProfileForSessionManagement ReferenceId="SM-Noop"/>
        </TechnicalProfile>
      </TechnicalProfiles>
    </ClaimsProvider>
    ```

## <a name="register-the-claims-provider-for-sign-up-and-sign-in"></a>Registrera anspråksprovidern för registrering och inloggning

För att göra identitetsprovidern som AD FS-konto i sidorna för registrering och inloggning, du måste lägga till den till din **SignUpOrSignIn** användarresa. 

Göra en kopia av en befintlig mall för användarresan och ändra den så att den inkluderar identitetsprovidern som AD FS:

>[!NOTE]
>Om du tidigare har kopierat den **UserJourneys** element från bas-filen i din princip att tilläggsfilen (*TrustFrameworkExtensions.xml*) kan du hoppa över det här avsnittet.

1. Öppna filen bas i din princip. Till exempel *TrustFrameworkBase.xml*.
2. Kopiera hela innehållet i **UserJourneys** element.
3. Öppna tilläggsfilen (*TrustFrameworkExtensions.xml*) och klistra in hela innehållet i **UserJourneys** element som du kopierade i tilläggsfilen.

### <a name="display-the-button"></a>Visa knappen

Den **ClaimsProviderSelections** elementet definierar en lista med anspråk providern val och deras inbördes ordning.  Den **ClaimsProviderSelection** element är detsamma som en knapp med identity-providern på en sida för registrering och inloggning. Om du lägger till en **ClaimsProviderSelection** element för ett AD FS-konto, en ny knapp visas när en användare ser sidan. Lägga till det här elementet:

1. I den **UserJourney** element med en identifierare för `SignUpOrSignIn` i utbildning för användare som du kopierade, letar du upp den **OrchestrationStep** element i `Order="1"`.
2. Lägg till följande **ClaimsProviderSelection** elementet under den **ClaimsProviderSelections** element:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

Nu när du har en knapp på plats kan behöva du länka den till en åtgärd. Åtgärden, i det här fallet är för Azure AD B2C att kommunicera med AD FS-konto för att ta emot en token. Länka knappen till en åtgärd genom att länka den tekniska profilen för din AD FS-konto anspråksprovidern:

1. Hitta den **OrchestrationStep** av `Order="2"` under den **UserJourney** element.
2. Lägg till följande **ClaimsExchange** elementet under den **ClaimsExchanges** element:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

> [!NOTE]
> * Se till att den `Id` har samma värde som `TargetClaimsExchangeId` i föregående avsnitt.
> * Se till att den `TechnicalProfileReferenceId` har angetts till den tekniska profilen du skapade tidigare (Contoso-SAML2).


## <a name="optional-register-the-claims-provider-for-profile-edit"></a>[Valfritt] Registrera anspråksprovidern för profilredigering

Du kanske vill lägga till identitetsleverantören för AD FS-konto i användarresan för redigera din profil.

### <a name="display-the-button"></a>Visa knappen

1. Öppna tilläggsfilen av din princip. Till exempel *TrustFrameworkExtensions.xml*.
2. I den **UserJourney** element med en identifierare som `ProfileEdit` i utbildning för användare som du kopierade, letar du upp den **OrchestrationStep** element i `Order="1"`.
3. Lägg till följande **ClaimsProviderSelection** elementet under **ClaimsProviderSelections** element:

    ```xml
    <ClaimsProviderSelection TargetClaimsExchangeId="ContosoExchange" />
    ```

### <a name="link-the-button-to-an-action"></a>Länka knappen till en åtgärd

1. Hitta den **OrchestrationStep** av `Order="2"` under den **UserJourney** element.
2. Lägg till följande **ClaimsExchange** elementet under den **ClaimsExchanges** element:

    ```xml
    <ClaimsExchange Id="ContosoExchange" TechnicalProfileReferenceId="Contoso-SAML2" />
    ```

## <a name="upload-the-policy-to-your-tenant"></a>Ladda upp principen till din klient

1. I Azure-portalen väljer du **alla principer**.
2. Välj **överföra princip**.
3. Aktivera **Skriv över principen om den finns**.
4. Bläddra till och välj din *TrustFrameworkExtensions.xml* principfil och välj sedan **överför**. Se till att valideringen har lyckats.


## <a name="configure-an-adfs-relying-party-trust"></a>Konfigurera en AD FS förlitande part

Om du vill använda AD FS som en identitetsprovider i Azure AD B2C måste du skapa en AD FS förtroende för förlitande part med Azure AD B2C SAML-metadata. I följande exempel visas en URL-adress till SAML-metadata för en Azure AD B2C-tekniska profilen:

```
https://login.microsoftonline.com/te/your-tenant/your-policy/samlp/metadata?idptp=your-technical-profile
```

Ersätt följande värden:

- **din klient** med klientnamnet på din, till exempel din tenant.onmicrosoft.com.
- **din princip** med namnet på din. Använd principen konfigurerar du den tekniska profilen för SAML-provider eller en policy som ärver från principen.
- **din tekniska profil** med namnet på din identitet providern tekniska SAML-profilen.
 
Öppna en webbläsare och gå till URL: en. Kontrollera att du anger rätt URL och att du har åtkomst till metadata XML-filen.

Om du vill lägga till en ny förlitande part med snapin-modulen AD FS-hantering och konfigurera inställningarna manuellt, utför du följande procedur på en federationsserver. Medlemskap i **administratörer** eller motsvarande på den lokala datorn är minimikravet för att kunna slutföra den här proceduren. Granska information om hur du använder lämpliga konton och gruppmedlemskap i [Local and Domain Default Groups](http://go.microsoft.com/fwlink/?LinkId=83477).

1. I Serverhanteraren väljer **verktyg**, och välj sedan **AD FS Management**.
2. Välj **Lägg till förtroende för förlitande part**.
3. På den **Välkommen** väljer **anspråk medveten**, och klicka sedan på **starta**.
4. På den **Välj datakälla** väljer **importera data om förlitande part publicera online eller i ett lokalt nätverk**, ange din Azure AD B2C metadata-URL och klicka sedan på **nästa**.
5. På den **ange visningsnamn** anger en **visningsnamn**under **anteckningar**, ange en beskrivning för den här förlitande parten och klickar sedan på **nästa**.
6. På den **Välj principer för åtkomstkontroll** sidan, väljer en princip och klicka sedan på **nästa**.
7. På den **redo att lägga till förtroende** sidan, granskar du inställningarna och klicka sedan på **nästa** att spara din förlitande part förtroende information.
8. På den **Slutför** klickar du på **Stäng**, den här åtgärden visas automatiskt i **redigera Anspråksregler** dialogrutan.
9. Välj **Lägg till regel**.  
10. I **anspråksregelmall**väljer **skicka LDAP-attribut som anspråk**.
11. Ange en **Regelnamn för anspråk**. För den **attributarkiv**väljer **Välj Active Directory**, lägga till följande anspråk och sedan på **Slutför** och **OK**.

    ![Ange egenskaper för regeln](./media/active-directory-b2c-custom-setup-adfs2016-idp/aadb2c-ief-setup-adfs2016-idp-claims-3.png)

12.  Baserat på din typ av certifikat kan behöva du ange HASH-algoritmen. I egenskapsfönstret förlitande part förtroende (B2C Demo) väljer den **Avancerat** fliken och ändra den **säkra hash-algoritm** till `SHA-1` eller `SHA-256`, och klicka på **Ok**.  

### <a name="update-the-relying-party-metadata"></a>Uppdatera metadata för förlitande part

Ändra den tekniska SAML-profilen måste du uppdatera AD FS med den uppdaterade metadata-versionen. Du behöver inte uppdatera metadata när du skapar det förlitande part-programmet, men när du gör en ändring, uppdatering av metadata i AD FS.

1. I Serverhanteraren väljer **verktyg**, och välj sedan **AD FS Management**.
2. Välj det förlitande partsförtroendet som du har skapat, Välj **uppdateringen från Federationsmetadata**, och klicka sedan på **uppdatering**. 

### <a name="test-the-policy-by-using-run-now"></a>Testa principen med hjälp av kör nu

1.  Öppna **Azure AD B2C-inställningar** och gå till **Identitetsramverk**.
2.  Öppna **B2C_1A_ProfileEdit**, den förlitande part (RP) anpassade principen som du överförde. Välj **kör nu**. Du bör kunna logga in med AD FS.

## <a name="download-the-complete-policy-files"></a>Hämta de fullständiga principfilerna

Valfritt: Du kan skapa ditt scenario med anpassad principfiler när du har slutfört stegen i [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md). Till exempel filer, se [princip exempelfilerna endast för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-setup-adfs2016-app).
