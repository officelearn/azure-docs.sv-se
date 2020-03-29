---
title: SharePoint-filer - QnA Maker
description: Lägg till säkra SharePoint-datakällor i kunskapsbasen för att berika kunskapsbasen med frågor och svar som kan skyddas med Active Directory.
ms.topic: conceptual
ms.date: 02/20/2020
ms.openlocfilehash: 6f05079e39c8afb001bd4ba09d68f435c18efad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294884"
---
# <a name="add-a-secured-sharepoint-data-source-to-your-knowledge-base"></a>Lägga till en säker SharePoint-datakälla i kunskapsbasen

Lägg till säkra molnbaserade SharePoint-datakällor i kunskapsbasen för att berika kunskapsbasen med frågor och svar som kan skyddas med Active Directory.

När du lägger till ett säkert SharePoint-dokument i kunskapsbasen, som QnA Maker-chef, måste du begära Active Directory-behörighet för QnA Maker. När den här behörigheten har getts från Active Directory-hanteraren till QnA Maker för åtkomst till SharePoint behöver den inte ges igen. Varje efterföljande dokumenttillägg till kunskapsbasen behöver inte auktorisering om det finns i samma SharePoint-resurs.

Om QnA Maker-kunskapsbashanteraren inte är Active Directory-hanteraren måste du kommunicera med Active Directory-hanteraren för att slutföra den här processen.

## <a name="prerequisites"></a>Krav

* Molnbaserad SharePoint - QnA Maker använder Microsoft Graph för behörigheter. Om din SharePoint är lokal kan du inte extrahera från SharePoint eftersom Microsoft Graph inte kan avgöra behörigheter.
* URL-format - QnA Maker stöder endast SharePoint-url:er som genereras för delning och är av format`https://\*.sharepoint.com`

## <a name="add-supported-file-types-to-knowledge-base"></a>Lägga till filtyper som stöds i kunskapsbasen

Du kan lägga till alla [QnA Maker-stödda filtyper](../Concepts/content-types.md) från en SharePoint-webbplats i din kunskapsbas. Du kan behöva bevilja [behörigheter](#permissions) om filresursen är skyddad.

1. Välj filens ellipsmeny på biblioteket med SharePoint-webbplatsen `...`.
1. Kopiera filens URL.

   ![Hämta SharePoint-filens URL genom att välja filens ellipsmeny och sedan kopiera URL:en.](../media/add-sharepoint-datasources/get-sharepoint-file-url.png)

1. Lägg [till url:en](manage-knowledge-bases.md#edit-knowledge-base) i kunskapsbasen på sidan **Inställningar** på QnA Maker-portalen.

### <a name="images-with-sharepoint-files"></a>Bilder med SharePoint-filer

Om filer innehåller bilder extraheras de inte. Du kan lägga till bilden från QnA Maker-portalen när filen har extraherats till QnA-par.

Lägg till bilden med följande markeringssyntax:

```markdown
![Explanation or description of image](URL of public image)
```

Texten på hakparenteserna, `[]`förklarar bilden. URL:en inom parenteserna `()`är den direkta länken till bilden.

När du testar QnA-paret på den interaktiva testpanelen visas bilden i QnA Maker-portalen i stället för markeringstexten. Detta validerar avbildningen kan hämtas offentligt från ditt klientprogram.

## <a name="permissions"></a>Behörigheter

Tilldelning av behörigheter inträffar när en säker fil från en SharePoint-server läggs till i en kunskapsbas. Beroende på hur SharePoint är konfigurerat och behörigheterna för den person som lägger till filen kan detta krävas:

* inga ytterligare steg - den person som lägger till filen har alla behörigheter som behövs.
* steg av både [kunskapsbashanteraren](#knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal) och [Active Directory-hanteraren](#active-directory-manager-grant-file-read-access-to-qna-maker).

Se stegen nedan.

### <a name="knowledge-base-manager-add-sharepoint-data-source-in-qna-maker-portal"></a>Knowledge base manager: lägg till SharePoint-datakälla i QnA Maker-portalen

När **QnA Maker-hanteraren** lägger till ett säkert SharePoint-dokument i en kunskapsbas initierar kunskapsbashanteraren en begäran om tillstånd som Active Directory-hanteraren behöver för att slutföra.

Begäran börjar med ett popup-fönster för att autentisera till ett Active Directory-konto.

![Autentisera användarkonto](../media/add-sharepoint-datasources/authenticate-user-account.png)

När QnA Maker-hanteraren har valt kontot får Azure Active Directory-administratören ett meddelande om att de måste tillåta QnA Maker-appen (inte QnA Maker-hanteraren) åtkomst till SharePoint-resursen. Azure Active Directory-hanteraren måste göra detta för varje SharePoint-resurs, men inte alla dokument i den resursen.

### <a name="active-directory-manager-grant-file-read-access-to-qna-maker"></a>Active Directory Manager: bevilja filläsåtkomst till QnA Maker

Active Directory-hanteraren (inte QnA Maker-hanteraren) behöver bevilja åtkomst till QnA Maker för att komma åt SharePoint-resursen genom att välja [den här länken](https://login.microsoftonline.com/common/oauth2/v2.0/authorize?response_type=id_token&scope=Files.Read%20Files.Read.All%20Sites.Read.All%20User.Read%20User.ReadBasic.All%20profile%20openid%20email&client_id=c2c11949-e9bb-4035-bda8-59542eb907a6&redirect_uri=https%3A%2F%2Fwww.qnamaker.ai%3A%2FCreate&state=68) för att auktorisera QnA Maker Portal SharePoint-företagsappen för att ha filläsningsbehörighet.

![Azure Active Directory-hanteraren beviljar behörighet interaktivt](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)

<!--
The Active Directory manager must grant QnA Maker access either by application name, `QnAMakerPortalSharePoint`, or by application ID, `c2c11949-e9bb-4035-bda8-59542eb907a6`.
-->
<!--
### Grant access from the interactive pop-up window

The Active Directory manager will get a pop-up window requesting permissions to the `QnAMakerPortalSharePoint` app. The pop-up window includes the QnA Maker Manager email address that initiated the request, an `App Info` link to learn more about **QnAMakerPortalSharePoint**, and a list of permissions requested. Select **Accept** to provide those permissions.

![Azure Active Directory manager grants permission interactively](../media/add-sharepoint-datasources/aad-manager-grants-permission-interactively.png)
-->
<!--

### Grant access from the App Registrations list

1. The Active Directory manager signs in to the Azure portal and opens **[App registrations list](https://ms.portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade)**.

1. Search for and select the **QnAMakerPortalSharePoint** app. Change the second filter box from **My apps** to **All apps**. The app information will open on the right side.

    ![Select QnA Maker app in App registrations list](../media/add-sharepoint-datasources/select-qna-maker-app-in-app-registrations.png)

1. Select **Settings**.

    [![Select Settings in the right-side blade](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png)](../media/add-sharepoint-datasources/select-settings-for-qna-maker-app-registration.png#lightbox)

1. Under **API access**, select **Required permissions**.

    ![Select 'Settings', then under 'API access', select 'Required permission'](../media/add-sharepoint-datasources/select-required-permissions-in-settings-blade.png)

1. Do not change any settings in the **Enable Access** window. Select **Grant Permission**.

    [![Under 'Grant Permission', select 'Yes'](../media/add-sharepoint-datasources/grant-app-required-permissions.png)](../media/add-sharepoint-datasources/grant-app-required-permissions.png#lightbox)

1. Select **YES** in the pop-up confirmation windows.

    ![Grant required permissions](../media/add-sharepoint-datasources/grant-required-permissions.png)
-->
### <a name="grant-access-from-the-azure-active-directory-admin-center"></a>Bevilja åtkomst från administrationscentret för Azure Active Directory

1. Active Directory-hanteraren loggar in på Azure-portalen och öppnar **[Enterprise-program](https://aad.portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps)**.

1. Sök `QnAMakerPortalSharePoint` efter den valda QnA Maker-appen.

    [![Sök efter QnAMakerPortalSharePoint i företagsapplistan](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png)](../media/add-sharepoint-datasources/search-enterprise-apps-for-qna-maker.png#lightbox)

1. Under **Säkerhet**går du till **Behörigheter**. Välj **Bevilja administratörssamtycke för organisation**.

    [![Välj autentiserade användare för Active Directory Admin](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png)](../media/add-sharepoint-datasources/grant-aad-permissions-to-enterprise-app.png#lightbox)

1. Välj ett inloggningskonto med behörighet att bevilja behörigheter för Active Directory.



<!--

## Add SharePoint data source with APIs

You need to get the SharePoint file's URI before adding it to QnA Maker.

## Get SharePoint File URI

Use the following steps to transform the SharePoint URL into a sharing token.

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

### Add or update a SharePoint File URI to your knowledge base

Use the **@microsoft.graph.downloadUrl** from the previous section as the `fileuri` in the QnA Maker API for [adding a knowledge base](https://go.microsoft.com/fwlink/?linkid=2092179) or [updating a knowledge base](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update). The following fields are mandatory: name, fileuri, filename, source.

```
{
    "name": "Knowledge base name",
    "files": [
        {
            "fileUri": "<@microsoft.graph.downloadURL>",
            "fileName": "filename.xlsx",
            "source": "<SharePoint link>"
        }
    ],
    "urls": [],
    "users": [],
    "hostUrl": "",
    "qnaList": []
}
```



## Remove QnA Maker app from SharePoint authorization

1. Use the steps in the previous section to find the Qna Maker app in the Active Directory admin center.
1. When you select the **QnAMakerPortalSharePoint**, select **Overview**.
1. Select **Delete** to remove permissions.

-->

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Samarbeta i din kunskapsbas](collaborate-knowledge-base.md)
