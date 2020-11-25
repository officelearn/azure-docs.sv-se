---
title: Felsöka fel när du tar bort klassiska Azure-lagringsenheter, behållare eller virtuella hård diskar | Microsoft Docs
description: Felsöka problem när du tar bort lagrings resurser som innehåller anslutna virtuella hård diskar.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002603"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Felsöka fel vid borttagning av klassiska lagrings resurser
Den här artikeln innehåller en fel söknings vägledning när något av följande fel uppstår vid försök att ta bort det klassiska Azure-lagrings kontot, behållaren eller *. VHD-sidans BLOB-fil. 


Den här artikeln beskriver endast problem med klassiska lagrings resurser. Om en användare tar bort en klassisk virtuell dator med hjälp av Azure Portal, PowerShell eller CLI raderas inte diskarna automatiskt. Användaren får alternativet att ta bort "disk"-resursen. Om alternativet inte är markerat förhindrar "disk"-resursen borttagning av lagrings kontot, behållaren och den faktiska *. VHD-sidans BLOB-fil.

Mer information om Azure-diskar hittar du [här](../../virtual-machines/managed-disks-overview.md). Azure förhindrar att en disk som är ansluten till en virtuell dator tas bort för att förhindra skada. Det förhindrar också att behållare och lagrings konton tas bort, som har en sid-blob som är kopplad till en virtuell dator. 

## <a name="what-is-a-disk"></a>Vad är en "disk"?
En "disk"-resurs används för att montera en *. VHD Page-BLOB-fil till en virtuell dator, som en OS-disk eller data disk. En OS-disk eller data disk resurs, tills den har tagits bort, fortsätter att inneha ett lån på filen *. VHD. Alla lagrings resurser i sökvägen som visas i bilden nedan kan inte tas bort om en "disk"-resurs pekar på den.

![Skärm bild av portalen med disk (klassisk) "egenskaps fönstret öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Steg när du tar bort en klassisk virtuell dator 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Ta bort den klassiska virtuella datorn.
2. Om kryss rutan "diskar" är markerad är **disk lånet** (visas i avbildningen ovan) som är kopplat till sidans blob *. VHD bruten. Den faktiska Page blob *. VHD-filen finns kvar i lagrings kontot.
![Skärm bild som visar en dialog ruta för att bekräfta borttagningen av en virtuell dator.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. När disk lånet bryts kan du ta bort själva sid blobben. Du kan ta bort ett lagrings konto eller en behållare när alla resurser i "disk" är tillgängliga i dem.

>[!NOTE] 
>Om användaren tar bort den virtuella datorn men inte den virtuella hård disken fortsätter lagrings avgifterna att påföras på Page blob *. VHD-filen. Avgifterna kommer att vara i linje med typen av lagrings konto, se [sidan med priser](https://azure.microsoft.com/pricing/details/storage/) för mer information. Om användaren inte längre avser att använda de virtuella hård diskarna kan du ta bort den/dem för att undvika framtida avgifter. 

## <a name="unable-to-delete-storage-account"></a>Det gick inte att ta bort lagrings kontot 

När en användare försöker ta bort ett klassiskt lagrings konto som inte längre behövs kan användaren se följande beteende.

#### <a name="azure-portal"></a>Azure Portal 
Användaren går till det klassiska lagrings kontot på [Azure Portal](https://portal.azure.com) och klickar på **ta bort**. då visas följande meddelande: 

Med disk (ar) anslutna till en virtuell dator

![Skärm bild som visar ett meddelande som förklarar varför det inte går att ta bort ett lagrings konto.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Med disk (ar) "unattached" till en virtuell dator

![Skärm bild av portalen med den virtuella datorn (klassisk) "ta bort" som inte är felöppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Användaren försöker ta bort ett lagrings konto som inte längre används, genom att använda klassiska PowerShell-cmdletar. Användaren får följande meddelande:

> <span style="color:cyan">**Remove-AzureStorageAccount-StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: BadRequest: lagrings kontots myclassicaccount innehåller några aktiva avbildningar och/eller disk (er), t. ex.  
> myclassicaccount. Se till att dessa avbildningar och/eller diskar tas bort innan du tar bort det här lagrings kontot.</span>

## <a name="unable-to-delete-storage-container"></a>Det gick inte att ta bort lagrings containern

När en användare försöker ta bort en klassisk lagrings-BLOB-behållare som inte längre behövs kan användaren se följande beteende.

#### <a name="azure-portal"></a>Azure Portal 
Azure Portal tillåter inte att användaren tar bort en behållare om ett "disk (s)"-lån pekar på en *. VHD Page BLOB-fil i behållaren. Det är enligt design för att förhindra oavsiktlig borttagning av en VHD-fil med disk (er) lån på dem. 

![Skärm bild av portalen med fönstret lista över lagrings behållare öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Om användaren väljer att ta bort med hjälp av PowerShell, leder det till följande fel. 

> <span style="color:cyan">**Remove-AzureStorageContainer-context $context-Name VHD**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: fjärrservern returnerade ett fel: (412) det finns för närvarande ett lån i behållaren och inget låne-ID har angetts i begäran.. HTTP-status kod: 412-HTTP-fel meddelande: det finns för närvarande ett lån i behållaren och inget låne-ID har angetts i begäran.</span>

## <a name="unable-to-delete-a-vhd"></a>Det går inte att ta bort en virtuell hård disk 

När du har tagit bort den virtuella Azure-datorn försöker användaren ta bort VHD-filen (Page BLOB) och ta emot meddelandet nedan:

#### <a name="azure-portal"></a>Azure Portal 
På portalen kan det finnas två upplevelser beroende på listan över blobbar som har marker ATS för borttagning.

1. Om endast "lånade" blobbar är markerade visas inte knappen Ta bort.
![Skärm bild av portalen, med list rutan behållare BLOB öppen och endast lånade blobbar har valts.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Om du väljer en blandning av "lånade" och "tillgängliga" blobbar visas knappen Ta bort. Men åtgärden ta bort lämnar sid-blobarna, som har ett disk lån. 
![Skärm bild av portalen med list rutan behållare BLOB öppen och både lånade och tillgängliga blobbar har valts. ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ Skärm bild av portalen med det valda BLOB-fönstret "ta bort" öppet](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Om användaren väljer att ta bort med hjälp av PowerShell, leder det till följande fel. 

> <span style="color:cyan">**Remove-AzureStorageBlob-context $context-container VHD-BLOB "classicvm-OS-8698. VHD"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: fjärrservern returnerade ett fel: (412) det finns för närvarande ett lån i blobben och inget låne-ID har angetts i begäran.. HTTP-status kod: 412-HTTP-fel meddelande: det finns för närvarande ett lån i blobben och inget låne-ID har angetts i begäran.</span>


## <a name="resolution-steps"></a>Lösningssteg

### <a name="to-remove-classic-disks"></a>Ta bort klassiska diskar
Följ de här stegen på Azure Portal:
1.  Navigera till [Azure Portal](https://portal.azure.com).
2.  Navigera till diskarna (klassisk). 
3.  Klicka på fliken diskar. ![ Skärm bild som visar Azure Portal med diskar (klassisk) valda och ett klassiskt disk namn och lagrings konto.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Välj din datadisk och klicka sedan på Ta bort disk.
 ![Skärm bild som visar Azure Portal med diskar (klassisk) vald, med en datadisk vald och alternativet att ta bort.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Gör om borttagnings åtgärden som tidigare misslyckades.
6.  Det går inte att ta bort ett lagrings konto eller en behållare så länge den har en enda disk.

### <a name="to-remove-classic-images"></a>Ta bort klassiska bilder   
Följ de här stegen på Azure Portal:
1.  Navigera till [Azure Portal](https://portal.azure.com).
2.  Navigera till OS-avbildningar (klassisk).
3.  Ta bort avbildningen.
4.  Gör om borttagnings åtgärden som tidigare misslyckades.
5.  Det går inte att ta bort ett lagrings konto eller en behållare så länge den har en enda avbildning.
