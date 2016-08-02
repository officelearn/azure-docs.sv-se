<properties 
   pageTitle="Kom igång med Data Lake Store med hjälp av REST API| Microsoft Azure" 
   description="Använd WebHDFS REST API:er för att utföra åtgärder på Data Lake Store" 
   services="data-lake-store" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="04/29/2016"
   ms.author="nitinme"/>

# Kom igång med Azure Data Lake Store med hjälp av REST API:er

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

I den här artikeln får du lära dig hur du använder WebHDFS REST API:er och Data Lake Store REST API:er för kontohantering samt filsystemsåtgärder på Azure Data Lake Store. Azure Data Lake Store visar sina egna REST API:er för kontohanteringsåtgärder. Men eftersom Data Lake Store är kompatibelt med HDFS och Hadoop-ekosystemet, stöder det användning av WebHDFS REST API:er för filsystemsåtgärder.

>[AZURE.NOTE] Mer detaljerad information om REST API-stöd för Data Lake Store finns i [Azure Data Lake Store REST API-referens](https://msdn.microsoft.com/library/mt693424.aspx).

## Förutsättningar

- **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).
- **Skapa ett program i Azure Active Directory**. Det finns två sätt att autentisera med Azure Active Directory – **interaktivt** och **icke-interaktivt**. Det finns olika förutsättningar baserat på hur du vill autentisera.
    * **För interaktiv autentisering** (används i den här artikeln) – Du måste skapa ett **Native Client-program** i Azure Active Directory. När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter

    * **För icke-interaktiv autentisering** – Du måste skapa en **Webbapp** i Azure Active Directory . När du har skapat programmet, hämtar du följande värden som är relaterade till programmet.
        - Hämta **klient-ID**, **klienthemlighet** och **omdirigerings-URI** för programmet
        - Ange delegerade behörigheter
        - Tilldela Azure Active Directory-programmet till en roll. Rollen kan vara för den nivå av omfång för vilken du vill ge behörighet till Azure Active Directory-programmet. Du kan till exempel tilldela programmet på prenumerationsnivån eller på en resursgruppsnivå. Instruktioner finns i [Tilldela program till en roll](../resource-group-create-service-principal-portal.md#assign-application-to-role). 

    Se [Skapa Active Directory-program och tjänstens huvudnamn med hjälp av portalen](../resource-group-create-service-principal-portal.md) för anvisningar om hur du hämtar dessa värden, anger behörigheter och tilldelar roller.

- [cURL](http://curl.haxx.se/). Den här artikeln använder cURL för att demonstrera hur du gör REST API-anrop mot ett Data Lake Store-konto.

## Hur autentiserar jag med Azure Active Directory?

Du kan använda två sätt för att autentisera med Azure Active Directory.

### Interaktivt (användarautentisering)

I det här scenariot uppmanar programmet användaren att logga in och alla åtgärder utförs i kontexten för användaren. Utför följande steg för interaktiv autentisering.

1. Omdirigera användaren via ditt program till följande URL:

        https://login.microsoftonline.com/<TENANT-ID>/oauth2/authorize?client_id=<CLIENT-ID>&response_type=code&redirect_uri=<REDIRECT-URI>

    >[AZURE.NOTE] \<REDIRECT-URI> måste kodas för användning i en URL. Så, https://localhost, använd `https%3A%2F%2Flocalhost`)

    För självstudierna kan du ersätta platshållarvärdena i URL-adressen ovan och klistra in den i webbläsarens adressfält. Du omdirigeras för att autentisera med Azure-autentiseringsuppgifter. När du har loggat in visas svaret i webbläsarens adressfält. Svaret ska ha följande format:
        
        http://localhost/?code=<AUTHORIZATION-CODE>&session_state=<GUID>

2. Avbilda auktoriseringskoden från svaret. För de här självstudierna kan du kopiera auktoriseringskoden från adressfältet i webbläsaren och skicka den i POST-begäran till tokenslutpunkten, enligt nedan:

        curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token \
        -F redirect_uri=<REDIRECT-URI> \
        -F grant_type=authorization_code \
        -F resource=https://management.core.windows.net/ \
        -F client_id=<CLIENT-ID> \
        -F code=<AUTHORIZATION-CODE>

    >[AZURE.NOTE] I det här fallet behöver \<REDIRECT-URI> inte vara kodad.

3. Svaret är ett JSON-objekt som innehåller en åtkomsttoken (t.ex. `"access_token": "<ACCESS_TOKEN>"`) och en uppdateringstoken (t.ex. `"refresh_token": "<REFRESH_TOKEN>"`). Programmet använder åtkomsttoken vid åtkomst till Azure Data Lake Store och uppdateringstoken för att få en annan åtkomsttoken när en åtkomst-token upphör att gälla.

        {"token_type":"Bearer","scope":"user_impersonation","expires_in":"3599","expires_on":"1461865782","not_before": "1461861882","resource":"https://management.core.windows.net/","access_token":"<REDACTED>","refresh_token":"<REDACTED>","id_token":"<REDACTED>"}

4.  När åtkomsttoken upphört att gälla kan du begära en ny åtkomsttoken med hjälp av den uppdateringstoken som visas nedan:

         curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
            -F grant_type=refresh_token \
            -F resource=https://management.core.windows.net/ \
            -F client_id=<CLIENT-ID> \
            -F refresh_token=<REFRESH-TOKEN>
 
Mer information om interaktiv användarautentisering finns i [Flöde beviljat med auktoriseringskod](https://msdn.microsoft.com/library/azure/dn645542.aspx).

### Icke-interaktivt

I det här scenariot ger programmet sina egna autentiseringsuppgifter för att utföra åtgärder. För detta måste du skicka en POST-begäran såsom visas nedan. 

    curl -X POST https://login.microsoftonline.com/<TENANT-ID>/oauth2/token  \
      -F grant_type=client_credentials \
      -F resource=https://management.core.windows.net/ \
      -F client_id=<CLIENT-ID> \
      -F client_secret=<AUTH-KEY>

Utdata för begäran innehåller en autentiseringstoken (betecknas med `access-token` i utdata nedan) som du sedan skickar med REST API-anrop. Spara den här autentiseringstoken i en textfil; du behöver den senare i den här artikeln.

    {"token_type":"Bearer","expires_in":"3599","expires_on":"1458245447","not_before":"1458241547","resource":"https://management.core.windows.net/","access_token":"<REDACTED>"}

I den här artikeln används metoden **icke-interaktivt**. Mer information om icke-interaktivt (serviceartikel) finns i [Serviceartikelanrop med autentiseringsuppgifter](https://msdn.microsoft.com/library/azure/dn645543.aspx).

## Skapa ett Data Lake Store-konto

Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://msdn.microsoft.com/library/mt694078.aspx).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -H "Content-Type: application/json" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview -d@"C:\temp\input.json"

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtats tidigare. Nyttolasten i begäran för det här kommandot finns i den **input.json**-fil som har angetts för parameter `-d` ovan. Innehållet i input.json-filen liknar följande:

    {
    "location": "eastus2",
    "tags": {
        "department": "finance"
        },
    "properties": {}
    }   

## Skapa mappar i ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Make_a_Directory).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=MKDIRS

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtats tidigare. Det här kommandot skapar en katalog med namnet **mytempdir** under rotmappen för ditt Data Lake Store-konto.

Om åtgärden slutförs bör du se ett svar som detta:

    {"boolean":true}

## Lista mappar i ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#List_a_Directory).

Använd följande cURL-kommando. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS

I kommandot ovan ersätter du \<`REDACTED`\> med den autentiseringstoken som hämtats tidigare.

Om åtgärden slutförs bör du se ett svar som detta:

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
            "owner": "NotSupportYet",
            "group": "NotSupportYet"
        }]
    }
    }

## Ladda upp data till ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Create_and_Write_to_a_File).

Att överföra data med hjälp av WebHDFS REST API är en tvåstegsprocess som beskrivs nedan.

1. Skicka en HTTP PUT-begäran utan att skicka de fildata som ska laddas upp. I följande kommando ersätter du **\<yourstorename>** med ditt Data Lake Store-namn.

        curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/?op=CREATE

    Utdata för det här kommandot kommer att innehålla en tillfällig omdirigerings-URL som den som visas nedan.

        HTTP/1.1 100 Continue

        HTTP/1.1 307 Temporary Redirect
        ...
        ...
        Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=CREATE&write=true
        ...
        ...

2. Du måste nu skicka en annan HTTP PUT-begäran mot den URL som visas för egenskapen **Plats** i svaret. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

        curl -i -X PUT -T myinputfile.txt -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=CREATE&write=true

    Utdata blir något som liknar:

        HTTP/1.1 100 Continue

        HTTP/1.1 201 Created
        ...
        ...

## Läs data från ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Open_and_Read_a_File).

Att läsa data från ett Data Lake Store-konto är en tvåstegsprocess.

* Du skickar först en GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN`. Detta returnerar en plats dit du skickar nästa GET-begäran.
* Du skickar sedan GET-begäran mot slutpunkten `https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN&read=true`. Detta visar innehållet i filen.

Eftersom det inte finns någon skillnad i indataparametrarna mellan det första och andra steget, kan du använda parameter `-L` för att skicka den första begäran. `-L` Alternativet kombinerar i stort sett två begäranden till ett och gör att cURL gör om begäran på den nya platsen. Slutligen visas utdata från alla anrop för begäran såsom visas nedan. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -L GET -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=OPEN

Du bör se utdata som liknar följande:

    HTTP/1.1 307 Temporary Redirect
    ...
    Location: https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/somerandomfile.txt?op=OPEN&read=true
    ...
    
    HTTP/1.1 200 OK
    ...
    
    Hello, Data Lake Store user!

## Byt namn på en fil i ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Rename_a_FileDirectory).

Använd kommandot cURL för att byta namn på en fil. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X PUT -H "Authorization: Bearer <REDACTED>" -d "" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile.txt?op=RENAME&destination=/mytempdir/myinputfile1.txt

Du bör se utdata som liknar följande:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## Ta bort en fil från ett Data Lake Store-konto

Den här åtgärden är baserad på det WebHDFS REST API-anrop som definierats [här](http://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/WebHDFS.html#Delete_a_FileDirectory).

Använd kommandot cURL för att ta bort en fil. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://<yourstorename>.azuredatalakestore.net/webhdfs/v1/mytempdir/myinputfile1.txt?op=DELETE

Du bör se utdata som liknar följande:

    HTTP/1.1 200 OK
    ...
    
    {"boolean":true}

## Ta bort ett Data Lake Store-konto

Den här åtgärden är baserad på det REST API-anrop som definierats [här](https://msdn.microsoft.com/library/mt694075.aspx).

Använd kommandot cURL för att ta bort ett Data Lake Store-konto. Ersätt **\<yourstorename>** med ditt Data Lake Store-namn.

    curl -i -X DELETE -H "Authorization: Bearer <REDACTED>" https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.DataLakeStore/accounts/<yourstorename>?api-version=2015-10-01-preview

Du bör se utdata som liknar följande:

    HTTP/1.1 200 OK
    ...
    ...

## Se även

- [Stordataprogram med öppen källkod som är kompatibla med Azure Data Lake Store](data-lake-store-compatible-oss-other-applications.md)
 



<!--HONumber=Jun16_HO2-->


