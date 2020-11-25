---
title: Felsöka fel vid borttagning av lagrings resurser på virtuella Linux-datorer i Azure | Microsoft Docs
description: Lär dig hur du felsöker problem på virtuella Linux-datorer när du tar bort lagrings resurser som innehåller anslutna virtuella hård
keywords: ''
services: virtual-machines
author: genlin
manager: dcscontentpm
tags: top-support-issue,azure-service-management,azure-resource-manager
ms.service: virtual-machines
ms.tgt_pltfrm: vm-linux
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: 8d727bc8bdc8f015504baa57f9596b3bacac9712
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96022878"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Felsöka fel vid borttagning av lagringsresurs

I vissa fall kan något av följande fel uppstå när du försöker ta bort ett Azure Storage-konto, en behållare eller en BLOB i en Azure Resource Manager-distribution:

> **Det gick inte att ta bort lagrings kontot StorageAccountName. Fel: lagrings kontot kan inte tas bort eftersom dess artefakter används.**
> 
> **Det gick inte att ta bort # av # containers (s): <br> VHD: er är för närvarande ett lån i behållaren och inget låne-ID har angetts i begäran.**
> 
> **Det gick inte att ta bort # av # blobbar: <br> BlobName. VHD: det finns för närvarande ett lån i blobben och inget låne-ID har angetts i begäran.**

De virtuella hård diskar som används i virtuella Azure-datorer är. VHD-filer som lagras som Page blobbar i ett standard-eller Premium lagrings konto i Azure. Mer information om Azure-diskar finns i [Introduktion till Managed disks](../managed-disks-overview.md).

Azure förhindrar att en disk som är ansluten till en virtuell dator tas bort för att förhindra skada. Det förhindrar också borttagning av behållare och lagrings konton som har en sid-blob som är kopplad till en virtuell dator. 

Processen för att ta bort ett lagrings konto, behållare eller BLOB när något av dessa fel tas emot är: 
1. Identifiera blobbar som är kopplade till en virtuell dator
2. [Ta bort virtuella datorer med en ansluten **OS-disk**](#step-2-delete-vm-to-detach-os-disk)
3. [Koppla från alla **data diskar** från återstående virtuella datorer](#step-3-detach-data-disk-from-the-vm)

Försök att ta bort lagrings kontot, behållaren eller blobben igen efter att dessa steg har slutförts.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Steg 1: identifiera bloben som är kopplad till en virtuell dator

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: ta bort en BLOB – identifiera kopplad virtuell dator
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i hubbmenyn. Gå till lagrings kontot, under **BLOB service** , Välj **behållare** och navigera till bloben som ska tas bort.
3. Om BLOB- **lånets tillstånd** är **lånat** högerklickar du och väljer **Redigera metadata** för att öppna fönstret BLOB-metadata. 

    ![Skärm bild av portalen med lagrings kontots blobbar och högerklicka på > "redigera metadata" markerat](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. I fönstret BLOB-metadata, kontrollerar du och registrerar värdet för **MicrosoftAzureCompute_VMName**. Det här värdet är namnet på den virtuella dator som den virtuella hård disken är kopplad till. (Se **viktigt** om det här fältet inte finns)
5. I fönstret BLOB-metadata, kontrollerar du och registrerar värdet för **MicrosoftAzureCompute_DiskType**. Det här värdet anger om den anslutna disken är operativ system eller data disk (se **viktigt** om fältet inte finns). 

     ![Skärm bild av portalen med fönstret för BLOB-metadata för lagring öppen](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Om typen av BLOB-disken är **OSDisk** följer [du steg 2: ta bort virtuell dator för att koppla från OS-disken](#step-2-delete-vm-to-detach-os-disk). Annars, om blobb disk typen är **DataDisk** , följer du stegen i [steg 3: koppla från datadisk från den virtuella datorn](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Om **MicrosoftAzureCompute_VMName** och **MicrosoftAzureCompute_DiskType** inte visas i BLOB-metadata, anger det att blobben uttryckligen lånas ut och inte är kopplat till en virtuell dator. Det går inte att ta bort lånade blobbar utan att först bryta lånet. Om du vill avbryta lånet högerklickar du på blobben och väljer **Bryt lån**. Lånade blobbar som inte är kopplade till en virtuell dator förhindrar borttagning av blobben, men förhindrar inte borttagning av behållare eller lagrings konton.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: ta bort en behållare – identifiera alla blobar i behållaren som är kopplade till virtuella datorer
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i hubbmenyn. Gå till lagrings kontot, under **BLOB service** , Välj **behållare** och Sök efter den behållare som ska tas bort.
3. Klicka för att öppna behållaren och listan över blobbar i den visas. Identifiera alla blobbar med Blob-typ = **Page BLOB** och Lease State = **lånas** från den här listan. Följ scenario 1 för att identifiera den virtuella datorn som är associerad med var och en av dessa blobbar.

    ![Skärm bild av portalen med lagrings kontots blobbar och "låne status" med "lånat" markerat](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [3](#step-3-detach-data-disk-from-the-vm) för att ta bort VM: ar med **OSDisk** och koppla från **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: ta bort lagrings konto – identifiera alla blobar i lagrings kontot som är kopplade till virtuella datorer
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser** i hubbmenyn. Gå till lagrings kontot under **BLOB service** Select **blobs**.
3. I rutan **behållare** , identifierar du alla behållare **där låne status** är **lånat** och följer [Scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) för varje **lånad** behållare.
4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [3](#step-3-detach-data-disk-from-the-vm) för att ta bort VM: ar med **OSDisk** och koppla från **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Steg 2: ta bort virtuell dator för att koppla från OS-disk
Om den virtuella hård disken är en OS-disk måste du ta bort den virtuella datorn innan du kan ta bort den anslutna virtuella hård disken. Ingen ytterligare åtgärd krävs för data diskar som är anslutna till samma virtuella dator när dessa steg har slutförts:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn hubb väljer du **Virtual Machines**.
3. Välj den virtuella dator som den virtuella hård disken är kopplad till.
4. Se till att inget aktivt använder den virtuella datorn och att du inte längre behöver den virtuella datorn.
5. Överst i informations fönstret för **virtuell dator** väljer du **ta bort** och klickar sedan på **Ja** för att bekräfta.
6. Den virtuella datorn bör tas bort, men den virtuella hård disken kan behållas. Den virtuella hård disken bör dock inte längre kopplas till en virtuell dator eller ha ett lån på den. Det kan ta några minuter innan lånet har frigjorts. Om du vill kontrol lera att lånet har frigjorts bläddrar du till BLOB-platsen och i fönstret **Egenskaper för BLOB** ska **låne statusen** vara **tillgänglig**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Steg 3: koppla från datadisk från den virtuella datorn
Om den virtuella hård disken är en datadisk kopplar du från den virtuella hård disken från den virtuella datorn för att ta bort lånet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. På menyn hubb väljer du **Virtual Machines**.
3. Välj den virtuella dator som den virtuella hård disken är kopplad till.
4. Välj **diskar** i informations fönstret för den **virtuella datorn** .
5. Välj den datadisk som du vill ta bort den virtuella hård disken som är ansluten till. Du kan avgöra vilken blob som är kopplad till disken genom att kontrol lera den virtuella hård diskens URL.
6. Du kan verifiera BLOB-platsen genom att klicka på disken för att kontrol lera sökvägen i fältet för **VHD-URI** .
7. Välj **Redigera** överst i **disk** fönstret.
8. Klicka på **Koppla bort ikonen** för den datadisk som ska tas bort.

     ![Skärm bild av portalen, där fönstret för BLOB-metadata för lagring är öppet, och den visar diskoppla-ikonen på den datadisk som ska tas bort.](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Välj **Spara**. Disken är nu frånkopplad från den virtuella datorn och den virtuella hård disken är inte längre lånad. Det kan ta några minuter innan lånet har frigjorts. För att kontrol lera att lånet har frigjorts bläddrar du till BLOB-platsen och i fönstret **Egenskaper för BLOB** måste **låne status** vara **låst** eller **tillgänglig**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

