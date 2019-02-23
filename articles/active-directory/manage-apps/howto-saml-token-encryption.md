---
title: SAML-tokenkryptering i Azure Active Directory
description: Lär dig hur du konfigurerar Azure Active Directory SAML-tokenkryptering.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/06/2019
ms.author: celested
ms.reviewer: paulgarn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a7a12cf677661c36a42df36a8fdc6b8f4a8ef75
ms.sourcegitcommit: 8ca6cbe08fa1ea3e5cdcd46c217cfdf17f7ca5a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2019
ms.locfileid: "56669898"
---
# <a name="how-to-configure-azure-ad-saml-token-encryption-preview"></a>Anvisningar: Konfigurera Azure AD SAML-tokenkryptering (förhandsversion)

> [!NOTE]
> Kryptering av säkerhetstoken är en Azure Active Directory (Azure AD) premium-funktion. Läs mer om Azure AD-versioner, funktioner och priser i [priser för Azure AD](https://azure.microsoft.com/pricing/details/active-directory/).

SAML-tokenkryptering kan du använda den krypterade SAML intyg med ett program som stöder den. När konfigureras för ett program, krypteras SAML-intyg det genererar för programmet med hjälp av den offentliga nyckeln hämtas från ett certifikat som lagras i Azure AD med Azure AD. Programmet måste använda den matchande privata nyckeln för att dekryptera token innan den kan användas som bevis på autentisering för den inloggade användaren.

Kryptera SAML-intyg mellan Azure AD och programmet tillhandahåller ytterligare trygghet som inte kan fångas upp innehållet i token och personlig eller företagsdata äventyras.

Även om du inte tokenkryptering skickas aldrig Azure AD SAML-tokens i nätverket i klartext. Azure AD kräver token begäran/svar-utbyten ska göras för krypterade HTTPS/TLS-kanaler så att kommunikationen mellan IDP: N, webbläsare och program kan ske under krypterade länkar. Överväg att värdet för kryptering av säkerhetstoken för din situation jämfört med arbetet med att hantera ytterligare certifikat.   

Om du vill konfigurera tokenkryptering, måste du överföra en fil för X.509-certifikat som innehåller den offentliga nyckeln till programmet Azure AD-objekt som representerar programmet. För att få X.509-certifikat kan hämta du den från programmet sig själv eller hämta det från programleverantören i fall där programleverantören ger krypteringsnycklar eller i fall där programmet förväntar sig att du kan ange en privat nyckel kan det vara skapas med hjälp av kryptografi-verktyg, den privata nyckeldelen överförs till programmets nyckelarkivet och matchande offentliga nyckelcertifikat som överförts till Azure AD.

Azure AD använder AES-256 för att kryptera SAML assertion data.

## <a name="configure-saml-token-encryption"></a>Konfigurera SAML-tokenkryptering

Följ dessa steg för att konfigurera SAML-tokenkryptering:

1. Hämta ett certifikat med offentlig nyckel som matchar en privat nyckel som har konfigurerats i programmet.

    Skapa ett asymmetriskt nyckelpar ska användas för kryptering. Eller om programmet tillhandahåller en offentlig nyckel ska användas för kryptering, följ programmets anvisningar för att hämta X.509-certifikat.

    Den offentliga nyckeln ska lagras i en fil med X.509-certifikatet i .cer-format.

    Om programmet använder en nyckel som du skapar för din instans, följer du instruktionerna från ditt program för att installera den privata nyckeln som programmet använder för att dekryptera token från Azure AD-klienten.

1. Lägga till certifikatet programkonfiguration i Azure AD.

### <a name="to-configure-token-encryption-in-the-azure-portal"></a>Konfigurera kryptering av säkerhetstoken i Azure portal

Du kan lägga till offentligt certifikat till din programkonfiguration i Azure-portalen.

1. Gå till [Azure-portalen](https://portal.azure.com).

1. Gå till den **Azure Active Directory > företagsprogram** bladet och välj sedan det program som du vill konfigurera kryptering av säkerhetstoken för.

1. På sidan för programmets väljer **Token kryptering**.

    ![Token krypteringsalternativet i Azure portal](./media/howto-saml-token-encryption/token-encryption-option-small.png)

    > [!NOTE]
    > Den **Token kryptering** alternativet är bara tillgängligt för SAML-program som har ställts in från den **företagsprogram** bladet i Azure-portalen, antingen från Programgalleriet eller en Icke-galleri-app. Alternativet är inaktiverat för andra program. För program som är registrerade via den **appregistreringar** upplevelse i Azure-portalen kan du konfigurera kryptering för SAML-token med hjälp av programmet manifest via Microsoft Graph eller PowerShell.

1. På den **Token kryptering** väljer **Importera certifikat** att importera CER-filen som innehåller din offentliga X.509-certifikat.

    ![Importera CER-filen som innehåller X.509-certifikat](./media/howto-saml-token-encryption/import-certificate-small.png)

1. När certifikatet har importerats och den privata nyckeln är konfigurerad för användning på programsidan, aktivera kryptering genom att välja den **...**  Nästa om du vill tumavtryck status och välj sedan **Aktivera kryptering av säkerhetstoken** från alternativen i den nedrullningsbara menyn.

1. Välj **Ja** att bekräfta aktivering av certifikat för tokenkryptering.

1. Bekräfta att SAML-intyg som genereras för programmet är krypterad.

### <a name="to-deactivate-token-encryption-in-the-azure-portal"></a>Så här inaktiverar du tokenkryptering i Azure portal

1. I Azure-portalen går du till **Azure Active Directory > företagsprogram**, och välj sedan det program som har SAML-tokenkryptering aktiverat.

1. På sidan för programmets väljer **Token kryptering**, hitta certifikatet och välj sedan den **...**  alternativet för att visa den nedrullningsbara menyn.

1. Välj **inaktivera tokenkryptering**.

## <a name="configure-saml-token-encryption-using-graph-api-powershell-or-app-manifest"></a>Konfigurera SAML-tokenkryptering med Graph API, PowerShell eller appmanifestet

Krypteringscertifikat lagras på programobjektet i Azure AD med en `encrypt` användning taggen. Du kan konfigurera flera krypteringscertifikat och det som är aktiv för kryptering av token har identifierats av den `tokenEncryptionKeyID` attribut.

Du måste programmets objekt-ID för att konfigurera kryptering av säkerhetstoken med Microsoft Graph API eller PowerShell. Du hittar det här värdet programmässigt eller genom att gå till programmets **egenskaper** sida i Azure-portalen och vid den **objekt-ID** värde.

När du konfigurerar en keyCredential med hjälp av Graph, PowerShell, eller i applikationsmanifestet, ska du skapa ett GUID för keyId.

### <a name="to-configure-token-encryption-using-microsoft-graph"></a>Konfigurera kryptering av säkerhetstoken med hjälp av Microsoft Graph

1. Uppdatera programmets `keyCredentials` med ett X.509-certifikat för kryptering. I följande exempel visas hur du gör detta.

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

1. Identifiera det krypteringscertifikat som är aktiv för kryptering av token. I följande exempel visas hur du gör detta.

    ```
    Patch https://graph.microsoft.com/beta/applications/<application objectid> 

    { 
       "tokenEncryptionKeyId":"fdf8c5d8-f727-43fd-beaf-0f1521cf3d35" (The keyId of the keyCredentials entry to use)
    }
    ```

### <a name="to-configure-token-encryption-using-powershell"></a>Konfigurera kryptering av säkerhetstoken med hjälp av PowerShell

Den här funktionen kommer snart. 

<!--
1. Use the latest Azure AD PowerShell module to connect to your tenant.

1. Set the token encryption settings using the **[Set-AzureApplication](https://docs.microsoft.com/powershell/module/azuread/set-azureadapplication?view=azureadps-2.0-preview)** command.

    ```
    Set-AzureADApplication -ObjectId <ApplicationObjectId> -KeyCredentials “<KeyCredentialsObject>”  -TokenEncryptionKeyId <keyID>
    ```

1. Read the token encryption settings using the following commands.

    ```
    $app=Get-AzureADApplication -ObjectId <ApplicationObjectId>
    $app.KeyCredentials
    $app.TokenEncryptionKeyId
    ```
-->

### <a name="to-configure-token-encryption-using-the-application-manifest"></a>Konfigurera kryptering av säkerhetstoken med hjälp av manifestet

1. Från Azure-portalen går du till **Azure Active Directory > appregistreringar**.

1. Välj **alla appar** i listrutan för att visa alla appar och välj sedan det enterprise-program som du vill konfigurera.

1. Programmets på sidan Välj **Manifest** att redigera den [programmanifestet](../develop/reference-app-manifest.md).

1. Ange värdet för den `tokenEncryptionKeyId` attribut.

    I följande exempel visas ett programmanifest konfigurerad med två krypteringscertifikat och andra valt som den aktiva partitionen med hjälp av tokenEnryptionKeyId.

    ```
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

* Ta reda på [hur Azure AD använder SAML-protokoll](../develop/active-directory-saml-protocol-reference.md)
* Lär dig format, security egenskaperna och innehållet i [SAML-token i Azure AD](../develop/reference-saml-tokens.md)
