---
title: "Felsöka när du tar bort Azure storage-konton, behållare eller virtuella hårddiskar | Microsoft Docs"
description: "Felsöka när du tar bort Azure storage-konton, behållare eller virtuella hårddiskar"
services: storage
documentationcenter: 
author: passaree
manager: cshepard
editor: na
tags: storage
ms.assetid: 17403aa1-fe8d-45ec-bc33-2c0b61126286
ms.service: storage
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 11/03/2017
ms.author: genli
ms.openlocfilehash: 7a3c9422887592c29c2eae8bd75cf960865808fd
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/04/2017
---
# <a name="troubleshoot-storage-delete-errors-in-resource-manager-deployment"></a>Felsöka lagring delete i Resource Manager-distribution
Den här artikeln innehåller felsökningsinformation när något av följande fel uppstår när du försöker ta bort Azure storage-konto, behållare eller blobb i Azure Resource Manager distribution.

>**Det gick inte att ta bort lagringskontot 'StorageAccountName'. Fel: Lagringskontot kan inte tas bort eftersom dess artefakter används.**

>**Det gick inte att ta bort # utanför # behållare:<br>virtuella hårddiskar: det finns för närvarande ett lån på behållaren och inga lease-ID har angetts i begäran.**

>**Det gick inte att ta bort # utanför # BLOB:<br>BlobName.vhd: det finns för närvarande ett lån på blob och inga lease-ID har angetts i begäran.**

De virtuella hårddiskarna som används i Azure Virtual Machines är VHD-filer som lagras som sidblobbar i en standard- eller premium storage-konto i Azure.  Mer information om Azure-diskar kan hittas [här](../../virtual-machines/windows/about-disks-and-vhds.md). Azure förhindrar borttagning av en disk som är kopplad till en virtuell dator för att förhindra skador. Den förhindrar också att borttagningen av behållare och storage-konton som har en sidblob som är kopplad till en virtuell dator. 

## <a name="solution"></a>Lösning
Processen för att ta bort ett lagringskonto, behållare eller blob när något av ovanstående fel är: 
1. [Identifiera blobbar som är kopplad till en virtuell dator](#step-1-identify-blobs-attached-to-a-vm)
2. [Ta bort virtuella datorer med anslutna **OS-disk**](#step-2-delete-vm-to-detach-os-disk)
3. [Koppla från alla **data diskarna** från återstående virtuella datorer](#step-3-detach-data-disk-from-the-vm)

Försök ta bort lagringskontot, behållare eller blob när ovanstående steg har slutförts.

### <a name="step-1-identify-blob-attached-to-a-vm"></a>Steg 1: Identifiera blob som är kopplad till en virtuell dator

#### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: Ta bort en blobb – identifiera anslutna VM
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blob-tjänsten** Välj **behållare** och navigera till blob som ska tas bort.
3. Om blob **Lease-tillstånd** är **Leased**, högerklicka och välj **redigera Metadata** att öppna fönstret för Blob-metadata. 

    ![Skärmbild av portalen med lagringen kontot blobbar och högerklicka på > ”Redigera Metadata” hilighted](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-edit-metadata-sm.png)

4. Kontrollera i Blob metadata-panelen och anteckna värdet för **MicrosoftAzureCompute_VMName**. Det här värdet är namnet på den virtuella datorn som den virtuella Hårddisken är ansluten till. (Se **viktiga** om fältet inte finns)
5. Kontrollera i Blob metadata-panelen och anteckna värdet för **MicrosoftAzureCompute_DiskType**. Detta anger om bifogade disken är OS eller data (se **viktiga** om fältet inte finns). 

     ![Skärmbild av portalen med lagring ”Blob” rutan Metadata öppna](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-blob-metadata-sm.png)

6. Om disktypen blob är **OSDisk** följer [steg 2: ta bort VM för att ta bort OS-disken](#step-2-delete-vm-to-detach-os-disk). Annars, om disktypen blob är **DataDisk** följer du stegen i [steg3: Koppla bort datadisk från den virtuella datorn](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Om **MicrosoftAzureCompute_VMName** och **MicrosoftAzureCompute_DiskType** visas inte i blobmetadata, anger det att blob hyrs uttryckligen och inte är ansluten till en virtuell dator. Lånade blobbar kan inte tas bort utan att bryta lånet första. Bryt lån, högerklickar du på blob och välj **Break lån**. Lånade blob som inte är kopplade till en virtuell dator att borttagningen av blob men förhindra inte borttagning av behållare eller storage-konto.

#### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: Ta bort en behållare – identifiera alla blob(s) i behållaren som är kopplade till virtuella datorer
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blob-tjänsten** Välj **behållare** och hitta behållaren som ska tas bort.
3. Klicka om du vill öppna behållaren och visas listan över blobbar i den. Identifiera alla blobbar med Blobbtypen = **sidblob** och Lease-tillstånd = **Leased** från den här listan. Följ [Scenario 1](#step-1-identify-blobs-attached-to-a-vm) att identifiera den virtuella datorn som är kopplade till var och en av dessa blobbar.

    ![Skärmbild av portalen med konto-Blob Storage och ”lån tillståndet” med ”Leased” markerat](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-disks-sm.png)

4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg3](#step-3-detach-data-disk-from-the-vm) att ta bort virtuella datorer med **OSDisk** och frånkoppling **DataDisk**. 

#### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: Ta bort storage-konto – identifiera alla blob(s) inom lagringskonto som är kopplade till virtuella datorer
1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **alla resurser**. Gå till storage-konto under **Blob-tjänsten** Välj **behållare**.

    ![Skärmbild av portalen med konto-behållare för lagring och ”lån tillståndet” med ”Leased” markerat](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-containers-sm.png)

3. I **behållare** bladet identifiera alla behållare där **Lease-tillstånd** är **Leased** och följ [Scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) för varje  **Hyrd** behållare.
4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg3](#step-3-detach-data-disk-from-the-vm) att ta bort virtuella datorer med **OSDisk** och frånkoppling **DataDisk**. 

### <a name="step-2-delete-vm-to-detach-os-disk"></a>Steg 2: Ta bort virtuell dator och koppla bort OS-disk
Om den virtuella Hårddisken är en OS-disk, måste du ta bort den virtuella datorn innan den anslutna virtuella Hårddisken kan tas bort. Ingen ytterligare åtgärd måste utföras för datadiskar som är kopplade till samma virtuella dator när dessa steg har slutförts:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **virtuella datorer**.
3. Välj den virtuella datorn som den virtuella Hårddisken är ansluten till.
4. Kontrollera att inget aktivt använder den virtuella datorn, och du inte längre behöver den virtuella datorn.
5. Längst upp i den **information om den virtuella datorn** bladet väljer **ta bort**, och klicka sedan på **Ja** att bekräfta.
6. Den virtuella datorn ska tas bort, men den virtuella Hårddisken kan behållas. Den virtuella Hårddisken inte längre att ansluta till en virtuell dator eller ha ett lån på den. Det kan ta några minuter för lånet frigörs. Om du vill verifiera att lånet släpps, bläddra till blobbplats och i den **Blob-egenskaper** fönstret den **lån Status** ska vara **tillgänglig**.

### <a name="step-3-detach-data-disk-from-the-vm"></a>Steg 3: Koppla från datadisk från den virtuella datorn
Om den virtuella Hårddisken är en datadisk, kan du koppla från den virtuella Hårddisken från den virtuella datorn att ta bort lånet:

1. Logga in på [Azure Portal](https://portal.azure.com).
2. På navmenyn väljer **virtuella datorer**.
3. Välj den virtuella datorn som den virtuella Hårddisken är ansluten till.
4. Välj **diskar** på den **information om den virtuella datorn** bladet.
5. Välj datadisk för att ta bort den virtuella Hårddisken är ansluten till. Du kan bestämma vilka blob är ansluten i disken genom att kontrollera URL: en för den virtuella Hårddisken.
6. Du kan verifiera blobbplats genom att klicka på disken för att kontrollera sökvägen **VHD-URI** fältet.
7. Välj **redigera** ovanpå på **diskar** bladet.
8. Klicka på **frånkoppling ikonen** på datadisken för att tas bort.

     ![Skärmbild av portalen med lagring ”Blob” rutan Metadata öppna](./media/storage-resource-manager-cannot-delete-storage-account-container-vhd/utd-vm-disks-edit.png)

9. Välj **Spara**. Disken nu att koppla från den virtuella datorn och den virtuella Hårddisken inte längre ska ha ett lån på den. Det kan ta några minuter för lånet frigörs. Om du vill verifiera att lånet har släppts, bläddra till blobbplats och i den **Blob-egenskaper** fönstret den **lån Status** . värdet bör vara **olåst** eller **Tillgängliga**.

## <a name="next-steps"></a>Nästa steg
Försök lagringsobjekt som tidigare hade inte tas bort.

