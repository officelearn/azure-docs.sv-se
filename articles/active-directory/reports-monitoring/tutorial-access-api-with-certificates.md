---
title: 'Självstudiekursen hämta data med hjälp av Azure AD Reporting API: et med certifikat | Microsoft Docs'
description: 'Den här självstudien beskrivs hur du använder Azure AD Reporting API: et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren.'
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: report-monitor
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 5c54af76fc1e145ea062c6bcb37f354a7de94781
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/19/2018
ms.locfileid: "46364184"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Självstudie: Hämta data med hjälp av Azure Active Directory reporting API: et med certifikat

[Azure Active Directory reporting API: er](concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Om du vill komma åt Azure AD Reporting API utan inblandning av användaren, måste du konfigurera din åtkomst för att använda certifikat.

I den här självstudien får du lära dig hur du skapar ett testcertifikat och använda den för att få åtkomst till MS Graph API för rapportering. Vi rekommenderar inte Testcertifikatet i en produktionsmiljö. 

## <a name="prerequisites"></a>Förutsättningar

1. Först måste slutföra den [krav för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

2. Ladda ned och installera [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

3. Installera [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - ADA-biblioteken krävs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

4. Om det är första gången du använder modulen kör **installera MSCloudIdUtilsModule**, importera annars den med hjälp av den **Import-Module** Powershell-kommando.

Sessionen bör likna den här skärmen:

  ![Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
## <a name="create-a-test-certificate"></a>Skapa ett testcertifikat

1. Använd den **New-SelfSignedCertificate** Powershell-kommandot för att skapa ett testcertifikat.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

2. Använd den **Export-Certificate** för att exportera den till en certifikatfil.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="register-the-certificate-in-your-app"></a>Registrera certifikatet i din app

1. Navigera till den [Azure-portalen](https://portal.azure.com)väljer **Azure Active Directory**och välj sedan **appregistreringar** och välj ditt program i listan. 

2. Välj **inställningar** > **nycklar** och välj **ladda upp offentlig nyckel**.

3. Markera den certifikatfil från föregående steg och välj **spara**. 

4. Observera program-ID och tumavtrycket för certifikatet som du just registrerade med ditt program. Om du vill hitta tumavtrycket från din sida för programmet i portalen går du till **inställningar** och klicka på **nycklar**. Tumavtrycket kommer att den **offentliga nycklar** lista.

5. Öppna applikationsmanifestet i infogade manifest-redigeraren och Ersätt den *keyCredentials* egenskap med din nya certifikatinformationen med hjälp av följande schema. 

   ```
   "keyCredentials": [
        {
            "customKeyIdentifier": "$base64Thumbprint", //base64 encoding of the certificate hash
            "keyId": "$keyid", //GUID to identify the key in the manifest
            "type": "AsymmetricX509Cert",
            "usage": "Verify",
            "value":  "$base64Value" //base64 encoding of the certificate raw data
        }
    ]
   ```

6. Spara manifestet. 
  
## <a name="get-an-access-token-for-ms-graph-api"></a>Hämta en åtkomsttoken för MS Graph API

1. Använd den **Get-MSCloudIdMSGraphAccessTokenFromCert** från MSCloudIdUtils PowerShell-modulen skicka i program-ID och tumavtrycket du hämtade i föregående steg. 

 ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Använd åtkomsttoken för att anropa Graph-API:et

1. Du kan nu använda åtkomsttoken i Powershell-skript för att fråga Graph API. Använd den **Invoke-MSCloudIdMSGraphQuery** cmdlet från MSCloudIDUtils att räkna upp inloggningar och directoryAudits slutpunkten. Den här cmdleten hanterar flerväxlade resultat och skickar dessa resultat till PowerShell-pipelinen.

2. Fråga directoryAudits slutpunkten för att hämta granskningsloggarna. 
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

3. Fråga inloggningar slutpunkten om du vill hämta loggar för inloggning.
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

4. Du kan nu välja att exportera dessa data till en CSV-fil och spara i ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



