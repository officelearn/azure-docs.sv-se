---
title: "Skapa en avbildning av virtuell dator lokalt för Azure Marketplace | Microsoft Docs"
description: "Förstå och utföra stegen för att skapa en lokal VM-avbildning och distribuera på Azure Marketplace för andra att köpa."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: mbaldwin
ms.openlocfilehash: 6b927ce6032092ce258eeebca49da0571439dbfb
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/16/2018
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Utveckla en avbildning av virtuell dator lokalt för Azure Marketplace
Vi rekommenderar starkt att du utveckla Azure virtuella hårddiskar (VHD) direkt i molnet genom att använda Remote Desktop Protocol. Om du måste går det att hämta en virtuell Hårddisk och utveckla genom att använda lokal infrastruktur.  

Du måste hämta operativsystemet VHD av skapade VM för lokal utveckling. De här stegen skulle ske steget 3.3, ovan.  

## <a name="download-a-vhd-image"></a>Hämta en VHD-avbildningen
### <a name="locate-a-blob-url"></a>Leta upp en blob-URL
För att kunna hämta den virtuella Hårddisken, att hitta blob-URL: en för operativsystemets disk.

Hitta blob-URL från den nya [Microsoft Azure-portalen](https://portal.azure.com):

1. Gå till **Bläddra** > **VMs**, och välj sedan den distribuerade virtuella datorn.
2. Under **konfigurera**, Välj den **diskar** panelen, vilket öppnar bladet diskar.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Välj den **OS-disken**, vilket öppnar ett annat blad som visar egenskaper för disk, inklusive VHD-plats.
4. Kopiera URL: en blob.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Ta bort den distribuerade virtuella datorn utan att ta bort diskar för säkerhetskopiering. Du kan även stoppa den virtuella datorn i stället för att ta bort den. Hämta inte operativsystemet VHD när den virtuella datorn körs.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Ladda ned en virtuell hårddisk
Du känner till blob-Adressen, kan du hämta den virtuella Hårddisken med hjälp av den [Azure-portalen](http://manage.windowsazure.com/) eller PowerShell.  

> [!NOTE]
> När den här guiden skapas finns funktionaliteten för att ladda ned en virtuell Hårddisk inte ännu i den nya Microsoft Azure-portalen.  
> 
> 

**Ladda ned operativsystemet VHD via aktuellt [Azure-portalen](http://manage.windowsazure.com/)**

1. Logga in på Azure portal om du inte har gjort det redan.
2. Klicka på den **lagring** fliken.
3. Välj lagringskonto för den virtuella Hårddisken lagras.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Detta visar lagringskontoegenskaperna. Välj den **behållare** fliken.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Markera den behållare där den virtuella Hårddisken lagras. När de skapas från portalen, lagras den virtuella Hårddisken i en VHD-behållare.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Välj rätt operativsystem VHD genom att jämföra Webbadressen till den som du sparade.
7. Klicka på **Hämta**.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Hämta en virtuell Hårddisk med hjälp av PowerShell
Förutom att använda Azure-portalen kan du använda den [spara AzureVhd](http://msdn.microsoft.com/library/dn495297.aspx) för att ladda ned operativsystemet VHD.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Exempelvis spara AzureVhd-källa ”https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” - LocalFilePath ”C:\Users\Administrator\Desktop\baseimagevm.vhd” - StorageKey <String>

> [!NOTE]
> **Spara-AzureVhd** har också en **NumberOfThreads** alternativ som kan användas för att öka parallellitet för att göra bästa användningen av bandbredd för hämtning.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Överför virtuella hårddiskar till ett Azure storage-konto
Om du har förberett din virtuella hårddiskar lokalt måste du överföra dem till ett lagringskonto i Azure. Det här steget sker efter att skapa den virtuella Hårddisken lokalt men innan du hämtar certifikat för VM-avbildning.

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare
Vi rekommenderar att virtuella hårddiskar ska överföras till ett lagringskonto i en region i USA. Alla virtuella hårddiskar för en enskild SKU ska placeras i en enskild behållare inom ett enda storage-konto.

Om du vill skapa ett lagringskonto som du kan använda den [Microsoft Azure-portalen](https://portal.azure.com/), PowerShell eller Linux kommandoradsverktyget.  

**Skapa ett lagringskonto från Microsoft Azure-portalen**

1. Klicka på **Skapa en resurs**.
2. Välj **lagring**.
3. Fyll i lagringskontonamn och välj sedan en plats.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Klicka på **Skapa**.
5. Bladet för det skapade lagringskontot ska vara öppen. Om inte, Välj **Bläddra** > **Lagringskonton**. Välj lagringskonto som skapats i bladet Storage-konto.
6. Välj **behållare**.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. På bladet behållare väljer **Lägg till**, och ange sedan ett behållarnamn och behörigheter för behållaren. Välj **privata** behörigheter för behållaren.

> [!TIP]
> Vi rekommenderar att du skapar en behållare per SKU som du planerar att publicera.
> 
> 

  ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Skapa ett lagringskonto med hjälp av PowerShell
Med hjälp av PowerShell, skapa ett lagringskonto med hjälp av den [ny AzureStorageAccount](http://msdn.microsoft.com/library/dn495115.aspx) cmdlet.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Därefter kan du skapa en behållare i detta lagringskonto med hjälp av den [NewAzureStorageContainer](http://msdn.microsoft.com/library/dn495291.aspx) cmdlet.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Dessa kommandon förutsätter att den aktuella kontexten för lagringskontot har redan ställts in i PowerShell.   Referera till [konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md) för mer information om PowerShell-installationen.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Skapa ett lagringskonto med hjälp av kommandoradsverktyget för Mac- och Linux
> Från [Linux kommandoradsverktyget](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), skapa ett lagringskonto på följande sätt.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Skapa en behållare på följande sätt.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk
När lagringskontot och behållaren har skapats kan överföra du din förberedda virtuella hårddiskar. Du kan använda PowerShell, Linux-kommandoradsverktyget eller andra hanteringsverktyg för Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Överföra en virtuell Hårddisk med PowerShell
Använd den [Add-AzureVhd](http://msdn.microsoft.com/library/dn495173.aspx) cmdlet.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Överföra en virtuell Hårddisk med hjälp av kommandoradsverktyget för Mac- och Linux
Med den [Linux kommandoradsverktyget](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), använder du följande: azure vm-avbildning skapa <image name> --plats <Location of the data center> --Operativsystemet Linux <LocationOfLocalVHD>

## <a name="see-also"></a>Se också
* [Skapa en avbildning av virtuell dator på Marketplace](marketplace-publishing-vm-image-creation.md)
* [Konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md)

