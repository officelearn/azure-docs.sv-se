<properties
   pageTitle="Kom igång med Data Lake Store | Azure"
   description="Använd Azure PowerShell för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/07/2016"
   ms.author="nitinme"/>

# Kom igång med Azure Data Lake Store med hjälp av Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](data-lake-store-get-started-portal.md)
- [PowerShell](data-lake-store-get-started-powershell.md)
- [.NET SDK](data-lake-store-get-started-net-sdk.md)
- [Java SDK](data-lake-store-get-started-java-sdk.md)
- [REST API](data-lake-store-get-started-rest-api.md)
- [Azure CLI](data-lake-store-get-started-cli.md)
- [Node.js](data-lake-store-manage-use-nodejs.md)

Lär dig mer om att använda Azure PowerShell för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Data Lake Store](data-lake-store-overview.md).

## Förutsättningar

Innan du påbörjar de här självstudierna måste du ha:

- **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
- **Aktivera din Azure-prenumeration** för en förhandsversion av Data Lake Store. Se [instruktioner](data-lake-store-get-started-portal.md#signup).


##Installera Azure PowerShell 1.0 eller senare

Se avsnittet Förutsättningar i [Använda Azure PowerShell med hjälp av Azure Resource Manager](../powershell-azure-resource-manager.md#prerequisites).

## Skapa ett Azure Data Lake Store-konto

1. Öppna ett nytt Azure PowerShell-fönster på skrivbordet och ange följande fragment för att logga in på kontot, ställ in prenumerationen och registrera Data Lake Store-providern. När du uppmanas att logga in, se till att logga in som en av prenumerationens admininistratörer/ägare:

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

    ![Skapa en Azure-resursgrupp](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")

2. Skapa ett Azure Data Lake Store-konto. Namnet du anger får bara innehålla gemener och siffror.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Skapa ett Azure Data Lake Store-konto](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")

3. Kontrollera att kontot har skapats.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Utdata för detta ska vara **Sant**.

## Skapa katalogstrukturer i din Azure Data Lake Store

Du kan skapa kataloger under Azure Data Lake Store-kontot för att hantera och lagra data.

1. Ange en rotkatalog.

        $myrootdir = "/"

2. Skapa en ny katalog som kallas **mynewdirectory** under den angivna roten.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory

3. Kontrollera att den nya katalogen har skapats.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Du bör se utdata som liknar följande:

    ![Verifiera katalog](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")


## Ladda upp data till din Azure Data Lake Store

Du kan ladda upp data till Data Lake Store direkt på rotnivå eller till en katalog som du har skapat i kontot. Fragmenten nedan visar hur du kan ladda upp exempeldata till katalogen (**mynewdirectory**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den på en lokal katalog på din dator, till exempel C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## Byt namn, hämta och ta bort data från din Data Lake Store

Om du vill byta namn på en fil, använder du följande kommando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Om du vill hämta en fil, använder du följande kommando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Om du vill ta bort en fil, använder du följande kommando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

När du uppmanas, trycker du på **Y** för att ta bort objektet. Om du har fler än en fil att ta bort, kan du ange alla sökvägar avgränsade med kommatecken.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## Ta bort ditt Azure Data Lake Store-konto

Använd följande kommando för att ta bort ditt Data Lake Store-konto.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

När du uppmanas, anger du **Y** för att ta bort kontot.


## Nästa steg

- [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
- [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
- [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)



<!--HONumber=Jun16_HO2-->


