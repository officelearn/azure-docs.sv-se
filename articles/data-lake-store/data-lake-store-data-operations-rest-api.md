---
title: 'REST-API: Filsystemsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: 'Använd WebHDFS REST API: er för att utföra filsystemsåtgärder på Azure Data Lake Storage Gen1'
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
ms.openlocfilehash: 351c92f1e1a698893f61004d523ba79ebca253e8
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58877649"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-rest-api"></a>Filsystemsåtgärder på Azure Data Lake Storage Gen1 med hjälp av REST API
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

I den här artikeln får du lära dig hur du använder WebHDFS REST API: er och Data Lake Storage Gen1 REST API: er för att utföra filsystemsåtgärder på Azure Data Lake Storage Gen1. Anvisningar för hur du utför kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av REST API finns i [Kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-get-started-rest-api.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1 konto**. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md).

* **[cURL](https://curl.haxx.se/)**. Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett Data Lake Storage Gen1-konto.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
Du kan använda två sätt för att autentisera med Azure Active Directory.

* Information om slutanvändarautentisering för programmet (interaktivt) finns i [slutanvändarautentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-rest-api.md).
* Tjänst-till-tjänst-autentisering för programmet (interaktivt) finns i [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-rest-api.md).


## <a name="create-folders"></a>Skapa mappar
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Använd följande cURL-kommando. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS'

I det föregående kommandot ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare. Det här kommandot skapar en katalog med namnet **mytempdir** under rotmappen för ditt Data Lake Storage Gen1-konto.

Om åtgärden slutförs bör du se ett svar som följande fragment:

    {"boolean":true}

## <a name="list-folders"></a>Lista mappar
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Använd följande cURL-kommando. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS'

I det föregående kommandot ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtades tidigare.

Om åtgärden slutförs bör du se ett svar som följande fragment:

    {
    "FileStatuses": {
        "FileStatus": [{
            "length": 0,
            "pathSuffix": "mytempdir",
            "type": "DIRECTORY",
            "blockSize": 268435456,
            "accessTime": 1458324719512,
            "modificationTime": 1458324719512,
            "replication": 0,
            "permission": "777",
            "owner": "<GUID>",
            "group": "<GUID>"
        }]
    }
    }

## <a name="upload-data"></a>Ladda upp data
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Använd följande cURL-kommando. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X PUT -L -T 'C:\temp\list.txt' -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE'

I ovanstående syntax är parametern **-T** platsen för den fil som du överför.

De utdata som genereras liknar följande fragment:
   
    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/list.txt?op=CREATE&write=true
    ...
    Content-Length: 0

    HTTP/1.1 100 Continue

    HTTP/1.1 201 Created
    ...

## <a name="read-data"></a>Läsa data
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Läsa data från ett Data Lake Storage Gen1 är konto en tvåstegsprocess.

* Du skickar först en GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Det här anropet returnerar en plats dit du skickar nästa GET-begäran.
* Du skickar sedan GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Det här anropet visar innehållet i filen.

Eftersom det inte finns någon skillnad i indataparametrarna mellan det första och andra steget, kan du använda parameter `-L` för att skicka den första begäran. `-L`-alternativet kombinerar i stort sett två begärande till ett och gör att cURL gör om begäran på den nya platsen. Till sist visas utdata från alla begärananrop, som det visas i följande fragment. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN'

Du bör se utdata som liknar följande fragment:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...

    HTTP/1.1 200 OK
    ...

    Hello, Data Lake Store user!

## <a name="rename-a-file"></a>Byt namn på en fil
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Använd kommandot cURL för att byta namn på en fil. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt'

Du bör se utdata som liknar följande fragment:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="delete-a-file"></a>Ta bort en fil
Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Använd kommandot cURL för att ta bort en fil. Ersätt  **\<yourstorename >** med namnet på ditt Data Lake Storage Gen1.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" 'https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE'

Du bör se utdata som liknar följande:

    HTTP/1.1 200 OK
    ...

    {"boolean":true}

## <a name="next-steps"></a>Nästa steg
* [Kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av REST API](data-lake-store-get-started-rest-api.md).

## <a name="see-also"></a>Se också
* [Azure Data Lake Storage Gen1 REST API-referens](https://docs.microsoft.com/rest/api/datalakestore/)
* [Öppna källa Big Data-program som är kompatibla med Azure Data Lake Storage Gen1](data-lake-store-compatible-oss-other-applications.md)

