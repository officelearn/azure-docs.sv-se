---
title: Självstudie för att konfigurera Azure Active Directory B2C med Jumio
titleSuffix: Azure AD B2C
description: Självstudie för att konfigurera Azure Active Directory B2C med Jumio för automatisk ID-verifiering, skydda kund information
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/20/2020
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 47e06c84eb2e0463b31b7bdea5897ceca4339419
ms.sourcegitcommit: d39f2cd3e0b917b351046112ef1b8dc240a47a4f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/25/2020
ms.locfileid: "88817805"
---
# <a name="tutorial-for-configuring-jumio-with-azure-active-directory-b2c"></a>Självstudie för att konfigurera Jumio med Azure Active Directory B2C

I den här självstudien ger vi vägledning om hur du integrerar Azure AD B2C med [Jumio](https://www.jumio.com/). Jumio är en tjänst för ID-verifiering som möjliggör verifiering av automatiserat ID i real tid, vilket skyddar kund information.

## <a name="prerequisites"></a>Förutsättningar

För att komma igång behöver du:

- En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).

- En [Azure AD B2C klient](https://review.docs.microsoft.com/azure/active-directory-b2c/tutorial-create-tenant). Klienten är länkad till din Azure-prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

Jumio-integreringen innehåller följande komponenter:

- Azure AD B2C – auktoriseringsservern som ansvarar för att verifiera användarens autentiseringsuppgifter, även kallat identitets leverantören

- Jumio – tjänsten som tar de ID-uppgifter som användaren har fått och som verifierar den.

- Mellanliggande REST API – det här API: et implementerar integreringen mellan Azure AD B2C och Jumio-tjänsten.

- Blob Storage – används för att tillhandahålla anpassade UI-filer till Azure AD B2C principer.

I följande arkitektur diagram visas implementeringen.

![Skärm bild för jumio-Architecture-diagram](./media/partner-jumio/jumio-architecture-diagram.png)

|Steg | Beskrivning |
|:-----| :-----------|
| 1. | Användaren kommer till inloggnings sidan. Användare väljer registrering för att skapa ett nytt konto och ange information på sidan. Azure AD B2C samlar in användarattribut.
| 2. | Azure AD B2C anropar API: t för mellanlager och passerar användar-attributen.
| 3. | API för mellanlager samlar in användarattribut och omvandlar dem till ett format som Jumio-API: et kan förbruka. Sedan skickar den till Jumio.
| 4. | När Jumio förbrukar informationen och bearbetar den returneras resultatet till API: t i mitten.
| 5. | API för mellan lager bearbetar informationen och skickar tillbaka relevant information till Azure AD B2C.
| 6. | Azure AD B2C tar emot information tillbaka från API för mellanliggande lager. Om det visar ett felsvar visas ett fel meddelande för användaren. Om det visar sig ett lyckat svar, autentiseras och skrivs användaren till katalogen.

## <a name="onboard-with-jumio"></a>Publicera med Jumio

1. Kontakta [Jumio](https://www.jumio.com/contact/) om du vill skapa ett Jumio-konto

2. När ett konto har skapats används information i API-konfigurationen. I följande avsnitt beskrivs processen.

## <a name="configure-azure-ad-b2c-with-jumio"></a>Konfigurera Azure AD B2C med Jumio

### <a name="part-1---deploy-the-api"></a>Del 1 – distribuera API: et

Distribuera den tillhandahållna [API-koden](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/API/Jumio.Api) till en Azure-tjänst. Du kan publicera koden från Visual Studio genom att följa dessa [anvisningar](https://docs.microsoft.com/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

>[!NOTE]
>Du behöver URL: en för den distribuerade tjänsten för att konfigurera Azure AD med nödvändiga inställningar.

### <a name="part-2---deploy-the-client-certificate"></a>Del 2 – distribuera klient certifikatet

1. Jumio-API-anropet skyddas av ett klient certifikat. Skapa ett självsignerat certifikat med hjälp av PowerShell-exempel koden som anges nedan:

``` PowerShell
$cert = New-SelfSignedCertificate -Type Custom -Subject "CN=Demo-SigningCertificate" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3") -KeyUsage DigitalSignature -KeyAlgorithm RSA -KeyLength 2048 -NotAfter (Get-Date).AddYears(2) -CertStoreLocation "Cert:\CurrentUser\My"
$cert.Thumbprint
$pwdText = "Your password"
$pwd = ConvertTo-SecureString -String $pwdText -Force -AsPlainText
Export-PfxCertificate -Cert $Cert -FilePath "{your-local-path}\Demo-SigningCertificate.pfx" -Password $pwd.

```

2. Certifikatet kommer sedan att exporteras till den plats som angetts för { ``your-local-path`` }.

3. Följ anvisningarna i det här [dokumentet](https://docs.microsoft.com/azure/app-service/configure-ssl-certificate#upload-a-private-certificate)och importera certifikatet till Azure App-tjänsten.

### <a name="part-3---create-a-signingencryption-key"></a>Del 3 – skapa en signerings-/krypterings nyckel

Skapa en slumpmässig sträng med en längd på högst 64 tecken som bara innehåller alfabet eller siffror.

Exempelvis: ``C9CB44D98642A7062A0D39B94B6CDC1E54276F2E7CFFBF44288CEE73C08A8A65``

Använd PowerShell-skriptet nedan för att skapa ett alfanumeriskt värde på 64 tecken.

```PowerShell
-join ((0x30..0x39) + ( 0x41..0x5A) + ( 0x61..0x7A) + ( 65..90 ) | Get-Random -Count 64  | % {[char]$_})

```

### <a name="part-4---configure-the-api"></a>Del 4 – Konfigurera API

Program inställningar kan [konfigureras i App Service i Azure](https://docs.microsoft.com/azure/app-service/configure-common#configure-app-settings). Med den här metoden kan du konfigurera inställningar på ett säkert sätt utan att kontrol lera dem i en lagrings plats. Du måste ange följande inställningar för REST-API: et:

| Programinställningar | Källa | Anteckningar |
| :-------- | :------------| :-----------|
|JumioSettings:AuthUsername | Konfiguration av Jumio-konto |     |
|JumioSettings:AuthPassword | Konfiguration av Jumio-konto |     |
|AppSettings: SigningCertThumbprint|Tumavtryck för självsignerat certifikat har skapats|  |
|AppSettings: IdTokenSigningKey| Signerings nyckel som skapats med PowerShell | |
| AppSettings: IdTokenEncryptionKey |Krypterings nyckel som skapats med PowerShell
| AppSettings: IdTokenIssuer | Utfärdare som ska användas för JWT-token (ett GUID-värde rekommenderas) |
| AppSettings: IdTokenAudience  | Mål grupp som ska användas för JWT-token (ett GUID-värde rekommenderas) |
|AppSettings: BaseRedirectUrl | Bas-URL: en för B2C-principen | https://{din-Tenant-Name}. b2clogin. com/{ditt-Application-ID}|
| WEBSITE_LOAD_CERTIFICATES| Tumavtryck för självsignerat certifikat har skapats |

### <a name="part-5---deploy-the-ui"></a>Del 5 – distribuera användar gränssnittet

1. Konfigurera en [Blob Storage-behållare i ditt lagrings konto](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal#create-a-container).

2. Lagra UI-filerna från [mappen UI](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI) till BLOB-behållaren.

#### <a name="update-ui-files"></a>Uppdatera UI-filer

1. I UI-filerna går du till mappen [ocean_blue](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/UI/ocean_blue)

2. Öppna varje HTML-fil.

3. Sök och ersätt {Your-UI-BLOB-container-URL} med din BLOB container-URL.

4. Sök och ersätt {din-mellanliggande API-URL} med URL: en för mellanliggande API app service.

>[!NOTE]
> Som bästa praxis rekommenderar vi att kunderna lägger till medgivande meddelanden på sidan samling av attribut. Meddela användarna att informationen kommer att skickas till tjänster från tredje part för identitets verifiering.

### <a name="part-6---configure-the-azure-ad-b2c-policy"></a>Del 6 – konfigurera principen för Azure AD B2C

1. Gå till [Azure AD B2C principen](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Jumio/Policies) i mappen principer.

2. Följ det här [dokumentet](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications#custom-policy-starter-pack) för att ladda ned [LocalAccounts start paket](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts)

3. Konfigurera principen för Azure AD B2C klient.

>[!NOTE]
>Uppdatera de angivna principerna så att de relaterar till din angivna klient.

## <a name="test-the-user-flow"></a>Testa användar flödet

1. Öppna Azure AD B2C-klienten och under principer väljer du **identitets miljö ramverk**.

2. Välj din tidigare skapade **SignUpSignIn**.

3. Välj **Kör användar flöde** och välj inställningarna:

   a. **Program**: Välj den registrerade appen (EXEMPLET är JWT)

   b. **Svars-URL**: Välj **omdirigerings-URL**

   c. Välj **Kör användar flöde**.

4. Gå igenom registrerings flödet och skapa ett konto

5. Jumio-tjänsten anropas under flödet efter att användarattribut har skapats. Om flödet är ofullständigt, kontrollerar du att användaren inte har sparats i katalogen.

## <a name="next-steps"></a>Nästa steg

Mer information finns i följande artiklar:

- [Anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-overview)

- [Kom igång med anpassade principer i Azure AD B2C](https://docs.microsoft.com/azure/active-directory-b2c/custom-policy-get-started?tabs=applications)
