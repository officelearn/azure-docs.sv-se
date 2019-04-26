---
title: 'Tjänst-till-tjänst-autentisering: REST-API med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med hjälp av REST API
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: c48f7d7608b2b70f4ae41e2af5792cff72bb0dd2
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60195790"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-rest-api"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

I den här artikeln lär du dig hur du använder REST API för att göra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. Slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av REST API, se [slutanvändarautentisering med Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program för Azure Active Directory ”Web”**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering
I det här scenariot ger programmet sina egna autentiseringsuppgifter för att utföra åtgärder. För detta, måste du skicka en POST-begäran som den som visas i följande kodavsnitt: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Utdata för begäran innehåller en autentiseringstoken (betecknas med `access-token` i utdata nedan) att du sedan skickar med REST API-anrop. Spara autentiseringstoken i en textfil; Du behöver den när du gör REST-anrop till Data Lake Storage Gen1.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I den här artikeln används metoden **icke-interaktivt**. Mer information om icke-interaktivt (serviceartikel) finns i [Serviceartikelanrop med autentiseringsuppgifter](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder tjänst-till-tjänst-autentisering för att autentisera med Data Lake Storage Gen1 med hjälp av REST API. Du kan nu se ut i följande artiklar som pratar om hur du använder REST API för att arbeta med Data Lake Storage Gen1.

* [Kontohanteringsåtgärder i Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-get-started-rest-api.md)
* [Dataåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-data-operations-rest-api.md)

