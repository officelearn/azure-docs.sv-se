---
title: Ansluta till Azure Data Lake Storage Gen1 från virtuella nätverk | Microsoft Docs
description: Ansluta till Azure Data Lake Storage Gen1 från virtuella Azure-nätverk
services: data-lake-store,data-catalog
documentationcenter: ''
author: esung22
manager: jhubbard
editor: cgronlun
ms.assetid: 683fcfdc-cf93-46c3-b2d2-5cb79f5e9ea5
ms.service: data-lake-store
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: elsung
ms.openlocfilehash: 130d0154fc0558ae7284e8407ba88fda3a2a53d5
ms.sourcegitcommit: 794bfae2ae34263772d1f214a5a62ac29dcec3d2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/11/2018
ms.locfileid: "44391308"
---
# <a name="access-azure-data-lake-storage-gen1-from-vms-within-an-azure-vnet"></a>Åtkomst till Azure Data Lake Storage Gen1 från virtuella datorer i ett Azure VNET
Azure Data Lake Storage Gen1 är en PaaS-tjänst som körs på offentliga Internet-IP-adresser. Alla servrar som kan ansluta till Internet kan vanligtvis ansluta till Azure Data Lake Storage Gen1-slutpunkter. Som standard alla virtuella datorer som finns i virtuella Azure-nätverk kan ansluta till Internet och därför kan komma åt Azure Data Lake Storage Gen1. Det är dock möjligt att konfigurera virtuella datorer i ett virtuellt nätverk ska inte ha åtkomst till Internet. För sådana virtuella datorer begränsas åtkomst till Azure Data Lake Storage Gen1 också. Blockera offentliga Internetåtkomst för virtuella datorer i virtuella Azure-nätverk kan göras med hjälp av någon av följande metoder:

* Genom att konfigurera Nätverkssäkerhetsgrupper (NSG)
* Genom att konfigurera användardefinierade vägar (UDR)
* Genom att utbyta vägar via BGP (dynamisk routning protokollet som är branschstandard) när ExpressRoute används som blockerar åtkomst till Internet

I den här artikeln får lära du dig att aktivera åtkomst till Azure Data Lake Storage Gen1 från Azure virtuella datorer som har begränsats för åtkomst till resurser med någon av de tre metoderna som angavs tidigare.

## <a name="enabling-connectivity-to-azure-data-lake-storage-gen1-from-vms-with-restricted-connectivity"></a>Aktivera anslutning till Azure Data Lake Storage Gen1 från virtuella datorer med begränsade anslutningen
Om du vill få åtkomst till Azure Data Lake Storage Gen1 från sådana virtuella datorer, måste du konfigurera dem för att komma åt den IP-adressen där Gen1 för Azure Data Lake Storage-konto är tillgänglig. Du kan identifiera IP-adresserna för dina Data Lake Storage Gen1 konton genom DNS-namnmatchning för dina konton (`<account>.azuredatalakestore.net`). Om du vill matcha DNS-namn för dina konton, kan du använda verktyg som **nslookup**. Öppna en kommandotolk på datorn och kör följande kommando:

    nslookup mydatastore.azuredatalakestore.net

Utdata liknar följande. Värdet mot **adress** egenskapen är den IP-adressen som är associerat med ditt Data Lake Storage Gen1-konto.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Aktivera anslutningen från virtuella datorer som är begränsad av använder NSG-regler
När en NSG-regel används för att blockera åtkomst till Internet, kan du skapa en annan NSG som tillåter åtkomst till Data Lake Storage Gen1 IP-adress. Mer information om regler för Nätverkssäkerhetsgrupper finns i [nätverkssäkerhetsöversikt](../virtual-network/security-overview.md). Anvisningar om hur du skapar NSG: er finns i [hur du skapar en nätverkssäkerhetsgrupp](../virtual-network/tutorial-filter-network-traffic.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Aktivera anslutningen från virtuella datorer som har begränsade genom att använda UDR eller ExpressRoute
När vägar, Udr eller BGP-parterna vägar, används för att blockera åtkomst till Internet, måste en särskild väg konfigureras så att virtuella datorer i dessa undernät har åtkomst till Data Lake Storage Gen1 slutpunkter. Mer information finns i [översikten över användardefinierade vägar](../virtual-network/virtual-networks-udr-overview.md). Anvisningar om hur du skapar udr: er finns i [skapa udr-vägar i Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Aktivera anslutningen från virtuella datorer som har begränsade genom att använda ExpressRoute
När en ExpressRoute-krets har konfigurerats på lokala servrar kan komma åt Data Lake Storage Gen1 via offentlig peering. Mer information om hur du konfigurerar ExpressRoute för offentlig peering finns på [ExpressRoute vanliga frågor och svar](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Storage Gen1](data-lake-store-overview.md)
* [Skydda data som lagrats i Azure Data Lake Storage Gen1](data-lake-store-security-overview.md)

