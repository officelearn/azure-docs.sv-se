---
title: Ansluta till Azure Data Lake Store från Vnet | Microsoft Docs
description: Ansluta till Azure Data Lake Store från Azure Vnet
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
ms.openlocfilehash: 489e7eb35352e2e8fd3d159381c2177098a90399
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198131"
---
# <a name="access-azure-data-lake-store-from-vms-within-an-azure-vnet"></a>Åtkomst till Azure Data Lake Store från virtuella datorer i ett Azure-VNET
Azure Data Lake Store är en PaaS-tjänst som körs på offentliga Internet IP-adresser. Alla servrar som kan ansluta till Internet kan vanligtvis ansluta till Azure Data Lake Store-slutpunkter. Som standard alla virtuella datorer som finns i virtuella Azure-nätverk kan ansluta till Internet och därför kan komma åt Azure Data Lake Store. Det är dock möjligt att konfigurera virtuella datorer i ett VNET till inte har åtkomst till Internet. För sådana virtuella datorer kan är åtkomst till Azure Data Lake Store begränsad även. Blockerar tillgång till Internet för virtuella datorer i Azure Vnet kan göras med hjälp av någon av följande metoder:

* Genom att konfigurera Nätverkssäkerhetsgrupp grupper (NSG)
* Genom att konfigurera användare användardefinierade vägar (UDR)
* Genom att utbyta vägar via BGP (dynamisk routning standardprotokoll) när ExpressRoute används som blockerar åtkomst till Internet

I den här artikeln får du lära dig hur du aktiverar åtkomst till Azure Data Lake Store från Azure virtuella datorer som har begränsad åtkomst till resurser med hjälp av någon av tre metoder som angavs tidigare.

## <a name="enabling-connectivity-to-azure-data-lake-store-from-vms-with-restricted-connectivity"></a>Aktivera anslutningen till Azure Data Lake Store från virtuella datorer med begränsade anslutningen
Om du vill komma åt Azure Data Lake Store från dessa virtuella datorer, måste du konfigurera dem för att få åtkomst till IP-adressen där Azure Data Lake Store-konto är tillgängligt. Du kan identifiera IP-adresserna för dina Data Lake Store-konton genom DNS-namnmatchning konton (`<account>.azuredatalakestore.net`). Om du vill matcha DNS-namn för dina konton, kan du använda verktyg som **nslookup**. Öppna en kommandotolk på datorn och kör följande kommando:

    nslookup mydatastore.azuredatalakestore.net

Utdata liknar följande. Värdet mot **adress** egenskapen är IP-adressen som är kopplad till ditt Data Lake Store-konto.

    Non-authoritative answer:
    Name:    1434ceb1-3a4b-4bc0-9c69-a0823fd69bba-mydatastore.projectcabostore.net
    Address:  104.44.88.112
    Aliases:  mydatastore.azuredatalakestore.net


### <a name="enabling-connectivity-from-vms-restricted-by-using-nsg"></a>Aktivera anslutningen från virtuella datorer som har begränsats med hjälp av NSG
När en NSG-regel för att blockera åtkomst till Internet, kan du skapa en annan NSG som ger åtkomst till Data Lake Store IP-adress. Mer information om NSG-regler finns [Network security groups översikt](../virtual-network/security-overview.md). Instruktioner om hur du skapar NSG: er finns i [hantera NSG: er med hjälp av Azure portal](../virtual-network/virtual-networks-create-nsg-arm-pportal.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-udr-or-expressroute"></a>Aktivera anslutningen från virtuella datorer som har begränsats med hjälp av UDR eller ExpressRoute
När vägar udr: er eller utväxlats BGP-vägar används för att blockera åtkomst till Internet, måste en särskild väg konfigureras så att virtuella datorer i dessa undernät har åtkomst till Data Lake Store-slutpunkter. Mer information finns i [användardefinierade vägar översikt](../virtual-network/virtual-networks-udr-overview.md). Instruktioner om hur du skapar udr: er finns i [skapa udr: er i Resource Manager](../virtual-network/tutorial-create-route-table-powershell.md).

### <a name="enabling-connectivity-from-vms-restricted-by-using-expressroute"></a>Aktivera anslutningen från virtuella datorer som har begränsats med hjälp av ExpressRoute
När en ExpressRoute-krets konfigureras lokala servrar kan komma åt Data Lake Store via offentlig peering. Mer information om hur du konfigurerar ExpressRoute för offentlig peering finns på [ExpressRoute vanliga frågor och svar](../expressroute/expressroute-faqs.md).

## <a name="see-also"></a>Se också
* [Översikt över Azure Data Lake Store](data-lake-store-overview.md)
* [Att säkra data som lagras i Azure Data Lake Store](data-lake-store-security-overview.md)

