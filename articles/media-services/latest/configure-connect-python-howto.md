---
title: Ansluta till Azure Media Services v3 API - Python
description: Den här artikeln visar hur du ansluter till Media Services v3 API med Python.
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
ms.date: 04/15/2019
ms.author: juliako
ms.openlocfilehash: 98a8cdf4120cf56184eb5735249640e3423acdf4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74888469"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ansluta till Media Services v3 API - Python

Den här artikeln visar hur du ansluter till Azure Media Services v3 Python SDK med hjälp av tjänstens huvudloggmetod.

## <a name="prerequisites"></a>Krav

- Ladda ner Python från [python.org](https://www.python.org/downloads/)
- Se till att `PATH` ställa in miljövariabeln
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Kom ihåg resursgruppsnamnet och mediatjänstkontonamnet.
- Följ stegen i avsnittet [Access API:er.](access-api-cli-how-to.md) Registrera prenumerations-ID, program-ID (klient-ID), autentiseringsnyckeln (hemlig) och klient-ID som du behöver i det senare steget.

> [!IMPORTANT]
> Granska [namngivningskonventioner](media-services-apis-overview.md#naming-conventions).

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Azure Media Services med Python måste du installera dessa moduler.

* Modulen, `azure-mgmt-resource` som innehåller Azure-moduler för Active Directory.
* Modulen, `azure-mgmt-media` som innehåller Media Services-entiteterna.

Öppna ett kommandoradsverktyg och använd följande kommandon för att installera modulerna.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Anslut till Python-klienten

1. Skapa en fil `.py` med ett tillägg
1. Öppna filen i din favoritredigerare
1. Lägg till koden som följer i filen. Koden importerar de moduler som krävs och skapar det Active Directory-autentiseringsobjekt som du behöver för att ansluta till Media Services.

      Ange variablernas värden till de värden du fick från [Access-API:er](access-api-cli-how-to.md)

      ```
      import adal
      from msrestazure.azure_active_directory import AdalAuthentication
      from msrestazure.azure_cloud import AZURE_PUBLIC_CLOUD
      from azure.mgmt.media import AzureMediaServices
      from azure.mgmt.media.models import MediaService

      RESOURCE = 'https://management.core.windows.net/'
      # Tenant ID for your Azure Subscription
      TENANT_ID = '00000000-0000-0000-000000000000'
      # Your Service Principal App ID
      CLIENT = '00000000-0000-0000-000000000000'
      # Your Service Principal Password
      KEY = '00000000-0000-0000-000000000000'
      # Your Azure Subscription ID
      SUBSCRIPTION_ID = '00000000-0000-0000-000000000000'
      # Your Azure Media Service (AMS) Account Name
      ACCOUNT_NAME = 'amsv3account'
      # Your Resource Group Name
      RESOUCE_GROUP_NAME = 'AMSv3ResourceGroup'

      LOGIN_ENDPOINT = AZURE_PUBLIC_CLOUD.endpoints.active_directory
      RESOURCE = AZURE_PUBLIC_CLOUD.endpoints.active_directory_resource_id

      context = adal.AuthenticationContext(LOGIN_ENDPOINT + '/' + TENANT_ID)
      credentials = AdalAuthentication(
          context.acquire_token_with_client_credentials,
          RESOURCE,
          CLIENT,
          KEY
      )

      # The AMS Client
      # You can now use this object to perform different operations to your AMS account.
      client = AzureMediaServices(credentials, SUBSCRIPTION_ID)

      print("signed in")

      # Now that you are authenticated, you can manipulate the entities.
      # For example, list assets in your AMS account
      print (client.assets.list(RESOUCE_GROUP_NAME, ACCOUNT_NAME).get(0))
      ```

1. Köra filen

## <a name="next-steps"></a>Nästa steg

- Använd [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Granska Media Services [Python-referensdokumentationen](https://aka.ms/ams-v3-python-ref).
