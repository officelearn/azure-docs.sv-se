---
title: Använd Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services-API | Microsoft Docs
description: Det här avsnittet visar hur du använder Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services-API.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: fcd0ea10bd39f9e7252e114e8d6401a4fe0ecadb
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/12/2018
ms.locfileid: "35962119"
---
# <a name="use-azure-cli-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Använd Azure CLI för att skapa en AAD-app och konfigurera den för att komma åt Azure Media Services-API

Det här avsnittet visar hur du använder Azure CLI för att skapa ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för Azure Media Services-resurser. 

## <a name="prerequisites"></a>Förutsättningar

- Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapar ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Använda Azure Cloudshell

1. Logga in på [Azure-portalen](https://portal.azure.com/).
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
