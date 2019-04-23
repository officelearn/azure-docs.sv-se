---
title: Skydda hanterade instansen offentliga slutpunkter – Azure SQL Database managed instance | Microsoft Docs
description: Använda offentliga slutpunkter på ett säkert sätt i Azure med hanterade instansen
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 04/16/2019
ms.openlocfilehash: 9d5a3d18e8a7d3c5a6cb08e16e74dd4fbda9ca31
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2019
ms.locfileid: "60014533"
---
# <a name="using-azure-sql-database-managed-instance-securely-with-public-endpoint"></a>Med Azure SQL Database-hanterad instans på ett säkert sätt med offentlig slutpunkt

Azure SQL Database hanterad instans kan aktiveras för att ge användaren anslutning över [offentlig slutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md). Den här artikeln innehåller råd om hur du kan skydda den här konfigurationen.

## <a name="scenarios"></a>Scenarier

Hanterad instans innehåller privat slutpunkt om du vill aktivera anslutningen från i dess virtuella nätverk. Standardalternativet är att ge högsta isolering. Det finns dock scenarier där en offentlig slutpunkt-anslutning krävs:

- Integrering med flera innehavare endast PaaS-erbjudanden.
- Högre dataflöde för datautbyte än att använda VPN.
- Företagets principer förhindrar PaaS i företagets nätverk.

## <a name="deploying-managed-instance-for-public-endpoint-access"></a>Distribuera hanterade instansen för den offentliga slutpunkten åtkomst

Även om det är inte obligatoriska är vanliga distributionsmodell för en hanterad instans med offentlig slutpunkt åtkomst att skapa instansen i ett dedikerat isolerade virtuella nätverk. I den här konfigurationen används det virtuella nätverket bara för isolering av virtuellt kluster. Det är inte relevant om hanterad instans-IP-adressutrymmet överlappar ett företagsnätverk IP-adressutrymme.

## <a name="securing-data-in-motion"></a>Skydda data i rörelse

Hanterad instans datatrafik krypteras alltid om klientdrivrutinen har stöd för kryptering. Data mellan den hanterade instansen och andra Azure-datorer och Azure-tjänster som lämnar aldrig Azures stamnät. Om det finns en hanterad instans till en lokal nätverksanslutning, rekommenderar vi för att använda Express Route med Microsoft-peering. Expressroute kan du undvika att flytta data via offentliga Internet (för hanterad instans privat anslutning, endast privat peering kan användas).

## <a name="locking-down-inbound-and-outbound-connectivity"></a>Låsa inkommande och utgående anslutningar

Följande diagram visar rekommenderade säkerhetskonfigurationer.

![managed-instance-vnet.png](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Hanterad instans har en [dedikerade offentlig slutpunkt adresser](sql-database-managed-instance-find-management-endpoint-ip-address.md). Den här IP-adressen ska anges i klienten sida utgående brandvägg och Nätverkssäkerhetsgruppen regler för att begränsa den utgående trafiken.

Om du vill kontrollera trafiken till den hanterade instansen kommer från betrodda källor, rekommenderar vi för att ansluta från källor med välkända IP-adresser. Begränsa åtkomsten till den hanterade instans offentliga slutpunkten på port 3342 med hjälp av en Nätverkssäkerhetsgrupp.

När klienter behöver initiera en anslutning från ett lokalt nätverk kan du kontrollera vilken adress översätts till en välkänd uppsättning IP-adresser. Om som inte kan åstadkommas (till exempel mobil personalstyrka som ett typiskt scenario), rekommenderar vi för att använda [punkt-till-plats VPN-anslutningar och en privat slutpunkt](sql-database-managed-instance-configure-p2s.md).

Om anslutningar startas från Azure, rekommenderar vi att trafiken kommer från välkända tilldelade [VIP](../virtual-network/virtual-networks-reserved-public-ip.md) (till exempel virtuella datorer). För att underlätta för att hantera virtuella IP-adresser kunder överväga att använda [offentliga IP-adressprefix](../virtual-network/public-ip-address-prefix.md).