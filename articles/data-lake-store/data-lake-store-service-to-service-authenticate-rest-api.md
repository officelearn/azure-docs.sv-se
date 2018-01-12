---
title: "Tjänst-till-tjänst-autentisering: REST-API med Data Lake Store med Azure Active Directory | Microsoft Docs"
description: "Lär dig att uppnå service to service autentisering med Data Lake Store med Azure Active Directory med hjälp av REST API"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 80934d3e5ded5c01e473f8450a3484d84c46e94e
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="service-to-service-authentication-with-data-lake-store-using-rest-api"></a>Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av REST API
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
> 
> 

I den här artikeln får information du om hur du använder REST API för att göra service to service autentisering med Azure Data Lake Store. Slutanvändarens autentisering med Data Lake Store med hjälp av REST-API, se [slutanvändarens autentisering med Data Lake Store med hjälp av REST API](data-lake-store-end-user-authenticate-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett program för Azure Active Directory ”Web”**. Du måste ha slutfört stegen i [tjänst-till-tjänst-autentisering med Data Lake Store med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering
I det här scenariot ger programmet sina egna autentiseringsuppgifter om du vill utföra åtgärderna. För att göra detta måste du skicka en POST-begäran såsom visas i följande utdrag: 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Utdata för begäran innehåller en autentiseringstoken (betecknas med `access-token` i utdata nedan) att du sedan skickar med REST API-anrop. Spara autentiseringstoken i en textfil; Du behöver den när du gör REST-anrop till Data Lake Store.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I den här artikeln används metoden **icke-interaktivt**. Mer information om icke-interaktivt (serviceartikel) finns i [Serviceartikelanrop med autentiseringsuppgifter](https://msdn.microsoft.com/library/azure/dn645543.aspx). 

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för tjänst-till-tjänst för att autentisera med Azure Data Lake Store med hjälp av REST API. Du kan nu se följande artiklar som talar om hur du använder REST API för att arbeta med Azure Data Lake Store.

* [Kontohanteringsåtgärder på Data Lake Store med hjälp av REST API](data-lake-store-get-started-rest-api.md)
* [Dataåtgärder på Data Lake Store med hjälp av REST API](data-lake-store-data-operations-rest-api.md)

