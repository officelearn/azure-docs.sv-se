---
title: Aktivera och skapa stora filresurser - Azure-filer
description: I den här artikeln får du lära dig hur du aktiverar och skapar stora filresurser.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd7726d2bbf2830d18d78b5f0b0d7202b734124d
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537686"
---
# <a name="enable-and-create-large-file-shares"></a>Aktivera och skapa stora filresurser

När du aktiverar stora filresurser på ditt lagringskonto kan filresurserna skala upp till 100 TiB. Du kan aktivera den här skalningen på dina befintliga lagringskonton för dina befintliga filresurser.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/) konto innan du börjar.
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Begränsningar

För tillfället kan du bara använda lokalt redundant lagring (LRS) eller zonundant lagring (ZRS) på stora filresursaktiverade konton. Du kan inte använda geo-zon-redundant lagring (GZRS), geo-redundant lagring (GRS) eller läsåtkomst geo-redundant lagring (RA-GRS).
Att aktivera stora filresurser på ett konto är en oåterkallelig process. När du har aktiverat det kan du inte konvertera ditt konto till GZRS, GRS eller RA-GRS.

## <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto

### <a name="portal"></a>Portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** i Azure-portalen. 
1. I listan över resurser anger du **Lagringskonton**. När du skriver filtrerar listan baserat på dina indata. Välj **Lagringskonton**.
1. Välj **Lägg till**i fönstret **Lagringskonton** som visas.
1. Välj den prenumeration som du ska använda för att skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn på den nya resursgruppen.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ange sedan ett namn för lagringskontot. Namnet måste vara unikt i Azure. Namnet måste också vara 3 till 24 tecken i längd, och det kan bara ha siffror och gemener.
1. Välj en plats för ditt lagringskonto.
1. Ställ in replikeringen på antingen **Lokalt redundant lagring** eller **Zonupptredande lagring**.
1. Lämna dessa fält till sina standardvärden:

   |Field  |Värde  |
   |---------|---------|
   |Distributionsmodell     |Resource Manager         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Åtkomstnivå     |Frekvent         |

1. Välj **Avancerat**och välj sedan knappen **Aktiverad** till höger om **Stora filresurser**.
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

    ![Skärmbild med alternativknappen "aktiverad" på ett nytt lagringskonto i Azure-portalen](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Välj **Skapa**.

### <a name="cli"></a>CLI

Installera först [den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) så att du kan aktivera stora filresurser.

Om du vill skapa ett lagringskonto med stora filresurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` och med din information.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Installera först [den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) så att du kan aktivera stora filresurser.

Om du vill skapa ett lagringskonto med stora filresurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourResourceGroup>`, `<yourDesiredRegion>` och med din information.

```powershell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Aktivera stora filresurser på ett befintligt konto

Du kan också aktivera stora filresurser på dina befintliga konton. Om du aktiverar stora filresurser kan du inte konvertera till GZRS, GRS eller RA-GRS. Det går inte att aktivera stora filresurser på det här lagringskontot.

### <a name="portal"></a>Portalen

1. Öppna [Azure-portalen](https://portal.azure.com)och gå till lagringskontot där du vill aktivera stora filresurser.
1. Öppna lagringskontot och välj **Konfiguration**.
1. Välj **Aktiverad** på **Stora filresurser**och välj sedan **Spara**.
1. Välj **Översikt** och välj **Uppdatera**.

![Välja knappen Aktiverad alternativ på ett befintligt lagringskonto i Azure-portalen](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Nu har du aktiverat stora filresurser på ditt lagringskonto. Därefter måste du uppdatera den befintliga resursens kvot för att dra nytta av ökad kapacitet och skala.

Om felmeddelandet "Stora filresurser inte är tillgängliga för kontot ännu" kan din region vara mitt i att slutföra distributionen. Kontakta support om du har ett akut behov av stora filresurser.

### <a name="cli"></a>CLI

Om du vill aktivera stora filresurser på ditt befintliga konto använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourResourceGroup>` och med din information.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Om du vill aktivera stora filresurser på ditt befintliga konto använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourResourceGroup>` och med din information.

```powershell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Skapa en stor filresurs

När du har aktiverat stora filresurser på ditt lagringskonto kan du skapa filresurser på det kontot med högre kvoter. 

### <a name="portal"></a>Portalen

Att skapa en stor filresurs är nästan identiskt med att skapa en standardfilresurs. Den största skillnaden är att du kan ställa in en kvot på upp till 100 TiB.

1. Välj **Filresurser**från ditt lagringskonto .
1. Välj **+ Filresurs**.
1. Ange ett namn på filresursen. Du kan också ange den kvotstorlek du vill ha, upp till 100 TiB. Välj sedan **Skapa**. 

![Azure-portalgränssnittet som visar rutorna Namn och Kvot](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Om du vill skapa en stor filresurs använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` och med din information.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Om du vill skapa en stor filresurs använder du följande kommando. Ersätt `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` och med din information.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandera befintliga filresurser

När du har aktiverat stora filresurser på ditt lagringskonto kan du också expandera befintliga filresurser på det kontot till den högre kvoten. 

### <a name="portal"></a>Portalen

1. Välj **Filresurser**från ditt lagringskonto .
1. Högerklicka på filresursen och välj sedan **Kvot**.
1. Ange den nya storlek som du vill använda och välj sedan **OK**.

![Azure-portalgränssnittet med kvot för befintliga filresurser](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Om du vill ange kvoten till den maximala storleken använder du följande kommando. Ersätt `<yourStorageAccountName>` `<yourStorageAccountKey>`, `<yourFileShareName>` och med din information.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Om du vill ange kvoten till den maximala storleken använder du följande kommando. Ersätt `<YourStorageAccountName>` `<YourStorageAccountKey>`, `<YourStorageAccountFileShareName>` och med din information.

```powershell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Nästa steg

* [Ansluta och montera en filresurs i Windows](storage-how-to-use-files-windows.md)
* [Ansluta och montera en filresurs på Linux](storage-how-to-use-files-linux.md)
* [Ansluta och montera en filresurs på macOS](storage-how-to-use-files-mac.md)
