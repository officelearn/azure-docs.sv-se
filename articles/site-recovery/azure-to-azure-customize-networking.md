---
title: Anpassa nätverkskonfigurationer för en vm-tjänst för redundans | Microsoft-dokument
description: Innehåller en översikt över hur du anpassar nätverkskonfigurationer för en återställnings-VM i replikering av virtuella Azure-datorer med Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 10/21/2019
ms.author: rajanaki
ms.openlocfilehash: 96ffa34166797945afc04c66b03fe151d26c65bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76292866"
---
# <a name="customize-networking-configurations-of-the-target-azure-vm"></a>Anpassa nätverkskonfigurationer för den virtuella Azure-måldatorn

Den här artikeln innehåller vägledning om hur du anpassar nätverkskonfigurationer på den virtuella virtuella datorn för mål Azure (VM) när du replikerar och återställer virtuella Azure-datorer från en region till en annan med hjälp av [Azure Site Recovery](site-recovery-overview.md).

## <a name="before-you-start"></a>Innan du börjar

Lär dig hur Site Recovery ger haveriberedskap för [det här scenariot](azure-to-azure-architecture.md).

## <a name="supported-networking-resources"></a>Nätverksresurser som stöds

Du kan tillhandahålla följande nyckelresurskonfigurationer för den virtuella redundansdatorn när du replikerar virtuella Azure-datorer:

- [Intern belastningsutjämnare](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)
- [Offentlig IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm#public-ip-addresses)
- [Nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group) både för undernätet och för nätverkskortet

## <a name="prerequisites"></a>Krav

- Se till att du planerar dina konfigurationer på återställningssidan i förväg.
- Skapa nätverksresurserna i förväg. Ange det som en indata så att Azure Site Recovery-tjänsten kan uppfylla dessa inställningar och se till att redundans-VM följer dessa inställningar.

## <a name="customize-failover-and-test-failover-networking-configurations"></a>Anpassa redundans- och testväxlingsnätverkskonfigurationer

1. Gå till **replikerade objekt**. 
2. Välj önskad Azure VM.
3. Välj **Beräkna och nätverk** och välj **Redigera**. Observera att konfigurationsinställningarna för nätverkskortet innehåller motsvarande resurser vid källan. 

     ![Anpassa redundansnätverkskonfigurationerna](media/azure-to-azure-customize-networking/edit-networking-properties.png)

4. Välj ett virtuellt test failover-nätverk. Du kan välja att lämna den tom och välja en vid tidpunkten för test redundans.
5. Redundansnätverk är Välj **Redigera** nära det nätverkskort som du vill konfigurera. I nästa blad som öppnas väljer du motsvarande förskapade resurser på test redundans- och redundansplatsen.

    ![Redigera nätverkskortets konfiguration](media/azure-to-azure-customize-networking/nic-drilldown.png) 

6. Välj **OK**.

Site Recovery kommer nu att uppfylla dessa inställningar och se till att den virtuella datorn vid redundans är ansluten till den valda resursen via motsvarande nätverkskort.

När du utlöser test redundans via återställningsplan, kommer det alltid att be Azure virtuella nätverket. Det här virtuella nätverket används för test redundans för de datorer som inte har förkonfigurerade testundanvikelseinställningar.

## <a name="troubleshooting"></a>Felsökning

### <a name="unable-to-view-or-select-a-resource"></a>Det går inte att visa eller välja en resurs

Om du inte kan välja eller visa en nätverksresurs går du igenom följande kontroller och villkor:

- Målfältet för en nätverksresurs aktiveras endast om källdatorn hade en motsvarande indata. Detta baseras på principen att för ett katastrofåterställningsscenario vill du ha antingen den exakta eller nedskalade versionen av källan.
- För varje nätverksresurs används vissa filter i listrutan för att säkerställa att den virtuella redundansen kan koppla sig till den valda resursen och redundanstillförlitligheten bibehålls. Dessa filter baseras på samma nätverksvillkor som skulle ha verifierats när du konfigurerade källdatorn.

Valideringar för intern belastningsutjämnare:

- Abonnemang och region för belastningsutjämnaren och måldatorn ska vara desamma.
- Det virtuella nätverket som är associerat med den interna belastningsutjämnaren och måldatorns bör vara detsamma.
- Måldasssens offentliga IP-SKU och den interna belastningsutjämnarens SKU bör vara samma.
- Om måldatorn är konfigurerad för att placeras i en tillgänglighetszon kontrollerar du om belastningsutjämnaren är zonlös eller en del av någon tillgänglighetszon. (Grundläggande SKU-belastningsutjämnare stöder inte zoner och visas inte i listrutan i det här fallet.)
- Kontrollera att den interna belastningsutjämnaren har en förskapad backend-pool och frontend-konfiguration.

Offentlig IP-adress:

- Prenumerationen och regionen för den offentliga IP-adressen och måldatorn ska vara desamma.
- Måldasssens offentliga IP-SKU och den interna belastningsutjämnarens SKU bör vara samma.

Nätverkssäkerhetsgrupp:
- Prenumerationen och regionen för nätverkssäkerhetsgruppen och måldatorn ska vara desamma.


> [!WARNING]
> Om måldatorn är associerad med en tillgänglighetsuppsättning måste du associera den offentliga IP-adressen och intern belastningsutjämnare för samma SKU med den andra virtuella datorns offentliga IP- och internbelastningsbalanserare i tillgänglighetsuppsättningen. Om du inte gör det kanske redundansen inte lyckas.
