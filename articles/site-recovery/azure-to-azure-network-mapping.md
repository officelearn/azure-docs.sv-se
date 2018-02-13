---
title: "Mappa virtuella nätverk mellan två Azure-regioner i Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller till ett sekundärt datacenter."
services: site-recovery
documentationcenter: 
author: mayanknayar
manager: rochakm
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 12/15/2017
ms.author: manayar
ms.openlocfilehash: d7dd35a8382f4a99ababbe804c5c71b29148c44a
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="map-virtual-networks-in-different-azure-regions"></a>Mappa virtuella nätverk i olika Azure-regioner


Den här artikeln beskriver hur du mappar två instanser av Azure-nätverk finns i olika Azure-regioner med varandra. Nätverksmappning så att när en replikerad virtuell dator skapas i mål-Azure-region, skapas den virtuella datorn även på det virtuella nätverket som är mappad till det virtuella nätverket för den virtuella källdatorn.  

## <a name="prerequisites"></a>Förutsättningar
Innan du ansluter nätverk, kontrollera att du har skapat en [virtuella Azure-nätverket](../virtual-network/virtual-networks-overview.md) i både region för källan och målet Azure-region.

## <a name="map-virtual-networks"></a>Mappa virtuella nätverk

Om du vill mappa Azure-nätverk som finns i ett Azure region (Källnätverk) till ett virtuellt nätverk som finns i en annan region (målnätverket) i virtuella Azure-datorer, gå till **Site Recovery-infrastruktur**  >  **Nätverk mappning**. Skapa en nätverksmappning.

![Nätverk mappningar fönstret - Skapa en nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


I följande exempel körs den virtuella datorn i Östasien region. Den virtuella datorn replikeras till Sydostasien region.

Om du vill skapa en nätverksmappning från regionen Östasien till Sydostasien region, välj platsen för källnätverket och platsen för målnätverket. Markera **OK**.

![Lägga till nätverket mappning window - Välj käll- och målplatserna för källnätverket](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Upprepa föregående process för att skapa en nätverksmappning från området Sydostasien Östasien regionen.

![Lägg till nätverket mappning fönstret - Välj käll- och målplatserna för målnätverket](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="map-a-network-when-you-enable-replication"></a>Mappa ett nätverk när du aktiverar replikering

När du replikerar en virtuell dator från en Azure-region till en annan region för första gången, om det finns ingen nätverksmappning, kan du ange målnätverket när du konfigurerar replikering. Baserat på den här inställningen, skapar Azure Site Recovery nätverksmappningar från källområdet till målet region och målregionen till käll-region.   

![Konfigurera inställningar – Välj målplatsen](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Som standard skapar Site Recovery ett nätverk i mål-region som är identisk med källnätverket. Skapar site Recovery ett nätverk genom att lägga till **-asr** som suffix till namnet på Källnätverk. Välj ett nätverk som redan har skapats genom att markera **anpassa**.

![Anpassa mål inställningsfönstret - Set target resursgruppens namn och namn på virtuellt nätverk](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)

Om nätverksmappning redan har inträffat, kan du inte ändra det virtuella målnätverket när du aktiverar replikering. I det här fallet om du vill ändra det virtuella målnätverket, ändra den befintliga nätverksmappningen.  

![Anpassa mål rutan inställningar - ange mål resursgruppens namn](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Ändra nätverk mappning rutan: ändra ett befintligt virtuellt nätverk målnamn](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Om du ändrar nätverksmappning från regionen A till B-region, kontrollera att du också ändra nätverksmappning från regionen B till region A.
>
>


## <a name="subnet-selection"></a>Val av undernät
Undernätet för den virtuella måldatorn är valt baserat på namnet på undernätet för den virtuella källdatorn. Om ett undernät som har samma namn som den virtuella källdatorn är tillgängliga i målnätverket, har undernätet angetts för den virtuella måldatorn. Om ett undernät med samma namn inte finns i målnätverket, har alfabetiskt första undernätet angetts som mål-undernät. 

Om du vill ändra undernätet, gå till den **beräknings- och nätverksinställningar** inställningar för den virtuella datorn.

![Beräknings- och beräkna egenskapsfönstret](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-adress

IP-adressen för varje nätverksgränssnitt för den virtuella måldatorn har angetts som beskrivs i följande avsnitt.

### <a name="dhcp"></a>DHCP
Om nätverksgränssnittet för den virtuella källdatorn använder DHCP, är nätverksgränssnittet för den virtuella måldatorn också konfigurerad att använda DHCP.

### <a name="static-ip-address"></a>Statisk IP-adress
Om nätverksgränssnittet för den virtuella källdatorn använder en statisk IP-adress, anges också nätverksgränssnittet för den virtuella måldatorn till en statisk IP-adress. I följande avsnitt beskrivs hur en statisk IP-adress har angetts.

#### <a name="same-address-space"></a>Samma-adressutrymme

Om käll-undernät och mål-undernät har samma adressutrymme, har IP-adressen för nätverksgränssnittet för den virtuella källdatorn angetts som mål-IP-adress. Om samma IP-adress inte är tillgänglig har nästa tillgängliga IP-adress angetts som mål-IP-adress.

#### <a name="different-address-spaces"></a>Olika adressutrymmen

Om käll-undernät och mål-undernät har olika adressutrymmen, har nästa tillgängliga IP-adressen i undernätet som mål angetts som mål-IP-adress.

Om du vill ändra mål-IP för varje gränssnitt, gå till den **beräknings- och nätverksinställningar** inställningar för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

* Granska [nätverk vägledning för att replikera virtuella datorer i Azure](site-recovery-azure-to-azure-networking-guidance.md).
