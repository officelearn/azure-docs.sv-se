---
title: Felsökning av problem med nätverksanslutningen mellan virtuella Azure-datorer | Microsoft Docs
description: Lär dig mer om att felsöka anslutningsproblem mellan virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: fc3d6ab1d7fdf05963d9ecd350deccd940a95b87
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61036410"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Felsökning av problem med nätverksanslutningen mellan virtuella Azure-datorer

Du kan uppleva problem med nätverksanslutningen mellan Azure-datorer (VM). Den här artikeln innehåller felsökningssteg för att hjälpa dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symtom

En virtuell Azure-dator inte kan ansluta till en annan virtuell Azure-dator.

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrollera om nätverkskortet är felkonfigurerat](#step-1-check-whether-nic-is-misconfigured)
2. [Kontrollera om trafik blockeras av NSG- eller UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Kontrollera om trafik blockeras av VM-brandväggen](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Kontrollera om VM-appen eller tjänsten lyssnar på port](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Kontrollera om problemet orsakas av SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Kontrollera om trafik blockeras av ACL: er för klassisk virtuell dator](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Kontrollera om slutpunkten har skapats för klassisk virtuell dator](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Försök att ansluta till en nätverksresurs för virtuell dator](#step-8-try-to-connect-to-a-vm-network-share)
9. [Kontrollera Inter-Vnet-anslutning](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Följ stegen nedan för att felsöka problemet. När du har slutfört alla steg kan du kontrollera om problemet är löst. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Steg 1: Kontrollera om nätverkskortet är felkonfigurerat

Följ stegen i [återställa nätverksgränssnitt för virtuella Azure Windows-datorer](../virtual-machines/windows/reset-network-interface.md). 

Om problemet uppstår när du har ändrat nätverksgränssnitt (NIC), följer du dessa steg:

**Virtuella datorer med flera nätverkskort**

1. Lägg till ett nätverkskort.
2. Åtgärda problem i felaktiga nätverkskortet eller ta bort felaktiga nätverkskortet.  Lägg sedan till nätverkskortet igen.

Mer information finns i [lägga till nätverksgränssnitt till eller ta bort från virtuella datorer](virtual-network-network-interface-vm.md).

**Single-NIC-VM** 

- [Distribuera om Windows VM](../virtual-machines/windows/redeploy-to-new-node.md)
- [Distribuera om virtuell Linux-dator](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Steg 2: Kontrollera om trafik blockeras av NSG- eller UDR

Använd [Network Watcher IP-Flödesverifieringen](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [Flödesloggning för NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) att avgöra om det är en Nätverkssäkerhetsgrupp (NSG) eller användardefinierade vägen (UDR) kan störa trafikflödet.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Steg 3: Kontrollera om trafik blockeras av VM-brandväggen

Inaktivera brandväggen och testar sedan resultatet. Om problemet är löst, Kontrollera brandväggsinställningarna och sedan återaktivera brandväggen.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Steg 4: Kontrollera om VM-appen eller tjänsten lyssnar på port

Du kan använda någon av följande metoder för att kontrollera om VM-appen eller tjänsten lyssnar på porten.

- Kör följande kommandon för att kontrollera om servern lyssnar på porten.

**Virtuell Windows-dator**

    netstat –ano

**Virtuell Linux-dator**

    netstat -l

- Kör den **telnet** på den virtuella datorn sig själv för att testa porten. Om testet misslyckas, har programmet eller tjänsten inte konfigurerats för att lyssna på porten.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Steg 5: Kontrollera om problemet orsakas av SNAT

I vissa fall kan är den virtuella datorn placerad bakom en belastning saldo lösning som har ett beroende på resurser utanför Azure. I dessa scenarier, om det uppstår problem med tillfällig anslutning, problemet kan orsakas av [SNAT portöverbelastning](../load-balancer/load-balancer-outbound-connections.md). Lös problemet genom att skapa en VIP (eller ILPIP för klassisk) för varje virtuell dator som finns bakom belastningsutjämnaren och skydda med NSG eller ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Steg 6: Kontrollera om trafik blockeras av ACL: er för klassisk virtuell dator

En åtkomstkontrollista (ACL) ger möjlighet att tillåta eller neka trafik för en virtuell dator-slutpunkten. Mer information finns i [hantera ACL på en slutpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Steg 7: Kontrollera om slutpunkten har skapats för klassisk virtuell dator

Alla virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan kommunicera automatiskt via en kanal för privata nätverk med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer i andra virtuella nätverk kräver dock slutpunkter dirigera inkommande trafik till en virtuell dator. Mer information finns i [så här ställer du in slutpunkter](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Steg 8: Försök att ansluta till en nätverksresurs för virtuell dator

Om du inte kan ansluta till en nätverksresurs för virtuell dator, kan problemet ha orsakats av otillgänglig nätverkskort på den virtuella datorn. Om du vill ta bort de tillgängliga nätverkskorten, se [hur du tar bort otillgänglig nätverkskort](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Steg 9: Kontrollera Inter-Vnet-anslutning

Använd [Network Watcher IP-Flödesverifieringen](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [Flödesloggning för NSG](../network-watcher/network-watcher-nsg-flow-logging-overview.md) att avgöra om det finns en NSG- eller UDR kan störa trafikflödet. Du kan också kontrollera Inter-Vnet-konfigurationen [här](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp, [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) att lösa problemet snabbt.