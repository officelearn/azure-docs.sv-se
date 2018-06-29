---
title: Skydda din RESTful-tjänst genom att använda klientcertifikat i Azure Active Directory B2C | Microsoft Docs
description: Skydda din anpassade REST API anspråk utbyte i din Azure AD B2C med hjälp av klientcertifikat
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/25/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: fc95974fb7db856d0a255d4a5d1d754649b71eca
ms.sourcegitcommit: d7725f1f20c534c102021aa4feaea7fc0d257609
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37098455"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Skydda din RESTful-tjänst med hjälp av klientcertifikat

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I en relaterad artikel du [skapa en RESTful-tjänst](active-directory-b2c-custom-rest-api-netfw.md) som samverkar med Azure Active Directory B2C (Azure AD B2C).

Lär dig hur du begränsar åtkomsten till ditt Azure webbapp (RESTful-API) med hjälp av ett klientcertifikat i den här artikeln. Den här mekanismen kallas TLS ömsesidig autentisering, eller *klientcertifikatautentisering*. Endast tjänster som har rätt certifikat, till exempel Azure AD B2C kan komma åt tjänsten.

>[!NOTE]
>Du kan även skydda RESTful-tjänst med [grundläggande HTTP-autentisering](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Grundläggande HTTP-autentisering anses vara mindre säkert över ett klientcertifikat. Vår rekommendation är att skydda RESTful-tjänst genom att använda certifikat för klientautentisering som beskrivs i den här artikeln.

Det här artikeln beskriver hur du:
* Konfigurera ditt webbprogram att använda autentisering av klientcertifikat.
* Överför certifikatet till Azure AD B2C princip nycklar.
* Konfigurera en anpassad princip för att använda klientcertifikatet.

## <a name="prerequisites"></a>Förutsättningar
* Utför stegen i den [integrera REST API-anspråk utbyten](active-directory-b2c-custom-rest-api-netfw.md) artikel.
* Skaffa ett giltigt certifikat (en .pfx-fil med en privat nyckel).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Steg 1: Konfigurera ett webbprogram för autentisering av klientcertifikat
Att ställa in **Azure App Service** vill kräva klientcertifikat anger webbappen `clientCertEnabled` plats inställningen för att *SANT*. Den här ändringen i Azure-portalen, öppna webbsidan för appen. I det vänstra navigeringsfönstret under **inställningar** Välj **SSL-inställningar**. I den **klientcertifikat** avsnittet, aktivera den **inkommande klientcertifikat** alternativet.

>[!NOTE]
>Se till att din Azure App Service-plan är Standard eller högre. Mer information finns i [Azure App Service-planer djupgående översikt över](https://docs.microsoft.com/azure/app-service/azure-web-sites-web-hosting-plans-in-depth-overview).

>[!NOTE]
>Mer information om hur den **clientCertEnabled** egenskapen finns [konfigurera TLS ömsesidig autentisering för web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Steg 2: Överför certifikatet till Azure AD B2C princip nycklar
När du ställer in `clientCertEnabled` till *SANT*, kommunikation med RESTful-API kräver ett klientcertifikat. Om du vill hämta, ladda upp och lagra klientcertifikatet i din Azure AD B2C-klient kan du göra följande: 
1. Välj i din Azure AD B2C-klient **B2C inställningar** > **identitet upplevelse Framework**.

2. Om du vill visa de nycklar som är tillgängliga i din klient **princip nycklar**.

3. Välj **Lägg till**.  
    Den **skapar du en nyckel** öppnas.

4. I den **alternativ** väljer **överför**.

5. I den **namn** skriver **B2cRestClientCertificate**.  
    Prefixet *B2C_1A_* läggs till automatiskt.

6. I den **filuppladdning** väljer du ditt certifikat PFX-fil med en privat nyckel.

7. I den **lösenord** skriver lösenord för certifikatet.

    ![Ladda upp nyckeln för principen](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Välj **Skapa**.

8. Visa de nycklar som är tillgängliga i din klient och bekräfta att du har skapat den `B2C_1A_B2cRestClientCertificate` nyckel, Välj **princip nycklar**.

## <a name="step-3-change-the-technical-profile"></a>Steg 3: Ändra tekniska profilen
För att stödja autentisering av klientcertifikat i en egen princip, ändrar du den tekniska profilen genom att göra följande:

1. Öppna i arbetskatalogen i *TrustFrameworkExtensions.xml* principfil för tillägget.

2. Sök efter den `<TechnicalProfile>` nod som innehåller `Id="REST-API-SignUp"`.

3. Leta upp den `<Metadata>` element.

4. Ändra den *AuthenticationType* till *ClientCertificate*enligt följande:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Omedelbart efter avslutningen `<Metadata>` element, Lägg till följande XML-kodstycke: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    När du har lagt till kodavsnittet tekniska profilen bör se ut som följande XML-kod:

    ![Ange ClientCertificate autentisering XML-element](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Steg 4: Överför principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext för din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **identitet upplevelse Framework**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **skriva över principen om den finns** kryssrutan.

6. Överför den *TrustFrameworkExtensions.xml* filen och sedan kontrollera att den har klarat valideringen.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Steg 5: Testa den anpassade principen genom att använda Kör nu
1. Öppna **Azure AD B2C inställningar**, och välj sedan **identitet upplevelse Framework**.

    >[!NOTE]
    >Kör nu kräver minst ett program till preregistered för innehavaren. Om du vill lära dig mer om att registrera program, finns i Azure AD B2C [Kom igång](active-directory-b2c-get-started.md) artikel eller [appregistrering](active-directory-b2c-app-registration.md) artikel.

2. Öppna **B2C_1A_signup_signin**, förlitande part (RP) anpassade principer som du överfört och välj sedan **kör nu**.

3. Testa processen genom att skriva **Test** i den **Förnamn** rutan.  
    Azure AD B2C visar ett felmeddelande visas överst i fönstret.    

    ![Testa din identitet API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. I den **Förnamn** Skriv ett namn (andra än ”Test”).  
    Azure AD B2C registrerar sig användaren och skickar sedan en förmåner numret för ditt program. Anteckna numret i det här exemplet JWT:

   ```
   {
     "typ": "JWT",
     "alg": "RS256",
     "kid": "X5eXk4xyojNFum1kl2Ytv8dlNP4-c57dO6QGTVBwaNk"
   }.{
     "exp": 1507125903,
     "nbf": 1507122303,
     "ver": "1.0",
     "iss": "https://login.microsoftonline.com/f06c2fe8-709f-4030-85dc-38a4bfd9e82d/v2.0/",
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
   >Om du får felmeddelandet *namnet är inte giltig, ange ett giltigt namn*, innebär det att Azure AD B2C har anropades RESTful-tjänst när den visas klientcertifikatet. Nästa steg är att verifiera certifikatet.

## <a name="step-6-add-certificate-validation"></a>Steg 6: Lägg till certifikatvalidering
Det klientcertifikat som Azure AD B2C skickar till RESTful-tjänst genomgår inte validering av förutom att kontrollera om certifikatet finns i plattformen Azure Web Apps. Verifiera certifikatet ansvarar för webbappen. 

Lägg till ASP.NET exempelkod som validerar egenskaper för certifikat för autentisering i det här avsnittet.

> [!NOTE]
>Mer information om hur du konfigurerar Azure App Service för autentisering av klientcertifikat finns [konfigurera TLS ömsesidig autentisering för web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Lägg till tillämpningsinställningar web.config-filen för ditt projekt
I Visual Studio-projektet som du skapade tidigare, lägger du till följande programinställningar för att den *web.config* fil efter den `appSettings` element:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Ersätter du certifikatet **ämnesnamn**, **utfärdarnamnet**, och **certifikattumavtrycket** värden med certifikat-värden.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 lägga till funktionen IsValidClientCertificate
Öppna den *Controllers\IdentityController.cs* filen och Lägg sedan till den `Identity` domänkontrollant klassen följande funktion: 

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

I föregående exempelkod accepterar certifikatet som gäller bara om samtliga följande villkor är uppfyllda:
* Certifikatet har inte upphört att gälla och är aktiv för den aktuella tiden på servern.
* Den `Subject` namnet på certifikatet som är lika med det `ClientCertificate:Subject` värdet för tillämpningsinställningen.
* Den `Issuer` namnet på certifikatet som är lika med det `ClientCertificate:Issuer` värdet för tillämpningsinställningen.
* Den `thumbprint` för certifikatet är lika med det `ClientCertificate:Thumbprint` värdet för tillämpningsinställningen.

>[!IMPORTANT]
>Beroende på känslighet för din tjänst, kanske du behöver lägga till flera verifieringar. Du kan behöva kontrollera om certifikatet som länkar till en betrodd rotcertifikatutfärdare, valideringen av utfärdaren organisation och så vidare.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 anropa funktionen IsValidClientCertificate
Öppna den *Controllers\IdentityController.cs* fil och sedan i början av den `SignUp()` fungera, Lägg till följande kodfragment: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

När du har lagt till fragment, din `Identity` domänkontrollanten bör se ut som följande kod:

![Lägg till certifikat verifieringskoden](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Steg 7: Publicera projektet till Azure och testa den
1. I **Solution Explorer**, högerklicka på den **Contoso.AADB2C.API** projektet och välj sedan **publicera**.

2. Upprepa ”steg 6” och testa en anpassad princip med certifikatsverifieringen. Försök att köra principen och se till att allt fungerar när du har lagt till verifieringen.

3. I din *web.config* fil, ändrar du värdet för `ClientCertificate:Subject` till **ogiltigt**. Kör principen igen och du bör se ett felmeddelande.

4. Ändra värdet tillbaka till **giltig**, och kontrollera att principen kan anropa REST-API.

Om du behöver felsöka det här steget finns [insamling av loggar med hjälp av Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Hämta fullständig principfiler och kod
* När du har slutfört den [komma igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgång, rekommenderar vi att du skapar ditt scenario genom att använda en anpassad princip för filerna. Referens, har vi angett [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
