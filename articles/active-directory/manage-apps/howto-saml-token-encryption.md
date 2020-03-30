---
title: SAML-tokenkryptering i Azure Active Directory
description: Lär dig hur du konfigurerar AZURE Active Directory SAML-tokenkryptering.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: mimart
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0082d841faf22745e609d38444f4a97553b3c867
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79365874"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption"></a>Så här konfigurerar du Azure AD SAML-tokenkryptering

> [!NOTE]
> Tokenkryptering är en Azure Active Directory-premiumfunktion (Azure AD). Mer information om Azure AD-utgåvor, funktioner och priser finns i [Azure AD-priser](https://azure.microsoft.com/pricing/details/active-directory/).

SAML-tokenkryptering möjliggör användning av krypterade SAML-påståenden med ett program som stöder det. När azure AD har konfigurerats för ett program krypteras SAML-påståenden som det avger för det programmet med hjälp av den offentliga nyckeln som hämtats från ett certifikat som lagras i Azure AD. Programmet måste använda den matchande privata nyckeln för att dekryptera token innan den kan användas som bevis på autentisering för den inloggade användaren.

Kryptera SAML-påståenden mellan Azure AD och programmet ger ytterligare garantier för att innehållet i token inte kan fångas upp och personliga data eller företagsdata äventyras.

Även utan tokenkryptering skickas aldrig Azure AD SAML-token på nätverket i clear. Azure AD kräver token begäran /svar utbyten att äga rum över krypterade HTTPS / TLS-kanaler så att kommunikationen mellan IDP, webbläsare och program sker över krypterade länkar. Tänk på värdet av tokenkryptering för din situation jämfört med omkostnaderna för att hantera ytterligare certifikat.   

Om du vill konfigurera tokenkryptering måste du överföra en X.509-certifikatfil som innehåller den offentliga nyckeln till Azure AD-programobjektet som representerar programmet. Om du vill hämta X.509-certifikatet kan du hämta det från själva programmet, eller hämta det från programleverantören i de fall där programleverantören tillhandahåller krypteringsnycklar eller i de fall där programmet förväntar sig att du ska tillhandahålla en privat nyckel, kan det vara skapas med hjälp av kryptografverktyg, den privata nyckeldelen som överförs till programmets nyckelarkiv och det matchande offentliga nyckelcertifikatet som överförs till Azure AD.

Azure AD använder AES-256 för att kryptera SAML-kontrolldata.

## <a name="configure-saml-token-encryption"></a>Konfigurera KRYPTERING AV SAML-token

Så här konfigurerar du SAML-tokenkryptering:

1. Skaffa ett offentligt nyckelcertifikat som matchar en privat nyckel som är konfigurerad i programmet.

    Skapa ett asymmetriskt nyckelpar som ska användas för kryptering. Om programmet tillhandahåller en offentlig nyckel som ska användas för kryptering följer du programmets instruktioner för att hämta X.509-certifikatet.

    Den offentliga nyckeln bör lagras i en X.509-certifikatfil i .cer-format.

    Om programmet använder en nyckel som du skapar för din instans följer du instruktionerna från ditt program för att installera den privata nyckeln som programmet ska använda för att dekryptera token från din Azure AD-klientorganisation.

1. Lägg till certifikatet i programkonfigurationen i Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Så här konfigurerar du tokenkryptering i Azure-portalen

Du kan lägga till det offentliga certifikatet i programkonfigurationen i Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Gå till **Azure Active Directory > Enterprise-programblad** och välj sedan det program som du vill konfigurera tokenkryptering för.

1. På programmets sida väljer du **Tokenkryptering**.

    ![Alternativet Tokenkryptering i Azure-portalen](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Alternativet **Tokenkryptering** är endast tillgängligt för SAML-program som har konfigurerats från **bladet Enterprise-program** i Azure-portalen, antingen från programgalleriet eller en app som inte är galleri. För andra program är det här menyalternativet inaktiverat. För program som registreras via **appregistreringsupplevelsen** i Azure-portalen kan du konfigurera kryptering för SAML-token med hjälp av programmanifestet, via Microsoft Graph eller via PowerShell.

1. På sidan **Tokenkryptering** väljer du **Importera certifikat för** att importera .cer-filen som innehåller ditt offentliga X.509-certifikat.

    ![Importera .cer-filen som innehåller X.509-certifikatet](./media/howto-saml-token-encryption/import-certificate-small.png)

1. När certifikatet har importerats och den privata nyckeln har konfigurerats för användning på programsidan aktiverar du kryptering genom att välja **...** bredvid tumavtrycksstatusen och välj sedan **Aktivera tokenkryptering** från alternativen i rullgardinsmenyn.

1. Välj **Ja** för att bekräfta aktiveringen av tokenkrypteringscertifikatet.

1. Bekräfta att SAML-påståendena som avges för programmet är krypterade.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Så här inaktiverar du tokenkryptering i Azure-portalen

1. Gå till **Azure Active Directory > Enterprise-program i**Azure-portalen och välj sedan det program som har SAML-tokenkryptering aktiverad.

1. På programmets sida väljer du **Tokenkryptering,** hittar certifikatet och väljer sedan alternativet **...** för att visa rullgardinsmenyn.

1. Välj **Inaktivera tokenkryptering**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurera SAML-tokenkryptering med Graph API, PowerShell eller appmanifest

Krypteringscertifikat lagras på programobjektet i `encrypt` Azure AD med en användningstagg. Du kan konfigurera flera krypteringscertifikat och det som är aktivt för `tokenEncryptionKeyID` kryptering av token identifieras av attributet.

Du behöver programmets objekt-ID för att konfigurera tokenkryptering med Microsoft Graph API eller PowerShell. Du kan hitta det här värdet programmässigt eller genom att gå till programmets **egenskapssida** i Azure-portalen och notera **värdet Objekt-ID.**

När du konfigurerar en keyCredential med Graph, PowerShell eller i programmanifestet bör du generera ett GUID som ska användas för keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Så här konfigurerar du tokenkryptering med Microsoft Graph

1. Uppdatera programmets `keyCredentials` med ett X.509-certifikat för kryptering. Följande exempel visar hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid>

    { 
       "keyCredentials":[ 
          { 
             "type":"AsymmetricX509Cert","usage":"Encrypt",
             "keyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35",    (Use a GUID generator to obtain a value for the keyId)
             "key": "MIICADCCAW2gAwIBAgIQ5j9/b+n2Q4pDvQUCcy3…"  (Base64Encoded .cer file)
          }
        ]
    }
    ```

1. Identifiera det krypteringscertifikat som är aktivt för kryptering av token. Följande exempel visar hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Så här konfigurerar du tokenkryptering med PowerShell

1. Använd den senaste Azure AD PowerShell-modulen för att ansluta till din klient.

1. Ange tokenkrypteringsinställningarna med kommandot **[Set-AzureApplication.](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)**

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials "<KeyCredentialsObject>"  -TokenEncryptionKeyId <keyID>
    ```

1. Läs inställningarna för tokenkryptering med hjälp av följande kommandon.

    ```powershell
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Så här konfigurerar du tokenkryptering med hjälp av programmanifestet

1. Gå till **Azure Active Directory > Appregistreringar från Azure-portalen**.

1. Välj **Alla appar** i listrutan om du vill visa alla appar och välj sedan det företagsprogram som du vill konfigurera.

1. På programmets sida väljer du **Manifest för** att redigera [programmanifestet](../develop/reference-app-manifest.md).

1. Ange värdet för `tokenEncryptionKeyId` attributet.

    I följande exempel visas ett programmanifest som konfigurerats med två krypteringscertifikat och den andra markeras som den aktiva med tokenEnryptionKeyId.

    ```json
    { 
      "id": "3cca40e2-367e-45a5-8440-ed94edd6cc35",
      "accessTokenAcceptedVersion": null,
      "allowPublicClient": false,
      "appId": "cb2df8fb-63c4-4c35-bba5-3d659dd81bf1",
      "appRoles": [],
      "oauth2AllowUrlPathMatching": false,
      "createdDateTime": "2017-12-15T02:10:56Z",
      "groupMembershipClaims": "SecurityGroup",
      "informationalUrls": { 
         "termsOfService": null, 
         "support": null, 
         "privacy": null, 
         "marketing": null 
      },
      "identifierUris": [ 
        "https://testapp"
      ],
      "keyCredentials": [ 
        { 
          "customKeyIdentifier": "Tog/O1Hv1LtdsbPU5nPphbMduD=", 
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "8be4cb65-59d9-404a-a6f5-3d3fb4030351", 
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest" 
        }, 
        {
          "customKeyIdentifier": "U5nPphbMduDmr3c9Q3p0msqp6eEI=",
          "endDate": "2039-12-31T23:59:59Z", 
          "keyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851",
          "startDate": "2018-10-25T21:42:18Z", 
          "type": "AsymmetricX509Cert", 
          "usage": "Encrypt", 
          "value": <Base64EncodedKeyFile> 
          "displayName": "CN=SAMLEncryptTest2" 
        } 
      ], 
      "knownClientApplications": [], 
      "logoUrl": null, 
      "logoutUrl": null, 
      "name": "Test SAML Application", 
      "oauth2AllowIdTokenImplicitFlow": true, 
      "oauth2AllowImplicitFlow": false, 
      "oauth2Permissions": [], 
      "oauth2RequirePostResponse": false, 
      "orgRestrictions": [], 
      "parentalControlSettings": { 
         "countriesBlockedForMinors": [], 
         "legalAgeGroupRule": "Allow" 
        }, 
      "passwordCredentials": [], 
      "preAuthorizedApplications": [], 
      "publisherDomain": null, 
      "replyUrlsWithType": [], 
      "requiredResourceAccess": [], 
      "samlMetadataUrl": null, 
      "signInUrl": "https://127.0.0.1:444/applications/default.aspx?metadata=customappsso|ISV9.1|primary|z" 
      "signInAudience": "AzureADMyOrg",
      "tags": [], 
      "tokenEncryptionKeyId": "6b9c6e80-d251-43f3-9910-9f1f0be2e851" 
    }  
    ```

## <a name="next-steps"></a>Nästa steg

* Ta reda på [hur Azure AD använder SAML-protokollet](../develop/active-directory-saml-protocol-reference.md)
* Lär dig formatet, säkerhetsegenskaperna och innehållet i [SAML-token i Azure AD](../develop/reference-saml-tokens.md)