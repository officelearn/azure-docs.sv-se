---
title: "Felsökning av problem med nätverksanslutningen mellan virtuella datorer i Azure | Microsoft Docs"
description: "Lär dig hur du felsöker problem med nätverksanslutningen mellan virtuella Azure-datorer."
services: virtual-network
documentationcenter: na
author: chadmath
manager: cshepard
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 08/25/2017
ms.author: genli
ms.openlocfilehash: 3011ef7eced5a24ba07d06e2db2f5e4d344b94de
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Felsökning av problem med nätverksanslutningen mellan virtuella Azure-datorer

Kan det uppstå problem med nätverksanslutningen mellan Azure virtuella datorer (VM). Den här artikeln innehåller åtgärder för att hjälpa dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symtom

En virtuell dator i Azure inte kan ansluta till en annan virtuell dator i Azure.

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrollera om nätverkskortet är felkonfigurerat](#step-1-check-whether-nic-is-misconfigured)
2. [Kontrollera om nätverkstrafik blockeras av NSG eller UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Kontrollera om nätverkstrafik blockeras av VM-brandvägg](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Kontrollera om VM app eller tjänst lyssnar på port](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Kontrollera om problemet beror på SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Kontrollera om trafik blockeras av ACL: er för den klassiska virtuella datorn](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Kontrollera om slutpunkten skapas för den klassiska virtuella datorn](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Försök att ansluta till ett Virtuellt nätverk](#step-8-try-to-connect-to-a-vm-network-share)
9. [Kontrollera Inter-Vnet-anslutningen](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Felsökningssteg

Följ dessa steg om du vill felsöka problemet. När du har slutfört alla steg måste du kontrollera om problemet är löst. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Steg 1: Kontrollera om nätverkskortet är felkonfigurerat

Följ stegen i [återställa nätverksgränssnitt för Virtuella för Windows Azure](../virtual-machines/windows/reset-network-interface.md). 

Om problemet uppstår när du har ändrat nätverksgränssnitt (NIC), gör du följande:

**Flera nätverkskort virtuella datorer**

1. Lägg till ett nätverkskort.
2. Åtgärda problemen på felaktigt NIC eller ta bort felaktiga nätverkskortet.  Lägg sedan till nätverkskortet igen.

Mer information finns i [Lägg till nätverksgränssnitt till eller ta bort från virtuella datorer](virtual-network-network-interface-vm.md).

**Enskild NIC VM** 

- [Distribuera Windows VM](../virtual-machines/windows/redeploy-to-new-node.md)
- [Distribuera virtuell Linux-dator](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Steg 2: Kontrollera om nätverkstrafik blockeras av NSG eller UDR

Använd [nätverk Watcher IP-flöde Kontrollera](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG flödet loggning](../network-watcher/network-watcher-nsg-flow-logging-overview.md) att avgöra om det är en Nätverkssäkerhetsgrupp (NSG) eller User-defined väg (UDR) kan störa trafikflödet.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Steg 3: Kontrollera om nätverkstrafik blockeras av VM-brandvägg

Inaktivera brandväggen och sedan testa resultatet. Om problemet är löst Kontrollera brandväggsinställningar och sedan återaktivera brandväggen.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Steg 4: Kontrollera om VM app eller tjänst lyssnar på port

Du kan använda någon av följande metoder för att kontrollera om den Virtuella app eller tjänst lyssnar på porten.

- Kör följande kommandon för att kontrollera om servern lyssnar på porten.

**Virtuell Windows-dator**

    netstat –ano

**Virtuell Linux-dator**

    netstat -l

- Kör den **telnet** på den virtuella datorn sig själv för att testa porten. Om testet misslyckas, har programmet eller tjänsten inte konfigurerats för att lyssna på porten.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Steg 5: Kontrollera om problemet beror på SNAT

I vissa fall kan är den virtuella datorn placerad bakom en lösning för belastningsutjämna som har ett beroende resurser utanför Azure. I dessa scenarier, om det uppstår återkommande anslutningsproblem, problemet kan orsakas av [SNAT port resursuttömning](../load-balancer/load-balancer-outbound-connections.md). Lös problemet genom att skapa en VIP (eller går för klassisk) för varje virtuell dator som finns bakom belastningsutjämnaren och skydda med NSG eller ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Steg 6: Kontrollera om trafik blockeras av ACL: er för den klassiska virtuella datorn

En åtkomstkontrollista (ACL) ger dig möjlighet att tillåta eller neka trafik för en virtuell dator-slutpunkten. Mer information finns i [hantera ACL på en slutpunkt](../virtual-machines/windows/classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Steg 7: Kontrollera om slutpunkten skapas för den klassiska virtuella datorn

Alla virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan automatiskt kommunicera via en kanal för privat nätverk med andra virtuella datorer i samma molntjänst eller virtuella nätverk. Datorer med andra virtuella nätverk måste dock slutpunkter för att dirigera inkommande nätverkstrafik till en virtuell dator. Mer information finns i [hur du ställer in slutpunkter](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Steg 8: Försök att ansluta till ett Virtuellt nätverk

Om du inte kan ansluta till ett Virtuellt nätverk, kan problemet orsakas av tillgänglig nätverkskort i den virtuella datorn. Om du vill ta bort de tillgängliga nätverkskorten finns [hur du tar bort de tillgängliga nätverkskorten](../virtual-machines/windows/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Steg 9: Kontrollera Inter-Vnet-anslutningen

Använd [nätverk Watcher IP-flöde Kontrollera](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG flödet loggning](../network-watcher/network-watcher-nsg-flow-logging-overview.md) att avgöra om det finns en NSG eller UDR kan störa trafikflödet. Du kan också kontrollera konfigurationen av Inter-Vnet [här](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp [supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) få snabbt lösa problemet.