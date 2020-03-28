---
title: Flytta virtuella Azure-datorer till en annan region med Azure Site Recovery
description: Använda Azure Site Recovery för att flytta virtuella Azure-datorer från en Azure-region till en annan.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 3f715af835df6783ae5d59dd073a042a553fba4d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75498044"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Flytta virtuella Azure-datorer till en annan Azure-region

Den här artikeln innehåller en översikt över orsakerna till och stegen i att flytta virtuella Azure-datorer till en annan Azure-region med Hjälp av [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Skäl att flytta virtuella Azure-datorer

Du kan flytta virtuella datorer av följande skäl:

- Du har redan distribuerats i en region och ett nytt regionstöd har lagts till som ligger närmare slutanvändarna av ditt program eller din tjänst. I det här fallet vill du flytta dina virtuella datorer som är till den nya regionen för att minska svarstiden. Använd samma metod om du vill konsolidera prenumerationer eller om det finns styrnings- eller organisationsregler som kräver att du flyttar.
- Den virtuella datorn har distribuerats som en virtuell dator med en instans eller som en del av en tillgänglighetsuppsättning. Om du vill öka tillgänglighets-SLA:erna kan du flytta dina virtuella datorer till en tillgänglighetszon.

## <a name="steps-to-move-azure-vms"></a>Steg för att flytta virtuella Azure-datorer

De här stegen ingår i att flytta virtuella datorer:

1. Kontrollera förutsättningarna.
2. Förbered käll-virtuella datorer.
3. Förbered målområdet.
4. Kopiera data till målområdet. Använd Azure Site Recovery replikeringsteknik för att kopiera data från källdatorn till målregionen.
5. Testa konfigurationen. När replikeringen är klar testar du konfigurationen genom att utföra en testundanställning till ett icke-produktionsnätverk.
6. Utför flytten.
7. Ignorera resurserna i källregionen.

> [!NOTE]
> Information om dessa steg finns i följande avsnitt.
> [!IMPORTANT]
> Azure Site Recovery stöder för närvarande att flytta virtuella datorer från en region till en annan men stöder inte att flytta inom en region.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Vanliga arkitekturer för distributioner med flera nivåer

I det här avsnittet beskrivs de vanligaste distributionsarkitekturerna för ett program på flera nivåer i Azure. Exemplet är ett tredelat program med en offentlig IP. Var och en av nivåerna (webb, program och databas) har två virtuella datorer vardera, och de är anslutna av en Azure-belastningsutjämnare till de andra nivåerna. Databasnivån har SQL Server Always On Replication mellan de virtuella datorerna för hög tillgänglighet.

* **Virtuella datorer med en instans som distribueras över olika nivåer:** Varje virtuell dator på en nivå är konfigurerad som en virtuell dator med en instans och ansluts av belastningsutjämnare till de andra nivåerna. Den här konfigurationen är den enklaste att anta.

     ![Vm-distribution med en instans på olika nivåer](media/move-vm-overview/regular-deployment.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighetsuppsättningar:** Varje virtuell dator på en nivå har konfigurerats i en tillgänglighetsuppsättning. [Tillgänglighetsuppsättningarna](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ser till att de virtuella datorer du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta säkerställer att om ett maskinvaru- eller programvarufel i Azure inträffar påverkas endast en delmängd av dina virtuella datorer och din övergripande lösning förblir tillgänglig och fungerar.

     ![VM-distribution över tillgänglighetsuppsättningar](media/move-vm-overview/avset.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighetszoner:** Varje virtuell dator på en nivå är konfigurerad över [tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

     ![Distribution av tillgänglighetszon](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Flytta virtuella datorer i befintligt skick till en målregion

Baserat på de arkitekturer som [nämndes](#typical-architectures-for-a-multi-tier-deployment) tidigare, här är vad distributionerna kommer att se ut när du utför flytten som är till målregionen.

* **Virtuella datorer med en instans som distribueras på olika nivåer**

     ![Vm-distribution med en instans på olika nivåer](media/move-vm-overview/single-zone.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighetsuppsättningar**

     ![Tillgänglighetsuppsättningar för flera regioner](media/move-vm-overview/crossregionaset.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighetszoner**

     ![VM-distribution över tillgänglighetszoner](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Flytta virtuella datorer för att öka tillgängligheten

* **Virtuella datorer med en instans som distribueras på olika nivåer**

     ![Vm-distribution med en instans på olika nivåer](media/move-vm-overview/single-zone.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighetsuppsättningar:** Du kan konfigurera dina virtuella datorer i en tillgänglighetsuppsättning i separata tillgänglighetszoner när du aktiverar replikering för den virtuella datorn med hjälp av Azure Site Recovery. Serviceavtalet för tillgänglighet kommer att vara 99,99% när du har slutfört flyttåtgärden.

     ![VM-distribution över tillgänglighetsuppsättningar och tillgänglighetszoner](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> 
> * [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
> 
> * [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-vms-avset-azone.md)

