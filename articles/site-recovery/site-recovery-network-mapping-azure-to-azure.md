---
title: "Nätverksmappningen mellan två Azure-regioner i Azure Site Recovery | Microsoft Docs"
description: "Azure Site Recovery samordnar replikering, redundans och återställning av virtuella datorer och fysiska servrar. Läs mer om redundans till Azure eller ett sekundärt datacenter."
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
ms.openlocfilehash: bf3d557c77e3cb6ade6f1bb3773c807f9c8b43f6
ms.sourcegitcommit: 357afe80eae48e14dffdd51224c863c898303449
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="network-mapping-between-two-azure-regions"></a>Nätverksmappningen mellan två Azure-regioner


Den här artikeln beskriver hur du mappa virtuella Azure-nätverk av två Azure-regioner med varandra. Nätverksmappning säkerställer att när en replikerad virtuell dator skapas i målet Azure-region skapas på det virtuella nätverket som är mappad till det virtuella nätverket för den virtuella källdatorn.  

## <a name="prerequisites"></a>Krav
Innan du ansluter nätverk, kontrollera att du har skapat [virtuella Azure-nätverk](../virtual-network/virtual-networks-overview.md) i både källa och mål-Azure-regioner.

## <a name="map-networks"></a>Mappa nätverk

Azure-nätverk i en Azure-region att mappa till ett annat virtuellt nätverk i en annan region, gå till Site Recovery-infrastruktur -> nätverk-mappning (för Azure virtuella datorer) och skapa en nätverksmappning.

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping1.png)


I exemplet nedan den virtuella datorn körs i Östasien region och replikeras till Sydostasien.

Välj källa och mål nätverk och klicka sedan på OK om du vill skapa en nätverksmappning från Östasien till Sydostasien.

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping2.png)


Upprepa ovanstående procedur för att skapa en nätverksmappning från Sydostasien till Östasien.

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping3.png)


## <a name="mapping-network-when-enabling-replication"></a>Mappa nätverk när du aktiverar replikering

Om nätverket inte har mappats när du replikerar en virtuell dator för första gången från en Azure-region till en annan, kan du välja målnätverket som en del av samma process. Nätverksmappningar skapar site Recovery från käll-region för målregionen och målregionen till källan region baserat på det här valet.   

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping4.png)

Som standard skapar Site Recovery ett nätverk i målregionen som är identiskt till källnätverket och genom att lägga till ”-asr' som suffix till namnet på Källnätverk. Du kan välja ett nätverk som redan har skapat genom att klicka på Anpassa.

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping5.png)


Om nätverksmappningen redan har gjort kan du inte ändra det virtuella målnätverket vid replikering. Ändra befintliga nätverksmappningen för att ändra den.  

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/network-mapping6.png)

![Nätverksmappning](./media/site-recovery-network-mapping-azure-to-azure/modify-network-mapping.png)

> [!IMPORTANT]
> Om du ändrar nätverksmappning från region-1 till 2 region, kontrollera att du ändrar nätverksmappning från region-2 till region-1 samt.
>
>


## <a name="subnet-selection"></a>Val av undernät
Undernätet för den virtuella måldatorn är valt baserat på namnet på undernätet för den virtuella källdatorn. Om ett undernät med samma namn som den virtuella källdatorn är tillgängliga i målnätverket, väljs undernätet för den virtuella måldatorn. Om det finns inget undernät med samma namn i målnätverket, sedan väljs alfabetiskt det första undernätet som mål-undernät. Du kan ändra det här undernätet genom att gå till beräknings- och nätverksinställningar inställningarna för den virtuella datorn.

![Ändra ett undernät](./media/site-recovery-network-mapping-azure-to-azure/modify-subnet.png)


## <a name="ip-address"></a>IP-adress

IP-adress för varje gränssnitt på den virtuella måldatorn är valt på följande sätt:

### <a name="dhcp"></a>DHCP
Om nätverksgränssnittet för den virtuella källdatorn använder DHCP, anges också nätverksgränssnittet för den virtuella måldatorn som DHCP.

### <a name="static-ip"></a>Statisk IP-adress
Om nätverksgränssnittet för den virtuella källdatorn använder statisk IP-adress, ställs också nätverksgränssnittet för den virtuella måldatorn att använda statiska IP-Adressen. Statiska IP-Adressen är valt på följande sätt:

#### <a name="same-address-space"></a>Samma-adressutrymme

Om käll-undernät och mål-undernät har samma adressutrymme, ange IP-adressen för nätverksgränssnittet för den virtuella källdatorn som mål-IP-adress. Om samma IP-adress inte är tillgänglig har nästa tillgängliga IP-adress angetts som mål-IP-adress.

#### <a name="different-address-space"></a>Olika adressutrymmen

Om käll-undernät och mål-undernät har olika adressutrymmen, har nästa tillgängliga IP-adressen i undernätet som mål angetts som mål-IP-adress.

Du kan ändra mål-IP för varje gränssnitt genom att gå till beräknings- och inställningarna för den virtuella datorn.

## <a name="next-steps"></a>Nästa steg

Lär dig mer om [nätverk vägledning för att replikera virtuella datorer i Azure](site-recovery-azure-to-azure-networking-guidance.md).
