---
title: Skydda hanterade instansen offentliga slutpunkter – Azure SQL Database managed instance | Microsoft Docs
description: Använda offentliga slutpunkter på ett säkert sätt i Azure med en hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 05/08/2019
ms.openlocfilehash: f06677b66c8f6586fec8cc5dfe97b1515b741e9c
ms.sourcegitcommit: 399db0671f58c879c1a729230254f12bc4ebff59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/09/2019
ms.locfileid: "65470285"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Använda en hanterad Azure SQL Database-instans på ett säkert sätt med offentliga slutpunkter

Azure SQL Database hanterade instanser kan ge användarens anslutning via [offentliga slutpunkter](../virtual-network/virtual-network-service-endpoints-overview.md). Den här artikeln förklarar hur du kan skydda den här konfigurationen.

## <a name="scenarios"></a>Scenarier

En hanterad SQL Database-instans innehåller en privat slutpunkt för att tillåta anslutningar från i dess virtuella nätverk. Standardalternativet är att ge högsta isolering. Det finns dock scenarier där du måste ange en offentlig slutpunkt-anslutning:

- Den hanterade instansen måste integrera med flera-tenant endast platform as a service (PaaS)-erbjudanden.
- Du behöver högre dataflöde för datautbyte än vad som är möjligt när du använder en VPN-anslutning.
- Företagets principer förhindrar PaaS i företagets nätverk.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuera en hanterad instans för offentlig slutpunkt åtkomst

Även om det är inte obligatoriska är vanliga distributionsmodell för en hanterad instans med offentlig slutpunkt åtkomst att skapa instansen i ett dedikerat isolerade virtuella nätverk. I den här konfigurationen används det virtuella nätverket endast för isolering av virtuellt kluster. Det spelar ingen roll om den hanterade instansen IP-adressutrymmet överlappar ett företagsnätverk IP-adressutrymme.

## <a name="secure-data-in-motion"></a>Skydda data i rörelse

Hanterad instans datatrafik krypteras alltid om klientdrivrutinen har stöd för kryptering. Data som skickas mellan den hanterade instansen och andra Azure-datorer och Azure-tjänster som lämnar aldrig Azures stamnät. Om det finns en anslutning mellan den hanterade instansen och ett lokalt nätverk, rekommenderar vi att du använder Azure ExpressRoute med Microsoft-peering. ExpressRoute hjälper dig att undvika att flytta data via det offentliga internet. För hanterad instans privat anslutning, kan endast privat peering användas.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Låsa inkommande och utgående anslutningar

Följande diagram visar de rekommenderade säkerhetskonfigurationerna:

![Säkerhetskonfigurationer för att låsa inkommande och utgående anslutningar](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

En hanterad instans har en [dedikerade offentlig slutpunkt adresser](sql-database-managed-instance-find-management-endpoint-ip-address.md). Utgående brandväggen på klientsidan och regler för network security group, ange den här offentliga slutpunkten IP-adress för att begränsa den utgående trafiken.

Om du vill kontrollera trafiken till den hanterade instansen kommer från betrodda källor, rekommenderar vi ansluter från källor med välkända IP-adresser. Använda en nätverkssäkerhetsgrupp för att begränsa åtkomst till den hanterade instans offentliga slutpunkten på port 3342.

När klienter behöver initiera en anslutning från ett lokalt nätverk kan du kontrollera vilken adress översätts till en välkänd uppsättning IP-adresser. Om du inte kan göra så att (t.ex, en mobil personalstyrka som ett typiskt scenario), rekommenderar vi du använder [punkt-till-plats VPN-anslutningar och en privat slutpunkt](sql-database-managed-instance-configure-p2s.md).

Om anslutningar startas från Azure, rekommenderar vi att trafik som kommer från en känd tilldelade [virtuella IP-adressen](../virtual-network/virtual-networks-reserved-public-ip.md) (till exempel en virtuell dator). Om du vill att hantera virtuella IP (VIP) adresser enklare, kanske du vill använda [offentliga IP-adressprefix](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar offentlig slutpunkt för hantera instanser: [Konfigurera offentlig slutpunkt](sql-database-managed-instance-public-endpoint-configure.md)
