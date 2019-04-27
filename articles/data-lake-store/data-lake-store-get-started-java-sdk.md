---
title: 'Java SDK: Filsystemsåtgärder på Azure Data Lake Storage Gen1 | Microsoft Docs'
description: Använd Azure Data Lake Storage Gen1 Java SDK till att utföra filsystemsåtgärder på Data Lake Storage Gen1 till exempel skapa mappar osv.
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bc6e0718cdc4ccb18480dc760279da9c177db4cb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60877472"
---
# <a name="filesystem-operations-on-azure-data-lake-storage-gen1-using-java-sdk"></a>Filsystemsåtgärder på Azure Data Lake Storage Gen1 med hjälp av Java SDK
> [!div class="op_single_selector"]
> * [.NET SDK](data-lake-store-data-operations-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-data-operations-rest-api.md)
> * [Python](data-lake-store-data-operations-python.md)
>
> 

Lär dig hur du använder Azure Data Lake Storage Gen1 Java SDK för att utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta datafiler osv. Läs mer om Data Lake Storage Gen1 [Azure Data Lake Storage Gen1](data-lake-store-overview.md).

Du kan komma åt Java SDK API-dokumentation för Data Lake Storage Gen1 på [Azure Data Lake Storage Gen1 Java API-dokumentation](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Nödvändiga komponenter
* Java Development Kit (JDK 7 eller senare, med Java version 1.7 eller senare)
* Data Lake Storage Gen1-konto. Följ anvisningarna på [Kom igång med Azure Data Lake Storage Gen1 med Azure portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att skapa utan att använda ett build-system som Maven eller Gradle, gör de här systemen det mycket enklare att hantera beroenden.
* (Valfritt) Och IDE-liknande [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) eller [Eclipse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="create-a-java-application"></a>Skapa ett Java-program
Kodavsnittet som finns tillgängligt [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) ger dig en genomgång av processen att skapa filer i arkivet, sammanfoga filer, hämta en fil och ta bort några filer i arkivet. Det här avsnittet av artikeln går igenom de viktigaste delarna av koden.

1. Skapa ett Maven-projekt med [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) från kommandoraden eller med hjälp av en IDE. Anvisningar för hur du skapar ett Java-projekt med IntelliJ finns [här](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Anvisningar för hur du skapar ett Java-projekt med Eclipse finns [här](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 

2. Lägg till följande beroenden till din Maven **pom.xml**-fil. Lägg till följande fragment före taggen **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.5</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Det första beroendet är att använda Data Lake Storage Gen1 SDK (`azure-data-lake-store-sdk`) från maven-centrallagret. Det andra beroendet är för att ange vilket loggningsramverk (`slf4j-nop`) som ska användas för programmet. Data Lake Storage Gen1 SDK använder [slf4j](https://www.slf4j.org/) loggningsfasaden som låter dig välja från ett antal populära loggningsramverk som log4j, Java-loggning, logback och så vidare, eller ingen loggning. I det här exemplet inaktiverar vi loggning, därför använder vi **slf4j-nop** bindning. Om du vill använda andra alternativ för loggning i din app, se [här](https://www.slf4j.org/manual.html#projectDep).

3. Lägg till följande importuttryck i programmet.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

        import java.io.*;
        import java.util.Arrays;
        import java.util.List;

## <a name="authentication"></a>Autentisering

* Information om slutanvändarautentisering för programmet, se [klient-slutanvändaren-autentisering med Data Lake Storage Gen1 med Java](data-lake-store-end-user-authenticate-java-sdk.md).
* Tjänst-till-tjänst-autentisering för programmet, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="create-a-data-lake-storage-gen1-client"></a>Skapa en Data Lake Storage Gen1-klient
Skapa en [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/) objekt måste du ange kontonamnet för Data Lake Storage Gen1 och den tokenleverantör du genererade när du autentiserade med Data Lake Storage Gen1 (se [autentisering](#authentication) avsnittet). Data Lake Storage Gen1 kontonamnet måste vara ett fullständigt kvalificerat domännamn. Ersätt till exempel **Fill-in-HERE** med något som liknar **mydatalakestoragegen1.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

Fragmenten i följande avsnitt innehåller exempel på vissa vanliga filsystemsåtgärder. Du kan titta på fullständiga [Data Lake Storage Gen1 Java SDK API-dokumentation](https://azure.github.io/azure-data-lake-store-java/javadoc/) av den **ADLStoreClient** objekt att visa andra åtgärder.

## <a name="create-a-directory"></a>Skapa en katalog

Följande fragment skapar en katalogstruktur i roten för Data Lake Storage Gen1-kontot som du angav.

    // create directory
    client.createDirectory("/a/b/w");
    System.out.println("Directory created.");

## <a name="create-a-file"></a>Skapa en fil

Följande fragment skapar en fil (c.txt) i katalogstrukturen och skriver data till filen.

    // create file and write some content
    String filename = "/a/b/c.txt";
    OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
    PrintStream out = new PrintStream(stream);
    for (int i = 1; i <= 10; i++) {
        out.println("This is line #" + i);
        out.format("This is the same line (%d), but using formatted output. %n", i);
    }
    out.close();
    System.out.println("File created.");

Du kan också skapa en fil (d.txt) med bytematriser.

    // create file using byte arrays
    stream = client.createFile("/a/b/d.txt", IfExists.OVERWRITE);
    byte[] buf = getSampleContent();
    stream.write(buf);
    stream.close();
    System.out.println("File created using byte array.");

Definitionen för funktionen `getSampleContent` som används i föregående fragment finns som en del av exemplet [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/). 

## <a name="append-to-a-file"></a>Lägg till till en fil

Följande fragment lägger till innehåll i en befintlig fil.

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();
    System.out.println("File appended.");

Definitionen för funktionen `getSampleContent` som används i föregående fragment finns som en del av exemplet [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="read-a-file"></a>Läs en fil

Följande kodfragment läser innehåll från en fil i ett Data Lake Storage Gen1-konto.

    // Read File
    InputStream in = client.getReadStream(filename);
    BufferedReader reader = new BufferedReader(new InputStreamReader(in));
    String line;
    while ( (line = reader.readLine()) != null) {
        System.out.println(line);
    }
    reader.close();
    System.out.println();
    System.out.println("File contents read.");

## <a name="concatenate-files"></a>Sammanfoga filer

Följande fragment sammanfogar två filer i ett Data Lake Storage Gen1-konto. Om det lyckas ersätter den sammanfogade filen de två befintliga filerna.

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);
    System.out.println("Two files concatenated into a new file.");

## <a name="rename-a-file"></a>Byt namn på en fil

Följande fragment byter namn på en fil i ett Data Lake Storage Gen1-konto.

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");
    System.out.println("New file renamed.");

## <a name="get-metadata-for-a-file"></a>Hämta metadata för en fil

Följande fragment hämtar metadata för en fil i ett Data Lake Storage Gen1-konto.

    // get file metadata
    DirectoryEntry ent = client.getDirectoryEntry(filename);
    printDirectoryInfo(ent);
    System.out.println("File metadata retrieved.");

## <a name="set-permissions-on-a-file"></a>Ange behörigheter för en fil

Följande fragment anger behörigheter för filen du har skapat i föregående avsnitt.

    // set file permission
    client.setPermission(filename, "744");
    System.out.println("File permission set.");

## <a name="list-directory-contents"></a>Lista kataloginnehåll

Följande fragment visar innehållet i en katalog, rekursivt.

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }
    System.out.println("Directory contents listed.");

Definitionen för funktionen `printDirectoryInfo` som används i föregående fragment finns som en del av exemplet [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/).

## <a name="delete-files-and-folders"></a>Ta bort filer och mappar

Följande kodavsnitt tar bort de angivna filerna och mapparna på ett Data Lake Storage Gen1-konto, rekursivt.

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");
    System.out.println("All files and folders deleted recursively");
    promptEnterKey();

## <a name="build-and-run-the-application"></a>Skapa och kör appen
1. För att köra inifrån en IDE, letar du upp och trycker på **Kör**-knappen. För att köra från Maven, använder du [exec:exec](https://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. För att skapa en fristående jar som du kan köra från kommandoraden, skapar du jaren med alla beroenden inkluderade, med hjälp av [Maven-plugin-paketet](https://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.XML-filen i den [exempelkällkoden på GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) innehåller ett exempel.

## <a name="next-steps"></a>Nästa steg
* [Utforska JavaDoc för Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)


