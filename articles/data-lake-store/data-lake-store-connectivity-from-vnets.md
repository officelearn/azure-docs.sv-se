---
title: Ansluta till Azure Data Lake Storage Gen1 från VNETs | Microsoft-dokument
description: Ansluta till Azure Data Lake Storage Gen1 från Azure VNETs
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: mtillman
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: c8d028a981d7811ed2c864db5750afc83ab93b2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60878876"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Komma åt Azure Data Lake Storage Gen1 från virtuella datorer i ett Azure VNET
Azure Data Lake Storage Gen1 är en PaaS-tjänst som körs på offentliga IP-adresser på Internet. Alla servrar som kan ansluta till det offentliga Internet kan vanligtvis ansluta till Azure Data Lake Storage Gen1-slutpunkter också. Som standard kan alla virtuella datorer som finns i Azure VNETs komma åt Internet och kan därför komma åt Azure Data Lake Storage Gen1. Det är dock möjligt att konfigurera virtuella datorer i ett VNET för att inte ha tillgång till Internet. För sådana virtuella datorer är åtkomsten till Azure Data Lake Storage Gen1 begränsad. Blockera offentlig Internet-åtkomst för virtuella datorer i Azure VNETs kan göras med någon av följande metoder:

* Genom att konfigurera nätverkssäkerhetsgrupper (NSG)
* Genom att konfigurera användardefinierade vägar (UDR)
* Genom att byta ut rutter via BGP (branschstandard dynamisk routing protokoll), när ExpressRoute används, som blockerar åtkomst till Internet

I den här artikeln får du lära dig hur du aktiverar åtkomst till Azure Data Lake Storage Gen1 från virtuella Azure-datorer, som har begränsats till åtkomst till resurser med någon av de tre metoderna som tidigare angetts.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Aktivera anslutning till Azure Data Lake Storage Gen1 från virtuella datorer med begränsad anslutning
Om du vill komma åt Azure Data Lake Storage Gen1 från sådana virtuella datorer måste du konfigurera dem för att komma åt IP-adressen för den region där Azure Data Lake Storage Gen1-kontot är tillgängligt. Du kan identifiera IP-adresserna för dina datasjölagringsgend1-kontoregioner`<account>.azuredatalakestore.net`genom att lösa DNS-namnen på dina konton ( ). Om du vill matcha DNS-namn på dina konton kan du använda verktyg som **nslookup**. Öppna en kommandotolk på datorn och kör följande kommando:

    nslookup mydatastore.azuredatalakestore.net

Utdata liknar följande. Egenskapen Value against **Address** är den IP-adress som är kopplad till ditt Data Lake Storage Gen1-konto.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Aktivera anslutning från virtuella datorer som begränsas med hjälp av NSG
När en NSG-regel används för att blockera åtkomst till Internet kan du skapa en annan NSG som ger åtkomst till IP-adressen för Data Lake Storage Gen1. Mer information om NSG-regler finns i [Översikt över nätverkssäkerhetsgrupper](../virtual-network/security-overview.md). Instruktioner om hur du skapar NSG:er finns i [Så här skapar du en nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Aktivera anslutning från virtuella datorer som begränsas med UDR eller ExpressRoute
När vägar, antingen UDR eller BGP-utbytta vägar, används för att blockera åtkomst till Internet, måste en särskild väg konfigureras så att virtuella datorer i sådana undernät kan komma åt datasjölagring gen1-slutpunkter. Mer information finns i [Översikt över användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md). Instruktioner om hur du skapar UDR:er finns [i Skapa UDR:er i Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Aktivera anslutning från virtuella datorer som begränsas med hjälp av ExpressRoute
När en ExpressRoute-krets har konfigurerats kan de lokala servrarna komma åt Data Lake Storage Gen1 via offentlig peering. Mer information om hur du konfigurerar ExpressRoute för offentlig peering finns på Vanliga frågor om [ExpressRoute](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Se även
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data som lagras i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

