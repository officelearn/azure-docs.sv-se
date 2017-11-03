---
title: "Använda CLI 2.0 för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services API | Microsoft Docs"
description: "Det här avsnittet visar hur du använder CLI 2.0 för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services API."
services: media-services
documentationcenter: 
author: Juliako
manager: cfowler
editor: 
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/17/2017
ms.author: juliako
ms.openlocfilehash: 01a2bb6d99776feec936315bc882c3097ce832d4
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="use-cli-20-to-create-an-aad-app-and-configure-it-to-access-azure-media-services-api"></a>Använda CLI 2.0 för att skapa en AAD-app och konfigurera den för att komma åt Azure Media Services API

Det här avsnittet visar hur du skapar ett Azure Active Directory (Azure AD)-program och tjänstens huvudnamn för att komma åt resurser i Azure Media Services med hjälp av CLI 2.0. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Mer information finns i avsnittet om [den kostnadsfria utvärderingsversionen av Azure](https://azure.microsoft.com/pricing/free-trial/). 
- Ett Media Services-konto. Mer information finns i [skapa ett Azure Media Services-konto med hjälp av Azure portal](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Använd Azure-molnet Shell

1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Starta molnet Shell från det övre navigeringsfönstret på portalen.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Mer information finns i [översikt av Azure Cloud Shell](../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-cli-20"></a>Skapa en Azure AD-app och konfigurera åtkomst till media-konto med CLI 2.0
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> --password <strong password>
az role assignment create -- assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Exempel:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

I det här exemplet i **omfång** är fullständig resursens sökväg för media services-konto. Men den **omfång** kan när som helst.

Det kan till exempel vara något av följande nivåer:
 
* Den **prenumeration** nivå.
* Den **resursgruppen** nivå.
* Den **resurs** nivå (till exempel ett konto för Media).

Mer information finns i [skapa en Azure tjänstens huvudnamn med Azure CLI 2.0](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Se även [Manage Role-Based åtkomstkontroll med kommandoradsgränssnittet i Azure](../active-directory/role-based-access-control-manage-access-azure-cli.md). 

## <a name="next-steps"></a>Nästa steg

Kom igång med [överföringen av filer till ditt konto](media-services-portal-upload-files.md).
