---
title: Autentisering från tjänst till tjänst – Gen1 för datasjölagring – Java SDK
description: Lär dig hur du uppnår autentisering mellan tjänst och tjänst med Azure Data Lake Storage Gen1 med Azure Active Directory med Java
author: twooley
ms.service: data-lake-store
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: f355da7cd9c035b4ed0845bbd374a93bfb4a7350
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "73904541"
---
# <a name="service-to-service-authentication-with-azure-data-lake-storage-gen1-using-java"></a>Tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1 med Java

> [!div class="op_single_selector"]
> * [Använda Java](data-lake-store-service-to-service-authenticate-java.md)
> * [Använda .NET SDK](data-lake-store-service-to-service-authenticate-net-sdk.md)
> * [Använda Python](data-lake-store-service-to-service-authenticate-python.md)
> * [Använda REST-API](data-lake-store-service-to-service-authenticate-rest-api.md)
>
>  

I den här artikeln får du lära dig mer om hur du använder Java SDK för att göra tjänst-till-tjänst-autentisering med Azure Data Lake Storage Gen1. Slutanvändarens autentisering med Data Lake Storage Gen1 med Java SDK stöds inte.

## <a name="prerequisites"></a>Krav

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).

* **Skapa ett Azure Active Directory "Web"-program**. Du måste ha slutfört stegen i [Tjänst-till-tjänst-autentisering med Data Lake Storage Gen1 med Azure Active Directory](data-lake-store-service-to-service-authenticate-using-active-directory.md).

* [Maven](https://maven.apache.org/install.html). Den här självstudien använder Maven för bygg- och projektberoenden. Även om det är möjligt att bygga utan att använda ett byggsystem som Maven eller Gradle, gör dessa system det mycket enklare att hantera beroenden.

* (Valfritt) En IDE som [IntelliJ IDEA](https://www.jetbrains.com/idea/download/) eller [Eclipse](https://www.eclipse.org/downloads/) eller liknande.

## <a name="service-to-service-authentication"></a>Tjänst-till-tjänst-autentisering

1. Skapa ett Maven-projekt med [mvn archetype](https://maven.apache.org/guides/getting-started/maven-in-five-minutes.html) från kommandoraden eller med hjälp av en IDE. Anvisningar för hur du skapar ett Java-projekt med IntelliJ finns [här](https://www.jetbrains.com/help/idea/2016.1/creating-and-running-your-first-java-application.html). Anvisningar för hur du skapar ett Java-projekt med Eclipse finns [här](https://help.eclipse.org/mars/index.jsp?topic=%2Forg.eclipse.jdt.doc.user%2FgettingStarted%2Fqs-3.htm).

2. Lägg till följande beroenden till din Maven **pom.xml**-fil. Lägg till följande kodavsnitt före taggen ** \</project>:**

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

    Det första beroendet är att använda Data Lake`azure-data-lake-store-sdk`Storage Gen1 SDK ( ) från maven-databasen. Det andra beroendet är för att ange vilket loggningsramverk (`slf4j-nop`) som ska användas för programmet. Data Lake Storage Gen1 SDK använder [slf4j](https://www.slf4j.org/) loggning fasad, som låter dig välja mellan ett antal populära loggning ramverk, som log4j, Java loggning, inloggning, etc., eller ingen loggning. I det här exemplet inaktiverar vi loggning, därför använder vi **slf4j-nop** bindning. Om du vill använda andra alternativ för loggning i din app, se [här](https://www.slf4j.org/manual.html#projectDep).

3. Lägg till följande importuttryck i programmet.

        import com.microsoft.azure.datalake.store.ADLException;
        import com.microsoft.azure.datalake.store.ADLStoreClient;
        import com.microsoft.azure.datalake.store.DirectoryEntry;
        import com.microsoft.azure.datalake.store.IfExists;
        import com.microsoft.azure.datalake.store.oauth2.AccessTokenProvider;
        import com.microsoft.azure.datalake.store.oauth2.ClientCredsTokenProvider;

4. Använd följande kodavsnitt i Java-programmet för att hämta token för det Active Directory-webbprogram som du skapade tidigare med någon av underklasserna `AccessTokenProvider` till (i följande exempel används). `ClientCredsTokenProvider` Tokenleverantören cachelagrar autentiseringsuppgifter som används för att hämta token i minnet och förnyar automatiskt token när de håller på att gå ut. Det är möjligt att skapa egna `AccessTokenProvider` underklasser av så tokens erhålls av din kundkod. För nu, låt oss bara använda en som finns i SDK.

    Ersätt **FILL-IN-HERE** med de faktiska värdena för Azure Active Directory-webbappen.

        private static String clientId = "FILL-IN-HERE";
        private static String authTokenEndpoint = "FILL-IN-HERE";
        private static String clientKey = "FILL-IN-HERE";
    
        AccessTokenProvider provider = new ClientCredsTokenProvider(authTokenEndpoint, clientId, clientKey);   

Data Lake Storage Gen1 SDK innehåller praktiska metoder som gör att du kan hantera de säkerhetstoken som behövs för att prata med Data Lake Storage Gen1-kontot. Dock tvingar inte SDK:n dig att använda enbart de här metoderna. Du kan använda valfria andra metoder för att hämta token, som att använda [Azure Active Directory SDK:n](https://github.com/AzureAD/azure-activedirectory-library-for-java) eller din egna anpassade kod.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig hur du använder slutanvändarens autentisering för att autentisera med Data Lake Storage Gen1 med Java SDK. Du kan nu titta på följande artiklar som talar om hur du använder Java SDK för att arbeta med Data Lake Storage Gen1.

* [Dataåtgärder på DataSjölagring Gen1 med Java SDK](data-lake-store-get-started-java-sdk.md)
