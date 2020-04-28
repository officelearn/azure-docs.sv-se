---
title: 'REST API: konto hanterings åtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Använd Azure Data Lake Storage Gen1-och WebHDFS-REST API för att utföra konto hanterings åtgärder i Data Lake Storage Gen1-kontot
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "60877120"
---
# <a name="account-management-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Konto hanterings åtgärder på Azure Data Lake Storage Gen1 med REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig hur du utför konto hanterings åtgärder på Azure Data Lake Storage Gen1 med hjälp av REST API. Konto hanterings åtgärder omfattar att skapa ett Data Lake Storage Gen1 konto, ta bort ett Data Lake Storage Gen1-konto osv. Instruktioner för hur du utför fil Systems åtgärder på Data Lake Storage Gen1 att använda REST API finns i [fil Systems åtgärder på data Lake Storage gen1 med REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Krav
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[sväng](https://curl.haxx.se/)**. I den här artikeln används en sväng för att demonstrera hur du gör REST API anrop mot ett Data Lake Storage Gen1-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* För slutanvändarens autentisering för ditt program (interaktiv), se [slutanvändarens autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Information om tjänst-till-tjänst-autentisering för ditt program (icke-interaktiv) finns i [tjänst-till-tjänst-autentisering med data Lake Storage gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://docs.microsoft.com/rest/api/datalakestore/accounts/create).

Använd följande cURL-kommando. Ersätt ** \<yourstoragegen1name->** med ditt data Lake Storage gen1 namn.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare. Nyttolasten i begäran för det här kommandot finns i den **input.json**-fil som har angetts för parameter `-d` ovan. Innehållet i input.json-filen liknar följande kodavsnitt:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-storage-gen1-account"></a>Ta bort ett Data Lake Storage Gen1 konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://docs.microsoft.com/rest/api/datalakestore/accounts/delete).

Använd följande spiral kommando för att ta bort ett Data Lake Storage Gen1-konto. Ersätt ** \<yourstoragegen1name>** med ditt data Lake Storage gen1 konto namn.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstoragegen1name>?api-version=2015-10-01-preview

Du bör se utdata som liknar följande kodavsnitt:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Nästa steg
* [Fil Systems åtgärder på data Lake Storage gen1 att använda REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Se även
* [Azure Data Lake Storage Gen1 REST API referens](https://docs.microsoft.com/rest/api/datalakestore/)
* [Stor data program med öppen källkod som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

