---
title: Ange och hantera principer för oföränderlighet för Blob-lagring - Azure Storage
description: Lär dig hur du använder WORM (Write Once, Read Many) stöd för Blob (objekt) lagring för att lagra data i ett icke-raderbart, icke-ändringsbart tillstånd för ett angivet intervall.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 11/26/2019
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 05a155584f0cb69191883cb82b3db0af435ccc12
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970099"
---
# <a name="set-and-manage-immutability-policies-for-blob-storage"></a>Ange och hantera principer för oföränderlighet för Blob-lagring

Oföränderlig lagring för Azure Blob-lagring gör det möjligt för användare att lagra affärskritiska dataobjekt i ett WORM-tillstånd (Write Once, Read Many). Det här tillståndet gör att data inte kan raderas och inte kan ändras för ett användarspecificerat intervall. Under kvarhållningsintervallets varaktighet kan blobbar skapas och läsas, men kan inte ändras eller tas bort. Oföränderlig lagring är tillgänglig för generella v2- och Blob-lagringskonton i alla Azure-regioner.

Den här artikeln visar hur du anger och hanterar principer för oföränderlighet och juridiska spärrar för data i Blob-lagring med Azure-portalen, PowerShell eller Azure CLI. Mer information om oföränderlig lagring finns i [Lagra affärskritiska blob-data med oföränderlig lagring](storage-blob-immutable-storage.md).

## <a name="set-retention-policies-and-legal-holds"></a>Ange bevarandeprinciper och juridiska spärrar

### <a name="portal"></a>[Portal](#tab/azure-portal)

1. Skapa en ny container eller välj en befintlig container för lagring av de blobbar som ska behållas i det oföränderliga tillståndet. Behållaren måste finnas i ett allmänt v2- eller Blob-lagringskonto.

2. Välj **Åtkomstprincip** i behållarinställningarna. Välj sedan **Lägg till princip** under **Oföränderlig bloblagring**.

    ![Behållarinställningar i portalen](media/storage-blob-immutability-policies-manage/portal-image-1.png)

3. Om du vill aktivera tidsbaserad kvarhållning väljer du **Tidsbaserad kvarhållning** på den nedrullningsbara menyn.

    !["Tidsbaserad kvarhållning" som valts under "Principtyp"](media/storage-blob-immutability-policies-manage/portal-image-2.png)

4. Ange kvarhållningsintervallet i dagar (godtagbara värden är 1 till 146000 dagar).

    ![Rutan "Uppdatera kvarhållningsperiod till"](media/storage-blob-immutability-policies-manage/portal-image-5-retention-interval.png)

    Det ursprungliga tillståndet för principen är upplåst så att du kan testa funktionen och göra ändringar i principen innan du låser den. Låsa politiken är viktigt för att följa regler som SEC 17a-4.

5. Lås principen. Högerklicka på ellipsen (**...**), och följande meny visas med ytterligare åtgärder:

    !["Lås princip" på menyn](media/storage-blob-immutability-policies-manage/portal-image-4-lock-policy.png)

6. Välj **Lås princip** och bekräfta låset. Principen är nu låst och kan inte tas bort, endast tillägg av kvarhållningsintervallet tillåts. Blob-borttagningar och åsidosättningar är inte tillåtna. 

    ![Bekräfta "Lås policy" på menyn](media/storage-blob-immutability-policies-manage/portal-image-5-lock-policy.png)

7. Om du vill aktivera juridiska spärrar väljer du **Lägg till princip**. Välj **Juridisk spärr** på rullgardinsmenyn.

    !["Juridiskt spärr" på menyn under "Policytyp"](media/storage-blob-immutability-policies-manage/portal-image-legal-hold-selection-7.png)

8. Skapa en juridisk spärr med en eller flera taggar.

    ![Rutan "Taggnamn" under principtypen](media/storage-blob-immutability-policies-manage/portal-image-set-legal-hold-tags.png)

9. Om du vill ta bort en juridisk spärr tar du bort den tillämpade juridiska spärridentifieraren.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Funktionen ingår i följande `az storage container immutability-policy` kommandogrupper: `az storage container legal-hold`och . Kör `-h` på dem för att se kommandona.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Az.Storage-modulen stöder oföränderlig lagring.  Så här aktiverar du funktionen:

1. Kontrollera att du har den senaste `Install-Module PowerShellGet –Repository PSGallery –Force`versionen av PowerShellGet installerad: .
2. Ta bort alla tidigare installationer av Azure PowerShell.
3. Installera Azure PowerShell: `Install-Module Az –Repository PSGallery –AllowClobber`.

Följande exempel på PowerShell-skript är som referens. Det här skriptet skapar ett nytt lagringskonto och en behållare. Den visar sedan hur du ställer in och rensar juridiska spärrar, skapar och låser en tidsbaserad bevarandeprincip (kallas även en princip för oföränderlighet) och utökar kvarhållningsintervallet.

Skapa först ett Azure Storage-konto:

```powershell
$resourceGroup = "<Enter your resource group>"
$storageAccount = "<Enter your storage account name>"
$container = "<Enter your container name>"
$location = "<Enter the storage account location>"

# Log in to Azure
Connect-AzAccount
Register-AzResourceProvider -ProviderNamespace "Microsoft.Storage"

# Create your Azure resource group
New-AzResourceGroup -Name $resourceGroup -Location $location

# Create your Azure storage account
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup -StorageAccountName `
    $storageAccount -SkuName Standard_ZRS -Location $location -Kind StorageV2

# Create a new container using the context
$container = New-AzStorageContainer -Name $container -Context $account.Context

# List the containers in the account
Get-AzStorageContainer -Context $account.Context

# Remove a container
Remove-AzStorageContainer -Name $container -Context $account.Context
```

Ställ in och tydliga juridiska spärrar:

```powershell
# Set a legal hold
Add-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag1>,<tag2>,...

# Clear a legal hold
Remove-AzRmStorageContainerLegalHold -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -Name $container -Tag <tag3>
```

Skapa eller uppdatera tidsbaserade principer för oföränderlighet:

```powershell
# Create a time-based immutablity policy
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10
```

Hämta principer för oföränderlighet:

```powershell
# Get an immutability policy
Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container
```

Lås principer för oföränderlighet (lägg till `-Force` för att avvisa prompten):

```powershell
# Lock immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container
Lock-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container `
    -Etag $policy.Etag
```

Utöka principerna för oföränderlighet:

```powershell
# Extend immutability policies
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Set-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy `
    $policy -ImmutabilityPeriod 11 -ExtendPolicy
```

Ta bort en olåst `-Force` princip för oföränderlighet (lägg till för att avvisa prompten):

```powershell
# Remove an unlocked immutability policy
$policy = Get-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName `
    $resourceGroup -StorageAccountName $storageAccount -ContainerName $container

Remove-AzRmStorageContainerImmutabilityPolicy -ImmutabilityPolicy $policy
```

---

## <a name="enabling-allow-protected-append-blobs-writes"></a>Aktivera tillåta skyddade tillskrivna blobbar skrivningar

### <a name="portal"></a>[Portal](#tab/azure-portal)

![Tillåt ytterligare tilläggsskrivningar](media/storage-blob-immutability-policies-manage/immutable-allow-additional-append-writes.png)

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Funktionen ingår i följande `az storage container immutability-policy` kommandogrupper: `az storage container legal-hold`och . Kör `-h` på dem för att se kommandona.

### <a name="powershell"></a>[Powershell](#tab/azure-powershell)

```powershell
# Create an immutablity policy with appends allowed
Set-AzRmStorageContainerImmutabilityPolicy -ResourceGroupName $resourceGroup `
    -StorageAccountName $storageAccount -ContainerName $container -ImmutabilityPeriod 10 -AllowProtectedAppendWrite $true
```

---

## <a name="next-steps"></a>Nästa steg

[Lagra affärskritiska blob-data med oföränderlig lagring](storage-blob-immutable-storage.md)
