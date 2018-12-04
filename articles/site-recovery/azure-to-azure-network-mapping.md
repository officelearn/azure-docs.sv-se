---
title: Mappa virtuella nätverk mellan två Azure-regioner i Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery samordnar de replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller till ett sekundärt datacenter.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: mayg
ms.openlocfilehash: 6140687d583534d21ee50652811c2fd1624a5cf5
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/04/2018
ms.locfileid: "52840460"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurera nätverksmappning och IP-adresser för virtuella nätverk

Den här artikeln beskrivs hur du mappar två instanser av virtuella Azure-nätverk (Vnet) finns i olika Azure-regioner och hur du ställer in IP-adressering mellan nätverk. Nätverksmappning så att en replikerad virtuell dator kan skapas i mål-Azure-region har skapats i det virtuella nätverket som är mappad till det virtuella nätverket för den Virtuella källdatorn.

## <a name="prerequisites"></a>Förutsättningar

Innan du mappa nätverk måste du ha [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) i källan och rikta in Azure-regioner. 

## <a name="set-up-network-mapping"></a>Konfigurera nätverksmappning

Mappa nätverk på följande sätt:

1. I **Site Recovery-infrastruktur**, klickar du på **+ nätverksmappning**.

    ![ Skapa en nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. I **Lägg till nätverksmappning**, Välj källa och mål platser. I vårt exempel källan virtuell dator körs i regionen östra Asien, och replikerar till regionen Sydostasien.

    ![Välj källa och mål ](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Skapa nu en nätverksmappning i motsatt katalogen. I vårt exempel källan kommer nu att Sydostasien och målet är Östasien.

    ![Lägg till nätverk mappning fönstret - Välj käll- och målplatserna för Målnätverk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mappa nätverk när du aktiverar replikering

Om du inte har skapat nätverksmappning innan du konfigurerar haveriberedskap för virtuella Azure-datorer kan du ange ett mål nätverk när du [konfigurera och aktivera replikering](azure-to-azure-how-to-enable-replication.md). När du gör detta följande händer:

- Baserat på det mål som du väljer, skapar Site Recovery automatiskt nätverksmappningar från källa till målregion och från målet till källregionen.
- Som standard skapar Site Recovery ett nätverk i målregionen som är identisk med källnätverket. Site Recovery lägger **-asr** som suffix till namnet på källnätverket. Du kan anpassa målnätverket.
- Om nätverksmappning redan har inträffat, kan du inte ändra det virtuella målnätverket när du aktiverar replikering. Om du vill ändra målets virtuella nätverk som du behöver ändra den befintliga nätverksmappningen.
- Om du ändrar en nätverksmappning från region A till B-region, se till att du också ändra nätverksmappningen från B-region till region A.]

## <a name="specify-a-subnet"></a>Ange ett undernät

Undernätet för målet virtuell dator baserat på namnet på undernätet för den Virtuella källdatorn.

- Om ett undernät med samma namn som käll-VM-undernätet är tillgängliga i målnätverket, har undernätet angetts för den Virtuella måldatorn.
- Om ett undernät med samma namn inte finns i Målnätverk, anger det första undernätet i alfabetisk ordning som målundernätet.
- Du kan ändra den i den **beräkning och nätverk** inställningar för den virtuella datorn.

    ![Beräkning och nätverk compute egenskapsfönstret](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurera IP-adresser för virtuella datorer mål

IP-adressen för varje nätverkskort på en virtuell måldator konfigureras på följande sätt:

- **DHCP**: om NIC för den Virtuella källdatorn använder DHCP, NIC för den Virtuella måldatorn är också konfigurerad att använda DHCP.
- **Statisk IP-adress**: om NIC för den Virtuella källdatorn använder statisk IP-adressering, mål VM NIC också använder statisk IP-adress.


## <a name="ip-address-assignment-during-failover"></a>Tilldelning av IP-adress under redundansväxling

**Käll- och undernät** | **Detaljer**
--- | ---
Samma adressutrymme | IP-adressen för källan VM NIC har angetts som mål VM NIC IP-adress.<br/><br/> Om adressen inte är tillgängligt, har nästa tillgängliga IP-adress angetts som mål.
Olika adressutrymmen<br/><br/> Nästa tillgängliga IP-adress i målundernätet har angetts som mål VM NIC-adress.



## <a name="ip-address-assignment-during-test-failover"></a>IP-adresstilldelning under redundanstest

**Målnätverk** | **Detaljer**
--- | ---
Målnätverket är redundans virtuellt nätverk | / Mål-IP-adressen är statisk men inte samma IP-adress som reserverats för redundans.<br/><br/>  -Tilldelade adressen är nästa tillgängliga adress från slutet av undernätsintervallet.<br/><br/> Till exempel: om källans IP-adress är 10.0.0.19 och redundansnätverk använder adressintervallet 10.0.0.0/24, så nästa IP-adress som tilldelats den Virtuella måldatorn är 10.0.0.254.
Målnätverket är inte redundansväxlingen VNet | / Mål-IP-adressen är statisk med samma IP-adress reserveras för växling vid fel.<br/><br/>  -Om samma IP-adress har redan tilldelats, är nästa kommando på eeach intervallets undernät med IP-adressen.<br/><br/> Till exempel: om statiska IP-källadressen är 10.0.0.19 och växling vid fel är i ett nätverk som inte är redundansnätverk, med adressintervallet 10.0.0.0/24, och sedan den statiska IP-adressen för målet att 10.0.0.0.19 om det är tillgängligt och annars blir det 10.0.0.254.

- Redundansväxlingen VNet är målnätverket som du väljer när du konfigurerar haveriberedskap.
- Vi rekommenderar att du alltid använder en icke-produktion-nätverk för redundanstestning.
- Du kan ändra mål-IP-adressen i den **beräkning och nätverk** inställningarna för den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

- Granska [nätverk vägledning](site-recovery-azure-to-azure-networking-guidance.md) för haveriberedskap för virtuella Azure-datorer.
- [Läs mer](site-recovery-retain-ip-azure-vm-failover.md) om behålla IP-adresser efter redundansväxling.

Om målnätverket valt är redundans vnet ”och 2 att säga” om målnätverket valt skiljer sig från redundans virtuella nätverket, men har samma undernätsintervall som redundans vnet ”