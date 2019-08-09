---
title: Anpassa nätverkskonfigurationer för virtuell dator för redundansväxling | Microsoft Docs
description: Innehåller en översikt över hur du anpassar nätverkskonfigurationer för virtuella datorer med virtuella datorer i replikeringen av virtuella Azure-datorer med hjälp av Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 08/07/2019
ms.author: rajanaki
ms.openlocfilehash: 907a698a675a039dfdc852210adecb94c7bfab25
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2019
ms.locfileid: "68886902"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Anpassa nätverkskonfigurationer för den virtuella Azure-datorn

Den här artikeln innehåller vägledning om hur du anpassar nätverkskonfigurationer på den virtuella Azure-datorn när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveri beredskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="support-networking-resources"></a>Stöd för nätverks resurser

Följande konfigurationer av nyckel resurser kan anges för den virtuella datorn med redundans vid replikering av virtuella Azure-datorer.

- [Intern lastbalanserare](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-overview#what-is-standard-load-balancer)
- [Offentlig IP-adress](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Nätverks säkerhets grupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) både för under nätet och för nätverkskortet

## <a name="pre-requisites"></a>Förutsättningar

- Se till att du planerar konfigurationen för återställnings sidan i förväg.
- Du måste skapa nätverks resurserna i förväg. Ange den som inmatad så att Azure Site Recovery tjänst kan följa dessa inställningar och se till att den virtuella redundansväxlingen följer de här inställningarna.

## <a name="steps-to-customize-failover-networking-configurations"></a>Steg för att anpassa nätverks konfigurationerna för redundans

1. Navigera till **replikerade objekt**. 
2. Klicka på önskad virtuell Azure-dator.
3. Klicka på **beräkning och nätverk**och **Redigera**. Du kommer att märka att konfigurations inställningarna för NÄTVERKSKORTen omfattar motsvarande resurser på källan. 

     ![anpassad](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Klicka på **Redigera** nära det nätverkskort som du vill konfigurera. I nästa blad som öppnas väljer du motsvarande förskapade resurser i målet.

    ![NIC-specificering](media/azure-to-azure-customize-networking/nic-drilldown.png) 

5. Klicka på **OK**.

Site Recovery kommer nu att respektera dessa inställningar och se till att den virtuella datorn vid redundansväxling är ansluten till den valda resursen via motsvarande nätverkskort.

## <a name="troubleshooting"></a>Felsökning

### <a name="unable-to-view-or-select-a-resource"></a>Det går inte att visa eller välja en resurs

Om du inte kan välja eller Visa en nätverks resurs går du igenom följande kontroller & villkor:

- Mål fältet för en nätverks resurs aktive ras bara om den virtuella käll datorn hade motsvarande ininformation. Detta baseras på principen om ett haveri återställnings scenario vill du ha antingen exakt eller skalad ned version av källan.
- För var och en av nätverks resurserna i fråga används vissa filter i list rutan för att se till att den virtuella redundansväxlingen kan koppla sig till den valda resursen och tillförlitligheten för redundansen upprätthålls. Dessa filter baseras på samma nätverks villkor som skulle ha verifierats när du konfigurerade den virtuella käll datorn.

Verifieringar av interna belastningsutjämnare:

1. Prenumerationen och regionen LB och den virtuella mål datorn måste vara samma.
2. Det virtuella nätverk som är associerat med den interna Load Balancer och som mål-VMshould är detsamma.
3. Den virtuella mål datorns offentliga IP SKU och den interna belastningsutjämnarens SKU: n bör vara samma.
4. Om den virtuella mål datorn har kon figurer ATS för att placeras i en tillgänglighets zon kontrollerar du om belastningsutjämnaren är zon redundant eller ingår i en tillgänglighets zon. (Basic SKU-belastningsutjämnare har inte stöd för zoner och visas inte i list rutan i det här fallet.)
5. Se till att den interna LoadBalancer har en förskapad backend-pool och konfiguration på klient sidan.


Offentlig IP-adress:
    
1. Prenumerationen och regionen för den offentliga IP-adressen och den virtuella mål datorn måste vara samma.
2. Den virtuella mål datorns offentliga IP SKU och den interna belastningsutjämnarens SKU: n bör vara samma.

Nätverks säkerhets grupp:
1. Prenumerationen och regionen för nätverks säkerhets gruppen och den virtuella mål datorn måste vara samma.


> [!WARNING]
> Om den virtuella mål datorn är kopplad till en tillgänglighets uppsättning måste du associera den offentliga IP/interna belastningsutjämnaren för samma SKU som den andra virtuella datorns offentliga IP/interna belastningsutjämnare i tillgänglighets uppsättningen. Om du inte gör det kan det uppstå ett fel i redundansväxlingen.
