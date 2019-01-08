---
title: Skapa en avbildning av en lokal virtuell dator för Azure Marketplace | Microsoft Docs
description: Förstå och kör stegen för att skapa en lokal VM-avbildning och distribuera till Azure Marketplace för andra att köpa.
services: marketplace-publishing
documentationcenter: ''
author: v-miclar
manager: hascipio
editor: ''
ms.assetid: 26dfbd5a-8685-4b19-987e-c20ca60540ec
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 04/29/2016
ms.author: hascipio; v-divte
ROBOTS: NOINDEX
ms.openlocfilehash: b9fbb2f50905b1b80a092ba13f860f30cb9423a9
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2019
ms.locfileid: "54077801"
---
# <a name="develop-an-on-premises-virtual-machine-image-for-the-azure-marketplace"></a>Utveckla en avbildning av en lokal virtuell dator för Azure Marketplace
Vi rekommenderar starkt att du utvecklar Azure virtuella hårddiskar (VHD) direkt i molnet genom att använda Remote Desktop Protocol. Om du måste går det att ladda ned en virtuell Hårddisk och utveckla med hjälp av den lokala infrastrukturen.  

För lokal utveckling, måste du ladda ned operativsystemet VHD för den skapade virtuella datorn. De här stegen skulle ske som en del av steg 3.3, ovan.  

## <a name="download-a-vhd-image"></a>Ladda ned en VHD-avbildning
### <a name="locate-a-blob-url"></a>Leta upp en blob-URL
För att kunna hämta den virtuella Hårddisken först lokalisera blob-URL: en för operativsystemdisken.

Hitta blob-URL: en från den nya [Microsoft Azure-portalen](https://portal.azure.com):

1. Gå till **Bläddra** > **VMs**, och välj sedan den distribuerade virtuella datorn.
2. Under **konfigurera**väljer den **diskar** panelen, vilket öppnar bladet diskar.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img01.png)
3. Välj den **Operativsystemdisken**, som du öppnar ett nytt blad som visar diskegenskaper, inklusive VHD-platsen.
4. Kopiera den här blob-URL.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img02.png)
5. Nu kan ta bort den distribuerade virtuella datorn utan att ta bort diskar för säkerhetskopiering. Du kan också stoppa den virtuella datorn i stället för att ta bort den. Hämta inte operativsystemet VHD när Virtuellt datorn körs.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img03.png)

### <a name="download-a-vhd"></a>Ladda ned en virtuell hårddisk
Du känner till blob-Adressen, kan du hämta den virtuella Hårddisken med hjälp av den [Azure-portalen](http://manage.windowsazure.com/) eller PowerShell.  

> [!NOTE]
> Vid tidpunkten för den här guiden Skapa finns funktionaliteten för att ladda ned en virtuell Hårddisk inte ännu i den nya Microsoft Azure-portalen.  
> 
> 

**Ladda ned operativsystemet VHD via aktuellt [Azure-portalen](http://manage.windowsazure.com/)**

1. Logga in på Azure portal om du inte har gjort det redan.
2. Klicka på den **Storage** fliken.
3. Välj det lagringskonto där den virtuella Hårddisken lagras.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img04.png)
4. Detta visar egenskaper för lagringskontot. Välj den **behållare** fliken.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img05.png)
5. Markera den behållare där den virtuella Hårddisken lagras. Som standard när de skapas från portalen, lagras den virtuella Hårddisken i en VHD-behållare.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img06.png)
6. Välj rätt operativsystem VHD genom att jämföra URL-Adressen till det du sparade.
7. Klicka på **Hämta**.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img07.png)

### <a name="download-a-vhd-by-using-powershell"></a>Ladda ned en virtuell Hårddisk med hjälp av PowerShell
Förutom att använda Azure-portalen kan du använda den [Save-AzureVhd](https://msdn.microsoft.com/library/dn495297.aspx) cmdlet för att ladda ned operativsystemet VHD.

        Save-AzureVhd –Source <storageURIOfVhd> `
        -LocalFilePath <diskLocationOnWorkstation> `
        -StorageKey <keyForStorageAccount>
Till exempel spara-AzureVhd-källa ”https://baseimagevm.blob.core.windows.net/vhds/BaseImageVM-6820cq00-BaseImageVM-os-1411003770191.vhd” - LocalFilePath ”C:\Users\Administrator\Desktop\baseimagevm.vhd” - StorageKey <String>

> [!NOTE]
> **Spara-AzureVhd** har också en **NumberOfThreads** alternativ som kan användas för att öka parallellitet för bästa användning av tillgänglig bandbredd för nedladdning av.
> 
> 

## <a name="upload-vhds-to-an-azure-storage-account"></a>Ladda upp VHD: er till ett Azure storage-konto
Om du har skapat dina virtuella hårddiskar lagras lokalt kan behöva du överföra dem till ett lagringskonto i Azure. Det här steget sker efter att skapa din VHD lokalt men innan du fått certifiering för din avbildning.

### <a name="create-a-storage-account-and-container"></a>Skapa ett lagringskonto och en behållare
Vi rekommenderar att virtuella hårddiskar ska laddas upp till ett lagringskonto i en region i USA. Alla virtuella hårddiskar för en enda SKU: N ska placeras i en enskild behållare inom ett enda lagringskonto.

Om du vill skapa ett lagringskonto som du kan använda den [Microsoft Azure-portalen](https://portal.azure.com/), PowerShell eller Linux kommandoradsverktyget.  

**Skapa ett lagringskonto från Microsoft Azure portal**

1. Klicka på **Skapa en resurs**.
2. Välj **Lagring**.
3. Fyll i lagringskontonamn och välj sedan en plats.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img08.png)
4. Klicka på **Skapa**.
5. Bladet för det skapade lagringskontot ska vara öppet. Om inte, Välj **Bläddra** > **Lagringskonton**. Välj det lagringskonto som skapats i bladet Storage-konto.
6. Välj **behållare**.
   
   ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img09.png) 
7. På bladet behållare väljer **Lägg till**, och ange sedan ett behållarnamn och behörigheter för behållare. Välj **privata** för behörigheter för behållare.

> [!TIP]
> Vi rekommenderar att du skapar behållare per SKU som du planerar att publicera.
> 
> 

  ![Rita](media/marketplace-publishing-vm-image-creation-on-premise/img10.png)

### <a name="create-a-storage-account-by-using-powershell"></a>Skapa ett lagringskonto med hjälp av PowerShell
Med hjälp av PowerShell, skapa ett lagringskonto med hjälp av den [New-AzureStorageAccount](https://docs.microsoft.com/powershell/module/servicemanagement/azure/new-azurestorageaccount) cmdlet.

        New-AzureStorageAccount -StorageAccountName “mystorageaccount” -Location “West US”

Därefter kan du skapa en behållare i det lagringskontot med hjälp av den [New-AzureStorageContainer](https://docs.microsoft.com/powershell/module/azure.storage/new-azurestoragecontainer) cmdlet.

        New-AzureStorageContainer -Name “containername” -Permission “Off”

> [!NOTE]
> Dessa kommandon förutsätter att den aktuella kontexten för lagringskontot redan har ställts in i PowerShell.   Referera till [att konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md) för mer information om PowerShell-installationen.  
> 
> ### <a name="create-a-storage-account-by-using-the-command-line-tool-for-mac-and-linux"></a>Skapa ett lagringskonto med hjälp av kommandoradsverktyget för Mac och Linux
> Från [Linux kommandoradsverktyget](../virtual-machines/linux/cli-manage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), skapa ett lagringskonto på följande sätt.
> 
> 

        azure storage account create mystorageaccount --location "West US"

Skapa en behållare på följande sätt.

        azure storage container create containername --account-name mystorageaccount --accountkey <accountKey>

## <a name="upload-a-vhd"></a>Ladda upp en virtuell hårddisk
När lagringskontot och behållaren har skapats kan du ladda upp de förberedda virtuella hårddiskarna. Du kan använda PowerShell, Linux-kommandoradsverktyget eller andra hanteringsverktyg för Azure Storage.

### <a name="upload-a-vhd-via-powershell"></a>Ladda upp en virtuell Hårddisk med PowerShell
Använd den [Add-AzureVhd](https://msdn.microsoft.com/library/dn495173.aspx) cmdlet.

        Add-AzureVhd –Destination “http://mystorageaccount.blob.core.windows.net/containername/vmsku.vhd” -LocalFilePath “C:\Users\Administrator\Desktop\vmsku.vhd”

### <a name="upload-a-vhd-by-using-the-command-line-tool-for-mac-and-linux"></a>Överför en virtuell Hårddisk med hjälp av kommandoradsverktyget för Mac och Linux
Med den [Linux kommandoradsverktyget](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2), använder du följande kommando: `azure vm image create <image name> --location <Location of the data center> --OS Linux <LocationOfLocalVHD>`

## <a name="see-also"></a>Se också
* [Skapa en avbildning av virtuell dator för Marketplace](marketplace-publishing-vm-image-creation.md)
* [Konfigurera Azure PowerShell](marketplace-publishing-powershell-setup.md)

