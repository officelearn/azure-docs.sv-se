---
title: Mappa virtuella nätverk mellan två Azure-regioner i Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery samordnar de replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller till ett sekundärt datacenter.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 4/9/2019
ms.author: mayg
ms.openlocfilehash: d08715b1b3e0db4dfcf31bb4c020ab44ed3916e1
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/13/2019
ms.locfileid: "59549058"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurera nätverksmappning och IP-adresser för virtuella nätverk

Den här artikeln beskrivs hur du mappar två instanser av virtuella Azure-nätverk (Vnet) finns i olika Azure-regioner och hur du ställer in IP-adressering mellan nätverk. Nätverksmappning innehåller en standardmetod för val av mål-nätverk baserat på källnätverket vid tidpunkten för att aktivera replikering.

## <a name="prerequisites"></a>Nödvändiga komponenter

Innan du mappa nätverk måste du ha [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) i källan och rikta in Azure-regioner. 

## <a name="set-up-network-mapping-manually-optional"></a>Konfigurera nätverksmappning manuellt (valfritt)

Mappa nätverk på följande sätt:

1. I **Site Recovery-infrastruktur**, klickar du på **+ nätverksmappning**.

    ![ Skapa en nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. I **Lägg till nätverksmappning**, Välj källa och mål platser. I vårt exempel källan virtuell dator körs i regionen östra Asien, och replikerar till regionen Sydostasien.

    ![Välj källa och mål](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Skapa nu en nätverksmappning i motsatt katalogen. I vårt exempel källan kommer nu att Sydostasien och målet är Östasien.

    ![Lägg till nätverk mappning fönstret - Välj käll- och målplatserna för Målnätverk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Mappa nätverk när du aktiverar replikering

Om du inte har skapat nätverksmappning innan du konfigurerar haveriberedskap för virtuella Azure-datorer kan du ange ett mål nätverk när du [konfigurera och aktivera replikering](azure-to-azure-how-to-enable-replication.md). När du gör detta följande händer:

- Baserat på det mål som du väljer, skapar Site Recovery automatiskt nätverksmappningar från källa till målregion och från målet till källregionen.
- Som standard skapar Site Recovery ett nätverk i målregionen som är identisk med källnätverket. Site Recovery lägger **-asr** som suffix till namnet på källnätverket. Du kan anpassa målnätverket.
- Om nätverksmappning redan har inträffat för en källnätverket mappade målnätverket alltid att vara standard vid tidpunkten för att aktivera replikeringar för flera virtuella datorer. Du kan välja att ändra det virtuella målnätverket genom att välja andra tillgängliga alternativ i listrutan. 
- Om du vill ändra standard målets virtuella nätverk för nya replikeringar, måste du ändra den befintliga nätverksmappningen.
- Om du vill ändra en nätverksmappning från region A till B-region, se till att du först ta bort nätverksmappningen från B-region till region A. Ändrar nätverksmappning från region A till B-region efter omvänd mappning borttagningen och skapa sedan den relevanta omvända mappningen.

>[!NOTE]
>* Ändra nätverksmappningen ändras bara standardinställningarna för nya VM-replikeringar. Det påverkar inte target virtuellt nätverk valen för befintliga replikeringar. 
>* Om du vill ändra målnätverket för en befintlig replikering, gå till beräknings- och nätverksinställningarna för det replikerade objektet.

## <a name="specify-a-subnet"></a>Ange ett undernät

Undernätet för målet virtuell dator baserat på namnet på undernätet för den Virtuella källdatorn.

- Om ett undernät med samma namn som käll-VM-undernätet är tillgängliga i målnätverket, har undernätet angetts för den Virtuella måldatorn.
- Om ett undernät med samma namn inte finns i Målnätverk, anger det första undernätet i alfabetisk ordning som målundernätet.
- Du kan ändra den i den **beräkning och nätverk** inställningar för den virtuella datorn.

    ![Beräkning och nätverk compute egenskapsfönstret](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurera IP-adresser för virtuella datorer mål

IP-adressen för varje nätverkskort på en virtuell måldator konfigureras på följande sätt:

- **DHCP**: Om nätverkskortet på den Virtuella källdatorn använder DHCP, är också NIC för den Virtuella måldatorn konfigurerad att använda DHCP.
- **Statisk IP-adress**: Om nätverkskortet på källan virtuell dator använder statiska IP-adresser, mål VM NIC också att använda en statisk IP-adress.


## <a name="ip-address-assignment-during-failover"></a>Tilldelning av IP-adress under redundansväxling

**Käll- och undernät** | **Detaljer**
--- | ---
Samma adressutrymme | IP-adressen för källan VM NIC har angetts som mål VM NIC IP-adress.<br/><br/> Om adressen inte är tillgängligt, har nästa tillgängliga IP-adress angetts som mål.

Olika adressutrymmen<br/><br/> Nästa tillgängliga IP-adress i målundernätet har angetts som mål VM NIC-adress.



## <a name="ip-address-assignment-during-test-failover"></a>IP-adresstilldelning under redundanstest

**Målnätverk** | **Detaljer**
--- | ---
Målnätverket är redundans virtuellt nätverk | / Mål-IP-adressen är statisk men inte samma IP-adress som reserverats för redundans.<br/><br/>  -Tilldelade adressen är nästa tillgängliga adress från slutet av undernätsintervallet.<br/><br/> Exempel: Om källans IP-adress är 10.0.0.19 och redundansnätverk använder adressintervallet 10.0.0.0/24, är nästa IP-adress som tilldelats den Virtuella måldatorn 10.0.0.254.
Målnätverket är inte redundansväxlingen VNet | / Mål-IP-adressen är statisk med samma IP-adress reserveras för växling vid fel.<br/><br/>  – Om samma IP-adress har redan tilldelats, är det nästa tillgängliga i slutet av undernätsintervallet med IP-adressen.<br/><br/> Exempel: Om statiska IP-källadressen är 10.0.0.19 och växling vid fel är i ett nätverk som inte är nätverket för redundanstestet, med adressintervallet 10.0.0.0/24, kommer den statiska IP-adressen för målet vara 10.0.0.0.19 om det är tillgängligt och annars blir det 10.0.0.254.

- Redundansväxlingen VNet är målnätverket som du väljer när du konfigurerar haveriberedskap.
- Vi rekommenderar att du alltid använder en icke-produktion-nätverk för redundanstestning.
- Du kan ändra mål-IP-adressen i den **beräkning och nätverk** inställningarna för den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

- Granska [nätverk vägledning](site-recovery-azure-to-azure-networking-guidance.md) för haveriberedskap för virtuella Azure-datorer.
- [Läs mer](site-recovery-retain-ip-azure-vm-failover.md) om behålla IP-adresser efter redundansväxling.

Om målnätverket valt är redundans vnet ”och 2 att säga” om målnätverket valt skiljer sig från redundans virtuella nätverket, men har samma undernätsintervall som redundans vnet ”
