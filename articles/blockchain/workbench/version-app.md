---
title: Blockchain app versioning - Azure Blockchain Workbench
description: How to use application versions in Azure Blockchain Workbench Preview.
ms.date: 11/20/2019
ms.topic: article
ms.reviewer: brendal
ms.openlocfilehash: 45219790cf0cd064e0fcd456e262b2f93aa03ac9
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74323916"
---
# <a name="azure-blockchain-workbench-preview-application-versioning"></a>Azure Blockchain Workbench Preview application versioning

You can create and use multiple versions of an Azure Blockchain Workbench Preview app. If multiple versions of the same application are uploaded, a version history is available and users can choose which version they want to use.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Krav

* En Blockchain Workbench-distribution. Mer information om distributioner finns i avsnittet om [Azure Blockchain Workbench-distributioner](deploy.md)
* Ett distribuerat blockkedjeprogram i Blockchain Workbench. Mer information finns i avsnittet om hur du [skapar ett blockkedjeprogram i Azure Blockchain Workbench](create-app.md)

## <a name="add-an-app-version"></a>Add an app version

To add a new version, upload the new configuration and smart contract files to Blockchain Workbench.

1. I en webbläsare går du till webbadressen för Blockchain Workbench. For example, `https://{workbench URL}.azurewebsites.net/` For information on how to find your Blockchain Workbench web address, see [Blockchain Workbench Web URL](deploy.md#blockchain-workbench-web-url)
2. Logga in som [Blockchain Workbench-administratör](manage-users.md#manage-blockchain-workbench-administrators).
3. Select the blockchain application you want to update with another version.
4. Select **Add version**. The **Add version** pane is displayed.
5. Choose the new version contract configuration and contract code files to upload. Konfigurationsfilen verifieras automatiskt. Fix any validation errors before you deploy the application.
6. Select **Add version** to add the new blockchain application version.

    ![Lägga till en ny version](media/version-app/add-version.png)

Deployment of the blockchain application can take a few minutes. When deployment is finished, refresh the application page. Choosing the application and selecting the **Version history** button, displays the version history of the application.

> [!IMPORTANT]
> Previous versions of the application are disabled. You can individually re-enable past versions.
>
> You may need to re-add members to application roles if changes were made to the application roles in the new version.

## <a name="using-app-versions"></a>Using app versions

By default, the latest enabled version of the application is used in Blockchain Workbench. If you want to use a previous version of an application, you need to choose the version from the application page first.

1. In Blockchain Workbench application section, select the application checkbox that contains the contract you want to use. If previous versions are enabled, the version history button is available.
2. Select the **Version history** button.
3. In the version history pane, choose the version of the application by selecting the link in the *Date modified* column.

    ![Choose a previous version](media/version-app/use-version.png)

    You can create new contracts or take actions on previous version contracts. The version of the application is displayed following the application name and a warning is displayed about the older version.

## <a name="next-steps"></a>Nästa steg

* [Azure Blockchain Workbench troubleshooting](troubleshooting.md)
