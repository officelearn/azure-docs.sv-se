---
title: "REST API: Kontohanteringsåtgärder i Azure Data Lake Store | Microsoft Docs"
description: "Använd Azure Data Lake Store och WebHDFS REST API för att utföra kontohanteringsåtgärder i Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57ac6501-cb71-4f75-82c2-acc07c562889
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/09/2018
ms.author: nitinme
ms.openlocfilehash: 5fafde870a01a6ceb5e86f7b00b0ca11b748c68a
ms.sourcegitcommit: 9292e15fc80cc9df3e62731bafdcb0bb98c256e1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2018
---
# <a name="account-management-operations-on-azure-data-lake-store-using-rest-api"></a>Kontohanteringsåtgärder i Azure Data Lake Store med hjälp av REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

I den här artikeln får du lära dig att utföra kontohanteringsåtgärder i Data Lake Store med REST API. Kontohanteringsåtgärder omfattar att skapa ett Data Lake Store-konto, ta bort ett Data Lake Store-konto osv. Anvisningar för att utföra filsystemsåtgärder i Data Lake Store med REST API finns i [Filsystemsåtgärder på Data Lake Store med REST API](data-lake-store-data-operations-rest-api.md).

## <a name="prerequisites"></a>Förutsättningar
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **[cURL](http://curl.haxx.se/)**. Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett Data Lake Store-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* Information om slutanvändarautentisering för programmet (interaktivt) finns i [Slutanvändarautentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Information om tjänst-till-tjänst-autentisering för programmet (ej interaktivt) finns i [Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-a-data-lake-store-account"></a>Skapa ett Data Lake Store-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://msdn.microsoft.com/library/mt694078.aspx).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare. Nyttolasten i begäran för det här kommandot finns i den **input.json**-fil som har angetts för parameter `-d` ovan. Innehållet i input.json-filen liknar följande kodavsnitt:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }    

## <a name="delete-a-data-lake-store-account"></a>Ta bort ett Data Lake Store-konto
Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://msdn.microsoft.com/library/mt694075.aspx).

Använd kommandot cURL för att ta bort ett Data Lake Store-konto. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Du bör se utdata som liknar följande kodavsnitt:

    HTTP/1.1 200 OK
    ...
    ...

## <a name="next-steps"></a>Nästa steg
* [Filsystemsåtgärder på Data Lake Store med hjälp av REST API](data-lake-store-data-operations-rest-api.md).

## <a name="see-also"></a>Se även
* [Azure Data Lake Store REST API-referens](https://docs.microsoft.com/rest/api/datalakestore/)
* [Stordataprogram med öppen källkod som är kompatibla med Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)

