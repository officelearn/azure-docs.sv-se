---
title: Felsöka fel när du tar bort Azure klassiska lagringskonton, behållare eller virtuella hårddiskar | Microsoft Docs
description: Så här felsöker du problem när du tar bort lagringsresurser som innehåller anslutna virtuella hårddiskar.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 673101ad7f55969c216adf7e970402a2109f8254
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60864344"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Felsöka fel vid borttagning av klassisk lagring resurs
Den här artikeln innehåller felsökningsinformation när något av följande fel inträffar försöker ta bort klassiska lagringskontot, behållaren eller *.vhd sidan blob-fil. 


Den här artikeln täcker endast problem med klassiska lagringsresurser. Om en användare tar bort en klassisk virtuell dator med hjälp av Azure portal, PowerShell eller CLI och sedan diskarna inte bort automatiskt. Användaren får möjlighet att ta bort resursen ”Disk”. Om alternativet inte är markerat, förhindrar ”Disk” resursen borttagning av lagringskontot, behållaren och faktiska *.vhd blob växlingsfilen.

Mer information om Azure-diskar finns [här](../../virtual-machines/windows/managed-disks-overview.md). Azure förhindrar borttagning av en disk som är kopplad till en virtuell dator för att förhindra skador. Det förhindrar även att borttagningen av behållare och storage-konton som har en sidblobb som är kopplad till en virtuell dator. 

## <a name="what-is-a-disk"></a>Vad är en ”Disk”?
En ”Disk”-resurs används för att montera en *.vhd page blob-fil till en virtuell dator som en OS-disk eller datadisk. En OS-disk eller Data diskresursen, tills du tar bort, fortsätter att vänta ett lån *.vhd-filen. Alla lagringsresurs i sökvägen som visas i bilden nedan kan inte tas bort om en ”Disk”-resurs som pekar på den.

![Skärmbild av portalen med den disk (klassisk) ”Property”-rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Steg när du tar bort en klassisk virtuell dator 
1. Ta bort klassisk virtuell dator.
2. Om kryssrutan ”diskar” är markerad i **diskens livslängd** (visas i bilden ovan) som är associerade med sidans blob *.vhd bryts. Faktisk sida *.vhd blobfil finns kvar i lagringskontot.
![Skärmbild av portalen med den virtuella dator (klassisk) ”ta bort” fel rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. När lånet diskarna har brutits kan kan de sida BLOB(ar) själva tas bort. Ett lagringskonto eller en behållare kan tas bort när alla ”Disk”-resurs som finns i dem tas bort.

>[!NOTE] 
>Om användaren tar bort den virtuella datorn men inte den virtuella Hårddisken, fortsätter lagringskostnaderna att tillkomma av växlingsfilen blob *.vhd. Avgifterna beräknas i enlighet med typ av storage-konto, kontrollera den [prissättningssidan](https://azure.microsoft.com/en-us/pricing/details/storage/) för mer information. Om användaren inte längre avser att använda de virtuella hårddiskarna bort it/dem för att undvika framtida avgifter. 

## <a name="unable-to-delete-storage-account"></a>Det går inte att ta bort lagringskonto 

När användare försöker ta bort ett klassiskt lagringskonto som inte längre behövs, kan användaren visas på följande.

#### <a name="azure-portal"></a>Azure Portal 
Användaren navigerar till det klassiska lagringskontot på den [Azure-portalen](https://portal.azure.com) och klickar på **ta bort**, visas följande meddelande: 

Med diskar som är ”kopplade” till en virtuell dator

![Skärmbild av portalen med den virtuella dator (klassisk) ”ta bort” fel rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Med diskar som är ”frånkopplad” till en virtuell dator

![Skärmbild av portalen med den virtuella dator (klassisk) ”ta bort” utan fel rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Användaren försöker ta bort ett lagringskonto som inte längre används, med klassiska PowerShell-cmdlets. Användaren visas följande meddelande:

> <span style="color:cyan">**Remove-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Storage-konto myclassicaccount har några aktiva avbildningar och/eller diskar, t.ex.  
> myclassicaccount. Se till att dessa avbildningar och/eller diskar avlägsnas innan lagringskontot tas bort.</span>

## <a name="unable-to-delete-storage-container"></a>Det går inte att ta bort lagringsbehållare

När användare försöker ta bort en klassisk lagring-blob-behållare som inte längre behövs, kan användaren visas på följande.

#### <a name="azure-portal"></a>Azure Portal 
Azure-portalen Tillåt inte användare att ta bort en behållare om ett ”diskar” lån finns som pekar på en blobfil *.vhd sidan i behållaren. Det är att förhindra oavsiktlig borttagning av en fil med virtuella hårddiskar med diskarna lånet på dem. 

![Skärmbild av portalen med fönstret storage-behållare ”lista” Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Om användaren väljer att ta bort med hjälp av PowerShell, resulterar det i följande fel. 

> <span style="color:cyan">**Remove-AzureStorageContainer -Context $context -Name vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : Fjärrservern returnerade ett fel: (412) det finns för närvarande ett lån för behållaren och inga lån-ID har angetts i begäran... HTTP-statuskod: 412 - HTTP-felmeddelande: Det finns för närvarande ett lån för behållaren och inga lån-ID har angetts i begäran.</span>

## <a name="unable-to-delete-a-vhd"></a>Det går inte att ta bort en virtuell hårddisk 

När du tar bort virtuella Azure-datorer, användare som försöker att ta bort den virtuella hårddiskfilen (sidblob) och ta emot meddelandet nedan:

#### <a name="azure-portal"></a>Azure Portal 
På portalen kan det finnas två upplevelser beroende på listan över blobar som har markerats för borttagning.

1. Om det bara ”utlånad” BLOB-objekt har markerats kan sedan visas ta bort-knappen inte.
![Skärmbild av portalen med den behållare blob ”lista”-rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Om en blandning av ”utlånad” och ”tillgänglig” BLOB-objekt har valts, visas knappen ”Ta bort”. Men lämnar efter sidblobbar, som har ett Disk-lån på dem. ”Ta bort”-åtgärd. 
![Skärmbild av portalen med fönstret container blob ”list” öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Skärmbild av portalen med den valda bloben ”ta bort” fönstret öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Om användaren väljer att ta bort med hjälp av PowerShell, resulterar det i följande fel. 

> <span style="color:cyan">**Remove-AzureStorageBlob -Context $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : Fjärrservern returnerade ett fel: (412) det finns för närvarande ett lån på blobben och inga lån-ID har angetts i begäran... HTTP-statuskod: 412 - HTTP-felmeddelande: Det finns för närvarande ett lån på blobben och inga lån-ID har angetts i begäran.</span>


## <a name="resolution-steps"></a>Lösningsanvisningar

### <a name="to-remove-classic-disks"></a>Ta bort klassiska diskar
Följ dessa steg på Azure portal:
1.  Navigera till [Azure-portalen](https://portal.azure.com).
2.  Navigera till Disks(classic). 
3.  Klicka på fliken Diskar. ![Skärmbild av portalen med den behållare blob ”lista”-rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Välj din datadisk och klicka sedan på Ta bort disk.
 ![Skärmbild av portalen med den behållare blob ”lista”-rutan Öppna](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Försöka göra om borttagningen som tidigare misslyckats.
6.  Ett lagringskonto eller en behållare kan inte tas bort så länge den har en enda Disk.

### <a name="to-remove-classic-images"></a>Ta bort klassiska avbildningar   
Följ dessa steg på Azure portal:
1.  Navigera till [Azure-portalen](https://portal.azure.com).
2.  Gå till OS-avbildningar (klassisk).
3.  Ta bort avbildningen.
4.  Försöka göra om borttagningen som tidigare misslyckats.
5.  Ett lagringskonto eller en behållare kan inte tas bort så länge den har en enda avbildning.
