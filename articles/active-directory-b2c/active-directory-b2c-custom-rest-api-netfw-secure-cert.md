---
title: Skydda RESTful-tjänst med hjälp av klientcertifikat i Azure Active Directory B2C | Microsoft Docs
description: Säkra din anpassade REST API anspråk Utbytena i din Azure AD B2C med klientcertifikat
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/25/2017
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 502b769e1a25874bfa5a9e8f082361605a52ccf6
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55165684"
---
# <a name="secure-your-restful-service-by-using-client-certificates"></a>Skydda RESTful-tjänst med hjälp av klientcertifikat

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

I en relaterad artikel du [skapa en RESTful-tjänst](active-directory-b2c-custom-rest-api-netfw.md) som interagerar med Azure Active Directory B2C (Azure AD B2C).

I den här artikeln får du lära dig hur du begränsar åtkomsten till din Azure webbapp (RESTful-API) med hjälp av ett klientcertifikat. Den här mekanismen kallas ömsesidig TLS-autentisering eller *klientcertifikatautentisering*. Endast de tjänster som har rätt certifikat, till exempel Azure AD B2C kan komma åt tjänsten.

>[!NOTE]
>Du kan även skydda RESTful-tjänst med hjälp av [grundläggande HTTP-autentisering](active-directory-b2c-custom-rest-api-netfw-secure-basic.md). Dock anses grundläggande HTTP-autentisering mindre säkert över ett klientcertifikat. Vår rekommendation är att skydda RESTful-tjänst genom att använda autentisering med klientcertifikat som beskrivs i den här artikeln.

Den här artikeln innehåller information om hur du:
* Konfigurera webbappen för att använda autentisering med klientcertifikat.
* Överför certifikatet till nycklar för Azure AD B2C-princip.
* Konfigurera en egen princip om du vill använda klientcertifikatet.

## <a name="prerequisites"></a>Förutsättningar
* Utför stegen i den [integrera REST API-anspråk utbyten](active-directory-b2c-custom-rest-api-netfw.md) artikeln.
* Skaffa ett giltigt certifikat (en .pfx-fil med en privat nyckel).

## <a name="step-1-configure-a-web-app-for-client-certificate-authentication"></a>Steg 1: Konfigurera en webbapp för autentisering av klientcertifikat
Du ställer in **Azure App Service** för att kräva klientcertifikat, ange webbappen `clientCertEnabled` plats inställningen för att *SANT*. Öppna webbappens sida för att göra ändringen i Azure-portalen. I det vänstra navigeringsfönstret under **inställningar** Välj **SSL-inställningar**. I den **klientcertifikat** avsnittet, aktivera den **inkommande klientcertifikat** alternativet.

>[!NOTE]
>Se till att din Azure App Service-plan är Standard eller högre. Mer information finns i [Azure App Service-planer djupgående översikt över](https://docs.microsoft.com/azure/app-service/overview-hosting-plans).

>[!NOTE]
>Mer information om hur den **clientCertEnabled** egenskap, finns i [konfigurera TLS ömsesidig autentisering för web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

## <a name="step-2-upload-your-certificate-to-azure-ad-b2c-policy-keys"></a>Steg 2: Överför certifikatet till nycklar för Azure AD B2C-princip
När du ställer in `clientCertEnabled` till *SANT*, kommunikationen med ditt RESTful-API kräver ett klientcertifikat. Om du vill hämta, överföra och lagra klientcertifikatet i din Azure AD B2C-klient, gör du följande: 
1. I din Azure AD B2C-klient väljer **B2C inställningar** > **Identitetsramverk**.

2. Om du vill visa de nycklar som är tillgängliga i din klient, Välj **Principnycklar**.

3. Välj **Lägg till**.  
    Den **skapar du en nyckel** öppnas.

4. I den **alternativ** väljer **överför**.

5. I den **namn** skriver **B2cRestClientCertificate**.  
    Prefixet *B2C_1A_* läggs till automatiskt.

6. I den **filuppladdning** väljer du ditt certifikat PFX-fil med en privat nyckel.

7. I den **lösenord** skriver lösenord för certifikatet.

    ![Ladda upp principnyckel](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-upload.png)

7. Välj **Skapa**.

8. Visa de nycklar som är tillgängliga i din klient och bekräfta att du har skapat den `B2C_1A_B2cRestClientCertificate` key, väljer **Principnycklar**.

## <a name="step-3-change-the-technical-profile"></a>Steg 3: Ändra den tekniska profilen
För att stödja autentisering av klientcertifikat i en egen princip, ändrar du den tekniska profilen genom att göra följande:

1. Öppna i din arbetskatalog i *TrustFrameworkExtensions.xml* principfil för tillägget.

2. Sök efter den `<TechnicalProfile>` nod som innehåller `Id="REST-API-SignUp"`.

3. Leta upp den `<Metadata>` element.

4. Ändra den *AuthenticationType* till *ClientCertificate*, enligt följande:

    ```xml
    <Item Key="AuthenticationType">ClientCertificate</Item>
    ```

5. Direkt efter avslutande `<Metadata>` element, Lägg till följande XML-fragment: 

    ```xml
    <CryptographicKeys>
        <Key Id="ClientCertificate" StorageReferenceId="B2C_1A_B2cRestClientCertificate" />
    </CryptographicKeys>
    ```

    När du har lagt till kodfragmentet dina tekniska profilen bör se ut som följande XML-kod:

    ![Ange ClientCertificate autentisering XML-element](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-cert-tech-profile.png)

## <a name="step-4-upload-the-policy-to-your-tenant"></a>Steg 4: Ladda upp principen till din klient

1. I den [Azure-portalen](https://portal.azure.com), växla till den [kontext som din Azure AD B2C-klient](active-directory-b2c-navigate-to-b2c-context.md), och välj sedan **Azure AD B2C**.

2. Välj **Identitetsramverk**.

3. Välj **alla principer**.

4. Välj **överföra princip**.

5. Välj den **Skriv över principen om den finns** markerar du kryssrutan.

6. Ladda upp den *TrustFrameworkExtensions.xml* filen och kontrollera att den godkänns vid.

## <a name="step-5-test-the-custom-policy-by-using-run-now"></a>Steg 5: Testa den anpassade principen med hjälp av kör nu
1. Öppna **Azure AD B2C-inställningar**, och välj sedan **Identitetsramverk**.

    >[!NOTE]
    >Kör nu kräver minst ett program att vara förväg registrerade på klienten. Läs hur du registrerar program i Azure AD B2C [börjar](active-directory-b2c-get-started.md) artikel eller [programregistrering](active-directory-b2c-app-registration.md) artikeln.

2. Öppna **B2C_1A_signup_signin**, den förlitande part (RP) anpassa princip som du överförde och väljer sedan **kör nu**.

3. Testa processen genom att skriva **Test** i den **Förnamn** box.  
    Azure AD B2C visar ett felmeddelande visas överst i fönstret.    

    ![Testa din identitet API](media/aadb2c-ief-rest-api-netfw-secure-basic/rest-api-netfw-test.png)

4. I den **Förnamn** skriver ett namn (andra än ”Test”).  
    Azure AD B2C registrerar sig användaren och skickar sedan ett lojalitet tal till ditt program. Observera antalet i det här JWT-exemplet:

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
   >Om du får felmeddelandet *namnet är inte giltigt, ange ett giltigt namn*, betyder det att Azure AD B2C har heter RESTful-tjänst när den visas klientcertifikatet. Nästa steg är att verifiera certifikatet.

## <a name="step-6-add-certificate-validation"></a>Steg 6: Lägg till certifikatverifiering
Klientcertifikatet som Azure AD B2C skickar till RESTful-tjänst genomgår inte verifiering av Azure App Service-plattformen, förutom att kontrollera om det finns certifikatet. Verifiering av certifikatet ansvarar för webbappen. 

I det här avsnittet ska du lägga till ASP.NET-exempelkoden som validerar egenskaper för certifikat för autentisering.

> [!NOTE]
>Läs mer om hur du konfigurerar Azure App Service för autentisering av klientcertifikat, [konfigurera TLS ömsesidig autentisering för web apps](https://docs.microsoft.com/azure/app-service-web/app-service-web-configure-tls-mutual-auth).

### <a name="61-add-application-settings-to-your-projects-webconfig-file"></a>6.1 Lägg till tillämpningsinställningar till ditt projekts web.config-filen
I Visual Studio-projektet som du skapade tidigare, lägger du till följande programinställningar för att den *web.config* fil efter den `appSettings` element:

```XML
<add key="ClientCertificate:Subject" value="CN=Subject name" />
<add key="ClientCertificate:Issuer" value="CN=Issuer name" />
<add key="ClientCertificate:Thumbprint" value="Certificate thumbprint" />
```

Ersätter du certifikatet **ämnesnamn**, **Utfärdarens namn**, och **certifikattumavtrycket** värdena med dina certifikatvärden.

### <a name="62-add-the-isvalidclientcertificate-function"></a>6.2 lägga till funktionen IsValidClientCertificate
Öppna den *Controllers\IdentityController.cs* filen och Lägg sedan till den `Identity` controller klassen följande funktion: 

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

I föregående exempelkoden accepterar vi certifikat som gäller bara om samtliga följande villkor är uppfyllda:
* Certifikatet har inte upphört att gälla och är aktiv i den aktuella tiden på servern.
* Den `Subject` namnet på certifikatet är lika med den `ClientCertificate:Subject` program inställningens värde.
* Den `Issuer` namnet på certifikatet är lika med den `ClientCertificate:Issuer` program inställningens värde.
* Den `thumbprint` för certifikatet är lika med den `ClientCertificate:Thumbprint` program inställningens värde.

>[!IMPORTANT]
>Beroende på hur känsliga din tjänst, kan du behöva lägga till flera verifieringar. Du kan behöva testa om certifikaten som länkar till en betrodd rotcertifikatutfärdare, valideringen av utfärdare organisation och så vidare.

### <a name="63-call-the-isvalidclientcertificate-function"></a>6.3 anropa funktionen IsValidClientCertificate
Öppna den *Controllers\IdentityController.cs* fil och sedan i början av den `SignUp()` fungera, Lägg till följande kodavsnitt: 

```csharp
if (IsValidClientCertificate() == false)
{
    return Content(HttpStatusCode.Conflict, new B2CResponseContent("Your client certificate is not valid", HttpStatusCode.Conflict));
}
```

När du lägger till kodfragmentet, din `Identity` controller bör se ut som följande kod:

![Lägg till kod för verifiering av certifikat](media/aadb2c-ief-rest-api-netfw-secure-cert/rest-api-netfw-secure-client-code.png)

## <a name="step-7-publish-your-project-to-azure-and-test-it"></a>Steg 7: Publicera projektet på Azure och testar det.
1. I **Solution Explorer**, högerklicka på den **Contoso.AADB2C.API** projektet och välj sedan **publicera**.

2. Upprepa ”steg 6” och testa en egen princip med certifikatsverifieringen igen. Försök att köra principen och se till att allt fungerar när du lägger till verifieringen.

3. I din *web.config* filen, ändra värdet för `ClientCertificate:Subject` till **ogiltigt**. Kör principen igen och du bör se ett felmeddelande.

4. Ändra värdet tillbaka till **giltig**, och se till att principen kan anropa REST-API.

Om du vill felsöka det här steget kan du läsa [samla in loggar med Application Insights](active-directory-b2c-troubleshoot-custom.md).

## <a name="optional-download-the-complete-policy-files-and-code"></a>(Valfritt) Ladda ned fullständig principfiler och kod
* När du har slutfört den [Kom igång med anpassade principer](active-directory-b2c-get-started-custom.md) genomgången ska vi rekommenderar att du skapar ditt scenario genom att använda din egen anpassade principfiler. För referens har vi samlat [exempel principfiler](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw-secure-cert).
* Du kan hämta den fullständiga koden från [exempel Visual Studio-lösning för referens](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/aadb2c-ief-rest-api-netfw/Contoso.AADB2C.API). 
