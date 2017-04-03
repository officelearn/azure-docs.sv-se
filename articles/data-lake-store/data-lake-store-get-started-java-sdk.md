---
title: "Använda Java SDK för att utveckla program i Azure Data Lake Store | Microsoft Docs"
description: "Använda Azure Data Lake Store Java SDK för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder i Data Lake Store"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: d10e09db-5232-4e84-bb50-52efc2c21887
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/03/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 1e6ae31b3ef2d9baf578b199233e61936aa3528e
ms.openlocfilehash: 3e4302e7c5b54a46994eccaa8cf9ecbb0a84446d
ms.lasthandoff: 03/03/2017


---
# <a name="get-started-with-azure-data-lake-store-using-java"></a>Kom igång med Azure Data Lake Store med hjälp av Java
> [!div class="op_single_selector"]
> * [Portalen](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
> 

Läs hur man använder Azure Data Lake Store Java-SDK:n för att utföra grundläggande åtgärder som att skapa mappar, överföra och hämta datafiler, och så vidare. Mer information om Data Lake finns i [Azure Data Lake Store](data-lake-store-overview.md).

Du kommer åt Java SDK API-dokumentation för Azure Data Lake Store på [Azure Data Lake Store Java API-dokumentation](https://azure.github.io/azure-data-lake-store-java/javadoc/).

## <a name="prerequisites"></a>Krav
* Java Development Kit (JDK 7 eller senare, med Java version 1.7 eller senare)
* Azure Data Lake Store-konto. Följ instruktionerna i [Kom igång med Azure Data Lake Store med hjälp av Azure Portal](data-lake-store-get-started-portal.md).
* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att skapa utan att använda ett build-system som Maven eller Gradle, gör de här systemen det mycket enklare att hantera beroenden.
* (Valfritt) Och IDE-liknande [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) eller [Eclipse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="how-do-i-authenticate-using-azure-active-directory"></a>Hur autentiserar jag med Azure Active Directory?
I den här självstudien använder vi en Azure AD-programklienthemlighet för att hämta en Azure Active Directory-token (tjänst-till-tjänst-autentisering). Vi använder den här token för att skapa ett Data Lake Store-klientobjekt för att genomföra fil- och katalog-åtgärder. Instruktioner om hur man autentiserar sig med Azure Data Lake Store med klienthemligheten, får du i följande steg på hög nivå:

1. Skapa en Azure AD-webbapp
2. Hämta klient-ID:t, klienthemligheten och token- slutpunkten för Azure AD-webbappen.
3. Konfigurera åtkomst för Azure AD-webbappen på Data Lake Store-filen/-mappen som du vill ha åtkomst till från Java-programmet du skapar.

Instruktioner om hur du utför de här stegen finns i [Skapa ett Active Directory-program](data-lake-store-authenticate-using-active-directory.md).

Azure Active Directory erbjuder även andra alternativ för att hämta en token. Du kan välja från ett antal olika autentiseringsmekanismer för att passa ditt scenario, till exempel ett program som körs i en webbläsare, ett program som distribueras som ett skrivbordsprogram eller ett serverprogram som körs lokalt eller på en virtuell Azure-dator. Du kan också välja mellan olika typer av autentiseringsuppgifter som lösenord, certifikat, tvåfaktorautentisering och så vidare. Dessutom låter Azure Active Directory dig synkronisera dina lokala Active Directory-användare med molnet. För information, se [Autentiseringsscenarier för Azure Active Directory](../active-directory/active-directory-authentication-scenarios.md). 

## <a name="create-a-java-application"></a>Skapa ett Java-program
Kodavsnittet som finns tillgängligt [på GitHub](https://azure.microsoft.com/documentation/samples/data-lake-store-java-upload-download-get-started/) ger dig en genomgång av processen att skapa filer i arkivet, sammanfoga filer, hämta en fil och ta bort några filer i arkivet. Det här avsnittet av artikeln går igenom de viktigaste delarna av koden.

1. Skapa ett Maven-projekt med [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) från kommandoraden eller med hjälp av en IDE. Anvisningar för hur du skapar ett Java-projekt med IntelliJ finns [här](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Anvisningar för hur du skapar ett Java-projekt med Eclipse finns [här](http://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm). 
2. Lägg till följande beroenden till din Maven **pom.xml**-fil. Lägg till följande textavsnitt mellan **\</version>**-taggen och **\</project>**-taggen:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.1.4</version>
          </dependency>
          <dependency>
            <groupId>org.slf4j</groupId>
            <artifactId>slf4j-nop</artifactId>
            <version>1.7.21</version>
          </dependency>
        </dependencies>
   
    Det första beroendet är för att använda Data Lake Store-SDK:n (`azure-data-lake-store-sdk`) från maven-centrallagret. Det andra beroendet (`slf4j-nop`) är för att ange vilket loggningsramverk som ska användas för programmet. Data Lake Store SDK:n använder sig av loggningsfasaden [slf4j](http://www.slf4j.org/) som låter dig välja från en rad populära ramverk för loggning som log4j, Java-loggning, logback och så vidare, eller ingen loggning alls. I det här exemplet inaktiverar vi loggning, därför använder vi **slf4j-nop** bindning. Om du vill använda andra alternativ för loggning i din app, se [här](http://www.slf4j.org/manual.html#projectDep).

### <a name="add-the-application-code"></a>Lägg till programkoden
Koden har tre huvuddelar.

1. Hämta Azure Active Directory-token
2. Använd token för att skapa en Data Lake Store-klient.
3. Använda Data Lake Store-klienten för att utföra åtgärder.

#### <a name="step-1-obtain-an-azure-active-directory-token"></a>Steg 1: Hämta en Azure Active Directory-token.
Data Lake Store SDK:n erbjuder praktiska metoder för att hantera de säkerhetstokens som behövs för att kommunicera med Data Lake Store-kontot. Dock tvingar inte SDK:n dig att använda enbart de här metoderna. Du kan använda valfria andra metoder för att hämta token, som att använda [Azure Active Directory SDK:n](https://github.com/AzureAD/azure-activedirectory-library-for-java) eller din egna anpassade kod.

När du använder Data Lake Store SDK:n för att hämta token för den Active Directory-webbapp som du skapade tidigare, använder du någon av underklasserna i `AccessTokenProvider` (i exemplet nedan används `ClientCredsTokenProvider`). Tokenleverantören cachelagrar autentiseringsuppgifter som används för att hämta token i minnet och förnyar automatiskt token när de håller på att gå ut. Det går att skapa egna underklasser av `AccessTokenProvider` så att token hämtas av kundkoden, men nu ska vi använda en som tillhandahålls av SDK:n.

Ersätt **FILL-IN-HERE** med de faktiska värdena för Azure Active Directory-webbappen.

    private static String clientId = "FILL-IN-HERE";
    private static String authTokenEndpoint = "FILL-IN-HERE";
    private static String clientKey = "FILL-IN-HERE";

    AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);

#### <a name="step-2-create-an-azure-data-lake-store-client-adlstoreclient-object"></a>Steg 2: Skapa ett Azure Data Lake Store-klientobjekt (ADLStoreClient)
Skapa ett [ADLStoreClient](https://azure.github.io/azure-data-lake-store-java/javadoc/)-objekt genom att ange kontonamnet för Data Lake Store och den tokenleverantör som du genererade i föregående steg. Observera att Data Lake Store-kontonamnet måste vara ett fullständigt kvalificerat domännamn. Ersätt till exempel **FILL-IN-HERE** med något som **mydatalakestore.azuredatalakestore.net**.

    private static String accountFQDN = "FILL-IN-HERE";  // full account FQDN, not just the account name
    ADLStoreClient client = ADLStoreClient.createClient(accountFQDN, provider);

### <a name="step-3-use-the-adlstoreclient-to-perform-file-and-directory-operations"></a>Steg 3: Använd ADLStoreClient för att utföra fil- och katalogåtgärder
Koden nedan innehåller exempelavsnitt för några vanliga åtgärder. Du kan titta på den fullständiga [Data Lake Store Java SDK API-dokumentationen](https://azure.github.io/azure-data-lake-store-java/javadoc/) för **ADLStoreClient**-objektet för att visa andra åtgärder.

Observera att filer läses från och skrivs till med hjälp av standard Java-strömmar. Det innebär att du kan skikta någon av Java-strömmarna ovanpå Data Lake Store-strömmarna för att dra nytta av standard Java-funktioner (exempelvis utskriftsströmmar för formaterad utdata, eller någon av komprimerings- eller krypteringsströmmarna för ytterligare funktioner ovanpå).

     // create file and write some content
     String filename = "/a/b/c.txt";
     OutputStream stream = client.createFile(filename, IfExists.OVERWRITE  );
     PrintStream out = new PrintStream(stream);
     for (int i = 1; i <= 10; i++) {
         out.println("This is line #" + i);
         out.format("This is the same line (%d), but using formatted output. %n", i);
     }
     out.close();
    
    // set file permission
    client.setPermission(filename, "744");

    // append to file
    stream = client.getAppendStream(filename);
    stream.write(getSampleContent());
    stream.close();

    // Read File
    InputStream in = client.getReadStream(filename);
    byte[] b = new byte[64000];
    while (in.read(b) != -1) {
        System.out.write(b);
    }
    in.close();

    // concatenate the two files into one
    List<String> fileList = Arrays.asList("/a/b/c.txt", "/a/b/d.txt");
    client.concatenateFiles("/a/b/f.txt", fileList);

    //rename the file
    client.rename("/a/b/f.txt", "/a/b/g.txt");

    // list directory contents
    List<DirectoryEntry> list = client.enumerateDirectory("/a/b", 2000);
    System.out.println("Directory listing for directory /a/b:");
    for (DirectoryEntry entry : list) {
        printDirectoryInfo(entry);
    }

    // delete directory along with all the subdirectories and files in it
    client.deleteRecursive("/a");

#### <a name="step-4-build-and-run-the-application"></a>Steg 4: Skapa och kör programmet
1. För att köra inifrån en IDE, letar du upp och trycker på **Kör**-knappen. För att köra från Maven, använder du [exec:exec](http://www.mojohaus.org/exec-maven-plugin/exec-mojo.html).
2. För att skapa en fristående jar som du kan köra från kommandoraden, skapar du jaren med alla beroenden inkluderade, med hjälp av [Maven-plugin-paketet](http://maven.apache.org/plugins/maven-assembly-plugin/usage.html). Pom.xml-filen i [exempelkällkoden på GitHub](https://github.com/Azure-Samples/data-lake-store-java-upload-download-get-started/blob/master/pom.xml) innehåller ett exempel på hur man gör detta.

## <a name="next-steps"></a>Nästa steg
* [Utforska JavaDoc för Java SDK](https://azure.github.io/azure-data-lake-store-java/javadoc/)
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


