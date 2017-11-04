---
title: Ansluta en datadisk till en Linux-VM | Microsoft Docs
description: "Använda portalen för att koppla disk för nya eller befintliga data till en Linux-VM."
services: virtual-machines-linux
documentationcenter: 
author: cynthn
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 5e1c6212-976c-4962-a297-177942f90907
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: cynthn
ms.openlocfilehash: 1a7c98207a5f19d514a0cd05b66898e7fb725944
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="use-the-portal-to-attach-a-data-disk-to-a-linux-vm"></a>Använda portalen för att ansluta en datadisk till en Linux-VM 
Den här artikeln visar hur du kopplar både nya och befintliga diskar till en virtuell Linux-dator via Azure-portalen. Du kan också [ansluta en datadisk till en virtuell Windows-dator i Azure portal](../windows/attach-managed-disk-portal.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 

Granska de här tipsen innan du kopplar diskar till den virtuella datorn:

* Storleken på den virtuella datorn styr hur många datadiskar som du kan bifoga. Mer information finns i [storlekar för virtuella datorer](sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Om du vill använda Premium-lagring, behöver du en virtuell dator DS-serien eller GS-serien. Du kan använda Premium- och Standard diskar med dessa virtuella datorer. Premium-lagring är tillgänglig i vissa regioner. Mer information finns i [Premium-lagring: högpresterande lagring för arbetsbelastningar på virtuella Azure](../windows/premium-storage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Diskar som är anslutna till virtuella datorer är faktiskt VHD-filer som är lagrade i Azure. Mer information finns i [om diskar och virtuella hårddiskar för virtuella datorer](about-disks-and-vhds.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).


## <a name="find-the-virtual-machine"></a>Hitta den virtuella datorn
1. Logga in på [Azure Portal](https://portal.azure.com/).
2. Klicka på den vänstra menyn **virtuella datorer**.
3. Välj den virtuella datorn i listan.
4. Att virtuellt datorer sida i **Essentials**, klickar du på **diskar**.
   
    ![Öppna diskinställningarna för](./media/attach-disk-portal/find-disk-settings.png)


## <a name="attach-a-new-disk"></a>Koppla en ny disk

1. På den **diskar** rutan klickar du på **+ Lägg till datadisk**.
2. Klicka på den nedrullningsbara menyn för **namn** och välj **skapa disk**:

    ![Skapa Azure hanterade diskar](./media/attach-disk-portal/create-new-md.png)

3. Ange ett namn för din hanterade disken. Granska standardinställningarna, uppdatera efter behov och klicka sedan på **skapa**.
   
   ![Granska diskinställningarna för](./media/attach-disk-portal/create-new-md-settings.png)

4. Klicka på **spara** att skapa den hantera disken och uppdatera VM-konfiguration:

   ![Spara nya Azure hanterade disken](./media/attach-disk-portal/confirm-create-new-md.png)

5. När Azure skapar disken och kopplar den till den virtuella datorn, den nya disken visas i inställningarna för den virtuella disken under **Datadiskar**. Som hanterade diskar är en översta resurs disken visas i roten av resursgruppen:

   ![Azure-hanterade disken i resursgruppen](./media/attach-disk-portal/view-md-resource-group.png)

## <a name="attach-an-existing-disk"></a>Ansluta en befintlig disk
1. På den **diskar** rutan klickar du på **+ Lägg till datadisk**.
2. Klicka på den nedrullningsbara menyn för **namn** att visa en lista över befintliga hanterade diskar som är tillgängliga för din Azure-prenumeration. Välj hanterade disken för att ansluta:

   ![Bifoga den befintliga Azure hanterade disken](./media/attach-disk-portal/select-existing-md.png)

3. Klicka på **spara** att bifoga den befintliga hantera disken och uppdatera VM-konfiguration:
   
   ![Spara Azure hanteras Disk uppdateringar](./media/attach-disk-portal/confirm-attach-existing-md.png)

4. När Azure bifogar disken till den virtuella datorn, visas den i inställningarna för den virtuella disken under **Datadiskar**.



## <a name="next-steps"></a>Nästa steg
Du kan också [ansluta en datadisk](add-disk.md) med hjälp av Azure CLI.
