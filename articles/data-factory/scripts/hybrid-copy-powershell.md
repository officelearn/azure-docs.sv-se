---
title: Kopiera data från lokalt till Azure med PowerShell
description: Det här PowerShell-skriptet kopierar data från en lokal SQL Server-databas till en annan Azure Blob Storage.
services: data-factory
ms.service: data-factory
ms.workload: data-services
ms.topic: article
ms.author: jingwang
author: linda33wj
manager: shwang
ms.custom: seo-lt-2019
ms.date: 10/31/2017
ms.openlocfilehash: 10555defc4888af66bb88d19190b6543aa8ae0c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75974691"
---
# <a name="use-powershell-to-create-a-data-factory-pipeline-to-copy-data-from-on-premises-to-azure"></a>Använda PowerShell för att skapa en pipeline för datafabrik för att kopiera data från lokalt till Azure

Det här exemplet powershell-skript skapar en pipeline i Azure Data Factory som kopierar data från en lokal SQL Server-databas till en Azure Blob Storage.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!INCLUDE [sample-powershell-install](../../../includes/sample-powershell-install-no-ssh-az.md)]

## <a name="prerequisites"></a>Krav

- **SQL Server**. Du använder en lokal SQL Server-databas som ett **källdatalager** i det här exemplet.
- **Azure Storage-konto**. Du använder Azure blob storage som ett **mål-/sink-datalager** i det här exemplet. om du inte har ett Azure Storage-konto finns det anvisningar om hur du skapar ett i artikeln [Skapa ett lagringskonto](../../storage/common/storage-account-create.md).
- **Självvärd för integrationskörning**. Ladda ner MSI-filen från [hämtningscentralen](https://www.microsoft.com/download/details.aspx?id=39717) och kör den för att installera en självvärd integreringskörning på datorn.  

### <a name="create-sample-database-in-sql-server"></a>Skapa exempeldatabas i SQL Server
1. Skapa en tabell med namnet **emp** i den lokala SQL Server-databasen med hjälp av följande SQL-skript:

   ```sql   
     CREATE TABLE dbo.emp
     (
         ID int IDENTITY(1,1) NOT NULL,
         FirstName varchar(50),
         LastName varchar(50),
         CONSTRAINT PK_emp PRIMARY KEY (ID)
     )
     GO
   ```

2. Infoga några exempeldata i tabellen:

   ```sql
     INSERT INTO emp VALUES ('John', 'Doe')
     INSERT INTO emp VALUES ('Jane', 'Doe')
   ```

## <a name="sample-script"></a>Exempelskript

> [!IMPORTANT]
> Det här skriptet skapar JSON-filer som definierar Data Factory-entiteter (länkad tjänst, datauppsättning och pipeline) på hårddisken i c:\ Mappen.

[!code-powershell[main](../../../powershell_scripts/data-factory/copy-from-onprem-sql-server-to-azure-blob/copy-from-onprem-sql-server-to-azure-blob.ps1 "Copy from on-premises SQL Server -> Azure Blob Storage")]


## <a name="clean-up-deployment"></a>Rensa distribution

När du har kört exempelskriptet kan du använda följande kommando för att ta bort resursgruppen och alla resurser som är associerade med det:

```powershell
Remove-AzResourceGroup -ResourceGroupName $resourceGroupName
```
Om du vill ta bort datafabriken från resursgruppen kör du följande kommando:

```powershell
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="script-explanation"></a>Förklaring av skript

I det här skriptet används följande kommandon:

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [Set-AzDataFactoryV2](/powershell/module/az.datafactory/set-Azdatafactoryv2) | Skapa en datafabrik. |
| [Nya-AzDataFactoryV2LinkedServiceEncryptCredential](/powershell/module/az.datafactory/new-Azdatafactoryv2linkedserviceencryptedcredential) | Krypterar autentiseringsuppgifter i en länkad tjänst och genererar en ny definierad länkad tjänst med den krypterade autentiseringstillståndet.
| [Set-AzDataFactoryV2LinkedService](/powershell/module/az.datafactory/Set-Azdatafactoryv2linkedservice) | Skapar en länkad tjänst i datafabriken. En länkad tjänst länkar ett datalager eller en beräkning till en datafabrik. |
| [Set-AzDataFactoryV2Dataset](/powershell/module/az.datafactory/Set-Azdatafactoryv2dataset) | Skapar en datauppsättning i datafabriken. En datauppsättning representerar indata/utdata för en aktivitet i en pipeline. |
| [Set-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Set-Azdatafactoryv2pipeline) | Skapar en pipeline i datafabriken. En pipeline innehåller en eller flera aktiviteter som utför en viss åtgärd. I den här pipelinen kopierar en kopieringsaktivitet data från en plats till en annan plats i en Azure Blob Storage. |
| [Anropa-AzDataFactoryV2Pipeline](/powershell/module/az.datafactory/Invoke-Azdatafactoryv2pipeline) | Skapar en körning för pipelinen. Med andra ord, kör pipelinen. |
| [Hämta-AzDataFactoryV2ActivityRun](/powershell/module/az.datafactory/get-Azdatafactoryv2activityrun) | Hämtar information om körningen av aktiviteten (aktivitetskörningen) på pipelinen.
| [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp, inklusive alla kapslade resurser. |
|||

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell finns i [Azure PowerShell-dokumentationen](https://docs.microsoft.com/powershell/).

Ytterligare Azure Data Factory PowerShell-skriptexempel finns i [Azure Data Factory PowerShell-exemplen](../samples-powershell.md).
