---
title: Skapa ett HPC Pack huvudnod i en Azure VM | Microsoft Docs
description: Lär dig hur du skapar en Microsoft HPC Pack 2012 R2 huvudnod i en Azure VM med hjälp av Azure portal och Resource Manager-distributionsmodellen.
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
ms.openlocfilehash: acd4cd44dd35a5b1755d9456f683076567d62165
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915267"
---
# <a name="create-the-head-node-of-an-hpc-pack-cluster-in-an-azure-vm-with-a-marketplace-image"></a>Skapa huvudnoden för ett HPC Pack-kluster i en virtuell Azure-dator med en Marketplace-avbildning
Använd en [Microsoft HPC Pack 2012 R2 avbildning av virtuell dator](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) från Azure Marketplace och Azure portal för att skapa huvudnod i HPC-kluster. HPC Pack VM avbildningen baseras på Windows Server 2012 R2 Datacenter med HPC Pack 2012 R2 uppdatering 3 förinstallerat. Använd den här huvudnod för ett bevis på koncept distributionen av HPC Pack i Azure. Du kan sedan lägga till compute-noderna till klustret för att köra arbetsbelastningar för HPC.

> [!TIP]
> Om du vill distribuera ett komplett HPC Pack 2012 R2-kluster i Azure som innehåller huvudnod och datornoder, rekommenderar vi att du använder en automatiserad metod. Alternativen är den [HPC Pack IaaS distributionsskriptet](classic/hpcpack-cluster-powershell-script.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) och Resource Manager-mallar som den [HPC Pack kluster för Windows-arbetsbelastningar](https://azure.microsoft.com/marketplace/partners/microsofthpc/newclusterwindowscn/). Resource Manager-mallar finns även [Microsoft HPC Pack 2016 kluster](https://github.com/MsHpcPack/HPCPack2016/tree/master/newcluster-templates). 
> 
> 

## <a name="planning-considerations"></a>Planera överväganden
I följande bild visas kan du distribuera HPC Pack huvudnod i en Active Directory-domän i Azure-nätverk.

![Huvudnod i HPC Pack][headnode]

* **Active Directory-domän**: I HPC Pack 2012 R2 huvudnod måste vara ansluten till en Active Directory-domän i Azure innan du startar HPC tjänster på den virtuella datorn. Du kan flytta den virtuella datorn som du skapar för huvudnoden som en domänkontrollant innan du startar HPC-tjänster som visas i den här artikeln för ett bevis på koncept distribution. Ett annat alternativ är att distribuera en separat domänkontrollant och skog i Azure som du ansluter till huvudnoden VM.

* **Distributionsmodell**: för de flesta nya distributioner Microsoft rekommenderar att du använder Resource Manager-distributionsmodellen. Den här artikeln förutsätter att du använder denna distributionsmodell.

* **Virtuella Azure-nätverket**: när du använder Resource Manager-distributionsmodellen för att distribuera huvudnoden kan du ange eller skapa ett Azure-nätverk. Du kan använda det virtuella nätverket om du behöver ansluta till huvudnoden i en befintlig Active Directory-domän. Du måste också den senare för att lägga till Beräkningsnoden virtuella datorer i klustret.

## <a name="steps-to-create-the-head-node"></a>Steg för att skapa huvudnoden
Följande är övergripande steg för att använda Azure portal för att skapa en Azure VM för HPC Pack huvudnod med hjälp av Resource Manager-distributionsmodellen. 

1. Om du vill skapa en ny Active Directory-skog i Azure med separata domänkontrollant VMs ett alternativ är att använda en [Resource Manager-mall](https://github.com/Azure/azure-quickstart-templates/tree/master/active-directory-new-domain-ha-2-dc). Det går bra att utelämna det här steget och konfigurera huvudnod VM sig själv som en domänkontrollant för en enkel bevis på koncept distribution. Det här alternativet beskrivs senare i den här artikeln.
2. På den [HPC Pack 2012 R2 på Windows Server 2012 R2 sidan](https://azure.microsoft.com/marketplace/partners/microsoft/hpcpack2012r2onwindowsserver2012r2/) i Azure Marketplace, klickar du på **Skapa virtuell dator**. 
3. I portalen på den **HPC Pack 2012 R2 på Windows Server 2012 R2** väljer den **Resource Manager** distributionsmodell och klicka sedan på **skapa**.
   
    ![HPC Pack bild][marketplace]
4. Använda portalen för att konfigurera inställningarna och skapa den virtuella datorn. Om du har använt Azure följa kursen [skapa en Windows-dator i Azure portal](../virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). För ett bevis på koncept distribution kan du oftast acceptera standardinställningarna eller rekommenderade inställningar.
   
   > [!NOTE]
   > Om du vill ansluta till huvudnoden i en befintlig Active Directory-domän i Azure, kontrollera att du anger det virtuella nätverket för domänen när du skapar den virtuella datorn.
   > 
   > 
5. När du skapar den virtuella datorn och den virtuella datorn körs, [ansluta till den virtuella datorn](connect-logon.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) av fjärrskrivbord. 
6. Anslut den virtuella datorn till en skog för Active Directory-domänen genom att välja något av följande alternativ:
   
   * Om du har skapat den virtuella datorn i Azure-nätverk med en befintlig domänskog Anslut den virtuella datorn till skogen med hjälp av Serverhanteraren eller Windows PowerShell standardverktyg. Starta sedan om.
   * Om du har skapat den virtuella datorn i ett nytt virtuellt nätverk (utan en befintlig domänskog), befordra du den virtuella datorn som en domänkontrollant. Använd standard steg för att installera och konfigurera Active Directory Domain Services-rollen på huvudnoden. Detaljerade anvisningar finns i [installera en ny Windows Server 2012 Active Directory-skog](https://technet.microsoft.com/library/jj574166.aspx).
7. När den virtuella datorn körs och är ansluten till en Active Directory-skog, starta tjänsterna HPC Pack på följande sätt:
   
    a. Ansluta till huvudnoden virtuella datorn med ett domänkonto som är medlem i gruppen lokala administratörer. Till exempel använda administratörskontot som du ställer in när du skapade huvudnod VM.
   
    b. Starta Windows PowerShell som administratör för en standardkonfiguration av huvudnod och Skriv följande:
   
    ```PowerShell
    & $env:CCP_HOME\bin\HPCHNPrepare.ps1 –DBServerInstance ".\ComputeCluster"
    ```
   
    Det kan ta flera minuter för HPC Pack tjänster att starta.
   
    Ytterligare huvudnod konfigurationsalternativ, Skriv `get-help HPCHNPrepare.ps1`.

## <a name="next-steps"></a>Nästa steg
* Nu kan du arbeta med huvudnod i HPC Pack-kluster. Till exempel starta HPC Cluster Manager och slutföra de [distribution uppgiftslista](https://technet.microsoft.com/library/jj884141.aspx).
* Om du vill öka den klustret beräkning kapacitet på begäran, lägger du till [Azure burst noder](classic/hpcpack-cluster-node-burst.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json) i en molntjänst. 
* Försök att köra en test-arbetsbelastning i klustret. Ett exempel finns i HPC Pack [Kom igång med](https://technet.microsoft.com/library/jj884144).

<!--Image references-->
[headnode]: ./media/hpcpack-cluster-headnode/headnode.png
[marketplace]: ./media/hpcpack-cluster-headnode/marketplace.png
