---
title: Skapa en huvudnod i HPC Pack i en Azure-dator | Microsoft Docs
description: Lär dig hur du använder Azure portal och distributionsmodellen för Resource Manager för att skapa en Microsoft HPC Pack 2012 R2-huvudnod i en Azure VM.
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-resource-manager,hpc-pack
ms.assetid: e6a13eaf-9124-47b4-8d75-2bc4672b8f21
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: big-compute
ms.date: 12/29/2016
ms.author: danlep
ms.openlocfilehash: 70c1d95f704315ee6a481367188e2bb620916702
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/02/2018
ms.locfileid: "39428964"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Skapa huvudnoden för ett HPC Pack-kluster i en virtuell Azure-dator med en Marketplace-avbildning
Använd en [Microsoft HPC Pack 2012 R2 avbildning av virtuell dator](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) från Azure Marketplace och Azure-portalen för att skapa huvudnoden för ett HPC-kluster. Den här HPC Pack VM-avbildning är baserad på Windows Server 2012 R2 Datacenter med HPC Pack 2012 R2 uppdatering 3 förinstallerat. Använd den här huvudnoden för en proof of concept distributionen av HPC Pack i Azure. Du kan sedan lägga till compute-noder i klustret att köra HPC-arbetsbelastningar.

> [!TIP]
> För att distribuera en fullständig HPC Pack 2012 R2-kluster i Azure som innehåller huvudnoden och beräkningsnoder, rekommenderar vi att du använder en automatiserad metod. Alternativ inkluderar den [HPC Pack IaaS-distributionsskriptet](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) och Resource Manager-mallar som den [HPC Pack-kluster för Windows-arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Resource Manager-mallar kan också användas för [Microsoft HPC Pack 2016-kluster](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Planera överväganden
Som du ser i följande bild kan distribuera du HPC Pack huvudnod i en Active Directory-domän i Azure-nätverk.

![Huvudnod i HPC Pack][headnode]

* **Active Directory-domän**: The HPC Pack 2012 R2 huvudnoden måste vara ansluten till en Active Directory-domän i Azure innan du börjar HPC-tjänster på den virtuella datorn. Du kan flytta upp den virtuella datorn som du skapar för huvudnoden som en domänkontrollant innan du startar HPC-tjänster som visas i den här artikeln för en proof of concept-distribution. Ett annat alternativ är att distribuera en separat domänkontrollant och en skog i Azure som du ansluta till huvudnoden VM.

* **Distributionsmodell**: för de flesta nya distributioner Microsoft rekommenderar att du använder Resource Manager-distributionsmodellen. Den här artikeln förutsätter att du använder denna distributionsmodell.

* **Azure-nätverk**: när du använder Resource Manager-distributionsmodellen för att distribuera huvudnoden kan du ange eller skapa en Azure-nätverk. Du kan använda det virtuella nätverket om du vill ansluta till huvudnoden i en befintlig Active Directory-domän. Du måste också dem senare för att lägga till Beräkningsnoden virtuella datorer i klustret.

## <a name="steps-to-create-the-head-node"></a>Steg för att skapa huvudnoden
Följande är anvisningar du använder Azure-portalen för att skapa en virtuell Azure-dator för HPC Pack-huvudnoden med hjälp av Resource Manager-distributionsmodellen. 

1. Om du vill skapa en ny Active Directory-skog i Azure med separata virtuella datorer för domänkontrollanter, ett alternativ är att använda en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Det går bra att utelämna det här steget och konfigurera huvudnoden Virtuella datorn som en domänkontrollant för en enkel proof of concept-distribution. Det här alternativet beskrivs senare i den här artikeln.
1. På den [HPC Pack 2012 R2 på Windows Server 2012 R2 sidan](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) i Azure Marketplace, klickar du på **Skapa virtuell dator**. 
1. I portalen på den **HPC Pack 2012 R2 på Windows Server 2012 R2** väljer den **Resource Manager** distributionsmodellen och klicka sedan på **skapa**.
   
    ![HPC Pack-bild][marketplace]
1. Använda portalen för att konfigurera inställningarna och skapa den virtuella datorn. Om du är nybörjare på Azure, kursen [skapa en virtuell Windows-dator i Azure-portalen](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). För en proof of concept-distribution kan du oftast acceptera standardinställningarna eller rekommenderade inställningar.
   
   > [!NOTE]
   > Om du vill ansluta till huvudnoden i en befintlig Active Directory-domän i Azure kan du kontrollera att du anger det virtuella nätverket för domänen när du skapar den virtuella datorn.
   > 
   > 
1. När du skapar den virtuella datorn och Virtuellt datorn körs, [ansluta till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) av fjärrskrivbord. 
1. Anslut till den virtuella datorn till en skog för Active Directory-domän genom att välja något av följande alternativ:
   
   * Om du har skapat den virtuella datorn i ett Azure-nätverk med en befintlig domänskog att ansluta till skogen med hjälp av Serverhanteraren eller Windows PowerShell standardverktyg. Starta sedan om.
   * Om du har skapat den virtuella datorn i ett nytt virtuellt nätverk (utan en befintlig domänskog) sedan uppgradera den virtuella datorn som en domänkontrollant. Använd standard steg för att installera och konfigurera Active Directory Domain Services-rollen på huvudnoden. Detaljerade anvisningar finns i [installera en ny Windows Server 2012 Active Directory-skog](https://technet.microsoft.com/library/jj574166.aspx).
1. När den virtuella datorn körs och är ansluten till en Active Directory-skog, starta tjänsterna HPC Pack på följande sätt:
   
    a. Ansluta till huvudnoden virtuell dator med ett domänkonto som är medlem i gruppen lokala administratörer. Till exempel använda administratörskontot som du ställer in när du har skapat huvudnoden VM.
   
    b. Starta Windows PowerShell som administratör för en standardkonfiguration av huvudnod och skriver du följande:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Det kan ta flera minuter för HPC Pack-tjänster för att börja.
   
    Ytterligare huvudnoden konfigurationsalternativ, skriver du in `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Nästa steg
* Du kan nu arbeta med huvudnod i HPC Pack-kluster. Till exempel starta HPC Cluster Manager och slutför den [distribution uppgiftslista](https://technet.microsoft.com/library/jj884141.aspx).
* Om du vill öka det klustret beräkning kapacitet på begäran, lägger du till [Azure burst noder](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) i en molntjänst. 
* Försök att köra ett test-arbetsbelastningar i klustret. Ett exempel finns i HPC Pack [Kom igång med](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
