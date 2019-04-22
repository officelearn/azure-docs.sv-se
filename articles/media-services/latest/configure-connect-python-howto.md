---
title: Ansluta till Azure Media Services v3-API – Python
description: Lär dig hur du ansluter till Media Services v3-API med Python.
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
ms.openlocfilehash: 971e36b600a2c6be516e39ce84ca5780a2f23bbd
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2019
ms.locfileid: "59700219"
---
# <a name="connect-to-media-services-v3-api---python"></a>Ansluta till Media Services v3-API – Python

Den här artikeln visar hur du ansluter till Azure Media Services v3 Python SDK med det tjänstens huvudnamn i metoden.

## <a name="prerequisites"></a>Nödvändiga komponenter

- Hämta Python [python.org](https://www.python.org/downloads/)
- Se till att ange den `PATH` miljövariabeln
- [Skapa ett Media Services-konto](create-account-cli-how-to.md). Var noga med att komma ihåg resursgruppens namn och namnet på Media Services-konto.
- Följ stegen i den [åtkomst API: er](access-api-cli-how-to.md) avsnittet. Anteckna prenumerations-ID, program-ID (klient-ID), autentiseringsnyckeln (hemligt) och klient-ID som du behöver i senare steg.

## <a name="install-the-modules"></a>Installera modulerna

Om du vill arbeta med Azure Media Services med hjälp av Python som du behöver installera dessa moduler.

* Den `azure-mgmt-resource` modulen, som innehåller Azure-moduler för Active Directory.
* Den `azure-mgmt-media` modulen, som innehåller entiteter för Media Services.

Öppna Kommandotolken och använda följande kommandon för att installera modulerna.

```
pip3 install azure-mgmt-resource
pip3 install azure-mgmt-media==1.1.1
```

## <a name="connect-to-the-python-client"></a>Ansluta till Python-klienten

1. Skapa en fil med en `.py` tillägg
1. Öppna filen i din favoritredigerare
1. Lägg till koden nedan i filen. Koden importerar modulerna som krävs och skapar Active Directory-autentiseringsuppgifter objektet måste du ansluta till Media Services.

      Ange de variabler värden till de värden som du fick från [API: er för åtkomst](access-api-cli-how-to.md)

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

1. Kör filen

## <a name="next-steps"></a>Nästa steg

- Använd [Python SDK](https://aka.ms/ams-v3-python-sdk).
- Granska Media Services [Python-referensdokumentationen](https://aka.ms/ams-v3-python-ref).
