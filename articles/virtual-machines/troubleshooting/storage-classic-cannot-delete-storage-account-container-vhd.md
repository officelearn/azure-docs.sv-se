---
title: Felsöka fel när du tar bort klassiska Azure-lagringskonton, behållare eller virtuella hårddiskar | Microsoft-dokument
description: Felsöka problem när du tar bort lagringsresurser som innehåller kopplade virtuella hårddiskar.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: 95c85309058911d6767eb44efd7b37ddac7a9119
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77915045"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Felsöka klassiska fel i borttagning av lagringsresurser
Den här artikeln innehåller felsökningsvägledning när något av följande fel uppstår när du försöker ta bort Azure classic storage-konto, behållare eller *.vhd-sidblobbfil. 


Den här artikeln täcker bara problem med klassiska lagringsresurser. Om en användare tar bort en klassisk virtuell dator med Azure-portalen, PowerShell eller CLI tas diskarna inte bort automatiskt. Användaren får möjlighet att ta bort resursen "Disk". Om alternativet inte är markerat förhindrar resursen "Disk" borttagning av lagringskontot, behållaren och den faktiska *.vhd-sidblobbfilen.

Mer information om Azure-diskar finns [här](../../virtual-machines/windows/managed-disks-overview.md). Azure förhindrar borttagning av en disk som är kopplad till en virtuell dator för att förhindra skador. Det förhindrar också borttagning av behållare och lagringskonton, som har en sidblob som är kopplad till en virtuell dator. 

## <a name="what-is-a-disk"></a>Vad är en "Disk"?
En "Disk"-resurs används för att montera en *.vhd-sidblobfil på en virtuell dator, som en OS-disk eller datadisk. En OS-disk eller datadiskresurs, tills den har tagits bort, fortsätter att hålla ett lån på filen *.vhd. Alla lagringsresurser i sökvägen som visas i bilden nedan kan inte tas bort om en "Disk"-resurs pekar på den.

![Skärmbild av portalen, med fönstret "Klassisk) egenskap" öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Steg när du tar bort en klassisk virtuell dator 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Ta bort den klassiska virtuella datorn.
2. Om kryssrutan "Diskar" är markerad bryts **disklånet** (som visas i bilden ovan) som är associerat med sidblobben *.vhd. Den faktiska sidblobben *.vhd-filen finns fortfarande kvar i lagringskontot.
![Skärmbild av portalen, med den virtuella datorn (klassisk) "Ta bort" felfönstret öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. När disken/diskar-lånet har brutits kan själva sidbloben tas bort. Ett lagringskonto eller en behållare kan tas bort när alla "Disk"-resurser som finns i dem har tagits bort.

>[!NOTE] 
>Om användaren tar bort den virtuella datorn men inte den virtuella hårddisken fortsätter lagringsavgifterna att ackumuleras på sidan blob *.vhd-filen. Avgifterna kommer att vara i linje med vilken typ av lagringskonto, kontrollera [prissidan](https://azure.microsoft.com/pricing/details/storage/) för mer information. Om användaren inte längre har för avsikt att använda den eller de virtuella hårddiskarna tar du bort den/dem för att undvika framtida avgifter. 

## <a name="unable-to-delete-storage-account"></a>Det går inte att ta bort lagringskontot 

När användaren försöker ta bort ett klassiskt lagringskonto som inte längre behövs kan användaren se följande beteende.

#### <a name="azure-portal"></a>Azure Portal 
Användaren navigerar till det klassiska lagringskontot på [Azure-portalen](https://portal.azure.com) och klickar på **Ta bort**visas följande meddelande från användaren: 

Med diskar "anslutna" till en virtuell dator

![Skärmbild av portalen, med den virtuella datorn (klassisk) "Ta bort" felfönstret öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Med diskar "obundna" på en virtuell dator

![Skärmbild av portalen, med den virtuella datorn (klassisk) "Ta bort" icke-fel fönster öppet](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Användaren försöker ta bort ett lagringskonto, som inte längre används, med hjälp av klassiska PowerShell-cmdlets. Användaren ser följande meddelande:

> <span style="color:cyan">**Ta bort-AzureStorageAccount -StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount : BadRequest: Storage-konto myclassicaccount har några aktiva avbildningar och/eller diskar, t.ex.  
> mitt klassiska konto. Kontrollera att dessa bilder och/eller diskar tas bort innan du tar bort det här lagringskontot.</span>

## <a name="unable-to-delete-storage-container"></a>Det går inte att ta bort lagringsbehållaren

När användaren försöker ta bort en klassisk lagringsblobbbehållare som inte längre behövs kan användaren se följande beteende.

#### <a name="azure-portal"></a>Azure Portal 
Azure-portalen tillåter inte att användaren tar bort en behållare om det finns ett "Disk(s)"-lån som pekar på en *.vhd-sidblobbfil i behållaren. Det är avsiktligt för att förhindra oavsiktlig radering av en vhd(s) fil med disk (s) hyra på dem. 

![Skärmbild av portalen, med fönstret "lista" i lagringsbehållaren öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Om användaren väljer att ta bort med PowerShell resulterar det i följande fel. 

> <span style="color:cyan">**Ta bort AzureStorageContainer -Context $context -Namn vhds**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer : Fjärrservern returnerade ett fel: (412) Det finns för närvarande ett lån på behållaren och inget låne-ID angavs i request.. HTTP-statuskod: 412 - HTTP-felmeddelande: Det finns för närvarande ett lån på behållaren och inget låne-ID angavs i begäran.</span>

## <a name="unable-to-delete-a-vhd"></a>Det går inte att ta bort en virtuell 

När du har tagit bort den virtuella Azure-datorn försöker användaren ta bort vhd-filen (sidblob) och ta emot meddelandet nedan:

#### <a name="azure-portal"></a>Azure Portal 
På portalen kan det finnas två upplevelser beroende på listan över blobbar som valts för borttagning.

1. Om bara "Leasade" blobbar är markerade visas inte knappen Ta bort.
![Skärmbild av portalen, med fönstret "list" för behållaren "lista" öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Om en blandning av blobbar "Leasa" och "Tillgänglig" väljs visas knappen "Ta bort". Men åtgärden "Ta bort" lämnar efter sig sidblobar, som har ett disklån på dem. 
![Skärmbild av portalen, med rutan "list" för behållaren "lista" öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
![Skärmbild av portalen, med den valda blob "delete"-rutan öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Om användaren väljer att ta bort med PowerShell resulterar det i följande fel. 

> <span style="color:cyan">**Ta bort AzureStorageBlob -Kontext $context -Container vhds -Blob "classicvm-os-8698.vhd"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob : Fjärrservern returnerade ett fel: (412) Det finns för närvarande ett lån på blobben och inget låne-ID angavs i begäran.. HTTP-statuskod: 412 - HTTP-felmeddelande: Det finns för närvarande ett lån på blobben och inget låne-ID angavs i begäran.</span>


## <a name="resolution-steps"></a>Lösningsanvisningar

### <a name="to-remove-classic-disks"></a>Så här tar du bort klassiska diskar
Följ dessa steg på Azure-portalen:
1.  Navigera till [Azure-portalen](https://portal.azure.com).
2.  Navigera till diskarna (klassikern). 
3.  Klicka på fliken ![Diskar. Skärmbild av portalen, med fönstret "list" för behållaren "list" öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Välj din datadisk och klicka sedan på Ta bort disk.
 ![Skärmbild av portalen, med fönstret "list" för behållaren "lista" öppen](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Försök ta bort åtgärden som tidigare misslyckades.
6.  Ett lagringskonto eller en behållare kan inte tas bort så länge det har en enda disk.

### <a name="to-remove-classic-images"></a>Så här tar du bort klassiska bilder   
Följ dessa steg på Azure-portalen:
1.  Navigera till [Azure-portalen](https://portal.azure.com).
2.  Navigera till OS-bilder (klassisk).
3.  Ta bort bilden.
4.  Försök ta bort åtgärden som tidigare misslyckades.
5.  Ett lagringskonto eller en behållare kan inte tas bort så länge det har en enda bild.
