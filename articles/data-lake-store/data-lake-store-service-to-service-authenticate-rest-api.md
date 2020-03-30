---
title: Autentisering från tjänst - Gen1 för lagring av datasjölagring1 - REST API
description: Lär dig hur du uppnår autentisering från tjänst till tjänst med Azure Data Lake Storage Gen1 och Azure Active Directory med REST API.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 59d0bf20b16beda47d76e6a9940ac9fa4436da3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904519"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

I den här artikeln får du lära dig hur du använder REST API för att göra service-to-service-autentisering med Azure Data Lake Storage Gen1. För slutanvändareautentisering med Data Lake Storage Gen1 med REST API, se [Slutanvändarens autentisering med Data Lake Storage Gen1 med REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

I det här fallet tillhandahåller programmet sina egna autentiseringsuppgifter för att utföra åtgärderna. För detta måste du utfärda en POST-begäran som den som visas i följande utdrag:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Utdata från begäran innehåller en auktoriseringstoken (betecknad med `access-token` i utdata nedan) som du sedan skickar med dina REST API-anrop. Spara autentiseringstoken i en textfil. du behöver det när du gör REST-anrop till Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I den här artikeln används metoden **icke-interaktivt**. Mer information om icke-interaktivt (serviceartikel) finns i [Serviceartikelanrop med autentiseringsuppgifter](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med REST API. Du kan nu titta på följande artiklar som talar om hur du använder REST API för att arbeta med Data Lake Storage Gen1.

* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-get-started-rest-api.md)
* [Dataåtgärder på DataSjölagring Gen1 med REST API](data-lake-store-data-operations-rest-api.md)
