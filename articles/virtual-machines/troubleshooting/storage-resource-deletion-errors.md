---
title: Felsöka fel på virtuella linux-datorer i Azure | Microsoft-dokument
description: Felsöka problem när du tar bort lagringsresurser som innehåller kopplade virtuella hårddiskar.
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
ms.openlocfilehash: 50ab4b0f1e676ffcba0ce69ab6aa957e4c77ab88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71058158"
---
# <a name="troubleshoot-storage-resource-deletion-errors"></a>Felsöka fel på borttagningsfel för lagringsresurser

I vissa fall kan ett av följande fel uppstå när du försöker ta bort ett Azure-lagringskonto, en behållare eller en blob i en Azure Resource Manager-distribution:

> **Det gick inte att ta bort lagringskontot StorageAccountName. Fel: Lagringskontot kan inte tas bort på grund av att dess artefakter används.**
> 
> **Det gick inte att ta bort<br># från #-behållare:vhds: Det finns för närvarande ett lån på behållaren och inget låne-ID angavs i begäran.**
> 
> **Det gick inte att ta bort<br># av # blobbar: BlobName.vhd: Det finns för närvarande ett lån på blobben och inget låne-ID angavs i begäran.**

De virtuella hårddiskar som används i virtuella Azure-datorer är VHD-filer som lagras som sidblobar i ett standard- eller premiumlagringskonto i Azure. Mer information om Azure-diskar finns i vår [Introduktion till hanterade diskar](../linux/managed-disks-overview.md).

Azure förhindrar borttagning av en disk som är kopplad till en virtuell dator för att förhindra skador. Det förhindrar också borttagning av behållare och lagringskonton som har en sidblob som är kopplad till en virtuell dator. 

Processen för att ta bort ett lagringskonto, en behållare eller en blob när du tar emot något av dessa fel är: 
1. Identifiera blobbar som är kopplade till en virtuell dator
2. [Ta bort virtuella datorer med ansluten **OS-disk**](#step-2-delete-vm-to-detach-os-disk)
3. [Koppla bort alla **datadiskar** från återstående virtuella datorer](#step-3-detach-data-disk-from-the-vm)

Försök ta bort lagringskontot, behållaren eller bloben igen när de här stegen har slutförts.

## <a name="step-1-identify-blob-attached-to-a-vm"></a>Steg 1: Identifiera blob kopplad till en virtuell dator

### <a name="scenario-1-deleting-a-blob--identify-attached-vm"></a>Scenario 1: Ta bort en blob – identifiera ansluten virtuell dator
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser**på Hub-menyn . Gå till lagringskontot under **Blob-tjänst** välj **Behållare**och navigera till bloben som du vill ta bort.
3. Om **blob-lånetillståndet** **är hyrt**högerklickar du och väljer **Redigera metadata** för att öppna fönstret Blobmetadata. 

    ![Skärmbild av portalen, med lagringskontoblobar och högerklicka > "Redigera metadata" markerat](./media/troubleshoot-vhds/utd-edit-metadata-sm.png)

4. I fönstret Blobmetadata kontrollerar och registrerar du värdet för **MicrosoftAzureCompute_VMName**. Det här värdet är namnet på den virtuella dator som den virtuella hårddisken är kopplad till. (Se **viktigt** om det här fältet inte finns)
5. I fönstret Blobmetadata kontrollerar och registrerar du värdet **för MicrosoftAzureCompute_DiskType**. Det här värdet identifierar om den anslutna disken är OS eller datadisk (Se **viktigt** om det här fältet inte finns). 

     ![Skärmbild av portalen, med fönstret "Blob-metadata" öppet](./media/troubleshoot-vhds/utd-blob-metadata-sm.png)

6. Om blob-disktypen är **OSDisk** följer [du steg 2: Ta bort den virtuella datorn för att koppla från OS-disken](#step-2-delete-vm-to-detach-os-disk). Annars, om blob disktyp är **DataDisk** följ stegen i [steg 3: Koppla bort datadisk från den virtuella datorn](#step-3-detach-data-disk-from-the-vm). 

> [!IMPORTANT]
> Om **MicrosoftAzureCompute_VMName** och **MicrosoftAzureCompute_DiskType** inte visas i blobmetadata anger det att blobben uttryckligen är leasad och inte är kopplad till en virtuell dator. Hyrda blobbar kan inte tas bort utan att först bryta lånet. Om du vill bryta hyresavtalet högerklickar du på blobben och väljer **Break lease**. Hyrda blobbar som inte är kopplade till en virtuell dator förhindrar borttagning av blobben, men förhindrar inte borttagning av behållare eller lagringskonto.

### <a name="scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms"></a>Scenario 2: Ta bort en behållare – identifiera alla blob/er i behållaren som är kopplade till virtuella datorer
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser**på Hub-menyn . Gå till lagringskontot under **Blob-tjänst** välj **Behållare**och hitta behållaren som ska tas bort.
3. Klicka här om du vill öppna behållaren så visas listan över blobbar inuti den. Identifiera alla blobbar med Blob-typ = **Sidblob** och lånetillstånd = **Hyrs från** den här listan. Följ scenario 1 för att identifiera den virtuella datorn som är associerad med var och en av dessa blobbar.

    ![Skärmbild av portalen, med lagringskontoblobar och "Lånetillstånd" med "Leasat" markerat](./media/troubleshoot-vhds/utd-disks-sm.png)

4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg 3](#step-3-detach-data-disk-from-the-vm) om du vill ta bort virtuella datorer med **OSDisk** och koppla från **DataDisk**. 

### <a name="scenario-3-deleting-storage-account---identify-all-blobs-within-storage-account-that-are-attached-to-vms"></a>Scenario 3: Ta bort lagringskonto - identifiera alla blob(er) i lagringskonto som är kopplade till virtuella datorer
1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Alla resurser**på Hub-menyn . Gå till lagringskontot under **Blob-tjänst** väljer **du Blobbar**.
3. I fönstret **Behållare** identifierar du alla behållare där **lånetillstånd** **hyrs** ut och följer [scenario 2](#scenario-2-deleting-a-container---identify-all-blobs-within-container-that-are-attached-to-vms) för varje **hyrd** behållare.
4. Följ [steg 2](#step-2-delete-vm-to-detach-os-disk) och [steg 3](#step-3-detach-data-disk-from-the-vm) om du vill ta bort virtuella datorer med **OSDisk** och koppla från **DataDisk**. 

## <a name="step-2-delete-vm-to-detach-os-disk"></a>Steg 2: Ta bort virtuell dator för att koppla från OS-disken
Om den virtuella hårddisken är en OS-disk måste du ta bort den virtuella datorn innan den anslutna virtuella hårddisken kan tas bort. Inga ytterligare åtgärder krävs för datadiskar som är anslutna till samma virtuella dator när dessa steg har slutförts:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Virtuella datorer**på Hub-menyn .
3. Välj den virtuella dator som den virtuella hårddisken är kopplad till.
4. Kontrollera att ingenting aktivt använder den virtuella datorn och att du inte längre behöver den virtuella datorn.
5. Högst upp i **informationsfönstret för virtuell dator** väljer du Ta **bort**och klickar sedan på **Ja** för att bekräfta.
6. Den virtuella datorn ska tas bort, men den virtuella hårddisken kan behållas. Den virtuella hårddisken bör dock inte längre vara ansluten till en virtuell dator eller ha ett lån på den. Det kan ta några minuter innan hyresavtalet släpps. Om du vill kontrollera att lånet har frisläppts bläddrar du till blob-platsen och i fönstret **Blob-egenskaper** bör **lånestatus** vara **tillgänglig**.

## <a name="step-3-detach-data-disk-from-the-vm"></a>Steg 3: Koppla bort datadisken från den virtuella datorn
Om den virtuella hårddisken är en datadisk kopplar du från den virtuella datorn från den virtuella datorn för att ta bort lånet:

1. Logga in på [Azure-portalen](https://portal.azure.com).
2. Välj **Virtuella datorer**på Hub-menyn .
3. Välj den virtuella dator som den virtuella hårddisken är kopplad till.
4. Välj **Diskar** i **informationsfönstret för den virtuella datorn.**
5. Välj den datadisk som ska tas bort som den virtuella hårddisken är kopplad till. Du kan bestämma vilken blob som är kopplad till disken genom att kontrollera url:en för den virtuella hårddisken.
6. Du kan verifiera blob-platsen genom att klicka på disken för att kontrollera sökvägen i **VHD URI-fältet.**
7. Välj **Redigera** högst upp i fönstret **Diskar.**
8. Klicka på **ikonen för frånskilja** på den datadisk som ska tas bort.

     ![Skärmbild av portalen, med fönstret "Blob-metadata" öppet](./media/troubleshoot-vhds/utd-vm-disks-edit.png)

9. Välj **Spara**. Disken är nu fristående från den virtuella datorn och den virtuella hårddisken är inte längre uthyrd. Det kan ta några minuter innan hyresavtalet släpps. Om du vill kontrollera att lånet har frisläppts bläddrar du till blob-platsen och i fönstret **Blob-egenskaper** ska värdet **för lånestatus** vara **upplåst** eller **tillgängligt**.

[Storage deletion errors in Resource Manager deployment]: #storage-delete-errors-in-rm

