---
title: 'Självstudiekursen hämta data med hjälp av Azure AD Reporting API: et med certifikat | Microsoft Docs'
description: 'Den här självstudien beskrivs hur du använder Azure AD Reporting API: et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren.'
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: bf877449288b94c373a834d0bb046a1550808528
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/29/2019
ms.locfileid: "55173182"
---
# <a name="tutorial-get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Självstudier: Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

[Azure Active Directory reporting API: er](concept-reporting-api.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Om du vill komma åt Azure AD Reporting API utan inblandning av användaren, måste du konfigurera din åtkomst för att använda certifikat.

I den här självstudien får du lära dig hur du använder ett testcertifikat åtkomst till MS Graph API för rapportering. Vi rekommenderar inte testcertifikat i en produktionsmiljö. 

## <a name="prerequisites"></a>Förutsättningar

1. Kontrollera att du har en Azure Active Directory-klient med en premiumlicens (P1/P2) för att komma åt inloggningsdata. Se [Kom igång med Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) för att uppgradera din Azure Active Directory-version. Observera att om du inte har några aktivitetsdata före uppgraderingen tar det ett par dagar innan data visas i rapporterna när du har uppgraderat till en premiumlicens. 

2. Skapa eller växla till ett användarkonto i den **global administratör**, **säkerhetsadministratör**, **säkerhetsläsare** eller **rapportera läsare** roll för klienten. 

3. Slutför den [krav för att få åtkomst till Azure Active Directory reporting API](howto-configure-prerequisites-for-reporting-api.md). 

4. Ladda ned och installera [Azure AD PowerShell V2](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/docs-conceptual/azureadps-2.0/install-adv2.md).

5. Installera [MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - ADA-biblioteken krävs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

6. Om det är första gången du använder modulen kör **installera MSCloudIdUtilsModule**, importera annars den med hjälp av den **Import-Module** Powershell-kommando. Sessionen bör likna den här skärmen: ![Windows Powershell](./media/tutorial-access-api-with-certificates/module-install.png)
  
7. Använd den **New-SelfSignedCertificate** Powershell-kommandot för att skapa ett testcertifikat.

   ```
   $cert = New-SelfSignedCertificate -Subject "CN=MSGraph_ReportingAPI" -CertStoreLocation "Cert:\CurrentUser\My" -KeyExportPolicy Exportable -KeySpec Signature -KeyLength 2048 -KeyAlgorithm RSA -HashAlgorithm SHA256
   ```

8. Använd den **Export-Certificate** för att exportera den till en certifikatfil.

   ```
   Export-Certificate -Cert $cert -FilePath "C:\Reporting\MSGraph_ReportingAPI.cer"

   ```

## <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

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
  
7. Nu kan kan du hämta en åtkomsttoken för MS Graph API med det här certifikatet. Använd den **Get-MSCloudIdMSGraphAccessTokenFromCert** från MSCloudIdUtils PowerShell-modulen skicka i program-ID och tumavtrycket du hämtade i föregående steg. 

 ![Azure Portal](./media/tutorial-access-api-with-certificates/getaccesstoken.png)

8. Använda åtkomsttoken i Powershell-skript för att fråga Graph API. Använd den **Invoke-MSCloudIdMSGraphQuery** cmdlet från MSCloudIDUtils att räkna upp inloggningar och directoryAudits slutpunkten. Den här cmdleten hanterar flerväxlade resultat och skickar dessa resultat till PowerShell-pipelinen.

9. Fråga directoryAudits slutpunkten för att hämta granskningsloggarna. 
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-directoryAudits.png)

10. Fråga inloggningar slutpunkten om du vill hämta loggar för inloggning.
 ![Azure Portal](./media/tutorial-access-api-with-certificates/query-signins.png)

11. Du kan nu välja att exportera dessa data till en CSV-fil och spara i ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](concept-reporting-api.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)
