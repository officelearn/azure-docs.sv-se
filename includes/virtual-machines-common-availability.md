---
title: ta med fil
description: ta med fil
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 05/10/2019
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 1582f61befb9b6d71adbfda9482175d67874ffb9
ms.sourcegitcommit: fa45c2bcd1b32bc8dd54a5dc8bc206d2fe23d5fb
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/12/2019
ms.locfileid: "67850280"
---
# <a name="availability-options-for-virtual-machines-in-azure"></a>Tillgänglighets alternativ för virtuella datorer i Azure
Den här artikeln ger en översikt över tillgänglighets funktionerna i Azure Virtual Machines (VM).


## <a name="availability-sets"></a>Tillgänglighetsuppsättningar
En tillgänglighets uppsättning är en logisk gruppering av virtuella datorer i ett Data Center som gör det möjligt för Azure att förstå hur ditt program är utformat för att tillhandahålla redundans och tillgänglighet. Vi rekommenderar att två eller flera virtuella datorer skapas i en tillgänglighets uppsättning för att tillhandahålla ett program med hög tillgänglighet och uppfylla [99,95% Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/). För själva tillgänglighets uppsättningen kostar du bara att betala för varje VM-instans som du skapar. När en enskild virtuell dator använder [Azure Premium-SSD](../articles/virtual-machines/windows/disks-types.md#premium-ssd)gäller Azure SLA för oplanerade underhålls händelser.

En tillgänglighets uppsättning består av två ytterligare grupperingar som skyddar mot maskin varu fel och tillåter att uppdateringar på ett säkert sätt appliceras-fel domäner (fd) och uppdaterings domäner (UDs). Läs mer om hur du hanterar tillgänglighet för [virtuella Linux-datorer](../articles/virtual-machines/linux/manage-availability.md) och [virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md).

### <a name="fault-domains"></a>Feldomäner
En feldomän är en logisk grupp av underliggande maskinvara som delar en gemensam strömkälla och nätverksswitch, ungefär som ett rack i ett lokalt datacenter. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt dina virtuella datorer mellan dessa feldomäner. På så sätt begränsas påverkan av potentiella fel på fysisk maskinvara, nätverksavbrott och strömavbrott.

### <a name="update-domains"></a>Uppdateringsdomäner
En uppdateringsdomän är en logisk grupp av underliggande maskinvara som kan underhållas eller startas om samtidigt. När du skapar virtuella datorer i en tillgänglighetsuppsättning distribuerar Azure-plattformen automatiskt de virtuella datorerna mellan dessa uppdateringsdomäner. På så sätt säkerställs att minst en instans av ditt program alltid körs vid ett periodiskt underhåll av Azure-plattformen. Ordningen för de uppdateringsdomäner som startas om kanske inte fortsätter i följd under planerat underhåll, men endast en uppdateringsdomän i taget startas om.

![Tillgänglighetsuppsättningar](./media/virtual-machines-common-manage-availability/ud-fd-configuration.png)

### <a name="managed-disk-fault-domains"></a>Hanterade disk fel domäner
För virtuella datorer som använder [Azure Managed Disks](../articles/virtual-machines/windows/faq-for-disks.md) justeras de virtuella datorerna efter feldomänerna för hanterade diskar när en hanterad tillgänglighetsuppsättning används. På så sätt säkerställs att alla hanterade diskar som är kopplade till en virtuell dator finns i samma feldomän. Endast virtuella datorer med hanterade diskar kan skapas i en hanterad tillgänglighetsuppsättning. Antalet feldomäner kan vara två eller tre, beroende på region. Du kan läsa mer om dessa hanterade disk fel domäner för virtuella [Linux-datorer](../articles/virtual-machines/linux/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set) eller [virtuella Windows-datorer](../articles/virtual-machines/windows/manage-availability.md?#use-managed-disks-for-vms-in-an-availability-set).

![Hanterad tillgänglighets uppsättning](./media/virtual-machines-common-manage-availability/md-fd-updated.png)

## <a name="availability-zones"></a>Tillgänglighetszoner

[Tillgänglighets zoner](../articles/availability-zones/az-overview.md), ett alternativ till tillgänglighets uppsättningar, expanderar den kontroll nivå som du behöver för att upprätthålla tillgängligheten för program och data på dina virtuella datorer. En tillgänglighetszon är en fysiskt separat zon i en Azure-region. Det finns tre Tillgänglighetszoner per Azure-region som stöds. Varje tillgänglighetszon har en distinkt strömkälla, nätverk och kylning. Genom att skapa lösningar för att använda replikerade virtuella datorer i zoner kan du skydda dina appar och data från förlust av ett Data Center. Om en zon komprometteras, är replikerade appar och data omedelbart tillgängliga i en annan zon. 

![Tillgänglighetszoner](./media/virtual-machines-common-regions-and-availability/three-zones-per-region.png)

Lär dig mer om att distribuera en virtuell [Windows](../articles/virtual-machines/windows/create-powershell-availability-zone.md) -eller [Linux](../articles/virtual-machines/linux/create-cli-availability-zone.md) -dator i en tillgänglighets zon.


## <a name="next-steps"></a>Nästa steg
Nu kan du börja använda dessa tillgänglighets- och redundansfunktioner till att bygga din egen Azure-miljö. Metodtips hittar du i [Metodtips för Azure-tillgänglighet](../articles/best-practices-availability-checklist.md).

