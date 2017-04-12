---
title: "Använder PowerShell för att komma igång med Azure Data Lake Store | Microsoft Docs"
description: "Använd Azure PowerShell för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: c00aa4a2e79522a6817a135965f6c218b08e26f8
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Kom igång med Azure Data Lake Store med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Lär dig mer om att använda Azure PowerShell för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="authentication"></a>Autentisering
Den här artikeln använder en enklare metod för autentisering med Data Lake Store där du uppmanas att ange autentiseringsuppgifterna för ditt Azure-konto. Åtkomstnivå till Data Lake Store-kontot och filsystemet styrs av åtkomstnivån för den inloggade användaren. Det finns dock olika sätt att autentisera med Data Lake Store: **slutanvändarens autentisering** eller **serviceautentisering**. Instruktioner och mer information om hur du autentiserar finns i [Autentisera med Data Lake Store med Azure Active Directory (Authenticate with Data Lake Store using Azure Active Directory)](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-an-azure-data-lake-store-account"></a>Skapa ett Azure Data Lake Store-konto
1. Öppna ett nytt Windows PowerShell-fönster på skrivbordet och ange följande fragment för att logga in på kontot, ställ in prenumerationen och registrera Data Lake Store-providern. När du uppmanas att logga in, se till att logga in som en av prenumerationens admininistratörer/ägare:

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Ett Azure Data Lake Store-konto är kopplat till en resursgrupp i Azure. Börja med att skapa en Azure-resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Skapa en Azure-resursgrupp](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Skapa en Azure-resursgrupp")
3. Skapa ett Azure Data Lake Store-konto. Namnet du anger får bara innehålla gemener och siffror.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Skapa ett Azure Data Lake Store-konto](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Skapa ett Azure Data Lake Store-konto")
4. Kontrollera att kontot har skapats.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Utdata för detta ska vara **Sant**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Skapa katalogstrukturer i din Azure Data Lake Store
Du kan skapa kataloger under Azure Data Lake Store-kontot för att hantera och lagra data.

1. Ange en rotkatalog.

        $myrootdir = "/"
2. Skapa en ny katalog som kallas **mynewdirectory** under den angivna roten.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Kontrollera att den nya katalogen har skapats.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Du bör se utdata som liknar följande:

    ![Verifiera katalog](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verifiera katalog")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Ladda upp data till din Azure Data Lake Store
Du kan ladda upp data till Data Lake Store direkt på rotnivå eller till en katalog som du har skapat i kontot. Fragmenten nedan visar hur du kan ladda upp exempeldata till katalogen (**mynewdirectory**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den på en lokal katalog på din dator, till exempel C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Byt namn, hämta och ta bort data från din Data Lake Store
Om du vill byta namn på en fil, använder du följande kommando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Om du vill hämta en fil, använder du följande kommando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Om du vill ta bort en fil, använder du följande kommando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

När du uppmanas, trycker du på **Y** för att ta bort objektet. Om du har fler än en fil att ta bort, kan du ange alla sökvägar avgränsade med kommatecken.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Ta bort ditt Azure Data Lake Store-konto
Använd följande kommando för att ta bort ditt Data Lake Store-konto.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="performance-guidance-while-using-powershell"></a>Prestandavägledning när du använder PowerShell

Nedan visas de viktigaste inställningar som du kan ställa in för att få bästa prestanda när du använder PowerShell för att arbeta med Data Lake Store:

| Egenskap            | Standard | Beskrivning |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Med den här parametern kan du välja antalet parallella trådar för att ladda upp eller ned varje fil. Antalet representerar det högsta antal trådar som går att allokera per fil, men du kan få färre trådar beroende på ditt scenario (om du t.ex. laddar upp en fil på 1 KB får du en tråd även om du ber om 20 trådar).  |
| ConcurrentFileCount | 10      | Den här parametern är specifikt för att ladda upp och ned mappar. Den här parametern anger antalet samtidiga filer som kan laddas upp eller ned. Siffran representerar det högsta antalet filer som samtidigt kan laddas upp eller ned samtidigt, men du kan få mindre samtidighet beroende på ditt scenario (om du exempelvis laddar upp två filer får du två samtidiga filuppladddningar även om du ber om 15). |

**Exempel**

Det här kommandot laddar ned filer från Azure Data Lake Store till användarens lokala enhet med 20 trådar per fil och 100 samtidiga filer.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Hur tar jag reda på värdet som angetts för dessa parametrar?

Här är några riktlinjer som du kan använda.

* **Steg 1: Fastställ det totala antalet trådar** – Du ska börja med att beräkna det totala antalet trådar som ska användas. Som en generell riktlinje bör du använda 6 trådar för varje fysisk kärna.

        Total thread count = total physical cores * 6

    **Exempel**

    Vi antar att du kör PowerShell-kommandon från en virtuell D14-dator med 16 kärnor

        Total thread count = 16 cores * 6 = 96 threads


* **Steg 2: Beräkna PerFileThreadCount** – Vi beräknar vår PerFileThreadCount baserat på filernas storlek. För filer som är mindre än 2,5 GB behöver vi inte ändra den här parametern eftersom standardvärdet 10 är tillräckligt. För filer som är större än 2,5 GB ska du använda 10 trådar som bas för de första 2,5 GB och lägga till en tråd för varje ytterligare ökning på 256 MB. Om du kopierar en mapp med många olika filstorlekar bör du överväga att gruppera dem enligt liknande filstorlekar. Olika stora filstorlekar kan orsaka bristfälliga prestanda. Om du inte kan gruppera liknande filstorlekar ska du ställa in PerFileThreadCount baserat på den största filstorleken.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Exempel**

    Om vi antar att du har 100 filer från 1 GB till 10 GB använder vi 10 GB som den största filstorleken för beräkningen, vilket skulle se ut ungefär som följande.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Steg 3: Beräkna ConcurrentFilecount** – Använd det totala antalet trådar och PerFileThreadCount för att beräkna ConcurrentFileCount baserat på följande beräkning.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exempel**

    Utifrån de exempelvärden vi har använt

        96 = 40 * ConcurrentFileCount

    Så, **ConcurrentFileCount** är **2,4**, vilket vi kan runda av till **2**.

### <a name="further-tuning"></a>Ytterligare justering

Du kan behöva justera ytterligare eftersom det finns en mängd filstorlekar att arbeta med. Beräkningen ovan fungerar bra om alla eller de flesta av filerna är större och närmare intervallet 10 GB. Om det finns i stället många olika filstorlekar med många filer som är mindre kan du minska PerFileThreadCount. Du kan öka ConcurrentFileCount genom att minska PerFileThreadCount. Så om vi förutsätter att de flesta av våra filer är mindre än intervallet 5 GB kan vi göra om vår beräkning:

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Alltså blir **ConcurrentFileCount** nu 96/20, vilket är 4,8, avrundat till **4**.

Du kan fortsätta att finjustera inställningarna genom att ändra **PerFileThreadCount** upp och ned beroende på distributionen av din filstorlek.

### <a name="limitation"></a>Begränsning

* **Antalet filer är mindre än ConcurrentFileCount**: Om antalet filer som du laddar upp är mindre än **ConcurrentFileCount** du har beräknat ska du minska **ConcurrentFileCount** så att det motsvarar antalet filer. Du kan använda eventuella återstående trådar för att öka **PerFileThreadCount**.

* **För många trådar**: Om du ökar antalet trådar för mycket utan att öka klusterstorleken riskerar du att prestanda försämras. Det kan uppstå konkurrensproblem när du växlar innehåll på processorn.

* **Otillräcklig samtidighet**: Om samtidigheten inte är tillräcklig kan klustret vara för litet. Du kan öka antalet noder i klustret så att du får större samtidighet.

* **Begränsningsfel**: Om konkurrensen är för hög kan det leda till begränsningsfel. Om du råkar ut för begränsningsfel ska du antingen minska samtidigheten eller kontakta oss.

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


