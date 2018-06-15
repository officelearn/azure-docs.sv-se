---
title: Lägg till burst-noder i ett kluster med HPC Pack | Microsoft Docs
description: Lär dig hur du expandera ett HPC Pack kluster i Azure på begäran genom att lägga till rollen worker-instanser körs i en tjänst i molnet
services: virtual-machines-windows
documentationcenter: ''
author: dlepow
manager: jeconnoc
editor: ''
tags: azure-service-management,hpc-pack
ms.assetid: 24b79a8a-24ad-4002-ae76-75abc9b28c83
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-multiple
ms.workload: big-compute
ms.date: 10/14/2016
ms.author: danlep
ms.openlocfilehash: eee9183321f21676271c8a9c7e023c80c4daf554
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/06/2018
ms.locfileid: "30915116"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Lägg till på begäran ”burst” noder i ett HPC Pack kluster i Azure
Om du ställer in en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) kluster i Azure, kan du ett sätt att snabbt skala klustret kapacitet upp eller ned, utan att behålla en uppsättning förinställda beräkningsnod virtuella datorer. Den här artikeln visar hur du lägger till noder på begäran ”burst” (worker-rollinstanser som körs i en tjänst i molnet) som beräkningsresurser till huvudnod i Azure. 

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Burst-noder][burst]

Stegen i den här artikeln hjälpa dig att snabbt lägga till Azure-noder till en molnbaserad HPC Pack huvudnod VM för test- eller proof of concept distribution. De övergripande stegen är samma som stegen för att ”burst to Azure” att lägga till molnet beräkningskapacitet till ett lokalt HPC Pack-kluster. En självstudiekurs finns [ställa in ett hybrid beräkningskluster with Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Detaljerad information och överväganden för Produktionsdistribution finns [Burst to Azure with Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Förutsättningar
* **HPC Pack huvudnod distribueras i en Azure VM** -du kan använda en fristående huvudnod VM eller ett som är en del av en större kluster. Om du vill skapa en fristående huvudnod finns [distribuera ett HPC Pack huvudnod i en Azure VM](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatisk HPC Pack klustret distributionsalternativ finns [alternativ för att skapa och hantera en Windows HPC-kluster i Azure with Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Om du använder den [HPC Pack IaaS distributionsskriptet](hpcpack-cluster-powershell-script.md) för att skapa klustret i Azure, kan du inkludera Azure burst-noder i din automatisk distribution. Se exemplen i den artikeln.
  > 
  > 
* **Azure-prenumeration** – att lägga till Azure-noder kan du välja samma prenumeration som används för att distribuera huvudnod virtuell dator eller en annan prenumeration (eller prenumerationer).
* **Kärnor kvoten** – du kan behöva öka kvoten för kärnor, särskilt om du väljer att distribuera flera Azure-noder med flera kärnor. Att öka kvoten [öppna en supportbegäran online customer](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Steg 1: Skapa en molntjänst och ett lagringskonto för Azure-noder
Använd Azure-portalen eller likvärdiga verktyg för att konfigurera följande resurser som behövs för att distribuera din Azure-noder:

* En ny Azure-molntjänst (klassisk)
* Ett nytt Azure storage-konto (klassisk)

> [!NOTE]
> Inte återanvända en befintlig molntjänst i din prenumeration. 
> 
> 

**Överväganden**

* Konfigurera en separat molntjänst för varje nod i Azure-mall som du planerar att skapa. Du kan dock använda samma lagringskonto för flera noden mallar.
* Vi rekommenderar att du hitta Molntjänsten och lagringskontot för distributionen i samma Azure-region.

## <a name="step-2-configure-an-azure-management-certificate"></a>Steg 2: Konfigurera ett Azure-hanteringscertifikat
Om du vill lägga till Azure-noder som beräkningsresurser och behöver du ett certifikat på huvudnod och ladda upp en motsvarande certifikat till Azure-prenumerationen används för distribution av.

I det här scenariot kan du välja den **standard HPC Azure-Hanteringscertifikatet** som HPC Pack installeras och konfigureras automatiskt på huvudnoden. Det här certifikatet är användbart för testning syfte och proof of concept distributioner. Ladda upp filen C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer från huvudnod VM till prenumerationen för att använda det här certifikatet. Att ladda upp certifikatet i den [Azure-portalen](https://portal.azure.com):

1. Klicka på **prenumerationer** > *your_subscription_name*.

2. Klicka på **hanteringscertifikat** > **överför**.

Ytterligare alternativ för att konfigurera certifikatet, se [scenarier för att konfigurera Azure-Hanteringscertifikat för Azure Burst distributioner](http://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Steg 3: Distribuera Azure-noder i klustret
Stegen för att lägga till och starta Azure-noder i det här scenariot är vanligtvis samma som stegen i ett lokalt huvudnod. Mer information finns i följande avsnitt i [steg för att distribuera Azure-noder med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Skapa en mall för Azure nod
* Lägg till Azure-noder i Windows HPC-kluster
* Starta (tillhandahålla) i Azure-noder

De är klara som du kan använda för att köra klustret när du lägger till och starta noderna.

Om det uppstår problem när du distribuerar Azure-noder kan se [felsöka distributioner av Azure-noder med Microsoft HPC Pack](http://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Nästa steg
* Om du vill använda en instans av beräkningsintensiva storlek för burst-noder finns i överväganden i [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Om du vill automatiskt växa eller krympa Azure bearbetningsresurser enligt klustrets arbetsbelastning, se [automatiskt växa eller krympa Azure-beräkningsresurser i ett kluster med HPC Pack](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
