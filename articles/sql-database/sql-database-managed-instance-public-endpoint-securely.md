---
title: Offentliga slutpunkter för hanterade instanser
description: Använd offentliga slutpunkter i Azure på ett säkert sätt med en hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684943"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Använda en hanterad Azure SQL-databas-hanterad instans på ett säkert sätt med offentliga slutpunkter

Hanterade azure SQL-databas-hanterade instanser kan ge användaranslutning via [offentliga slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). I den här artikeln beskrivs hur du gör den här konfigurationen säkrare.

## <a name="scenarios"></a>Scenarier

En SQL Database-hanterad instans ger en privat slutpunkt för att tillåta anslutning inifrån det virtuella nätverket. Standardalternativet är att ge maximal isolering. Det finns dock scenarier där du måste tillhandahålla en offentlig slutpunktsanslutning:

- Den hanterade instansen måste integreras med PaaS-erbjudanden (endast flera innehavare som en tjänst).
- Du behöver högre dataflöde för datautbyte än vad som är möjligt när du använder en VPN.
- Företagets policyer förbjuder PaaS i företagsnätverk.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuera en hanterad instans för offentlig slutpunktsåtkomst

Även om det inte är obligatoriskt, är den gemensamma distributionsmodellen för en hanterad instans med offentlig slutpunktsåtkomst att skapa instansen i ett dedikerat isolerat virtuellt nätverk. I den här konfigurationen används det virtuella nätverket endast för isolering av virtuella kluster. Det spelar ingen roll om den hanterade instansens IP-adressutrymme överlappar ett företagsnätverks IP-adressutrymme.

## <a name="secure-data-in-motion"></a>Säkra data i rörelse

Datatrafik för hanterade instanser krypteras alltid om klientdrivrutinen stöder kryptering. Data som skickas mellan den hanterade instansen och andra virtuella Azure-datorer eller Azure-tjänster lämnar aldrig Azures stamnät. Om det finns en anslutning mellan den hanterade instansen och ett lokalt nätverk rekommenderar vi att du använder Azure ExpressRoute. ExpressRoute hjälper dig att undvika att flytta data över det offentliga internet. För privat anslutning med hanterade instanser kan endast privat peering användas.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Låsa inkommande och utgående anslutning

I följande diagram visas rekommenderade säkerhetskonfigurationer:

![Säkerhetskonfigurationer för låsning av inkommande och utgående anslutning](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

En hanterad instans har en [dedikerad offentlig slutpunktsadress](sql-database-managed-instance-find-management-endpoint-ip-address.md). I den utgående brandväggen på klientsidan och i nätverkssäkerhetsgruppsreglerna anger du den här offentliga IP-adressen för slutpunkt för att begränsa utgående anslutning.

För att säkerställa att trafiken till den hanterade instansen kommer från betrodda källor rekommenderar vi att du ansluter från källor med välkända IP-adresser. Använd en nätverkssäkerhetsgrupp för att begränsa åtkomsten till den offentliga slutpunkten för hanterade instanser i port 3342.

När klienter behöver initiera en anslutning från ett lokalt nätverk kontrollerar du att den ursprungliga adressen översätts till en välkänd uppsättning IP-adresser. Om du inte kan göra det (till exempel en mobil arbetsstyrka är ett typiskt scenario), rekommenderar vi att du använder [punkt-till-plats VPN-anslutningar och en privat slutpunkt](sql-database-managed-instance-configure-p2s.md).

Om anslutningar startas från Azure rekommenderar vi att trafiken kommer från en välkänd tilldelad [virtuell IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) (till exempel en virtuell dator). Om du vill göra det enklare att hantera VIP-adresser (Virtual IP) kanske du vill använda [offentliga IP-adressprefix](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar offentlig slutpunkt för hanteringsinstanser: [Konfigurera offentlig slutpunkt](sql-database-managed-instance-public-endpoint-configure.md)
