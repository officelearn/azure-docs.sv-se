---
title: Använd Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services-API | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/14/2019
ms.author: juliako
ms.openlocfilehash: e7ae5f83ff9dbb16733656a3bb4452ace750cf3f
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2019
ms.locfileid: "64690102"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Använd Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Media Services-API 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Upptäck den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledningen från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Det här avsnittet visar hur du använder Azure CLI för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för Azure Media Services-resurser. 

## <a name="prerequisites"></a>Nödvändiga komponenter

- Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapar ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Använda Azure Cloudshell

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Starta Cloud Shell från det övre navigeringsfönstret i portalen.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Mer information finns i [översikt av Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Skapa en Azure AD-app och konfigurera åtkomst till media-konto med Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Exempel:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

I det här exemplet på **omfång** är fullständig resurssökvägen för media services-konto. Men den **omfång** kan vara på alla nivåer.

Det kan till exempel vara något av följande nivåer:
 
* Den **prenumeration** nivå.
* Den **resursgrupp** nivå.
* Den **resource** nivå (till exempel ett konto för Media).

Mer information finns i [skapa Azure-tjänstens huvudnamn med Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Se även [Manage Role-Based åtkomstkontroll med kommandoradsgränssnittet i Azure](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Nästa steg

Kom igång med [ladda upp filer till ditt konto](media-services-portal-upload-files.md).
