---
title: Flytta Azure MS till en annan region med Azure Site Recovery
description: Använd Azure Site Recovery för att flytta virtuella IaaS-datorer i Azure från en Azure-region till en annan.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: e3a3db66b4833a8ba21dc9d3c1938f645919221c
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954174"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Azure växer tillsammans med kund basen och lägger till stöd för nya regioner för att hålla jämna steg med ökande krav. Nya funktioner läggs också till månatlig över tjänster. Du kanske vill flytta dina virtuella datorer (VM) till en annan region eller till Tillgänglighetszoner för att öka tillgängligheten.

I den här självstudien beskrivs olika scenarier där du vill flytta dina virtuella datorer. Det beskriver också hur du konfigurerar arkitekturen i mål regionen för att uppnå högre tillgänglighet. 

I den här självstudien lär du dig att:

> [!div class="checklist"]
> 
> * Skäl att flytta virtuella datorer
> * Typiska arkitekturer
> * Flytta virtuella datorer som är till en mål region
> * Flytta virtuella datorer för att öka tillgängligheten

## <a name="reasons-to-move-azure-vms"></a>Skäl att flytta virtuella Azure-datorer

Du kan flytta virtuella datorer av följande anledningar:

- Du har redan distribuerat i en region och ett nytt regions stöd har lagts till som är närmare slutanvändaren för ditt program eller din tjänst. I det här scenariot skulle du vilja flytta dina virtuella datorer till den nya regionen för att minska svars tiden. Använd samma metod om du vill konsolidera prenumerationer eller om det finns styrnings-eller organisations regler som kräver att du flyttar.
- Den virtuella datorn distribuerades som en virtuell dator med enkel instans eller som en del av en tillgänglighets uppsättning. Om du vill öka tillgänglighets service avtal kan du flytta dina virtuella datorer till en tillgänglighets zon.

## <a name="steps-to-move-azure-vms"></a>Steg för att flytta virtuella Azure-datorer

De här stegen ingår i att flytta virtuella datorer:

1. Kontrollera förutsättningarna.
2. Förbered de virtuella käll datorerna.
3. Förbered mål regionen.
4. Kopiera data till mål regionen. Använd Azure Site Recovery-teknik för replikering för att kopiera data från den virtuella käll datorn till mål regionen.
5. Testa konfigurationen. När replikeringen är klar testar du konfigurationen genom att utföra en redundanstest till ett nätverk som inte är i produktion.
6. Genomför flyttningen.
7. Ta bort resurserna i käll regionen.

> [!NOTE]
> Information om de här stegen finns i följande avsnitt.
> [!IMPORTANT]
> För närvarande har Azure Site Recovery stöd för att flytta virtuella datorer från en region till en annan men det går inte att flytta i en region.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Vanliga arkitekturer för distributioner med flera nivåer

I det här avsnittet beskrivs de vanligaste distributions arkitekturerna för ett program i flera nivåer i Azure. Exemplet är ett program med tre nivåer med en offentlig IP-adress. Varje nivå (webb, program och databas) har två virtuella datorer, och de är anslutna till de andra nivåerna av en Azure Load Balancer. Databas nivån har SQL Server Always on replikering mellan de virtuella datorerna för hög tillgänglighet.

* **Virtuella datorer med en enda instans som distribueras över olika nivåer**: varje virtuell dator i en nivå konfigureras som en virtuell dator med en instans och är ansluten av belastningsutjämnare till de andra nivåerna. Den här konfigurationen är den enklaste att anta.

     ![VM-distribution med enkel instans mellan nivåer](media/move-vm-overview/regular-deployment.png)

* **Virtuella datorer i varje nivå som distribueras över tillgänglighets uppsättningar**: varje virtuell dator i en nivå konfigureras i en tillgänglighets uppsättning. [Tillgänglighetsuppsättningarna](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ser till att de virtuella datorer du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta säkerställer att om ett maskinvaru-eller program varu haveri i Azure inträffar påverkas endast en del av de virtuella datorerna och den övergripande lösningen förblir tillgänglig och fungerar.

     ![VM-distribution över tillgänglighets uppsättningar](media/move-vm-overview/avset.png)

* **Virtuella datorer på varje nivå som distribueras mellan Tillgänglighetszoner**: varje virtuell dator i en nivå konfigureras i [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

     ![Distribution av tillgänglighets zon](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Flytta virtuella datorer i befintligt skick till en målregion

Baserat på de [arkitekturer](#typical-architectures-for-a-multi-tier-deployment) som nämnts tidigare, är det här hur distributionerna kommer att se ut när du har utfört flytta som är till mål regionen.

* **Virtuella datorer med enkel instans distribuerade över olika nivåer**

     ![VM-distribution med enkel instans mellan nivåer](media/move-vm-overview/single-zone.png)

* **Virtuella datorer i varje nivå som distribueras över tillgänglighets uppsättningar**

     ![Tillgänglighets uppsättningar för flera regioner](media/move-vm-overview/crossregionaset.png)

* **Virtuella datorer på varje nivå som distribueras i Tillgänglighetszoner**

     ![Distribution av virtuella datorer i Tillgänglighetszoner](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Flytta virtuella datorer för att öka tillgängligheten

* **Virtuella datorer med enkel instans distribuerade över olika nivåer**

     ![VM-distribution med enkel instans mellan nivåer](media/move-vm-overview/single-zone.png)

* **Virtuella datorer på varje nivå som distribueras över tillgänglighets uppsättningar**: du kan konfigurera dina virtuella datorer i en tillgänglighets uppsättning i separata Tillgänglighetszoner när du aktiverar replikering för den virtuella datorn med hjälp av Azure Site Recovery. Service avtalet för tillgänglighet är 99,9% när du har slutfört flytt åtgärden.

     ![VM-distribution över tillgänglighets uppsättningar och Tillgänglighetszoner](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> 
> * [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
> 
> * [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-vms-avset-azone.md)

