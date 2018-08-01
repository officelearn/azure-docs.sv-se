---
title: Hämta data med hjälp av Azure AD Reporting API:et med certifikat | Microsoft Docs
description: Beskriver hur du använder Azure AD Reporting-API:et med certifikatautentiseringsuppgifter för att hämta data från kataloger utan inblandning av användaren.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.component: compliance-reports
ms.date: 05/07/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 44ec19721ba59898915f6547231fb586cb44eb30
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/01/2018
ms.locfileid: "39389760"
---
# <a name="get-data-using-the-azure-active-directory-reporting-api-with-certificates"></a>Hämta data med hjälp av Azure Active Directory Reporting-API:et med certifikat

[Azure Active Directory reporting API: er](active-directory-reporting-api-getting-started-azure-portal.md) ger programmässig åtkomst till data via en uppsättning REST-baserade API: er. Du kan anropa API: erna från en mängd olika programmeringsspråk och verktyg. Du kan konfigurera din åtkomst för att använda certifikat om du vill få åtkomst till Azure AD Reporting API utan inblandning av användaren.

Detta omfattar följande steg:

1. [Installera nödvändiga komponenter](#install-prerequisites)
2. [Registrera certifikatet i din app](#register-the-certificate-in-your-app)
3. [Hämta en åtkomsttoken för MS Graph API](#get-an-access-token-for-ms-graph-api)
4. [Fråga MS Graph API-slutpunkter](#query-the-ms-graph-api-endpoints)


## <a name="install-prerequisites"></a>Installationskrav

1. Kontrollera först att du har slutfört den [krav för att få åtkomst till Azure Active Directory reporting API](active-directory-reporting-api-prerequisites-azure-portal.md). 

2. Ladda ned och installera Azure AD Powershell V2 genom att följa anvisningarna i [Azure Active Directory PowerShell](https://github.com/Azure/azure-docs-powershell-azuread/blob/master/Azure AD Cmdlets/AzureAD/index.md)

3. Installera MSCloudIDUtils från den [PowerShellGallery - MSCloudIdUtils](https://www.powershellgallery.com/packages/MSCloudIdUtils/). Den här modulen tillhandahåller flera verktygs-cmdlets, däribland:
    - ADA-biblioteken krävs för autentisering
    - Åtkomsttoken från användare, programnycklar och certifikat med ADAL
    - Växlingsbara resultat för Graph API-hantering

4. Om det är första gången du använder modulen kör **installera MSCloudIdUtilsModule** för att slutföra installationen, annars kan du helt enkelt importera det med hjälp av den **Import-Module** Powershell-kommando.

Sessionen bör likna den här skärmen:

  ![Windows PowerShell](./media/active-directory-reporting-api-with-certificates/module-install.png)

## <a name="register-the-certificate-in-your-app"></a>Registrera certifikatet i din app

1. Först går du till registreringssidan för ditt program. Du kan göra detta genom att navigera till den [Azure-portalen](https://portal.azure.com), klicka på **Azure Active Directory**, klicka på **appregistreringar** och välja ditt program i listan. 

2. Följ stegen för att [registrera certifikatet med Azure AD](https://docs.microsoft.com/en-us/azure/active-directory/develop/active-directory-certificate-credentials#register-your-certificate-with-azure-ad) för programmet. 

3. Observera program-ID och tumavtrycket för certifikatet som du just registrerade med ditt program. Om du vill hitta tumavtrycket från din sida för programmet i portalen går du till **inställningar** och klicka på **nycklar**. Tumavtrycket kommer att den **offentliga nycklar** lista.

  
## <a name="get-an-access-token-for-ms-graph-api"></a>Hämta en åtkomsttoken för MS Graph API

Om du vill hämta en åtkomsttoken för MS Graph API, använda den **Get-MSCloudIdMSGraphAccessTokenFromCert** från MSCloudIdUtils PowerShell-modulen. 

>[!NOTE]
>Du måste använda program-ID (även kallat ClientID) och tumavtrycket för certifikatet med den privata nyckeln som installeras i datorns certifikatarkiv (CurrentUser eller LocalMachine certifikatarkiv).
>

 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/getaccesstoken.png)

## <a name="use-the-access-token-to-call-the-graph-api"></a>Använd åtkomsttoken för att anropa Graph-API:et

Du kan nu använda åtkomsttoken i Powershell-skript för att fråga Graph API. Nedan är ett exempel med hjälp av den **Invoke-MSCloudIdMSGraphQuery** cmdlet från MSCloudIDUtils att räkna upp inloggningar eller diectoryAudits slutpunkt. Den här cmdleten hanterar flerväxlade resultat och skickar sedan dessa resultat till PowerShell-pipelinen.

### <a name="query-the-directoryaudits-endpoint"></a>Fråga DirectoryAudits slutpunkten
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-directoryAudits.png)

 ### <a name="query-the-signins-endpoint"></a>Fråga inloggningar slutpunkten
 ![Azure Portal](./media/active-directory-reporting-api-with-certificates/query-signins.png)

Du kan nu välja att exportera dessa data till en CSV-fil och spara i ett SIEM-system. Du kan också ta med skriptet i en schemalagd aktivitet för att regelbundet hämta Azure AD-data från din klientorganisation utan att behöva lagra programnycklar i källkoden. 


## <a name="next-steps"></a>Nästa steg

* [Få ett första intryck av rapport-API:er](active-directory-reporting-api-getting-started-azure-portal.md)
* [Granska API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) 
* [Inloggningsaktivitet rapport API-referens](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/signin)



