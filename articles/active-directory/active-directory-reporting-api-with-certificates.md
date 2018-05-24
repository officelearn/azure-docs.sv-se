---
title: Hämta data med hjälp av Azure AD Reporting API:et med certifikat | Microsoft Docs
description: Beskriver hur du använder Azure AD Reporting-API:et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren.
services: active-directory
documentationcenter: ''
author: ramical
writer: v-lorisc
manager: kannar
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: ramical
ms.openlocfilehash: 54e661284c539b835089e858ba7b5e0016e89a83
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

[Azure Active Directory reporting API: er](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-reports-and-events-preview) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg.

Om du vill komma åt Azure AD Reporting API:er utan åtgärder från användaren kan du använda certifikat för åtkomst

Den här artikeln:

- Innehåller steg för att få åtkomst till Azure AD Reporting API:er med certifikat.
- Förutsätter att du har slutfört [kraven för att få åtkomst till Azure Active Directory Reporting API](active-directory-reporting-api-prerequisites-azure-portal.md). 


Om du vill komma åt rapporterings-API:et med certifikat, måste du:

1. Installera förhandskraven
2. Konfigurera certifikatet i appen 
3. Bevilja behörigheter
4. Hämta en åtkomsttoken




Information om källkoden finns i [Leverage Report API Module](https://github.com/AzureAD/azure-activedirectory-powershell/tree/gh-pages/Modules/AzureADUtils) (Använda Report API-modulen). 

## <a name="install-prerequisites"></a>Installationskrav

Azure AD PowerShell V2 och AzureADUtils-modulen måste vara installerade.

1. Ladda ned och installera Azure AD Powershell V2 genom att följa anvisningarna i [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md).

2. Ladda ned Azure AD Utils-modulen från [AzureAD/azure-activedirectory-powershell](https://github.com/AzureAD/azure-activedirectory-powershell/blob/gh-pages/Modules/AzureADUtils/AzureADUtils.psm1). 
  Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - Den senaste versionen av ADAL med Nuget
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

**Så här installerar du Azure AD Utils-modulen:**

1. Skapa en katalog för att spara verktygsmodulen (till exempel c:\azureAD) och ladda ned modulen från GitHub.
2. Öppna en PowerShell-session och gå till den katalog som du nyss skapade. 
3. Importera modulen och installera den på PowerShell-modulens sökväg med hjälp av cmdleten Install-AzureADUtilsModule. 

Sessionen bör likna den här skärmen:

  ![Windows PowerShell](./media/active-directory-report-api-with-certificates/windows-powershell.png)

## <a name="set-the-certificate-in-your-app"></a>Konfigurera certifikatet i appen

**Konfigurera certifikatet i appen:**

1. [Hämta objekt-ID](active-directory-reporting-api-prerequisites-azure-portal.md#get-your-applications-client-id) för din app i Azure Portal. 

  ![Azure Portal](./media/active-directory-report-api-with-certificates/azure-portal.png)

2. Öppna en PowerShell-session och anslut till Azure AD med hjälp av cmdleten Connect-AzureAD.

  ![Azure Portal](./media/active-directory-report-api-with-certificates/connect-azuaread-cmdlet.png)

3. Använd cmdleten New-AzureADApplicationCertificateCredential från AzureADUtils för att lägga till certifikatautentiseringsuppgifter till den. 

>[!Note]
>Du måste ange programmets objekt-ID som du hämtade tidigare, samt certifikatobjektet (som du hämtar med hjälp av Cert:-enheten).
>


  ![Azure Portal](./media/active-directory-report-api-with-certificates/add-certificate-credential.png)
  
## <a name="get-an-access-token"></a>Hämta en åtkomsttoken

Du kommer åt token genom att använda cmdleten **Get-AzureADGraphAPIAccessTokenFromCert** från AzureADUtils. 

>[!NOTE]
>Du måste använda program-ID:t i stället för objekt-ID:t som du använde i det sista avsnittet.
>

 ![Azure Portal](./media/active-directory-report-api-with-certificates/application-id.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Använd åtkomsttoken för att anropa Graph-API:et

Nu kan du skapa skriptet. Nedan är ett exempel som använder cmdleten **Invoke-AzureADGraphAPIQuery** från AzureADUtils. Den här cmdleten hanterar flerväxlade resultat och skickar sedan dessa resultat till PowerShell-pipelinen. 

 ![Azure Portal](./media/active-directory-report-api-with-certificates/script-completed.png)

Nu kan du exportera till en CSV-fil och spara till ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 

## <a name="next-steps"></a>Nästa steg

- [Få ett första intryck av rapport-API:er](active-directory-reporting-api-getting-started-azure-portal.md#explore)

- [Skapa dina egen lösning](active-directory-reporting-api-getting-started-azure-portal.md#customize)




