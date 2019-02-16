---
title: Felsöka storage resource borttagning på virtuella Linux-datorer i Azure | Microsoft Docs
description: Så här felsöker du problem när du tar bort lagringsresurser som innehåller anslutna virtuella hårddiskar.
keywords: ''
services: virtual-machines
author: genlin
manager: cshepard
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 19a0e7f3317a5c4a87b2622de170b0fc2cc137be
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/16/2019
ms.locfileid: "56326831"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Felsöka fel vid borttagning av storage-resurs

I vissa fall, kan du stöta på något av följande fel inträffar när du försöker ta bort ett Azure storage-konto, behållare eller blob i en Azure Resource Manager-distribution:

>**Det gick inte att ta bort lagringskontot 'StorageAccountName'. Fel: Storage-konto kan inte tas bort eftersom dess artefakter används.**

>**Det gick inte att ta bort # utanför # behållare:<br>virtuella hårddiskar: Det finns för närvarande ett lån för behållaren och inga lån-ID har angetts i begäran.**

>**Det gick inte att ta bort # utanför # blobar:<br>BlobName.vhd: Det finns för närvarande ett lån på blobben och inga lån-ID har angetts i begäran.**

De virtuella hårddiskarna i virtuella datorer i Azure är .vhd-filer som lagras som sidblobar i en standard- eller premium storage-konto i Azure. Mer information om Azure-diskar finns i vår [introduktion till hanterade diskar](../linux/managed-disks-overview.md).

Azure förhindrar borttagning av en disk som är kopplad till en virtuell dator för att förhindra skador. Det förhindrar även att borttagningen av behållare och storage-konton som har en sidblobb som är kopplad till en virtuell dator. 

Processen för att ta bort ett lagringskonto, behållare eller blob när du tar emot en av de här felen är: 
1. Identifiera blobbar som är ansluten till en virtuell dator
2. [Ta bort virtuella datorer med anslutna **OS-disk**](#step-2-delete-vm-to-detach-os-disk)
3. [Koppla från alla **datadiskarna** från återstående virtuella datorer](#step-3-detach-data-disk-from-the-vm)

Tar bort lagringskontot, behållaren eller den blob när dessa steg har slutförts och försök igen.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Steg 1: Identifiera blob som är ansluten till en virtuell dator

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: Ta bort en blob – identifiera anslutna VM
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blobtjänsten** Välj **behållare**, och navigera till blobben att ta bort.
3. Om bloben **Lånestillstånd** är **utlånad**, högerklicka och välj **redigera Metadata** att öppna fönstret för Blob-metadata. 

    ![Skärmbild av portalen med Storage account BLOB-objekt och högerklicka på > ”Redigera Metadata” som markerat](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. I rutan för Blob-metadata, kontrollera och anteckna värdet för **MicrosoftAzureCompute_VMName**. Det här värdet är namnet på den virtuella datorn som den virtuella Hårddisken är kopplad till. (Se **viktiga** om fältet inte finns)
5. I rutan för Blob-metadata, kontrollera och anteckna värdet för **MicrosoftAzureCompute_DiskType**. Det här värdet anger om den anslutna disken är OS- eller datadisken (se **viktiga** om fältet inte finns). 

     ![Skärmbild av portalen med fönstret storage ”Blobbmetadata” öppna](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Om disktypen blob är **OSDisk** följer [steg 2: Ta bort virtuell dator för att koppla från OS-disken](#step-2-delete-vm-to-detach-os-disk). Om disktypen blob är **DataDisk** följer du stegen i [steg3: Koppla från datadisk från den virtuella datorn](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Om **MicrosoftAzureCompute_VMName** och **MicrosoftAzureCompute_DiskType** visas inte i blobmetadata, betyder det att blobben hyrs uttryckligen och är inte kopplat till en virtuell dator. Kan inte tas bort utlånat blobar utan avbryter lånet första. Att avbryta lånet, högerklicka på blobben och välja **lease Break**. Utlånat blobar som inte är kopplade till en virtuell dator att borttagningen av bloben, men förhindra inte borttagning av behållaren eller lagringskontot.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: Ta bort en behållare – identifiera alla blobbarna i behållaren som är kopplade till virtuella datorer
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blobtjänsten** Välj **behållare**, och hitta behållaren som ska tas bort.
3. Klicka för att öppna behållaren och kommer att visas i listan över blobar i den. Identifiera alla blobbar med Blobbtypen = **sidblob** och Lånestillstånd = **utlånad** i den här listan. Följ Scenario 1 för att identifiera den virtuella datorn som är associerade med var och en av dessa blobar.

    ![Skärmbild av portalen med lagringskontoblobbar och den ”Lånestillstånd” med ”utlånad” markerat](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg3](#step-3-detach-data-disk-from-the-vm) att ta bort virtuella datorer med **OSDisk** och koppla från **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: Tar bort storage account – identifiera alla blobbarna i storage-konto som är kopplade till virtuella datorer
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blobtjänsten** Välj **Blobar**.
3. I **behållare** fönstret identifiera alla behållare där **Lånestillstånd** är **utlånad** och följ [Scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) för varje  **Lånade** behållare.
4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg3](#step-3-detach-data-disk-from-the-vm) att ta bort virtuella datorer med **OSDisk** och koppla från **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Steg 2: Ta bort virtuell dator för att koppla från OS-disk
Om den virtuella Hårddisken är en OS-disk, måste du ta bort den virtuella datorn innan den anslutna virtuella Hårddisken kan tas bort. Ingen ytterligare åtgärd måste utföras för diskar som är anslutna till samma virtuella dator när de här stegen har slutförts:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **virtuella datorer**.
3. Välj den virtuella datorn som den virtuella Hårddisken är kopplad till.
4. Kontrollera att inget aktivt använder den virtuella datorn, och att du inte längre behöver den virtuella datorn.
5. Överst på den **information om den virtuella datorn** väljer **ta bort**, och klicka sedan på **Ja** att bekräfta.
6. Den virtuella datorn ska tas bort, men den virtuella Hårddisken kan bevaras. Den virtuella Hårddisken inte längre ska kopplas till en virtuell dator eller har ett lån på den. Det kan ta några minuter för lånet frigörs. För att verifiera att lånet upphör, bläddra till blobbplats och i den **Blobegenskaper** fönstret den **lånestatus** ska vara **tillgänglig**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Steg 3: Koppla från datadisk från den virtuella datorn
Om den virtuella Hårddisken är en datadisk, koppla från den virtuella Hårddisken från den virtuella datorn att ta bort lånet:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **virtuella datorer**.
3. Välj den virtuella datorn som den virtuella Hårddisken är kopplad till.
4. Välj **diskar** på den **information om den virtuella datorn** fönstret.
5. Välj datadisken som som ska tas bort den virtuella Hårddisken är kopplad till. Du kan bestämma vilka blob är ansluten i disken genom att kontrollera URL: en för den virtuella Hårddisken.
6. Du kan kontrollera blobbplats genom att klicka på disken för att kontrollera sökvägen **VHD-URI** fält.
7. Välj **redigera** i **diskar** fönstret.
8. Klicka på **koppla från ikonen** av datadisken som ska tas bort.

     ![Skärmbild av portalen med fönstret storage ”Blobbmetadata” öppna](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Välj **Spara**. Disken är nu oberoende från den virtuella datorn och den virtuella Hårddisken hyrs inte längre. Det kan ta några minuter för lånet frigörs. För att verifiera att lånet har släppts, bläddra till blobbplats och i den **Blobegenskaper** fönstret den **lånestatus** . värdet bör vara **olåst** eller **Tillgängliga**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

