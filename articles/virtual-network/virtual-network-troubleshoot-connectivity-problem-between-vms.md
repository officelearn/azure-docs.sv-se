---
title: Felsöka anslutningsproblem mellan virtuella Azure-datorer | Microsoft-dokument
description: Lär dig hur du felsöker anslutningsproblemen mellan virtuella Azure-datorer.
services: virtual-network
documentationcenter: na
author: chadmath
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/30/2018
ms.author: genli
ms.openlocfilehash: ab3ae45081ecc481cb90af8961174e23c86e84b5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71056804"
---
# <a name="troubleshooting-connectivity-problems-between-azure-vms"></a>Felsökning av anslutningsproblem mellan virtuella Azure-datorer

Anslutningsproblem kan uppstå mellan virtuella Azure-datorer (VMs). Den här artikeln innehåller felsökningssteg som hjälper dig att lösa problemet. 

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="symptom"></a>Symptom

En Azure-virtuell dator kan inte ansluta till en annan Azure-virtuell dator.

## <a name="troubleshooting-guidance"></a>Felsökningsanvisningar 

1. [Kontrollera om nätverkskortet är felkonfigurerat](#step-1-check-whether-nic-is-misconfigured)
2. [Kontrollera om nätverkstrafiken blockeras av NSG eller UDR](#step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr)
3. [Kontrollera om nätverkstrafiken blockeras av VM-brandväggen](#step-3-check-whether-network-traffic-is-blocked-by-vm-firewall)
4. [Kontrollera om VM-appen eller tjänsten lyssnar på porten](#step-4-check-whether-vm-app-or-service-is-listening-on-the-port)
5. [Kontrollera om problemet orsakas av SNAT](#step-5-check-whether-the-problem-is-caused-by-snat)
6. [Kontrollera om trafiken blockeras av ACL:er för den klassiska virtuella datorn](#step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm)
7. [Kontrollera om slutpunkten skapas för den klassiska virtuella datorn](#step-7-check-whether-the-endpoint-is-created-for-the-classic-vm)
8. [Försök att ansluta till en virtuell nätverksresurs](#step-8-try-to-connect-to-a-vm-network-share)
9. [Kontrollera inter-vnet-anslutning](#step-9-check-inter-vnet-connectivity)

## <a name="troubleshooting-steps"></a>Felsökningsanvisningar

Så här felsöker du problemet. När du har slutfört varje steg kontrollerar du om problemet är löst. 

### <a name="step-1-check-whether-nic-is-misconfigured"></a>Steg 1: Kontrollera om nätverkskortet är felkonfigurerat

Följ stegen i [Så här återställer du nätverksgränssnittet för Azure Windows VM](../virtual-machines/windows/reset-network-interface.md). 

Om problemet uppstår när du har ändrat nätverksgränssnittet (NIC) gör du så här:

**Virtuella datorer med flera nätverkskort**

1. Lägg till ett nätverkskort.
2. Åtgärda problemen i det dåliga nätverkskortet eller ta bort det dåliga nätverkskortet.  Lägg sedan till nätverkskortet igen.

Mer information finns i [Lägga till nätverksgränssnitt till eller ta bort från virtuella datorer](virtual-network-network-interface-vm.md).

**Virtuell dator med ett nätverkskort** 

- [Distribuera om Windows VM](../virtual-machines/windows/redeploy-to-new-node.md)
- [Distribuera om virtuella Linux-datorer](../virtual-machines/linux/redeploy-to-new-node.md)

### <a name="step-2-check-whether-network-traffic-is-blocked-by-nsg-or-udr"></a>Steg 2: Kontrollera om nätverkstrafiken blockeras av NSG eller UDR

Använd [NETWORK Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) för att avgöra om det finns en NSG (Network Security Group) eller USER-Defined Route (UDR) som stör trafikflödet.

### <a name="step-3-check-whether-network-traffic-is-blocked-by-vm-firewall"></a>Steg 3: Kontrollera om nätverkstrafiken blockeras av VM-brandväggen

Inaktivera brandväggen och testa sedan resultatet. Om problemet är löst kontrollerar du brandväggsinställningarna och aktiverar sedan brandväggen igen.

### <a name="step-4-check-whether-vm-app-or-service-is-listening-on-the-port"></a>Steg 4: Kontrollera om VM-appen eller tjänsten lyssnar på porten

Du kan använda någon av följande metoder för att kontrollera om vm-appen eller tjänsten lyssnar på porten.

- Kör följande kommandon för att kontrollera om servern lyssnar på porten.

**Virtuell Windows-dator**

    netstat –ano

**Virtuella Linux-datorer**

    netstat -l

- Kör **kommandot telnet** på själva den virtuella datorn för att testa porten. Om testet misslyckas är programmet eller tjänsten inte konfigurerad för att lyssna på den porten.

### <a name="step-5-check-whether-the-problem-is-caused-by-snat"></a>Steg 5: Kontrollera om problemet orsakas av SNAT

I vissa fall placeras den virtuella datorn bakom en belastningsbalanslösning som har ett beroende av resurser utanför Azure. I dessa fall, om du upplever återkommande anslutningsproblem, kan problemet orsakas av [SNAT-portutmattning](../load-balancer/load-balancer-outbound-connections.md). Lös problemet genom att skapa en VIP (eller ILPIP för klassisk) för varje virtuell dator som ligger bakom belastningsutjämnaren och säker med NSG eller ACL. 

### <a name="step-6-check-whether-traffic-is-blocked-by-acls-for-the-classic-vm"></a>Steg 6: Kontrollera om trafiken blockeras av ACL:er för den klassiska virtuella datorn

En åtkomstkontrollista (ACL) ger möjlighet att selektivt tillåta eller neka trafik för en slutpunkt för en virtuell dator. Mer information finns i [Hantera åtkomstkontrollistan på en slutpunkt](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

### <a name="step-7-check-whether-the-endpoint-is-created-for-the-classic-vm"></a>Steg 7: Kontrollera om slutpunkten skapas för den klassiska virtuella datorn

Alla virtuella datorer som du skapar i Azure med hjälp av den klassiska distributionsmodellen kan automatiskt kommunicera via en privat nätverkskanal med andra virtuella datorer i samma molntjänst eller virtuellt nätverk. Datorer i andra virtuella nätverk kräver dock slutpunkter för att dirigera inkommande nätverkstrafik till en virtuell dator. Mer information finns i [Så här ställer du in slutpunkter](../virtual-machines/windows/classic/setup-endpoints.md).

### <a name="step-8-try-to-connect-to-a-vm-network-share"></a>Steg 8: Försök att ansluta till en virtuell nätverksresurs

Om du inte kan ansluta till en virtuell nätverksresurs kan problemet bero på otillgängliga nätverkskort i den virtuella datorn. Information om hur du tar bort de nätverkskort som inte är tillgängliga finns i [Så här tar du bort de nätverkskort som inte är tillgängliga](../virtual-machines/troubleshooting/reset-network-interface.md#delete-the-unavailable-nics)

### <a name="step-9-check-inter-vnet-connectivity"></a>Steg 9: Kontrollera inter-vnet-anslutning

Använd [Network Watcher IP Flow Verify](../network-watcher/network-watcher-ip-flow-verify-overview.md) och [NSG Flow Logging](../network-watcher/network-watcher-nsg-flow-logging-overview.md) för att avgöra om det finns en NSG eller UDR som stör trafikflödet. Du kan också verifiera din Inter-Vnet-konfiguration [här](https://support.microsoft.com/en-us/help/4032151/configuring-and-validating-vnet-or-vpn-connections).

### <a name="need-help-contact-support"></a>Behöver du hjälp? Kontakta supporten.
Om du fortfarande behöver hjälp kan du [kontakta supporten](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) så får du hjälp att lösa problemet snabbt.