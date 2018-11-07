---
title: Lägg till utökade noder i ett HPC Pack-kluster | Microsoft Docs
description: Lär dig hur du expanderar ett HPC Pack-kluster i Azure på begäran genom att lägga till arbetsrollinstanser som körs i en molntjänst
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
ms.openlocfilehash: 7d42c026975a18c7574e4bc64ec28ab3ed0082bc
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/07/2018
ms.locfileid: "51248460"
---
# <a name="add-on-demand-burst-nodes-to-an-hpc-pack-cluster-in-azure"></a>Lägg till noder på begäran ”burst” i ett HPC Pack-kluster i Azure
Om du har konfigurerat en [Microsoft HPC Pack](https://technet.microsoft.com/library/cc514029) kluster i Azure och du kan snabbt skala klusterkapacitet upp eller ned, utan att behålla en uppsättning förinställda beräkningsnod virtuella datorer. Den här artikeln visar hur du lägger till noder på begäran ”burst” (worker-rollinstanser som körs i en molntjänst) som beräkningsresurser till huvudnoden i Azure. 

> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../../includes/virtual-machines-classic-portal.md)]

![Burst-noder][burst]

Stegen i den här artikeln hur du lägger till Azure-noder snabbt till en molnbaserad HPC Pack huvudnoden virtuell dator för en test eller konceptbevis-distribution. De övergripande stegen är samma som stegen för att ”utöka till Azure” att lägga till molnet beräkningskapacitet för att ett lokalt HPC Pack-kluster. En självstudiekurs finns i [konfigurera ett hybridberäkningskluster med Microsoft HPC Pack](../../../cloud-services/cloud-services-setup-hybrid-hpcpack-cluster.md). Detaljerad vägledning och överväganden för Produktionsdistribution finns i [utöka till Azure med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481749.aspx).

## <a name="prerequisites"></a>Förutsättningar
* **HPC Pack huvudnoden distribueras i en Azure VM** – du kan använda en fristående huvudnod virtuell dator eller ett som är en del av ett större kluster. Om du vill skapa en fristående huvudnod [distribuera ett HPC Pack huvudnod i en Azure VM](../../virtual-machines-windows-hpcpack-cluster-headnode.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). Automatiserad HPC Pack-kluster-distributionsalternativ, se [alternativ för att skapa och hantera en Windows HPC-kluster i Azure med Microsoft HPC Pack](../../virtual-machines-windows-hpcpack-cluster-options.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
  
  > [!TIP]
  > Om du använder den [HPC Pack IaaS-distributionsskriptet](hpcpack-cluster-powershell-script.md) för att skapa klustret i Azure, kan du inkludera Azure burst-noder i dina automatiserade distributioner. Se exemplen i den här artikeln.
  > 
  > 
* **Azure-prenumeration** – om du vill lägga till Azure noder och du kan välja samma prenumeration som används för att distribuera huvudnod virtuell dator, eller en annan prenumeration (eller prenumerationer).
* **Kärnkvot** – du kan behöva öka kärnkvoten kärnor, särskilt om du väljer att distribuera flera Azure-noder med flera kärnor storlekar. Att öka en kvot [öppna en supportbegäran online-kund](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/) utan kostnad.

## <a name="step-1-create-a-cloud-service-and-a-storage-account-for-the-azure-nodes"></a>Steg 1: Skapa en molntjänst och ett lagringskonto för Azure-noder
Använd Azure portal eller motsvarande verktyg för att konfigurera följande resurser som krävs för att distribuera Azure-noderna:

* En ny Azure molntjänst (klassisk)
* Ett nytt Azure storage-konto (klassisk)

> [!NOTE]
> Inte återanvända en befintlig molntjänst i din prenumeration. 
> 
> 

**Överväganden**

* Konfigurera en separat molntjänst för varje Azure-nod-mall som du planerar att skapa. Du kan dock använda samma lagringskonto för flera noden mallar.
* Vi rekommenderar att du hitta Molntjänsten och lagringskontot för distributionen i samma Azure-region.

## <a name="step-2-configure-an-azure-management-certificate"></a>Steg 2: Konfigurera en Azure-hanteringscertifikatet
Om du vill lägga till Azure-noder som beräkningsresurser, behöver du ett certifikat på huvudnoden och ladda upp en motsvarande certifikat till Azure-prenumeration används för distributionen.

Det här scenariot kan du välja den **standard HPC Azure-Hanteringscertifikatet** som HPC Pack installeras och konfigureras automatiskt på huvudnoden. Det här certifikatet är användbart för att testa syften och proof of concept-distributioner. Ladda upp filen C:\Program Files\Microsoft HPC Pack 2012\Bin\hpccert.cer från klustrets huvudnod VM till prenumerationen för att använda det här certifikatet. Ladda upp certifikatet i den [Azure-portalen](https://portal.azure.com):

1. Klicka på **prenumerationer** > *your_subscription_name*.

2. Klicka på **hanteringscertifikat** > **överför**.

Ytterligare alternativ att konfigurera hanteringscertifikatet finns i [scenarier för att konfigurera Azure-Hanteringscertifikat för Azure Burst distributioner](https://technet.microsoft.com/library/gg481759.aspx).

## <a name="step-3-deploy-azure-nodes-to-the-cluster"></a>Steg 3: Distribuera Azure-noder i klustret
Stegen för att lägga till och starta Azure-noder i det här scenariot är vanligtvis samma som stegen i en lokal huvudnod. Mer information finns i följande avsnitt i [steg för att distribuera Azure-noder med Microsoft HPC Pack](https://technet.microsoft.com/library/gg481758.aspx):

* Skapa en Azure-nod-mall
* Lägg till Azure-noder i Windows HPC-kluster
* Starta (tillhandahålla) Azure-noder

När du lägger till och starta noderna är de redo att använda för att köra jobb i klustret.

Om du får problem när du distribuerar Azure-noder kan se [felsöka distributioner av Azure-noder med Microsoft HPC Pack](https://technet.microsoft.com/library/jj159097.aspx).

## <a name="next-steps"></a>Nästa steg
* Om du vill använda en beräkningsintensiva instansstorleken för burst-noder finns i överväganden i [högpresterande compute VM-storlekar](../sizes-hpc.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Om du vill att automatiskt växa eller krympa Azure bearbetningsresurser enligt klustrets arbetsbelastning kan du läsa [automatiskt växa eller krympa Azure-beräkningsresurser i ett HPC Pack-kluster](hpcpack-cluster-node-autogrowshrink.md).

<!--Image references-->
[burst]: ./media/hpcpack-cluster-node-burst/burst.png
