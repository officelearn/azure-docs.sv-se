---
title: 'REST API: Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 | Microsoft-dokument'
description: Använda Azure Data Lake Storage Gen1 och WebHDFS REST API för att utföra kontohanteringsåtgärder i datasjölagringsgenm1-kontot
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: 97fe33309f36cd7545f8c9d6c2d34671641caa1f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60877120"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig hur du utför kontohanteringsåtgärder på Azure Data Lake Storage Gen1 med REST API. Kontohanteringsåtgärder omfattar att skapa ett Data Lake Storage Gen1-konto, ta bort ett Data Lake Storage Gen1-konto, etc. Instruktioner om hur du utför filsystemåtgärder på Data Lake Storage Gen1 med REST API finns i [Filesystem-åtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Krav
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](https://curl.haxx.se/)**. Den här artikeln använder cURL för att visa hur du gör REST API-anrop mot ett Data Lake Storage Gen1-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* För slutanvändareautentisering för ditt program (interaktivt) finns i [Slutanvändarens autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* För tjänst-till-tjänst-autentisering för ditt program (icke-interaktiv), se [Autentisering från Tjänst till tjänst med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Använd följande cURL-kommando. Ersätt ** \<dittstoranamn1namn>** med namnet Data Lake Storage Gen1.

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

Använd följande cURL-kommando för att ta bort ett Data Lake Storage Gen1-konto. Ersätt ** \<dittstoranamn1namn>** med ditt Data Lake Storage Gen1-kontonamn.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Du bör se utdata som liknar följande kodavsnitt:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Nästa steg
* [Filsystemåtgärder på Data Lake Storage Gen1 med REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Se även
* [AZURE Data Lake Storage Gen1 REST API-referens](https://docs.microsoft.com/rest/api/datalakestore/)
* [Big Data-program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

