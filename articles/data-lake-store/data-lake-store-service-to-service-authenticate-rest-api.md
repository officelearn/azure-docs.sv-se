---
title: REST-tjänst-till-tjänst-autentisering – Data Lake Storage Gen1 – Azure
description: Lär dig hur du uppnår tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 och Azure Active Directory att använda REST API.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d7d0ec39e9f0f294324eb18337f4234ddaa63e2c
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/01/2020
ms.locfileid: "82688146"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 som använder REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API:et](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

I den här artikeln får du lära dig hur du använder REST API för att utföra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. För slutanvändarens autentisering med Data Lake Storage Gen1 med hjälp av REST API, se autentisering av slutanvändare [med data Lake Storage gen1 med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

I det här scenariot tillhandahåller programmet sina egna autentiseringsuppgifter för att utföra åtgärderna. För detta måste du skicka en POST-begäran som den som visas i följande kodfragment:

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Utdata för begäran innehåller en autentiseringstoken (anges `access-token` i utdata nedan) som du sedan skickar med dina REST API-anrop. Spara autentiseringstoken i en textfil. du behöver den när du gör REST-anrop till Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I den här artikeln används metoden **icke-interaktivt**. Mer information om icke-interaktivt (serviceartikel) finns i [Serviceartikelanrop med autentiseringsuppgifter](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med hjälp av REST API. Nu kan du titta på följande artiklar som talar om hur du använder REST API för att arbeta med Data Lake Storage Gen1.

* [Konto hanterings åtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-get-started-rest-api.md)
* [Data åtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-data-operations-rest-api.md)
