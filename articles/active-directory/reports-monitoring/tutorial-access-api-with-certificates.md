---
title: Självstudiekurs för AD Reporting API med certifikat | Microsoft-dokument
description: Den här självstudien förklarar hur du använder Azure AD Reporting API med certifikatautentiseringsuppgifter för att hämta data från kataloger utan att användaren behöver göra något.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2808c8431a6b98b162920fb58a6e2ac0498d2055
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2020
ms.locfileid: "82081718"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Självstudiekurs: Hämta data med azure Active Directory-rapporterings-API:et med certifikat

[Azure Active Directory reporting API: er](concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Om du vill komma åt Azure AD Reporting API utan att användaren behöver göra något måste du konfigurera åtkomsten för att använda certifikat.

I den här självstudien får du lära dig hur du använder ett testcertifikat för att komma åt MS Graph API för rapportering. Vi rekommenderar inte att du använder testcertifikat i en produktionsmiljö. 

## <a name="prerequisites"></a>Krav

1. För att komma åt inloggningsdata, se till att du har en Azure Active Directory-klient med en premiumlicens (P1/P2). Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens. 

2. Skapa eller växla till ett användarkonto i den **globala administratören,** **säkerhetsadministratören,** **säkerhetsläsaren** eller **rapportläsarrollen** för klienten. 

3. Slutför [förutsättningarna för att komma åt Azure Active Directory-rapporterings-API:et](howto-configure-prerequisites-for-reporting-api.md). 

4. Ladda ned och installera [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installera [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - De ADAL-bibliotek som behövs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

6. Om det är första gången du använder modulen kör **Install-MSCloudIdUtilsModule**, annars importera den med **kommandot Import-Module** PowerShell. Sessionen ska se ut ungefär ![som den här skärmen: Windows PowerShell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Använd commandleten **New-SelfSignedCertificate** PowerShell för att skapa ett testcertifikat.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Använd kommandoleten **Exportera certifikat** för att exportera den till en certifikatfil.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

1. Navigera till [Azure-portalen,](https://portal.azure.com)välj **Azure Active Directory**och välj sedan **Appregistreringar** och välj ditt program i listan. 

2. Välj **Certifikat & hemligheter** under Avsnittet **Hantera** på Programregistreringsbladet och välj Ladda **upp certifikat**.

3. Markera certifikatfilen i föregående steg och välj **Lägg till**. 

4. Observera ansöknings-ID: t och tumavtrycket för certifikatet som du just registrerade med din ansökan. Om du vill hitta tumavtrycket går du till **Certifikat & hemligheter** under Avsnittet **Hantera** från programsidan i portalen. Tumavtrycket kommer att vara under **listan Certifikat.**

5. Öppna programmanifestet i den infogade manifestredigeraren och verifiera att egenskapen *keyCredentials uppdateras* med din nya certifikatinformation enligt nedan - 

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
6. Nu kan du få en åtkomsttoken för MS Graph API med det här certifikatet. Använd **cmdleten Get-MSCloudIdMSGraphAccessTokenFromCert** från MODULEN MSCloudIdUtils PowerShell, som skickar in program-ID:t och det tumavtryck du fick från föregående steg. 

   ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

7. Använd åtkomsttoken i PowerShell-skriptet för att fråga graph-API:et. Använd cmdleten **Invoke-MSCloudIdMSGraphQuery** från MSCloudIDUtils för att räkna upp signinerna och katalogenAudits-slutpunkten. Den här cmdleten hanterar flersidiga resultat och skickar dessa resultat till PowerShell-pipelinen.

8. Fråga katalogenSlutpunkten för att hämta granskningsloggarna. 
   ![Azure-portalen](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

9. Fråga signinslutpunkten för att hämta inloggningsloggarna.
    ![Azure-portalen](./media/tutorial-access-api-with-certificates/query-signins.png)

10. Du kan nu välja att exportera dessa data till en CSV och spara till ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Referens för gransknings-API](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [API-referens för inloggningsaktivitetsrapport](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
