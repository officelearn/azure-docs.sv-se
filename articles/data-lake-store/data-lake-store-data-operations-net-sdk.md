---
title: ".NET SDK: Filsystemsåtgärder på Azure Data Lake Store | Microsoft Docs"
description: "Använda Azure Data Lake Store .NET SDK till att utföra filsystemsåtgärder på Data Lake Store, som att skapa mappar osv."
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/30/2018
ms.author: nitinme
ms.openlocfilehash: 1094a5ea3c000707aa7736d22d4df0558da32b5e
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/27/2018
---
# <a name="filesystem-operations-on-azure-data-lake-store-using-net-sdk"></a>Filsystemsåtgärder på Azure Data Lake Store med hjälp av .NET SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

I den här artikeln får du lära dig att utföra filsystemsåtgärder i Data Lake Store med .NET SDK. Filsystemsåtgärder är bland annat att skapa mappar på ett Data Lake Store-konto, ladda upp filer, ladda ned filer osv.

Instruktioner för hur du utför kontohanteringsåtgärder på Data Lake Store med .NET SDK finns i [Kontohanteringsåtgärder på Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* **Visual Studio 2013, 2015 eller 2017**. Anvisningarna nedan använder Visual Studio 2017.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Store-konto**. Mer information om hur du skapar ett konto finns [Kom igång med Azure Data Lake Store](data-lake-store-get-started-portal.md)

## <a name="create-a-net-application"></a>Skapa ett .NET-program
Kodavsnittet som finns tillgängligt [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) ger dig en genomgång av processen att skapa filer i arkivet, sammanfoga filer, hämta en fil och ta bort några filer i arkivet. Det här avsnittet av artikeln går igenom de viktigaste delarna av koden.

1. Öppna Visual Studio och skapa ett konsolprogram.
2. Klicka på **Nytt** i **Arkiv**-menyn och klicka sedan på **Projekt**.
3. Från **Nytt projekt** anger eller väljer du följande värden:

   | Egenskap | Värde |
   | --- | --- |
   | Kategori |Mallar/Visual C#/Windows |
   | Mall |Konsolprogram |
   | Namn |CreateADLApplication |

4. Klicka på **OK** för att skapa projektet.

5. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   2. På fliken **NuGet Package Manager** ser du till att **Paketkälla** har angetts som **nuget.org** och att kryssrutan **Inkludera förhandsversion** är markerad.
   3. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.DataLake.Store` – I den här självstudien används v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication` – I den här självstudien används v2.3.1.
    
    Stäng **NuGet Package Manager**.

6. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

        using System;
        using System.IO;using System.Threading;
        using System.Linq;
        using System.Text;
        using System.Collections.Generic;
        using System.Security.Cryptography.X509Certificates; // Required only if you are using an Azure AD application created with certificates
                
        using Microsoft.Rest;
        using Microsoft.Rest.Azure.Authentication;
        using Microsoft.Azure.DataLake.Store;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;

7. Deklarera variablerna enligt nedan och ange värden för platshållarna. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn.

        namespace SdkSample
        {
            class Program
            {
                private static string _adlsAccountName = "<DATA-LAKE-STORE-NAME>.azuredatalakestore.net";        
            }
        }

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, ladda upp filer, osv.

## <a name="authentication"></a>Autentisering

* Information om slutanvändarautentisering för programmet finns i [Slutanvändarautentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* Information om tjänst-till-tjänst-autentisering för programmet finns i [Tjänst-till-tjänst-autentisering med Data Lake Store med hjälp av .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).


## <a name="create-client-object"></a>Skapa klientobjekt
Följande fragment skapar filsystemklientobjekten för Data Lake Store som används för att skicka begäranden till tjänsten.

    // Create client objects
    AdlsClient client = AdlsClient.CreateClient(_adlsAccountName, adlCreds);

## <a name="create-a-file-and-directory"></a>Skapa en fil och mapp
Lägg till följande kodfragment i ditt program. Det här kodfragmentet lägger till en fil och en överordnad katalog om det inte redan finns.

    // Create a file - automatically creates any parent directories that don't exist
    // The AdlsOuputStream preserves record boundaries - it does not break records while writing to the store
    using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);

        textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="append-to-a-file"></a>Lägg till till en fil
Följande kodfragment lägger till data till en befintlig fil i Data Lake Store-kontot.

    // Append to existing file
    using (var stream = client.GetAppendStream(fileName))
    {
        byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
        stream.Write(textByteArray, 0, textByteArray.Length);
    }

## <a name="read-a-file"></a>Läs en fil
Följande kodfragment läser innehåll från en fil i Data Lake Store.

    //Read file contents
    using (var readStream = new StreamReader(client.GetReadStream(fileName)))
    {
        string line;
        while ((line = readStream.ReadLine()) != null)
        {
            Console.WriteLine(line);
        }
    }

## <a name="get-file-properties"></a>Hämta filegenskaper
Följande kodavsnitt returnerar egenskaperna som är associerade med en fil eller katalog.

    // Get file properties
    var directoryEntry = client.GetDirectoryEntry(fileName);
    PrintDirectoryEntry(directoryEntry);

Definitionen av metoden `PrintDirectoryEntry` är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted). 

## <a name="rename-a-file"></a>Byt namn på en fil
Följande fragment byter namn på en befintlig fil i ett Data Lake Store-konto.

    // Rename a file
    string destFilePath = "/Test/testRenameDest3.txt";
    client.Rename(fileName, destFilePath, true);

## <a name="enumerate-a-directory"></a>Räkna upp en katalog
Följande kodfragment räknar upp kataloger i ett Data Lake Store-konto

    // Enumerate directory
    foreach (var entry in client.EnumerateDirectory("/Test"))
    {
        PrintDirectoryEntry(entry);
    }

Definitionen av metoden `PrintDirectoryEntry` är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Ta bort kataloger rekursivt
Följande kodfragment tar bort en katalog och alla dess underkataloger rekursivt.

    // Delete a directory and all it's subdirectories and files
    client.DeleteRecursive("/Test");

## <a name="samples"></a>Exempel
Här följer några exempel på hur du använder Data Lake Store Filesystem SDK.
* [Grundläggande exempel på Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Avancerade exempel på Github](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Se även
* [Kontohanteringsåtgärder på Azure Data Lake Store med hjälp av .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Data Lake Store .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
