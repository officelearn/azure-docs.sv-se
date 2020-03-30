---
title: '.NET SDK: Filsystemåtgärder på Azure Data Lake Storage Gen1'
description: Använd Azure Data Lake Storage Gen1 .NET SDK för filsystemåtgärder på Data Lake Storage Gen1, till exempel skapa mappar osv.
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 01/03/2020
ms.author: twooley
ms.openlocfilehash: 7e33ecbbb49fc2b0683d0757da36deec72796806
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75638909"
---
# <a name="filesystem-operations-on-data-lake-storage-gen1-using-the-net-sdk"></a>Filsystemåtgärder på Data Lake Storage Gen1 med hjälp av .NET SDK

> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
>

I den här artikeln får du lära dig hur du utför filsystemåtgärder på Data Lake Storage Gen1 med .NET SDK. Filsystem åtgärder omfattar att skapa mappar i en Data Lake Storage Gen1 konto, ladda upp filer, ladda ner filer, etc.

Instruktioner om hur du utför kontohanteringsåtgärder på Data Lake Storage Gen1 med hjälp av .NET SDK finns i [Kontohanteringsåtgärder på DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md).

## <a name="prerequisites"></a>Krav

* **Visual Studio 2013 eller högre**. Instruktionerna i den här artikeln använder Visual Studio 2019.

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Azure Data Lake Storage Gen1-konto**. Instruktioner om hur du skapar ett konto finns i [Komma igång med Azure Data Lake Storage Gen1](data-lake-store-get-started-portal.md).

## <a name="create-a-net-application"></a>Skapa ett .NET-program

Kodavsnittet som finns tillgängligt [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted) ger dig en genomgång av processen att skapa filer i arkivet, sammanfoga filer, hämta en fil och ta bort några filer i arkivet. Det här avsnittet av artikeln går igenom de viktigaste delarna av koden.

1. Välj Arkiv-menyn, **File** **Ny**och sedan **Project**.
1. Välj **Console App (.NET Framework)** och välj sedan **Nästa**.
1. I **Projektnamn** `CreateADLApplication`anger du och väljer sedan **Skapa**.
1. Lägg till NuGet-paketen i projektet.

   1. Högerklicka på projektnamnet i Solution Explorer och klicka på **Hantera NuGet-paket**.
   1. Kontrollera att **paketkällan** är inställd **på nuget.org**på fliken **NuGet Package Manager** . Kontrollera också att kryssrutan **Inkludera förhandsversion** är markerad.
   1. Sök efter och installera följande NuGet-paket:

      * `Microsoft.Azure.DataLake.Store`- Den här artikeln använder v1.0.0.
      * `Microsoft.Rest.ClientRuntime.Azure.Authentication`- Denna artikel använder v2.3.1.

      Stäng **NuGet Package Manager**.

1. Öppna **Program.cs**, ta bort den befintliga koden och lägg sedan till följande instruktioner för att lägga till referenser till namnområden.

    ```
    using System;
    using System.IO;using System.Threading;
    using System.Linq;
    using System.Text;
    using System.Collections.Generic;
    using System.Security.Cryptography.X509Certificates; // Required only if you're using an Azure AD application created with certificates
                
    using Microsoft.Rest;
    using Microsoft.Rest.Azure.Authentication;
    using Microsoft.Azure.DataLake.Store;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```

1. Deklarera variablerna enligt nedan och ange värden för platshållarna. Kontrollera också att den lokala sökvägen och filnamnet som du anger här finns på datorn.

    ```
    namespace SdkSample
    {
        class Program
        {
            private static string _adlsg1AccountName = "<DATA-LAKE-STORAGE-GEN1-NAME>.azuredatalakestore.net";
        }
    }
    ```

I de återstående avsnitten i artikeln kan du se hur du använder tillgängliga .NET-metoder för att utföra åtgärder som autentisering, filöverföring, etc.

## <a name="authentication"></a>Autentisering

* För slutanvändareautentisering för ditt program, se [Slutanvändarens autentisering med Data Lake Storage Gen1 med .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md).
* För autentisering från tjänst för ditt program finns i Autentisering från [Tjänst till tjänst med Data Lake Storage Gen1 med .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md).

## <a name="create-client-object"></a>Skapa klientobjekt

Följande kodavsnitt skapar klientobjektet Data Lake Storage Gen1, som används för att utfärda begäranden till tjänsten.

```
// Create client objects
AdlsClient client = AdlsClient.CreateClient(_adlsg1AccountName, adlCreds);
```

## <a name="create-a-file-and-directory"></a>Skapa en fil och mapp

Lägg till följande kodfragment i ditt program. Det här kodavsnittet lägger till en fil och en överordnad katalog som inte finns.

```
// Create a file - automatically creates any parent directories that don't exist
// The AdlsOutputStream preserves record boundaries - it does not break records while writing to the store

using (var stream = client.CreateFile(fileName, IfExists.Overwrite))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is test data to write.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);

    textByteArray = Encoding.UTF8.GetBytes("This is the second line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="append-to-a-file"></a>Lägg till till en fil

Följande kodavsnitt lägger till data till en befintlig fil i Data Lake Storage Gen1-kontot.

```
// Append to existing file

using (var stream = client.GetAppendStream(fileName))
{
    byte[] textByteArray = Encoding.UTF8.GetBytes("This is the added line.\r\n");
    stream.Write(textByteArray, 0, textByteArray.Length);
}
```

## <a name="read-a-file"></a>Läs en fil

Följande kodavsnitt läser innehållet i en fil i Data Lake Storage Gen1.

```
//Read file contents

using (var readStream = new StreamReader(client.GetReadStream(fileName)))
{
    string line;
    while ((line = readStream.ReadLine()) != null)
    {
        Console.WriteLine(line);
    }
}
```

## <a name="get-file-properties"></a>Hämta filegenskaper

Följande kodavsnitt returnerar egenskaperna som är associerade med en fil eller katalog.

```
// Get file properties
var directoryEntry = client.GetDirectoryEntry(fileName);
PrintDirectoryEntry(directoryEntry);
```

Definitionen av `PrintDirectoryEntry` metoden är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="rename-a-file"></a>Byt namn på en fil

Följande kodavsnitt byter namn på en befintlig fil i ett Data Lake Storage Gen1-konto.

```
// Rename a file
string destFilePath = "/Test/testRenameDest3.txt";
client.Rename(fileName, destFilePath, true);
```

## <a name="enumerate-a-directory"></a>Räkna upp en katalog

Följande kodavsnitt räknar upp kataloger i ett Data Lake Storage Gen1-konto.

```
// Enumerate directory
foreach (var entry in client.EnumerateDirectory("/Test"))
{
    PrintDirectoryEntry(entry);
}
```

Definitionen av `PrintDirectoryEntry` metoden är tillgänglig som en del av exemplet [på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted).

## <a name="delete-directories-recursively"></a>Ta bort kataloger rekursivt

Följande kodavsnitt tar bort en katalog och alla dess underkataloger, rekursivt.

```
// Delete a directory and all its subdirectories and files
client.DeleteRecursive("/Test");
```

## <a name="samples"></a>Exempel

Här är några exempel som visar hur du använder Data Lake Storage Gen1 Filesystem SDK.

* [Grundläggande exempel på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-get-started/tree/master/AdlsSDKGettingStarted)
* [Avancerat exempel på GitHub](https://github.com/Azure-Samples/data-lake-store-adls-dot-net-samples)

## <a name="see-also"></a>Se även

* [Kontohanteringsåtgärder på DataSjölagring Gen1 med .NET SDK](data-lake-store-get-started-net-sdk.md)
* [Datasjölagring Gen1 .NET SDK-referens](https://docs.microsoft.com/dotnet/api/overview/azure/data-lake-store?view=azure-dotnet)

## <a name="next-steps"></a>Nästa steg

* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
