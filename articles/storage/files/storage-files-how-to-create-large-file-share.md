---
title: Aktivera och skapa stora fil resurser – Azure Files
description: I den här artikeln får du lära dig hur du aktiverar och skapar stora fil resurser.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: a9b545d71f21138c0374cf199ce10dc2dc246afb
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2020
ms.locfileid: "75732152"
---
# <a name="enable-and-create-large-file-shares"></a>Aktivera och skapa stora fil resurser

Ursprungligen kunde standard fil resurser bara skala upp till 5 TiB. Nu kan de med stora fil resurser skala upp till 100 TiB. Du kan aktivera den här skalningen på dina befintliga lagrings konton för dina befintliga fil resurser. Premium fil resurser skalas upp till 100 TiB som standard.

## <a name="prerequisites"></a>Krav

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Om du tänker använda Azure CLI [installerar du den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Om du tänker använda Azure PowerShell [installerar du den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Begränsningar

För tillfället kan du bara använda LRS eller ZRS på stora fil resurser – aktiverade konton. Du kan inte använda GZRS, GRS eller RA-GRS.
Att aktivera stora fil resurser på ett konto är en oåterkallelig process. När du har aktiverat det kan du inte konvertera ditt konto till GZRS, GRS eller RA-GRS.

## <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto

### <a name="portal"></a>Portalen

1. Logga in på [Azure-portalen](https://portal.azure.com).
1. Välj **Alla tjänster** i Azure-portalen. 
1. Ange **lagrings konton**i listan över resurser. När du skriver filtreras listan baserat på dina inaktuella inaktuella inaktuella inaktuella. Välj **Lagringskonton**.
1. I fönstret **lagrings konton** som visas väljer du **Lägg till**.
1. Välj den prenumeration som du ska använda för att skapa lagrings kontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resurs grupp.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ange sedan ett namn för lagringskontot. Namnet måste vara unikt i Azure. Namnet måste också vara mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagrings konto och se till att det är [en av de regioner som stöds för stora fil resurser](storage-files-planning.md#regional-availability).
1. Ange replikeringen till antingen **Lokalt Redundant lagring** eller **zon-redundant lagring**.
1. Lämna dessa fält till standardvärdena:

   |Field  |Värde  |
   |---------|---------|
   |Distributionsmodell     |Resurshanterare         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Åtkomstnivå     |Frekvent         |

1. Välj **Avancerat**och välj sedan knappen **aktive rad** alternativ till höger om **stora fil resurser**.
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

    ![Skärm bild med alternativ knappen "aktive rad" i ett nytt lagrings konto i Azure Portal](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Välj **Skapa**.

### <a name="cli"></a>CLI

Installera först [den senaste versionen av Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) så att du kan aktivera stora fil resurser.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>`, `<yourResourceGroup>`och `<yourDesiredRegion>` med din information.

```azurecli-interactive
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
az storage account create --name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> --sku Standard_LRS --kind StorageV2 --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Installera först [den senaste versionen av PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0) så att du kan aktivera stora fil resurser.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade använder du följande kommando. Ersätt `<yourStorageAccountName>`, `<yourResourceGroup>`och `<yourDesiredRegion>` med din information.

```PowerShell
## This command creates a large file share–enabled account. It will not support GZRS, GRS, or RA-GRS.
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-large-files-shares-on-an-existing-account"></a>Aktivera stora fil resurser på ett befintligt konto

Du kan också aktivera stora fil resurser på dina befintliga konton. Om du aktiverar stora fil resurser kommer du inte att kunna konvertera till GZRS, GRS eller RA-GRS. Att aktivera stora fil resurser går inte att ångra på det här lagrings kontot.

### <a name="portal"></a>Portalen

1. Öppna [Azure Portal](https://portal.azure.com)och gå till det lagrings konto där du vill aktivera stora fil resurser.
1. Öppna lagrings kontot och välj **konfiguration**.
1. Välj **aktive rad** på **stora fil resurser**och välj sedan **Spara**.
1. Välj **Översikt** och välj **Uppdatera**.

![Att välja alternativ knappen aktive rad i ett befintligt lagrings konto i Azure Portal](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Nu har du aktiverat stora fil resurser på ditt lagrings konto. Sedan måste du uppdatera den befintliga resursens kvot för att dra nytta av ökad kapacitet och skalning.

Om du får ett fel meddelande om att det inte finns några stora fil resurser tillgängliga för kontot än, så kan din region vara i mitten av att slutföra distributionen. Kontakta supporten om du har ett brådskande behov av stora fil resurser.

### <a name="cli"></a>CLI

Använd följande kommando för att aktivera stora fil resurser på ditt befintliga konto. Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` med din information.

```azurecli-interactive
az storage account update --name <yourStorageAccountName> -g <yourResourceGroup> --enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Använd följande kommando för att aktivera stora fil resurser på ditt befintliga konto. Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` med din information.

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Skapa en stor fil resurs

När du har aktiverat stora fil resurser på ditt lagrings konto kan du skapa fil resurser i det kontot med högre kvoter. 

### <a name="portal"></a>Portalen

Att skapa en stor fil resurs är nästan identiskt med att skapa en standard fil resurs. Den största skillnaden är att du kan ange en kvot på upp till 100 TiB.

1. Välj **fil resurser**från ditt lagrings konto.
1. Välj **+ Filresurs**.
1. Ange ett namn på fil resursen. Du kan också ange den kvot storlek som du vill, upp till 100 TiB. Välj sedan **Skapa**. 

![Azure Portal gränssnittet visar rutorna namn och kvot](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

Använd följande kommando för att skapa en stor fil resurs. Ersätt `<yourStorageAccountName>`, `<yourStorageAccountKey>`och `<yourFileShareName>` med din information.

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

Använd följande kommando för att skapa en stor fil resurs. Ersätt `<YourStorageAccountName>`, `<YourStorageAccountKey>`och `<YourStorageAccountFileShareName>` med din information.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandera befintliga fil resurser

När du har aktiverat stora fil resurser på ditt lagrings konto kan du också expandera befintliga fil resurser i det kontot till den högre kvoten. 

### <a name="portal"></a>Portalen

1. Välj **fil resurser**från ditt lagrings konto.
1. Högerklicka på fil resursen och välj sedan **kvot**.
1. Ange den nya storlek som du vill ha och välj sedan **OK**.

![Azure Portal gränssnittet med kvoten för befintliga fil resurser](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

Använd följande kommando för att ange kvoten till maximal storlek. Ersätt `<yourStorageAccountName>`, `<yourStorageAccountKey>`och `<yourFileShareName>` med din information.

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

Använd följande kommando för att ange kvoten till maximal storlek. Ersätt `<YourStorageAccountName>`, `<YourStorageAccountKey>`och `<YourStorageAccountFileShareName>` med din information.

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
# update quota
Set-AzStorageShareQuota -ShareName $shareName -Context $ctx -Quota 102400
```

## <a name="next-steps"></a>Nästa steg

* [Ansluta och montera en fil resurs i Windows](storage-how-to-use-files-windows.md)
* [Ansluta och montera en fil resurs på Linux](storage-how-to-use-files-linux.md)
* [Ansluta och montera en fil resurs på macOS](storage-how-to-use-files-mac.md)
