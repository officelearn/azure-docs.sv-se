---
title: Mappa virtuella nätverk mellan två Azure-regioner i Azure Site Recovery | Microsoft Docs
description: Azure Site Recovery samordnar de replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller till ett sekundärt datacenter.
services: site-recovery
documentationcenter: ''
author: mayurigupta13
manager: rochakm
editor: ''
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 10/16/2018
ms.author: mayg
ms.openlocfilehash: 95e6a388d0638d2fd477d33aaf7c39cf120e29aa
ms.sourcegitcommit: 8e06d67ea248340a83341f920881092fd2a4163c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/16/2018
ms.locfileid: "49353449"
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mappa virtuella nätverk i olika Azure-regioner


Den här artikeln beskriver hur du mappar två instanser av Azure-nätverk finns i olika Azure-regioner med varandra. Nätverksmappning säkerställer att när en replikerad virtuell dator skapas i mål-Azure-region, den virtuella datorn skapas också på det virtuella nätverket som är mappad till det virtuella nätverket för den virtuella källdatorn.  

## <a name="prerequisites"></a>Förutsättningar
Innan du mappa nätverk ska du kontrollera att du har skapat en [Azure-nätverk](../virtual-network/virtual-networks-overview.md) i både regionen källa och mål-Azure-region.

## <a name="map-virtual-networks"></a>Mappa virtuella nätverk

Om du vill mappa ett Azure-nätverk som finns i en Azure-region (källnätverket) till ett virtuellt nätverk som finns i en annan region (målnätverket) för Azure-datorer går du till **Site Recovery-infrastruktur**  >  **Network mappning**. Skapa en nätverksmappning.

![Network mappningar fönstret – Skapa en nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


I följande exempel visas körs den virtuella datorn i regionen östra Asien. Den virtuella datorn replikeras till regionen Sydostasien.

Om du vill skapa en nätverksmappning från regionen Asien till regionen Sydostasien, väljer du platsen för källnätverket och platsen för målnätverket. Välj **OK**.

![Lägger du till nätverket mappning fönster - Välj käll- och målplatserna för källnätverket](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Upprepa föregående stegen för att skapa en nätverksmappning från regionen Sydostasien till regionen Asien.

![Lägg till nätverk mappning fönstret - Välj käll- och målplatserna för Målnätverk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mappa ett nätverk när du aktiverar replikering

När du replikerar en virtuell dator från en Azure-region till en annan region för första gången, om det finns ingen nätverksmappning, kan du ange målnätverket när du konfigurerar replikering. Azure Site Recovery skapar baserat på den här inställningen, nätverksmappningar från regionen källa till målregion och målregionen som källregionen.   

![Konfigurera inställningsfönstret – Välj målplats](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Som standard skapar Site Recovery ett nätverk i målregionen som är identisk med källnätverket. Site Recovery skapar ett nätverk genom att lägga till **-asr** som suffix till namnet på källnätverket. Välj ett nätverk som redan har skapats genom att välja **anpassa**.

![Anpassa target inställningsfönstret - Set target resursgruppens namn och namn på virtuellt nätverk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Om nätverksmappning redan har inträffat, kan du inte ändra det virtuella målnätverket när du aktiverar replikering. I det här fallet om du vill ändra målets virtuella nätverk, ändra den befintliga nätverksmappningen.  

![Anpassa target inställningsfönstret - ange mål resursgruppens namn](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Ändra nätverk mappning fönstret – Ändra ett befintligt virtuellt nätverk målnamn](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Om du ändrar en nätverksmappning från region A till B-region, kontrollerar du att du också ändra nätverksmappningen från B-region till region A.
>
>


## <a name="subnet-selection"></a>Val av undernät
Undernätet för den virtuella måldatorn är valt baserat på namnet på undernätet för den virtuella källdatorn. Om ett undernät som har samma namn som den virtuella källdatorn är tillgängliga i målnätverket, har undernätet angetts för den virtuella måldatorn. Om ett undernät med samma namn inte finns i Målnätverk har alfabetiskt första undernätet angetts som målundernätet.

Om du vill ändra undernätet går du till den **beräkning och nätverk** inställningar för den virtuella datorn.

![Beräkning och nätverk compute egenskapsfönstret](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-adress

IP-adressen för varje nätverksgränssnitt på den virtuella måldatorn har angetts som beskrivs i följande avsnitt.

### <a name="dhcp"></a>DHCP
Om den virtuella källdatorn nätverksgränssnitt använder DHCP, ange nätverksgränssnitt för den virtuella måldatorn också att använda DHCP.

### <a name="static-ip-address"></a>Statisk IP-adress
Om den virtuella källdatorn nätverksgränssnitt använder statisk IP-adress, ange nätverksgränssnitt för den virtuella måldatorn också att använda en statisk IP-adress. I följande avsnitt beskrivs hur en statisk IP-adress har angetts.

### <a name="ip-assignment-behavior-during-failover"></a>IP-tilldelning beteende under redundans
#### <a name="1-same-address-space"></a>1. Samma adressutrymme

Om käll-undernätet och målundernätet har samma-adressutrymme, har IP-adressen för nätverksgränssnittet för den virtuella källdatorn angetts som mål-IP. Om samma IP-adress inte är tillgänglig, har nästa tillgängliga IP-adress angetts som mål-IP.

#### <a name="2-different-address-spaces"></a>2. Olika adressutrymmen

Om käll-undernätet och målundernätet har olika adressutrymmen, har nästa tillgängliga IP-adress i målundernätet angetts som mål-IP.


### <a name="ip-assignment-behavior-during-test-failover"></a>IP-tilldelning beteende under Redundanstest
#### <a name="1-if-the-target-network-chosen-is-the-production-vnet"></a>1. Om målnätverket valt är vNet för produktion
- Recovery IP (mål-IP) är en statisk IP-adress men den **kan inte samma IP-adress** som är reserverat för redundans.
- Den tilldelade IP-adressen kommer att nästa tillgängliga IP-Adressen från slutet av adressintervall för undernätet.
- För t.ex. Om Virtuella statisk IP-adress som är konfigurerad för att vara: 10.0.0.19 och testa redundans gjordes med konfigurerade produktionsnätverket: ***dr-PROD-NV***, med undernätsintervall som 10.0.0.0/24. </br>
Den redundansväxlade virtuella datorn tilldelas med – nästa tillgängliga IP-Adressen från slutet av adressintervall för undernätet är: 10.0.0.254 </br>

**Obs:** termer som **produktion vNet** hänvisar vi till ”målnätverket' mappade under katastrofberedskapskonfigurationen.
#### <a name="2-if-the-target-network-chosen-is-not-the-production-vnet-but-has-the-same-subnet-range-as-production-network"></a>2. Om målnätverket valt är inte produktion vNet men har samma undernätsintervall som produktionsnätverket

- Recovery IP (mål-IP) är en statisk IP-adress med det **samma IP-adress** (d.v.s. konfigurerat statisk IP-adress) som är reserverat för redundans. Den angivna samma IP-adress är tillgänglig.
- Om den angivna statiska IP-Adressen har redan tilldelats en annan virtuell dator/enhet, kommer recovery IP-Adressen att nästa tillgängliga IP-Adressen från slutet av adressintervall för undernätet.
- För t.ex. Om Virtuella statisk IP-adress som är konfigurerad för att vara: 10.0.0.19 och testa redundans gjordes med ett testnätverk: ***dr-icke-PROD-NV***, med samma undernätsintervall som produktionsnätverket - 10.0.0.0/24. </br>
  Den redundansväxlade virtuella datorn tilldelas med följande statisk IP-adress </br>
    - konfigurerad statisk IP-adress: 10.0.0.19 om IP-Adressen är tillgänglig.
    - Nästa tillgängliga IP: 10.0.0.254 om IP-adressen 10.0.0.19 finns redan i använda.


Om du vill ändra mål-IP för varje gränssnitt, går du till den **beräkning och nätverk** inställningar för den virtuella datorn.</br>
Vi rekommenderar rekommenderas alltid för att välja ett testnätverk att utföra Redundanstestning.
## <a name="next-steps"></a>Nästa steg

* Granska [nätverk vägledning för att replikera virtuella Azure-datorer](site-recovery-azure-to-azure-networking-guidance.md).
