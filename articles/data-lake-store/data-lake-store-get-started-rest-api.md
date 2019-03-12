---
title: 'REST-API: Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Använd Azure Data Lake Storage Gen1 och WebHDFS REST API för att utföra kontohanteringsåtgärder i Data Lake Storage Gen1-konto
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: b2c4d38462cf617d8417b269d47a8c1bb0c266c3
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/07/2019
ms.locfileid: "57530134"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med hjälp av REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig hur du utför kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med hjälp av REST-API. Kontohanteringsåtgärder omfattar att skapa ett Data Lake Storage Gen1-konto, ta bort ett Data Lake Storage Gen1 konto, osv. Anvisningar att utföra filsystemsåtgärder på Data Lake Storage Gen1 med hjälp av REST API finns i [filsystemsåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett Data Lake Storage Gen1-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* Information om slutanvändarautentisering för programmet (interaktivt) finns i [slutanvändarautentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Tjänst-till-tjänst-autentisering för programmet (interaktivt) finns i [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Använd följande cURL-kommando. Ersätt  **\<yourstoragegen1name >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare. Nyttolasten i begäran för det här kommandot finns i den **input.json**-fil som har angetts för parameter `-d` ovan. Innehållet i input.json-filen liknar följande kodavsnitt:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Använd följande cURL-kommando för att ta bort ett Data Lake Storage Gen1-konto. Ersätt  **\<yourstoragegen1name >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Du bör se utdata som liknar följande kodavsnitt:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Nästa steg
* [Filsystemsåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Se också
* [Azure Data Lake Storage Gen1 REST API-referens](https://docs.microsoft.com/rest/api/datalakestore/)
* [Öppna källa Big Data-program som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

