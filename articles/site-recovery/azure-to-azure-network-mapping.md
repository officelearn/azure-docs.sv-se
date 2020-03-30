---
title: Mappa virtuella nätverk mellan två regioner i Azure Site Recovery
description: Lär dig mer om att mappa virtuella nätverk mellan två Azure-regioner för Azure VM-haveriberedskap med Azure Site Recovery.
author: mayurigupta13
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 10/15/2019
ms.author: mayg
ms.openlocfilehash: b8f0512f978f25ca196ad6e9a7a03243c47f0662
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258088"
---
# <a name="set-up-network-mapping-and-ip-addressing-for-vnets"></a>Konfigurera nätverksmappning och IP-adressering för virtuella nätverk

I den här artikeln beskrivs hur du mappar två instanser av virtuella Azure-nätverk (VNets) som finns i olika Azure-regioner och hur du konfigurerar IP-adressering mellan nätverk. Nätverksmappning ger standardbeteende för val av målnätverk baserat på källnätverk vid tidpunkten för replikering.

## <a name="prerequisites"></a>Krav

Innan du mappar nätverk bör du ha [Virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) i käll- och målregionerna för Azure. 

## <a name="set-up-network-mapping-manually-optional"></a>Konfigurera nätverksmappning manuellt (valfritt)

Kartnätverk enligt följande:

1. Klicka på **+Nätverksmappning**i infrastruktur för **platsåterställning**.

    ![ Skapa en nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)

3. Välj käll- och målplatser i **Lägg till nätverksmappning.** I vårt exempel körs källdatorn i regionen Östasien och replikeras till regionen Sydostasien.

    ![Välj källa och mål](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)
3. Nu skapa ett nätverk kartläggning i motsatt riktning. I vårt exempel kommer källan nu att vara Sydostasien, och målet kommer att vara Östasien.

    ![Fönstret Lägg till nätverksmappning – Välj käll- och målplatser för målnätverket](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-networks-when-you-enable-replication"></a>Kartnätverk när du aktiverar replikering

Om du inte har förberett nätverksmappning innan du konfigurerar haveriberedskap för virtuella Azure-datorer kan du ange ett målnätverk när du [konfigurerar och aktiverar replikering](azure-to-azure-how-to-enable-replication.md). När du gör detta händer följande:

- Baserat på det mål du väljer skapar Site Recovery automatiskt nätverksmappningar från käll till målregion och från mål- till källområdet.
- Som standard skapar Site Recovery ett nätverk i målområdet som är identiskt med källnätverket. Site Recovery lägger **till -asr** som ett suffix i namnet på källnätverket. Du kan anpassa målnätverket.
- Om nätverksmappning redan har inträffat för ett källnätverk är det mappade målnätverket alltid standard vid tidpunkten för aktivering av replikeringar för fler virtuella datorer. Du kan välja att ändra målvirkad nätverksanslutet genom att välja andra tillgängliga alternativ i listrutan. 
- Om du vill ändra standardmålvirtualt nätverk för nya replikeringar måste du ändra den befintliga nätverksmappningen.
- Om du vill ändra en nätverksmappning från region A till region B ska du se till att du först tar bort nätverksmappningen från region B till region A. När omvänd mappning har tagits bort ändrar du nätverksmappningen från region A till region B och skapar sedan relevant omvänd mappning.

>[!NOTE]
>* Om du ändrar nätverksmappningen ändras bara standardinställningarna för nya VM-replikeringar. Det påverkar inte målvalet för virtuella nätverk för befintliga replikeringar. 
>* Om du vill ändra målnätverket för en befintlig replikering går du till Beräknings- och nätverksinställningar för det replikerade objektet.

## <a name="specify-a-subnet"></a>Ange ett undernät

Undernätet för måldatorn väljs baserat på namnet på undernätet för källdatorn.

- Om ett undernät med samma namn som käll-VM-undernätet är tillgängligt i målnätverket anges det undernätet för måldatorn.
- Om det inte finns ett undernät med samma namn i målnätverket anges det första undernätet i alfabetisk ordning som målundernät.
- Du kan ändra målundernätet i **beräknings- och nätverksinställningarna** för den virtuella datorn.

    ![Fönstret Beräknings- och nätverksberäkningsegenskaper](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="set-up-ip-addressing-for-target-vms"></a>Konfigurera IP-adressering för virtuella mål-datorer

IP-adressen för varje nätverkskort på en virtuell måldator är konfigurerad enligt följande:

- **DHCP**: Om nätverkskortet för källdatorn använder DHCP är nätverkskortet för måldatorn också inställt på att använda DHCP.
- **Statisk IP-adress:** Om nätverkskortet för källdatorn använder statisk IP-adressering, använder mål-VM-nätverkskortet också en statisk IP-adress.


## <a name="ip-address-assignment-during-failover"></a>IP-adresstilldelning under redundans

**Undernät för källa och mål** | **Detaljer**
--- | ---
Samma adressutrymme | IP-adressen för käll-VM-nätverkskortet anges som IP-adressen för mål-VM-nätverkskortet.<br/><br/> Om adressen inte är tillgänglig anges nästa tillgängliga IP-adress som mål.
Olika adressutrymme | Nästa tillgängliga IP-adress i målundernätet anges som mål-VM-nätverkskortsadress.



## <a name="ip-address-assignment-during-test-failover"></a>IP-adresstilldelning under test redundans

**Målnätverk** | **Detaljer**
--- | ---
Målnätverket är det virtuella redundansnätverket | - Mål-IP-adressen kommer att vara statisk med samma IP-adress. <br/><br/>  - Om samma IP-adress redan har tilldelats är IP-adressen nästa som är tillgänglig i slutet av undernätsområdet. Till exempel: Om käll-IP-adressen är 10.0.0.19 och redundansnätverk använder intervall 10.0.0.0/24, är nästa IP-adress som tilldelats måldatorn 10.0.0.254.
Målnätverk är inte det virtuella redundansnätverket | - Mål-IP-adressen kommer att vara statisk med samma IP-adress.<br/><br/>  - Om samma IP-adress redan har tilldelats är IP-adressen nästa som är tillgänglig i slutet av undernätsområdet.<br/><br/> Till exempel: Om källans statiska IP-adress är 10.0.0.19 och redundans finns i ett nätverk som inte är redundansnätverket, med intervallet 10.0.0.0/24, blir den statiska IP-adressen 10.0.0.0.19 om tillgängligt, och annars blir den 10.0.0.254.

- Redundans-virtuella nätverk är det målnätverk som du väljer när du ställer in haveriberedskap.
- Vi rekommenderar att du alltid använder ett icke-produktionsnätverk för test redundans.
- Du kan ändra mål-IP-adressen i **beräknings- och nätverksinställningarna** för den virtuella datorn.


## <a name="next-steps"></a>Nästa steg

- Granska [nätverksvägledning](site-recovery-azure-to-azure-networking-guidance.md) för azure vm-haveriberedskap.
- [Läs mer](site-recovery-retain-ip-azure-vm-failover.md) om hur du behåller IP-adresser efter redundans.
