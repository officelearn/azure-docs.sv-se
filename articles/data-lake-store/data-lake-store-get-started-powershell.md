---
title: "Kom igång med Data Lake Store | Microsoft Docs"
description: "Använd Azure PowerShell för att skapa ett Data Lake Store-konto och utföra grundläggande åtgärder"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/04/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 0c70eff85416b6b41eb455edc430a66b588dec41


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Kom igång med Azure Data Lake Store med hjälp av Azure PowerShell
> [!div class="op_single_selector"]
> * [Portal](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [.NET SDK](data-lake-store-get-started-net-sdk.md)
> * [Java SDK](data-lake-store-get-started-java-sdk.md)
> * [REST-API](data-lake-store-get-started-rest-api.md)
> * [Azure CLI](data-lake-store-get-started-cli.md)
> * [Node.js](data-lake-store-manage-use-nodejs.md)
> 
> 

Lär dig mer om att använda Azure PowerShell för att skapa ett Azure Data Lake Store-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta filer, ta bort ditt konto, osv. Mer information om Data Lake Store finns i [Översikt över Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Krav
Innan du påbörjar de här självstudierna måste du ha:

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](../powershell-install-configure.md).

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
   
    ![Skapa en Azure-resursgrupp](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Create an Azure Resource Group")
3. Skapa ett Azure Data Lake Store-konto. Namnet du anger får bara innehålla gemener och siffror.
   
        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"
   
    ![Skapa ett Azure Data Lake Store-konto](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Create an Azure Data Lake Store account")
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
   
    ![Verifiera katalog](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verify Directory")

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

## <a name="next-steps"></a>Nästa steg
* [Säkra data i Data Lake Store](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)




<!--HONumber=Nov16_HO2-->


