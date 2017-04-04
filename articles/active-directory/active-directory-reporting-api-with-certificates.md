---
title: "Hämta data med hjälp av Azure AD Reporting API:et med certifikat | Microsoft Docs"
description: "Beskriver hur du använder Azure AD Reporting-API:et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren."
services: active-directory
documentationcenter: 
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: 
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/24/2017
ms.author: ramical
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: c1345dcda6e52267a8037ffd7207e6bc3b0d3b31
ms.lasthandoff: 03/28/2017


---
# <a name="get-data-using-the-azure-ad-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure AD Reporting-API:et med certifikat
Den här artikeln beskriver hur du använder Azure AD Reporting-API:et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren. 

## <a name="use-the-azure-ad-reporting-api"></a>Använda Azure AD Reporting-API:et 
Azure AD Reporting-API:et kräver att du utför följande steg:
 *    Installationskrav
 *    Konfigurera certifikatet i appen
 *    Hämta en åtkomsttoken
 *    Använd åtkomsttoken för att anropa Graph-API:et

Information om källkoden finns i [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Använda Report API-modulen). 

### <a name="install-prerequisites"></a>Installationskrav
Azure AD PowerShell V2 och AzureADUtils-modulen måste vara installerade.

1. Ladda ned och installera Azure AD Powershell V2 genom att följa anvisningarna i [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).
2. Ladda ned Azure AD Utils-modulen från [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
   * Den senaste versionen av ADAL med Nuget
   * Åtkomsttoken från användare, programnycklar och certifikat med ADAL
   * Växlingsbara resultat för Graph API-hantering

**Så här installerar du Azure AD Utils-modulen:**

1. Skapa en katalog för att spara verktygsmodulen (till exempel c:\azureAD) och ladda ned modulen från GitHub.
2. Öppna en PowerShell-session och gå till den katalog som du nyss skapade. 
3. Importera modulen och installera den på PowerShell-modulens sökväg med hjälp av cmdleten Install-AzureADUtilsModule. 

Sessionen bör likna den här skärmen:

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

### <a name="set-the-certificate-in-your-app"></a>Konfigurera certifikatet i appen
1. Om du redan har en app hämtar du dess objekt-ID från Azure Portal. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Öppna en PowerShell-session och anslut till Azure AD med hjälp av cmdleten Connect-AzureAD.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Använd cmdleten New-AzureADApplicationCertificateCredential från AzureADUtils för att lägga till certifikatautentiseringsuppgifter till den. 

>[!Note]
>Du måste ange programmets objekt-ID som du hämtade tidigare, samt certifikatobjektet (som du hämtar med hjälp av Cert:-enheten).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
### <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Du kommer åt token genom att använda cmdleten Get-AzureADGraphAPIAccessTokenFromCert från AzureADUtils. 

>[!NOTE]
>Du måste använda program-ID:t i stället för objekt-ID:t som du använde i det sista avsnittet.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

### <a name="use-the-access-token-to-call-the-graph-api"></a>Använd åtkomsttoken för att anropa Graph-API:et

Nu kan du skapa skriptet. Nedan är ett exempel som använder cmdleten Invoke-AzureADGraphAPIQuery från AzureADUtils. Den här cmdleten hanterar flerväxlade resultat och skickar sedan dessa resultat till PowerShell-pipelinen. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Nu kan du exportera till en CSV-fil och spara till ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg
[Grunderna i Azures identitetshantering](https://docs.microsoft.com/en-us/azure/active-directory/fundamentals-identity)<br>




