---
title: Flytta virtuella Azure IaaS-datorer till en annan Azure-region med hjälp av Azure Site Recovery-tjänsten | Microsoft Docs
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 1560dad31e00b00a2d27cf1675e05901e6611825
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309870"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Azure växer i takt med antalet kunder, och den ökande efterfrågan gör att vi lägger till stöd för nya regioner. Dessutom läggs nya funktioner till i de olika tjänsterna varje månad. Därför kan det finnas tillfällen när du vill flytta dina virtuella datorer till en annan region eller till en tillgänglighetszon för att öka tillgängligheten.

I det här dokumentet går vi igenom olika situationer där du vill flytta dina virtuella datorer, och du får en guide för hur arkitekturen ska konfigureras på målet för att ge bättre tillgänglighet. 
> [!div class="checklist"]
> * [Varför skulle du vilja flytta virtuella datorer i Azure?](#why-would-you-move-azure-vms)
> * [Så flyttar du virtuella Azure-datorer](#how-to-move-azure-vms)
> * [Vanliga arkitekturer](#typical-architectures-for-a-multi-tier-deployment)
> * [Flytta virtuella datorer i befintligt skick till en målregion](#move-azure-vms-to-another-region)
> * [Flytta virtuella datorer för att öka tillgängligheten](#move-vms-to-increase-availability)


## <a name="why-would-you-move-azure-vms"></a>Varför skulle du vilja flytta virtuella datorer i Azure?

Kunder kan vilja flytta sina virtuella datorer av följande skäl:

- Om du redan har distribuerat i en region och vi lägger stöd för en ny region som ligger närmare dina slutanvändare så vore det bra för dig att **flytta dina virtuella datorer i befintligt skick till den nya regionen** för att få kortare svarstider. Samma metod används om du vill konsolidera dina prenumerationer eller om du måste göra flytten på grund av gällande regler och förordningar. 
- Om den virtuella datorn är distribuerad som fristående instans eller om den ingår i en tillgänglighetsuppsättning och du vill förbättra serviceavtalen för tillgänglighet kan du **flytta dina virtuella datorer till en tillgänglighetszon**. 

## <a name="how-to-move-azure-vms"></a>Så flyttar du virtuella Azure-datorer
De här stegen ingår i att flytta virtuella datorer:

1. Kontrollera förutsättningar 
2. Förbereda källregionens virtuella datorer 
3. Förbereda målregionen 
4. Kopiera data till målregionen – Använd replikeringstekniken i Azure Site Recovery till att kopiera data från datorerna i källregionen till målregionen
5. Testa konfigurationen: När replikeringen är färdig testar du konfigurationen genom att utföra ett redundanstest till ett nätverk utanför produktion.
6. Utföra flytten 
7. Ta bort resurserna från källregionen 


> [!IMPORTANT]
> För närvarande har Azure Site Recovery stöd för flytt av virtuella datorer mellan olika regioner, inte inom samma region. 

> [!NOTE]
> Detaljerade instruktioner för de här stegen ges i dokumentationen till vart och ett av scenarierna såsom det nämns [här](#next-steps)

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Vanliga arkitekturer för distributioner med flera nivåer
I avsnittet nedan går vi igenom de vanligaste distributionsarkitekturerna som våra kunder använder för program med flera nivåer i Azure. I exemplet använder vi ett program med tre nivåer och en offentlig IP-adress. Nivåerna webb, program och databas har 2 virtuella datorer var och är anslutna till de andra nivåerna via en lastbalanserare. Databasnivån har SQL Always ON-replikering mellan de virtuella datorerna för hög tillgänglighet (HA).

1.  **Fristående virtuella datorer distribuerade över olika nivåer** Varje virtuell dator på en nivå är konfigurerad som en enskild instans, som ansluts till de andra nivåerna via lastbalanserare. Det här är den enklaste konfigurationen våra kunder använder.

       ![enskilda virtuella datorer](media/move-vm-overview/regular-deployment.PNG)

2. **Virtuella datorer på varje nivå distribuerade över tillgänglighetsuppsättningar** Varje virtuell dator på en nivå är konfigurerad i en tillgänglighetsuppsättning. [Tillgänglighetsuppsättningarna](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ser till att de virtuella datorer du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta innebär att endast en del av de virtuella datorerna påverkas om det skulle uppstå ett maskinvaru- eller programvarufel i Azure, och att din lösning fortfarande är tillgänglig och fungerar. 
   
      ![avset](media/move-vm-overview/AVset.PNG)

3. **Virtuella datorer på varje nivå distribuerade över tillgänglighetsuppsättningar** Varje virtuell dator på en nivå är konfigurerad över [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

      ![zondistribuering](media/move-vm-overview/zone.PNG)



 ## <a name="move-vms-as-is-to-a-target-region"></a>Flytta virtuella datorer i befintligt skick till en målregion

Baserat på ovanstående [arkitekturer](#typical-architectures-for-a-multi-tier-deployment) så ser distributionerna ut så här när du flyttar i befintligt skick till målregionen.


1. **Fristående virtuell dator distribuerad över olika nivåer** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Virtuella datorer i olika nivåer distribuerade över en tillgänglighetsuppsättning**

     ![crossregionAset.PNG](media/move-vm-overview/crossregionAset.PNG)


3. **Virtuella datorer i olika nivåer distribuerade över en tillgänglighetszon**
      

     ![AzoneCross.PNG](media/move-vm-overview/AzoneCross.PNG)

## <a name="move-vms-to-increase-availability"></a>Flytta virtuella datorer för att öka tillgängligheten

1. **Fristående virtuell dator distribuerad över olika nivåer** 

     ![single-zone.PNG](media/move-vm-overview/single-zone.PNG)

2. **Virtuella datorer i olika nivåer distribuerade över en tillgänglighetsuppsättning** – du kan välja att konfigurera dina virtuella datorer i en tillgänglighetsuppsättning i separata tillgänglighetszoner när du väljer att aktivera replikering för de virtuella datorerna med Azure Site Recovery. Serviceavtalet för tillgänglighet är 99,9 % när du har slutfört flytten.

     ![aset-Azone.PNG](media/move-vm-overview/aset-Azone.PNG)


## <a name="next-steps"></a>Nästa steg

I det här dokumentet kan du läsa om allmänna riktlinjer för att flytta virtuella datorer. Du kan läsa stegvisa instruktioner i de här artiklarna:


> [!div class="nextstepaction"]
> * [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)

> * [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-VMs-AVset-Azone.md)

