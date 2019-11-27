---
title: Aktivera och skapa stora fil resurser – Azure Files
description: I den här artikeln får du lära dig hur du aktiverar och skapar stora fil resurser.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 11/20/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 34b8af56a8f2f108b96ca07fa73f90bb9eb5bf13
ms.sourcegitcommit: 4c831e768bb43e232de9738b363063590faa0472
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2019
ms.locfileid: "74422738"
---
# <a name="how-to-enable-and-create-large-file-shares"></a>Så här aktiverar och skapar du stora fil resurser

Från början kunde standard fil resurser bara skala upp till 5 TiB, nu, med stora fil resurser, kan de skala upp till 100 TiB. Premium fil resurser skalas upp till 100 TiB som standard. 

För att kunna skala upp till 100 TiB med standard fil resurser måste du aktivera lagrings kontot för att använda stora fil resurser. Du kan antingen aktivera ett befintligt konto eller skapa ett nytt konto om du vill använda stora fil resurser.

## <a name="prerequisites"></a>Förutsättningar

- Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
- Om du tänker använda Azure CLI måste du först [installera den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).
- Om du tänker använda Azure PowerShell måste du [installera den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0).

## <a name="restrictions"></a>Begränsningar

Stora fil resurser aktiverade konton stöder LRS eller ZRS. För närvarande stöder inte stora fil resurser aktiverade konton GZRS, GRS eller RA-GRS. Att aktivera stora fil resurser på ett konto är en oåterkallelig process när det har Aktiver ATS kan kontot inte konverteras till GZRS, GRS eller RA-GRS.

## <a name="create-a-new-storage-account"></a>Skapa ett nytt lagringskonto

### <a name="portal"></a>Portalen

Logga in på [Azure-portalen](https://portal.azure.com).

1. Välj **Alla tjänster** i Azure-portalen. Skriv **lagringskonton** i listan över resurser. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Lagringskonton**.
1. På fönstret **lagringskonton** som visas, väljer du **lägg till**.
1. Välj den prenumeration där du vill skapa lagringskontot.
1. Under fältet **Resursgrupp** väljer du **Skapa ny**. Ange ett namn för din nya resursgrupp som du ser i följande bild.

    ![Skärmbild som visar hur du skapar en resursgrupp i portalen](media/storage-files-how-to-create-large-file-share/create-large-file-share.png)

1. Ange sedan ett namn för lagringskontot. Namnet du väljer måste vara unikt för Azure. Namnet måste också bestå av mellan 3 och 24 tecken långt och får bara innehålla siffror och gemener.
1. Välj en plats för ditt lagrings konto, se till att det är [en av de regioner som stöds för LF](storage-files-planning.md#regional-availability).
1. Ställ in replikeringen till antingen **Lokalt Redundant lagring** eller **zon-redundant lagring**.
1. Lämna dessa fält med respektive standardvärde:

   |Fält  |Value  |
   |---------|---------|
   |Distributionsmodell     |Resurshanterare         |
   |Prestanda     |Standard         |
   |Typ av konto     |StorageV2 (generell användning v2)         |
   |Åtkomstnivå     |Frekvent         |

1. Välj **Avancerat** och välj **aktive rad** för **stora fil resurser**.
1. Välj **Granska + skapa** för att granska inställningarna för ditt lagringskonto och skapa kontot.

    ![Large-File-Shares-Advanced-enable. png](media/storage-files-how-to-create-large-file-share/large-file-shares-advanced-enable.png)

1. Välj **Skapa**.


### <a name="cli"></a>CLI

Se först till att du [installerar den senaste versionen](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)så att du kan aktivera den här funktionen.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade, ersätter du `<yourStorageAccountName>`, `<yourResourceGroup>`och `<yourDesiredRegion>` med värdena och använder sedan följande kommando:

```azurecli-interactive
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
az storage account create –name <yourStorageAccountName> -g <yourResourceGroup> -l <yourDesiredRegion> –sku Standard_LRS --kind StorageV2 –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Se först till att du [installerar den senaste versionen](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.0.0)så att du kan aktivera den här funktionen.

Om du vill skapa ett lagrings konto med stora fil resurser aktiverade, ersätter du `<yourStorageAccountName>`, `<yourResourceGroup>`och `<yourDesiredRegion>` med värdena och använder sedan följande kommando:

```PowerShell
## This command creates a large file share enabled account, it will not support GZRS, GRS, or RA-GRS
New-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -Location <yourDesiredRegion> -SkuName Standard_LRS -EnableLargeFileShare;
```

## <a name="enable-on-existing-account"></a>Aktivera på befintligt konto

### <a name="portal"></a>Portalen

Du kan också aktivera stora fil resurser på befintliga konton. Om du gör det kommer kontot inte längre att kunna konvertera till GZRS, GRS eller RA-GRS. Det här valet går inte att ångra på det här kontot.

1. Öppna [Azure Portal](https://portal.azure.com) och navigera till det lagrings konto som du vill aktivera stora fil resurser på.
1. Öppna lagrings kontot och välj **konfiguration**.
1. Välj **aktive rad** på **stora fil resurser**och välj sedan Spara.
1. Välj **Översikt** och välj **Uppdatera**.

![Enable-large-File-Shares-on-existing. png](media/storage-files-how-to-create-large-file-share/enable-large-file-shares-on-existing.png)

Nu har du aktiverat stora fil resurser på ditt lagrings konto.

Om följande fel meddelande visas: "stora fil resurser är inte tillgängliga för kontot ännu". Du kan antingen vänta en stund, eftersom din region förmodligen är i mitten av distributionen eller, om du har brådskande behov, kontakta supporten.

### <a name="cli"></a>CLI

Du kan aktivera stora fil resurser på dina befintliga konton. Om du gör det kommer kontot inte längre att kunna konvertera till GZRS, GRS eller RA-GRS. Det här valet går inte att ångra på det här kontot.

Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` i följande kommando och Använd det sedan för att aktivera stora fil resurser på ditt befintliga konto:

```azurecli-interactive
az storage account update –name <yourStorageAccountName> -g <yourResourceGroup> –enable-large-file-share
```

### <a name="powershell"></a>PowerShell

Du kan aktivera stora fil resurser på dina befintliga konton. Om du gör det kommer kontot inte längre att kunna konvertera till GZRS, GRS eller RA-GRS. Det här valet går inte att ångra på det här kontot.

Ersätt `<yourStorageAccountName>` och `<yourResourceGroup>` i följande kommando och Använd det sedan för att aktivera stora fil resurser på ditt befintliga konto:

```PowerShell
Set-AzStorageAccount -ResourceGroupName <yourResourceGroup> -Name <yourStorageAccountName> -EnableLargeFileShare
```

## <a name="create-a-large-file-share"></a>Skapa en stor fil resurs

### <a name="portal"></a>Portalen

Att skapa en stor fil resurs är nästan identiskt med att skapa en standard fil resurs. Den största skillnaden är att du kan ange en kvot på upp till 100 TiB.

1. Välj **fil resurser**från ditt lagrings konto.
1. Välj **+ Filresurs**.
1. Ange ett namn på fil resursen och (valfritt) den kvot storlek som du vill ha, upp till 100 TiB och välj sedan **skapa**. 

![Large-File-Shares-Create-Share. png](media/storage-files-how-to-create-large-file-share/large-file-shares-create-share.png)

### <a name="cli"></a>CLI

När du har aktiverat stora fil resurser på ditt lagrings konto kan du skapa fil resurser i det kontot med högre kvoter. Ersätt `<yourStorageAccountName>`, `<yourStorageAccountKey>`och `<yourFileShareName>` i följande kommando med dina värden. sedan kan du använda det för att skapa en stor fil resurs:

```azurecli-interactive
az storage share create --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName>
```

### <a name="powershell"></a>PowerShell

När du har aktiverat stora fil resurser på ditt lagrings konto kan du skapa fil resurser i det kontot med högre kvoter. Ersätt `<YourStorageAccountName>`, `<YourStorageAccountKey>`och `<YourStorageAccountFileShareName>` i följande kommando med dina värden. sedan kan du använda det för att skapa en stor fil resurs:

```PowerShell
##Config
$storageAccountName = "<YourStorageAccountName>"
$storageAccountKey = "<YourStorageAccountKey>"
$shareName="<YourStorageAccountFileShareName>"
$ctx = New-AzStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
New-AzStorageShare -Name $shareName -Context $ctx
```

## <a name="expand-existing-file-shares"></a>Expandera befintliga fil resurser

### <a name="portal"></a>Portalen

När du har aktiverat stora fil resurser på ditt lagrings konto kan du expandera befintliga resurser till den högre kvoten.

1. Välj **fil resurser**från ditt lagrings konto.
1. Högerklicka på fil resursen och välj **kvot**.
1. Ange den nya storlek som du vill ha och välj sedan **OK**.

![Update-Large-File-Share-quota. png](media/storage-files-how-to-create-large-file-share/update-large-file-share-quota.png)

### <a name="cli"></a>CLI

När du har aktiverat stora fil resurser på ditt lagrings konto kan du expandera befintliga fil resurser i det kontot till de högre kvoterna. Ersätt `<yourStorageAccountName>`, `<yourStorageAccountKey>`och `<yourFileShareName>` i följande kommando med dina värden, så du kan använda den för att ange kvoten till maximal storlek:

```azurecli-interactive
az storage share update --account-name <yourStorageAccountName> --account-key <yourStorageAccountKey> --name <yourFileShareName> --quota 102400
```

### <a name="powershell"></a>PowerShell

När du har aktiverat stora fil resurser på ditt lagrings konto kan du expandera befintliga fil resurser i det kontot till de högre kvoterna. Ersätt `<YourStorageAccountName>`, `<YourStorageAccountKey>`och `<YourStorageAccountFileShareName>` i följande kommando med dina värden, så du kan använda den för att ange kvoten till maximal storlek:

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

* [Anslut och montera filresurs – Windows](storage-how-to-use-files-windows.md)
* [Anslut och montera filresurs – Linux](../storage-how-to-use-files-linux.md)
* [Anslut och montera filresurs – macOS](storage-how-to-use-files-mac.md)