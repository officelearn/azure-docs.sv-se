---
title: Använd PowerShell för att komma igång med Azure Data Lake Storage Gen1 | Microsoft Docs
description: Använda Azure PowerShell för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 06/27/2018
ms.author: nitinme
ms.openlocfilehash: 7a465559bd4e46777f67121e9b3c7d2b0b8a0a22
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46986344"
---
# <a name="get-started-with-azure-data-lake-storage-gen1-using-azure-powershell"></a>Kom igång med Azure Data Lake Storage Gen1 med Azure PowerShell
> [!div class="op_single_selector"]
> * [Portalen](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Azure CLI](data-lake-store-get-started-cli-2.0.md)
>
>

[!INCLUDE [data-lake-storage-gen1-rename-note.md](../../includes/data-lake-storage-gen1-rename-note.md)]

Lär dig mer om att använda Azure PowerShell för att skapa ett Azure Data Lake Storage Gen1-konto och utföra grundläggande åtgärder, till exempel skapa mappar, ladda upp och hämta datafiler, ta bort ditt konto, osv. Läs mer om Data Lake Storage Gen1 [översikt av Data Lake Storage Gen1](data-lake-store-overview.md).

## <a name="prerequisites"></a>Förutsättningar

* **En Azure-prenumeration**. Se [Hämta en kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Installera Azure PowerShell 1.0 eller senare**. Se [Så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).

## <a name="authentication"></a>Autentisering
Den här artikeln använder en enklare metod för autentisering med Data Lake Storage Gen1 där du uppmanas att ange dina autentiseringsuppgifter för Azure-konto. Åtkomstnivå till Data Lake Storage Gen1 kontot och filsystemet styrs av åtkomstnivån för den inloggade användaren. Men det finns andra sätt att autentisera med Data Lake Storage Gen1 som är **slutanvändarautentisering** eller **tjänst-till-tjänst-autentisering**. Instruktioner och mer information om hur du autentiserar finns i [Slutanvändarautentisering](data-lake-store-end-user-authenticate-using-active-directory.md) eller [Tjänst-till-tjänst-autentisering](data-lake-store-authenticate-using-active-directory.md).

## <a name="create-a-data-lake-storage-gen1-account"></a>Skapa ett Data Lake Storage Gen1-konto
1. Öppna ett nytt Windows PowerShell-fönster på skrivbordet. Ange följande fragment för att logga in på kontot, Ställ in prenumerationen och registrera Data Lake Storage Gen1-providern. När du uppmanas att logga in, kontrollera att du loggar in som en av prenumerationens Administratörer/ägare:

        # Log in to your Azure account
        Connect-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Storage Gen1
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Ett Data Lake Storage Gen1-konto är kopplat till en Azure-resursgrupp. Börja med att skapa en Azure-resursgrupp.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Skapa en Azure-resursgrupp](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Skapa en Azure-resursgrupp")
3. Skapa ett Data Lake Storage Gen1-konto. Namnet du anger får bara innehålla gemener och siffror.

        $dataLakeStorageGen1Name = "<your new Data Lake Storage Gen1 account name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStorageGen1Name -Location "East US 2"

    ![Skapa ett Data Lake Storage Gen1 konto](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "skapa ett Data Lake Storage Gen1-konto")
4. Kontrollera att kontot har skapats.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

    Utdata för cmdleten ska vara **Sant**.

## <a name="create-directory-structures-in-your-data-lake-storage-gen1-account"></a>Skapa katalogstrukturer i ditt Data Lake Storage Gen1-konto
Du kan skapa kataloger under Data Lake Storage Gen1 kontot för att hantera och lagra data.

1. Ange en rotkatalog.

        $myrootdir = "/"
2. Skapa en ny katalog som kallas **mynewdirectory** under den angivna roten.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStorageGen1Name -Path $myrootdir/mynewdirectory
3. Kontrollera att den nya katalogen har skapats.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir

    Utdata bör visas så som på följande skärmbild:

    ![Verifiera katalog](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Verifiera katalog")

## <a name="upload-data-to-your-data-lake-storage-gen1-account"></a>Ladda upp data till ditt Data Lake Storage Gen1-konto
Du kan ladda upp data till Data Lake Storage Gen1 direkt på rotnivå eller till en katalog som du har skapat i kontot. Kodavsnitten i det här avsnittet visar hur du kan ladda upp exempeldata till katalogen (**mynewdirectory**) som du skapade i föregående avsnitt.

Om du behöver exempeldata att ladda upp, kan du hämta mappen **Ambulansdata** från [Azure Data Lake Git-lagringsplatsen](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Hämta filen och lagra den i en lokal katalog på datorn, till exempel C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-storage-gen1-account"></a>Byt namn på, hämta och ta bort data från ditt Data Lake Storage Gen1-konto
Om du vill byta namn på en fil, använder du följande kommando:

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Om du vill hämta en fil, använder du följande kommando:

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Om du vill ta bort en fil, använder du följande kommando:

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

När du uppmanas, trycker du på **Y** för att ta bort objektet. Om du har fler än en fil att ta bort, kan du ange alla sökvägar avgränsade med kommatecken.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStorageGen1Name -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-data-lake-storage-gen1-account"></a>Ta bort ditt Data Lake Storage Gen1-konto
Använd följande kommando för att ta bort ditt Data Lake Storage Gen1-konto.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStorageGen1Name

När du uppmanas, anger du **Y** för att ta bort kontot.

## <a name="next-steps"></a>Nästa steg
* [Prestandajusteringsvägledning för att använda PowerShell med Azure Data Lake Storage Gen1](data-lake-store-performance-tuning-powershell.md)
* [Använda Azure Data Lake Storage Gen1 för stordatakrav](data-lake-store-data-scenarios.md) 
* [Skydda data i Data Lake Storage Gen1](data-lake-store-secure-data.md)
* [Använd Azure Data Lake Analytics med Data Lake Storage Gen1](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Använd Azure HDInsight med Data Lake Storage Gen1](data-lake-store-hdinsight-hadoop-use-portal.md)
