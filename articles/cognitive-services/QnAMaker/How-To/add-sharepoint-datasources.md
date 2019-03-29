---
title: SharePoint-filer – QnA Maker
titleSuffix: Azure Cognitive Services
description: Lägg till skyddade Sharepoint-datakällor i kunskapsbasen att utöka kunskapsbas med frågor och svar som kan skyddas med Active Directory.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/26/2019
ms.author: tulasim
ms.openlocfilehash: 97da0608781c286e685fd70dd7be37192dd3c9fd
ms.sourcegitcommit: c63fe69fd624752d04661f56d52ad9d8693e9d56
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2019
ms.locfileid: "58580418"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Lägg till en skyddad Sharepoint-datakälla till din kunskapsbas

Lägg till skyddade Sharepoint-datakällor i kunskapsbasen att utöka kunskapsbas med frågor och svar som kan skyddas med Active Directory. 

När du lägger till en skyddad Sharepoint-dokument till din kunskapsbas som QnA Maker-manager måste du begära Active Directory-behörighet för QnA Maker. När den här behörigheten ges från Active Directory-hanteraren till QnA Maker för åtkomst till Sharepoint, har den inte ges igen. Varje efterföljande dokumentet tillägg i kunskapsbasen behöver inte auktorisering om den finns i samma Sharepoint-resurs. 

Om QnA Maker kunskapsbas manager inte är Active Directory-hanteraren, behöver du ska kunna kommunicera med Active Directory-hanteraren för att slutföra den här processen.

## <a name="add-supported-file-types-to-knowledge-base"></a>Lägg till filtyper som stöds i kunskapsbas

Du kan lägga till alla QnA Maker stöd [filtyper](../Concepts/data-sources-supported.md) från en Sharepoint-server till din kunskapsbas. Du kan behöva bevilja [behörigheter](#permissions) om resursen för filen är skyddad.

1. Från Sharepoint-servern väljer du ellips-menyn för filens `...`.
1. Kopiera filens URL.

    ![Hämta URL: en för Sharepoint-filen genom att välja filens ellips-menyn Kopiera URL: en.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. QnA Maker-portalen på den **inställningar** sidan [Lägg till URL: en](edit-knowledge-base.md#add-datasource) i kunskapsbasen. 

## <a name="permissions"></a>Behörigheter

Bevilja behörigheter som händer när en skyddad fil från en Sharepoint-server läggs till i en kunskapsbas. Beroende på hur Sharepoint ställs upp och behörigheterna för den person som att lägga till filen, det kan innebära att:

* Inga ytterligare åtgärder – den person som att lägga till filen har alla behörigheter som behövs.
* steg efter både [kunskapsbas manager](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) och [Active Directory-hanteraren](#active-directory-manager-grant-file-read-access-to-qna-maker).

Se de steg som visas nedan. 

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Kunskapsbas manager: Lägg till Sharepoint-datakälla i QnA Maker-portalen

När den **QnA Maker manager** lägger till en skyddad Sharepoint-dokument till en kunskapsbas kunskapsbas manager initierar en begäran om behörighet som Active Directory-hanteraren måste utföra.

Begäran börjar med ett popup-fönster för att autentisera till en Active Directory-konto. 

![Autentisera användarkontot](../media/add-sharepoint-datasources/authenticate-user-account.png)

När QnA Maker manager väljer kontot, får Active Directory-administratör ett meddelande om att han behöver för att tillåta QnA Maker app (inte QnA Maker manager) åtkomst till Sharepoint-resursen. Active Directory-manager behöver du göra detta för varje Sharepoint-resurs, men inte alla dokument i den här resursen. 

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active directory-hanteraren: bevilja läsbehörighet för filen till QnA Maker

Active Directory-hanteraren (inte QnA Maker manager) måste bevilja åtkomst till QnA Maker för åtkomst till Sharepoint-resursen genom att välja [den här länken](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=files.read%20openid%20profile&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) att auktorisera QnA Maker Portal Sharepoint enterprise appen att ha läs-fil behörigheter. 

![Azure Active Directory manager ger behörighet att interaktivt](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharepoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`. 
-->
<!--
### Grant access from the interactive pop-up window 

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharepoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharepoint**, and a list of permissions requested. Select **Accept** to provide those permissions. 

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**. 

1. Search for and select the **QnAMakerPortalSharepoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**. 

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**. 

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows. 

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)

### Grant access from the Azure Active Directory admin center

1. The Active Directory manager signs in to the Azure portal and opens **[Enterprise applications](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**. 

1. Search for `QnAMakerPortalSharepoint` the select the QnA Maker app. 

    [![Search for QnAMakerPortalSharepoint in Enterprise apps list](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Under **Security**, go to **Permissions**. Select **Grant admin consent for Organization**. 

    [![Select authenticated user for Active Directory Admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Select a Sign-On account with permissions to grant permissions for the Active Directory. 

-->
  
<!--

## Add Sharepoint data source with APIs

You need to get the Sharepoint file's URI before adding it to QnA Maker. 

## Get Sharepoint File URI

Use the following steps to transform the Sharepoint URL into a sharing token.

1. Encode the URL using [base64](https://en.wikipedia.org/wiki/Base64). 

1. Convert the base64-encoded result to an unpadded base64url format with the following character changes. 

    * Remove the equal character, `=` from the end of the value. 
    * Replace `/` with `_`. 
    * Replace `+` with `-`. 
    * Append `u!` to be beginning of the string. 

1. Sign in to Graph explorer and run the following query, where `sharedURL` is ...:

    ```
    https://graph.microsoft.com/v1.0/shares/<sharedURL>/driveitem
    ```

    Get the **@microsoft.graph.downloadUrl** and use this as `fileuri` in the QnA Maker APIs.

### Add or update a Sharepoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da75ff) or [updating a knowledge base](https://westus.dev.cognitive.microsoft.com/docs/services/5a93fcf85b4ccd136866eb37/operations/5ac266295b4ccd1554da7600). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<sharepoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from Sharepoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center. 
1. When you select the **QnAMakerPortalSharepoint**, select **Overview**. 
1. Select **Delete** to remove permissions. 

-->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta med din kunskapsbas](collaborate-knowledge-base.md)