---
title: 'Autentisering för slutanvändare: Java med Azure Data Lake Storage Gen1 med Azure Active Directory | Microsoft Docs'
description: Lär dig att uppnå slutanvändarautentisering med Azure Data Lake Storage Gen1 med Azure Active Directory med Java
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
ms.openlocfilehash: 8b558fca964f33d47d331e007329d1bae2626877
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "58881264"
---
# <a name="end-user-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Slutanvändarautentisering med Azure Data Lake Storage Gen1 med hjälp av Java
> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-end-user-authenticate-java-sdk.md)
> * [Använda .NET SDK](data-lake-store-end-user-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-end-user-authenticate-python.md)
> * [Använda REST-API](data-lake-store-end-user-authenticate-rest-api.md)
> 
>   

I den här artikeln lär du dig hur du använder Java SDK för att göra slutanvändarautentisering med Azure Data Lake Storage Gen1. Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Java SDK, se [tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med hjälp av Java](data-lake-store-service-to-service-authenticate-java.md).

## <a name="prerequisites"></a>Nödvändiga komponenter
* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory ”interna” program**. Du måste ha slutfört stegen i [slutanvändarautentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-end-user-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att skapa utan att använda ett build-system som Maven eller Gradle, gör de här systemen det mycket enklare att hantera beroenden.

* (Valfritt) Och IDE-liknande [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) eller [Eclipse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="end-user-authentication"></a>Slutanvändarautentisering
1. Skapa ett Maven-projekt med [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) från kommandoraden eller med hjälp av en IDE. Anvisningar för hur du skapar ett Java-projekt med IntelliJ finns [här](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Anvisningar för hur du skapar ett Java-projekt med Eclipse finns [här](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Lägg till följande beroenden till din Maven **pom.xml**-fil. Lägg till följande fragment före taggen **\</project>**:
   
        <dependencies>
          <dependency>
            <groupId>com.microsoft.azure</groupId>
            <artifactId>azure-data-lake-store-sdk</artifactId>
            <version>2.2.3</version>
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
        import com.microsoft.azure.datalake.store.oauth2.DeviceCodeTokenProvider;

4. Använd följande kodfragment i ditt Java-program för att hämta token för det interna Active Directory-program som du skapade tidigare med hjälp av den `DeviceCodeTokenProvider`. Ersätt **Fill-in-HERE** med de faktiska värdena för Azure Active Directory för internt program.

        private static String nativeAppId = "FILL-IN-HERE";
            
        AccessTokenProvider provider = new DeviceCodeTokenProvider(nativeAppId);   

Data Lake Storage Gen1 SDK erbjuder praktiska metoder för att hantera de säkerhetstokens som behövs för att kommunicera med Data Lake Storage Gen1-kontot. Dock tvingar inte SDK:n dig att använda enbart de här metoderna. Du kan använda valfria andra metoder för att hämta token, som att använda [Azure Active Directory SDK:n](https://github.com/AzureAD/azure-activedirectory-library-for-java) eller din egna anpassade kod.

## <a name="next-steps"></a>Nästa steg
I den här artikeln beskrivs hur du använder autentisering för slutanvändare för att autentisera med Azure Data Lake Storage Gen1 med hjälp av Java SDK. Du kan nu se ut i följande artiklar som pratar om hur du använder Java SDK för att arbeta med Azure Data Lake Storage Gen1.

* [Dataåtgärder på Data Lake Storage Gen1 med hjälp av Java SDK](data-lake-store-get-started-java-sdk.md)


