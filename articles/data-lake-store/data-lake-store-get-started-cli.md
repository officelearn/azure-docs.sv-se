<properties
   pageTitle="Kom igång med Data Lake Store med hjälp av kommandoradsgränssnitt över flera plattformar  | Microsoft Azure"
   description="Använd Azure-kommandoraden över flera plattformar för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder"
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
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Kom igång med Azure Data Lake Store med Azure Command Line

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lär dig mer om att använda Azure-kommandoradsgränssnitt för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta datafiler, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Data Lake Store](data-lake-store-overview.md).

Azure CLI är implementerat i Node.js. Det kan användas på alla plattformar som har stöd för Node.js, inklusive Windows, Mac- och Linux. Azure CLI är öppen källkod. Källkoden hanteras i GitHub på <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Den här artikeln beskriver bara hur man använder Azure CLI med hjälp av Data Lake Store. En allmän vägledning om hur du använder Azure CLI finns i [Så här använder du Azure CLI] [azure-command-line-tools].


##Förutsättningar

Innan du påbörjar den här artikeln måste du ha:

- **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).
- **Azure CLI** - Se [Installera och konfigurera Azure CLI](../xplat-cli-install.md) för information om installation och konfiguration. Se till att datorn startas om när du har installerat CLI.

##Logga in till din Azure-prenumeration

Följ stegen i [Anslut till en Azure-prenumeration från Azure-kommandoradsgränssnittet (Azure CLI)](../xplat-cli-connect.md) och anslut till din prenumeration med hjälp av metoden __inloggning__.


## Skapa ett Azure Data Lake Store-konto

Öppna en kommandotolk, gränssnitt, eller en terminalsession och kör följande kommandon.

1. Logga in till din Azure-prenumeration:

        azure login

    Du uppmanas att öppna en webbsida och ange en autentiseringskod. Följ anvisningarna på sidan för att logga in på Azure-prenumerationen.

2. Växla till läget Azure Resource Manager, med följande kommando:

        azure config mode arm


3. Visa en lista med Azure-prenumerationer för ditt konto.

        azure account list


4. Om du har flera Azure-prenumerationer, använder du följande kommando för att ange den prenumeration som Azure CLI-kommandona ska använda:

        azure account set <subscriptionname>

5. Skapa en ny resursgrupp. I följande kommando, anger du de parametervärden som du vill använda.

        azure group create <resourceGroup> <location>

    Om platsnamnet innehåller blanksteg måste du placera det inom citattecken. Till exempel "USA, östra 2".

5. Skapa Data Lake Store-kontot.

        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## Skapa mappar i din Data Lake Store

Du kan skapa mappar under Azure Data Lake Store-kontot för att hantera och lagra data. Använd följande kommando för att skapa en mapp med namnet "mynewfolder" i roten på Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Exempel:

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## Ladda upp data till din Data Lake Store

Du kan ladda upp data till Data Lake Store direkt på rotnivå eller till en mapp som du har skapat i kontot. Fragmenten nedan visar hur du laddar upp exempeldata till mappen (**mynewfolder**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den på en lokal katalog på din dator, till exempel C:\sampledata\.

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Exempel:

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## Lista över filer i Data Lake Store

Använd följande kommando för att visa filer i ett Data Lake Store-konto.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Exempel:

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

Resultatet av detta ska se ut ungefär så här:

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## Byt namn, hämta och ta bort data från din Data Lake Store

* **Byt namn på en fil** med hjälp av följande kommando:

        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>

    Exempel:

        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv

* **Hämta en fil** med hjälp av följande kommando. Kontrollera att den målsökväg som du anger redan finns.

        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>

    Exempel:

        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"

* **Ta bort en fil** med hjälp av följande kommando:

        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>

    Exempel:

        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv

    När du uppmanas, trycker du på **Y** för att ta bort objektet.

## Visa åtkomstkontrollistan för en mapp i Data Lake Store

Använd följande kommando om du vill visa ACL:er i en Data Lake Store-mapp. I den aktuella versionen kan ACL:er endast anges i roten på Data Lake Store. Sökvägsparametern kommer därför attvara rot (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Exempel:

    azure datalake store permissions show mynewdatalakestore /


## Ta bort ditt Data Lake Store-konto

Använd följande kommando för att ta bort ett Data Lake Store-konto.

    azure datalake store account delete <dataLakeStoreAccountName>

Exempel:

    azure datalake store account delete mynewdatalakestore

När du uppmanas, anger du **Y** för att ta bort kontot.


## Nästa steg

- [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
- [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Jun16_HO2-->


