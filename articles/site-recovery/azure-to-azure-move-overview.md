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
ms.openlocfilehash: dc49b33fd3e6d582b31af5fe0507884e60205757
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58078014"
---
# <a name="move-azure-vms-to-another-region"></a>Migrera virtuella Azure-datorer till en annan region

Azure växer tillsammans med kunden grundläggande och lägger till stöd för nya regioner för att hålla jämna steg med ökande behov. Nya funktioner läggs till varje månad för tjänster. Du kanske vill flytta dina virtuella datorer (VM) till en annan region eller i Tillgänglighetszoner att öka tillgängligheten.

Den här självstudien beskrivs olika scenarier där du vill flytta dina virtuella datorer. Det beskriver också hur du konfigurerar arkitekturen i målregionen att uppnå högre tillgänglighet. 

I de här självstudierna lär du dig att:

> [!div class="checklist"]
> 
> * Anledningar till att flytta virtuella datorer
> * Vanliga arkitekturer
> * Flytta virtuella datorer som är till en målregion
> * Flytta virtuella datorer för att öka tillgängligheten

## <a name="reasons-to-move-azure-vms"></a>Anledningar till att flytta virtuella datorer i Azure

Du kan också flytta virtuella datorer av följande skäl:

- Du har distribuerats redan i en region och en ny regionsstöd har lagts till som ligger närmast användarna av programmet eller tjänsten. I det här scenariot skulle du vill flytta dina virtuella datorer som är att den nya regionen som minskar svarstiderna. Använd samma metod om du vill att konsolidera prenumerationer eller om det finns regler för styrning eller organisation som kräver att du kan flytta.
- Den virtuella datorn distribuerades som en enda instans virtuell dator eller som en del av en tillgänglighetsuppsättning. Om du vill öka tillgängligheten serviceavtal kan du flytta dina virtuella datorer i en Tillgänglighetszon.

## <a name="steps-to-move-azure-vms"></a>Steg för att flytta virtuella datorer i Azure

De här stegen ingår i att flytta virtuella datorer:

1. Kontrollera krav är uppfyllda.
2. Förbereda virtuella datorer.
3. Förbered målregionen.
4. Kopiera data till målregionen. Använd Azure Site Recovery-replikeringsteknik för att kopiera data från den Virtuella källdatorn till målregionen.
5. Testa konfigurationen. När replikeringen är klar kan du testa konfigurationen genom att utföra ett redundanstest till ett nätverk för icke-produktion.
6. Utföra förflyttningen.
7. Ta bort resurser i källregionen.

> [!NOTE]
> Information om de här stegen finns i följande avsnitt.
> [!IMPORTANT]
> För närvarande kan Azure Site Recovery stöder flytta virtuella datorer från en region till en annan men har stöd inte för att flytta inom en region.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Vanliga arkitekturer för distributioner med flera nivåer

Det här avsnittet beskrivs de vanligaste distribution arkitekturerna för en flernivåapp i Azure. I exemplet är ett program med tre nivåer med en offentlig IP-adress. Var och en av nivåerna (webb, program och -databas) har två datorer, och de är anslutna via en Azure belastningsutjämnare till andra nivåer. Databasnivån har SQL Server Always On-replikering mellan de virtuella datorerna för hög tillgänglighet.

* **Enskild instans virtuella datorer som distribueras över olika nivåer**: Varje virtuell dator i en nivå är konfigurerad som en enda instans virtuell dator och är ansluten med belastningsutjämnare i de andra nivåerna. Den här konfigurationen är enkel att använda.

     ![Distribution av virtuella datorer med en instans på nivåerna](media/move-vm-overview/regular-deployment.png)

* **Virtuella datorer i varje nivå som distribuerats i tillgänglighetsuppsättningar**: Varje virtuell dator i en nivå är konfigurerad i en tillgänglighetsuppsättning. [Tillgänglighetsuppsättningarna](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) ser till att de virtuella datorer du distribuerar i Azure distribueras över flera isolerade maskinvarunoder i ett kluster. Detta säkerställer att om en maskin- eller programvarufel i Azure, endast en delmängd av dina virtuella datorer som påverkas och din lösning fortfarande är tillgänglig och fungerar.

     ![Distribution av virtuella datorer i tillgänglighetsuppsättningar](media/move-vm-overview/avset.png)

* **Virtuella datorer i varje nivå som distribueras över Tillgänglighetszoner**: Varje virtuell dator i en nivå är konfigurerad i [Tillgänglighetszoner](https://docs.microsoft.com/azure/availability-zones/az-overview). En tillgänglighetszon i en Azure-region är en kombination av en feldomän och en uppdateringsdomän. Om du skapar tre eller flera virtuella datorer över tre zoner i en Azure-region distribueras i praktiken dina virtuella datorer mellan tre feldomäner och tre uppdateringsdomäner. Azure-plattformen identifierar den här distributionen mellan uppdateringsdomänerna så att inte virtuella datorer i olika zoner uppdateras på samma gång.

     ![Tillgänglighetszon distribution](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Flytta virtuella datorer i befintligt skick till en målregion

Utifrån den [arkitekturer](#typical-architectures-for-a-multi-tier-deployment) här tidigare nämnts är hur distributioner ser ut när du har utfört flytten skick till målregion.

* **Enskild instans virtuella datorer som distribueras över olika nivåer**

     ![Distribution av virtuella datorer med en instans på nivåerna](media/move-vm-overview/single-zone.png)

* **Virtuella datorer i varje nivå som distribuerats i tillgänglighetsuppsättningar**

     ![Mellan region tillgänglighetsuppsättningar](media/move-vm-overview/crossregionaset.png)

* **Virtuella datorer i varje nivå som distribueras över Tillgänglighetszoner**

     ![Distribution av virtuella datorer mellan Tillgänglighetszoner](media/move-vm-overview/azonecross.png)

## <a name="move-vms-to-increase-availability"></a>Flytta virtuella datorer för att öka tillgängligheten

* **Enskild instans virtuella datorer som distribueras över olika nivåer**

     ![Distribution av virtuella datorer med en instans på nivåerna](media/move-vm-overview/single-zone.png)

* **Virtuella datorer i varje nivå som distribuerats i tillgänglighetsuppsättningar**: Du kan konfigurera dina virtuella datorer i en tillgänglighetsuppsättning i separata Tillgänglighetszoner när du aktiverar replikering för den virtuella datorn med hjälp av Azure Site Recovery. SERVICEAVTALET för tillgänglighet är 99,9% när du har slutfört åtgärden för att flytta.

     ![Distribution av virtuella datorer i tillgänglighetsuppsättningar och Tillgänglighetszoner](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> 
> * [Flytta virtuella Azure-datorer till en annan region](azure-to-azure-tutorial-migrate.md)
> 
> * [Flytta virtuella Azure-datorer till tillgänglighetszoner](move-azure-vms-avset-azone.md)

