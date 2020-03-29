---
title: Använd Azure CLI för att skapa en Azure AD-app och konfigurera den för åtkomst till Azure Media Services API | Microsoft-dokument
description: Det här avsnittet visar hur du använder Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Azure Media Services API.
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
ms.date: 08/26/2019
ms.author: juliako
ms.openlocfilehash: f136fb666e93adc0fe92aee014e3da9a37bbd6aa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70035802"
---
# <a name="use-azure-cli-to-create-an-azure-ad-app-and-configure-it-to-access-media-services-api"></a>Använda Azure CLI för att skapa en Azure AD-app och konfigurera den för att komma åt Media Services API 

> [!NOTE]
> Inga nya funktioner läggs till i Media Services v2. <br/>Kolla in den senaste versionen, [Media Services v3](https://docs.microsoft.com/azure/media-services/latest/). Se även [migreringsvägledning från v2 till v3](../latest/migrate-from-v2-to-v3.md)

Det här avsnittet visar hur du använder Azure CLI för att skapa ett Azure Active Directory -program och tjänsthuvudnamn för att komma åt Azure Media Services-resurser. 

## <a name="prerequisites"></a>Krav

- Ett Azure-konto. Mer information finns i [Kostnadsfri utvärderingsversion](https://azure.microsoft.com/pricing/free-trial/)av Azure . 
- Ett Media Services-konto. Mer information finns i [Skapa ett Azure Media Services-konto med Azure-portalen](media-services-portal-create-account.md).

## <a name="use-the-azure-cloud-shell"></a>Använda Azure Cloud Shell

1. Logga in på [Azure-portalen](https://portal.azure.com/).
2. Starta Cloud Shell från portalens övre navigeringsfönster.

    ![Cloud Shell](./media/media-services-cli-create-and-configure-aad-app/media-services-cli-create-and-configure-aad-app01.png) 

Mer information finns i [Översikt över Azure Cloud Shell](../../cloud-shell/overview.md).

## <a name="create-an-azure-ad-app-and-configure-access-to-the-media-account-with-azure-cli"></a>Skapa en Azure AD-app och konfigurera åtkomst till mediekontot med Azure CLI
 
```azurecli
az login
az ad sp create-for-rbac --name <appName> 
az role assignment create --assignee < user/app id> --role Contributor --scope <subscription/subscription id>
```

Ett exempel:

```azurecli
az role assignment create --assignee a3e068fa-f739-44e5-ba4d-ad57866e25a1 --role Contributor --scope /subscriptions/0b65e280-7917-4874-9fed-1307f2615ea2/resourceGroups/Default-AzureBatch-SouthCentralUS/providers/microsoft.media/mediaservices/sbbash
```

I det här exemplet är **omfånget** den fullständiga resurssökvägen för medietjänstkontot. **Omfattningen** kan dock vara på vilken nivå som helst.

Det kan till exempel vara en av följande nivåer:
 
* **Prenumerationsnivån.**
* **Resursgruppsnivån.**
* **Resursnivån** (till exempel ett Mediekonto).

Mer information finns i [Skapa ett Azure-tjänsthuvudnamn med Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)

Se även [Hantera rollbaserad åtkomstkontroll med Azure-kommandoradsgränssnittet](../../role-based-access-control/role-assignments-cli.md). 

## <a name="next-steps"></a>Nästa steg

Kom igång med [att ladda upp filer till ditt konto](media-services-portal-upload-files.md).
