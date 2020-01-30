---
title: Skydda en RESTful-tjänst genom att använda klient certifikat
titleSuffix: Azure AD B2C
description: Skydda dina anpassade REST API Claims-utbyten i din Azure AD B2C genom att använda klient certifikat
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b6f4c24bb5587ca67fd2de0d8aad2c6f5218a41b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2020
ms.locfileid: "76848517"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Skydda din RESTful-tjänst genom att använda klient certifikat

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I en relaterad artikel skapar du [en RESTful-tjänst](rest-api-claims-exchange-dotnet.md) som samverkar med Azure Active Directory B2C (Azure AD B2C).

I den här artikeln får du lära dig hur du begränsar åtkomsten till Azure Web App (RESTful API) med hjälp av ett klient certifikat. Denna mekanism kallas TLS-ömsesidig autentisering eller *autentisering av klient certifikat*. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C, har åtkomst till tjänsten.

>[!NOTE]
>Du kan också skydda din RESTful-tjänst genom att använda [http Basic-autentisering](secure-rest-api-dotnet-basic-auth.md). Men HTTP Basic-autentisering anses vara mindre säkert över ett klient certifikat. Vi rekommenderar att du skyddar RESTful-tjänsten genom att använda autentisering av klient certifikat enligt beskrivningen i den här artikeln.

Den här artikeln beskriver hur du:
* Konfigurera din webbapp för att använda autentisering med klient certifikat.
* Ladda upp certifikatet till Azure AD B2C princip nycklar.
* Konfigurera den anpassade principen att använda klient certifikatet.

## <a name="prerequisites"></a>Krav
* Slutför stegen i artikeln [integrera REST API Claims-utbyten](rest-api-claims-exchange-dotnet.md) .
* Hämta ett giltigt certifikat (en PFX-fil med en privat nyckel).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Steg 1: Konfigurera en webbapp för autentisering av klient certifikat
Om du vill konfigurera **Azure App Service** för att kräva klient certifikat ställer du in inställningen för webb program `clientCertEnabled` plats på *Sant*. Om du vill göra den här ändringen går du till sidan Azure Portal och öppnar din webbapp. I det vänstra navigerings fönstret väljer du **SSL-inställningar**under **Inställningar** . I avsnittet **klient certifikat** aktiverar du alternativet **inkommande klient certifikat** .

>[!NOTE]
>Se till att Azure App Services planen är standard eller större. Mer information finns [i Översikt över Azure App Service planer i djupet](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Mer information om hur du ställer in egenskapen **clientCertEnabled** finns i [Konfigurera ömsesidig TLS-autentisering för webbappar](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Steg 2: Ladda upp certifikatet till Azure AD B2C princip nycklar
När du har angett `clientCertEnabled` till *True*kräver kommunikationen med ditt RESTful-API ett klient certifikat. Gör så här för att hämta, ladda upp och lagra klient certifikatet i Azure AD B2C klient organisationen:
1. I Azure AD B2C klient väljer du **B2C inställningar** > **Identity Experience Framework**.

2. Om du vill visa de nycklar som är tillgängliga i din klient väljer du **princip nycklar**.

3. Välj **Lägg till**.
    Fönstret **skapa en nyckel** öppnas.

4. I rutan **alternativ** väljer du **överför**.

5. Skriv **B2cRestClientCertificate**i rutan **namn** .
    Prefixet *B2C_1A_* läggs till automatiskt.

6. I rutan **fil uppladdning** väljer du certifikatets PFX-fil med en privat nyckel.

7. I rutan **lösen ord** anger du certifikatets lösen ord.

    ![Ladda upp princip nyckel på sidan Skapa en nyckel i Azure Portal](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-upload.png)

7. Välj **Skapa**.

8. Om du vill visa de nycklar som är tillgängliga i din klient och bekräfta att du har skapat `B2C_1A_B2cRestClientCertificate` nyckeln väljer du **princip nycklar**.

## <a name="step-3-change-the-technical-profile"></a>Steg 3: ändra den tekniska profilen
Om du vill ha stöd för autentisering av klient certifikat i den anpassade principen ändrar du den tekniska profilen genom att göra följande:

1. Öppna tilläggs princip filen *TrustFrameworkExtensions. XML* i arbets katalogen.

2. Sök efter `<TechnicalProfile>`-noden som innehåller `Id="REST-API-SignUp"`.

3. Leta upp `<Metadata>`-elementet.

4. Ändra *AuthenticationType* till *ClientCertificate*enligt följande:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Lägg till följande XML-kodfragment direkt efter stängnings `<Metadata>` element:

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    När du har lagt till kodfragmentet bör din tekniska profil se ut som följande XML-kod:

    ![Ange XML-element för ClientCertificate-autentisering](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Steg 4: överför principen till din klient organisation

1. I [Azure Portal](https://portal.azure.com)väljer du ikonen **katalog + prenumeration** i portalens verktygsfält och väljer sedan den katalog som innehåller Azure AD B2C-klienten.

1. I Azure Portal söker du efter och väljer **Azure AD B2C**.

1. Välj **ramverk för identitets upplevelse**.

1. Välj **alla principer**.

1. Välj **Ladda upp princip**.

1. Markera kryss rutan **Skriv över principen om den finns** .

1. Ladda upp filen *TrustFrameworkExtensions. XML* och kontrol lera att den klarar verifieringen.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Steg 5: testa den anpassade principen med hjälp av kör nu
1. Öppna **Azure AD B2C inställningar**och välj sedan **Identity Experience Framework**.

    >[!NOTE]
    >Körning kräver nu att minst ett program är förregistrerade på klienten. Information om hur du registrerar program finns i artikeln Azure AD B2C [komma igång](tutorial-create-tenant.md) eller i [program registrering](tutorial-register-applications.md) .

2. Öppna **B2C_1A_signup_signin**, den förlitande parten (RP) anpassad princip som du överförde och välj sedan **Kör nu**.

3. Testa processen genom att skriva **test** i rutan **namn** .
    Azure AD B2C visar ett fel meddelande överst i fönstret.

    ![Text rutan med det angivna namnet och det inmatade verifierings felet visas](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-test.png)

4. I rutan **namn** anger du ett namn (annat än "test").
    Azure AD B2C registrerar användaren och skickar sedan ett förmåns nummer till ditt program. Observera talet i det här JWT-exemplet:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://contoso.b2clogin.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
     "aud": "e1d2612f-c2bc-4599-8e7b-d874eaca1ee1",
     "acr": "b2c_1a_signup_signin",
     "nonce": "defaultNonce",
     "iat": 1507122303,
     "auth_time": 1507122303,
     "loyaltyNumber": "290",
     "given_name": "Emily",
     "emails": ["B2cdemo@outlook.com"]
   }
   ```

   >[!NOTE]
   >Om du får fel meddelandet *är namnet inte giltigt. Ange ett giltigt namn*, vilket innebär att Azure AD B2C har anropat RESTful-tjänsten när den visade klient certifikatet. Nästa steg är att validera certifikatet.

## <a name="step-6-add-certificate-validation"></a>Steg 6: Lägg till certifikat verifiering
Klient certifikatet som Azure AD B2C skickar till RESTful-tjänsten genomgår inte verifiering av Azure App Services plattformen, förutom för att kontrol lera om certifikatet finns. Att verifiera certifikatet är webbappens ansvar.

I det här avsnittet lägger du till exempel kod för ASP.NET som validerar certifikat egenskaperna för autentisering.

> [!NOTE]
>Mer information om hur du konfigurerar Azure App Service för autentisering av klient certifikat finns i [Konfigurera ömsesidig TLS-autentisering för webbappar](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6,1 Lägg till program inställningar till projektets Web. config-fil
I Visual Studio-projektet som du skapade tidigare lägger du till följande program inställningar till filen *Web. config* efter `appSettings`-elementet:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Ersätt certifikatets **ämnes namn**, **utfärdarens namn**och tumavtryck-värdena för **certifikatet** med dina certifikat värden.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6,2 Lägg till funktionen IsValidClientCertificate
Öppna filen *Controllers\IdentityController.cs* och Lägg sedan till följande funktion i klassen `Identity` Controller:

```csharp
private bool IsValidClientCertificate()
{
    string ClientCertificateSubject = ConfigurationManager.AppSettings["ClientCertificate:Subject"];
    string ClientCertificateIssuer = ConfigurationManager.AppSettings["ClientCertificate:Issuer"];
    string ClientCertificateThumbprint = ConfigurationManager.AppSettings["ClientCertificate:Thumbprint"];

    X509Certificate2 clientCertInRequest = RequestContext.ClientCertificate;
    if (clientCertInRequest == null)
    {
        Trace.WriteLine("Certificate is NULL");
        return false;
    }

    // Basic verification
    if (clientCertInRequest.Verify() == false)
    {
        Trace.TraceError("Basic verification failed");
        return false;
    }

    // 1. Check the time validity of the certificate
    if (DateTime.Compare(DateTime.Now, clientCertInRequest.NotBefore) < 0 ||
        DateTime.Compare(DateTime.Now, clientCertInRequest.NotAfter) > 0)
    {
        Trace.TraceError($"NotBefore '{clientCertInRequest.NotBefore}' or NotAfter '{clientCertInRequest.NotAfter}' not valid");
        return false;
    }

    // 2. Check the subject name of the certificate
    bool foundSubject = false;
    string[] certSubjectData = clientCertInRequest.Subject.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certSubjectData)
    {
        if (String.Compare(s.Trim(), ClientCertificateSubject) == 0)
        {
            foundSubject = true;
            break;
        }
    }

    if (!foundSubject)
    {
        Trace.TraceError($"Subject name '{clientCertInRequest.Subject}' is not valid");
        return false;
    }

    // 3. Check the issuer name of the certificate
    bool foundIssuerCN = false;
    string[] certIssuerData = clientCertInRequest.Issuer.Split(new char[] { ',' }, StringSplitOptions.RemoveEmptyEntries);
    foreach (string s in certIssuerData)
    {
        if (String.Compare(s.Trim(), ClientCertificateIssuer) == 0)
        {
            foundIssuerCN = true;
            break;
        }
    }

    if (!foundIssuerCN)
    {
        Trace.TraceError($"Issuer '{clientCertInRequest.Issuer}' is not valid");
        return false;
    }

    // 4. Check the thumbprint of the certificate
    if (String.Compare(clientCertInRequest.Thumbprint.Trim().ToUpper(), ClientCertificateThumbprint) != 0)
    {
        Trace.TraceError($"Thumbprint '{clientCertInRequest.Thumbprint.Trim().ToUpper()}' is not valid");
        return false;
    }

    // 5. If you also want to test whether the certificate chains to a trusted root authority, you can uncomment the following code:
    //
    //X509Chain certChain = new X509Chain();
    //certChain.Build(certificate);
    //bool isValidCertChain = true;
    //foreach (X509ChainElement chElement in certChain.ChainElements)
    //{
    //    if (!chElement.Certificate.Verify())
    //    {
    //        isValidCertChain = false;
    //        break;
    //    }
    //}
    //if (!isValidCertChain) return false;
    return true;
}
```

I föregående exempel kod accepterar vi certifikatet som giltigt endast om samtliga följande villkor är uppfyllda:
* Certifikatet har inte upphört att gälla och är aktivt för den aktuella tiden på servern.
* Det `Subject` namnet på certifikatet är lika med värdet för inställningen `ClientCertificate:Subject` program.
* Det `Issuer` namnet på certifikatet är lika med värdet för inställningen `ClientCertificate:Issuer` program.
* Certifikatets `thumbprint` är detsamma som värdet för inställningen `ClientCertificate:Thumbprint` program.

>[!IMPORTANT]
>Beroende på tjänstens känslighet kan du behöva lägga till fler verifieringar. Du kan till exempel behöva testa om certifikatet är kopplat till en betrodd rot certifikat utfärdare, organisations namn validering och så vidare.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6,3 anropa funktionen IsValidClientCertificate
Öppna filen *Controllers\IdentityController.cs* och Lägg till följande kodfragment i början av `SignUp()`-funktionen:

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

När du har lagt till kodfragmentet bör `Identity` kontrollanten se ut som följande kod:

![Lägg till certifikatets verifierings kod](./media/secure-rest-api-dotnet-certificate-auth/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Steg 7: publicera ditt projekt i Azure och testa det
1. I **Solution Explorer**högerklickar du på projektet **contoso. AADB2C. API** och väljer sedan **publicera**.

2. Upprepa "steg 6" och testa den anpassade principen igen med certifikat verifieringen. Försök att köra principen och kontrol lera att allt fungerar när du har lagt till verifieringen.

3. Ändra värdet för `ClientCertificate:Subject` till **ogiltig**i din *Web. config-* fil. Kör principen igen så bör du se ett fel meddelande.

4. Ändra tillbaka värdet till **giltigt**och se till att principen kan anropa din REST API.

Om du behöver felsöka det här steget läser du [samla in loggar med hjälp av Application Insights](troubleshoot-with-application-insights.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>Valfritt Ladda ned fullständiga principfiler och kod
* När du har slutfört guiden [komma igång med anpassade principer](custom-policy-get-started.md) rekommenderar vi att du skapar ditt scenario genom att använda dina egna anpassade principfiler. Vi har angett [exempel på principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert)för din referens.
* Du kan ladda ned den fullständiga koden från [exempel Visual Studio-lösningen för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API).
