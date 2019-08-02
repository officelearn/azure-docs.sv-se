---
title: Säkra offentliga slut punkter för hanterade instanser – Azure SQL Database Hanterad instans | Microsoft Docs
description: Använd offentliga slut punkter på ett säkert sätt i Azure med en hanterad instans
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: c7f57a636e95bb137dd4285b8f9ce8343b27d2a0
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/26/2019
ms.locfileid: "68567370"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Använd en Azure SQL Database Hanterad instans på ett säkert sätt med offentliga slut punkter

Azure SQL Database hanterade instanser kan ge användar anslutning via [offentliga slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md). Den här artikeln förklarar hur du gör den här konfigurationen säkrare.

## <a name="scenarios"></a>Scenarier

En SQL Database Hanterad instans tillhandahåller en privat slut punkt för att tillåta anslutning från insidan av det virtuella nätverket. Standard alternativet är att tillhandahålla maximal isolering. Det finns dock scenarier där du behöver ange en anslutning till en offentlig slut punkt:

- Den hanterade instansen måste integreras med PaaS-erbjudanden (Platform-as-a-Service) med flera innehavare.
- Du behöver mer data flöde för data utbyte än vad som är möjligt när du använder en VPN-anslutning.
- Företags principer förhindrar PaaS i företags nätverk.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Distribuera en hanterad instans för offentlig slut punkts åtkomst

Även om det inte är obligatoriskt är den vanliga distributions modellen för en hanterad instans med offentlig slut punkt åtkomst att skapa instansen i ett dedikerat isolerat virtuellt nätverk. I den här konfigurationen används det virtuella nätverket bara för virtuell kluster isolering. Det spelar ingen roll om den hanterade instansens IP-adressutrymme överlappar ett företags nätverks IP-adressutrymme.

## <a name="secure-data-in-motion"></a>Skydda data i rörelse

Hanterad instans data trafik är alltid krypterad om klient driv rutinen stöder kryptering. Data som skickas mellan den hanterade instansen och andra virtuella Azure-datorer eller Azure-tjänster lämnar aldrig Azures stamnät. Om det finns en anslutning mellan den hanterade instansen och ett lokalt nätverk rekommenderar vi att du använder Azure-ExpressRoute med Microsoft-peering. ExpressRoute hjälper dig att undvika att flytta data via det offentliga Internet. För privat anslutning med hanterade instanser kan endast privat peering användas.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Lås inkommande och utgående anslutning

Följande diagram visar de rekommenderade säkerhets konfigurationerna:

![Säkerhetskonfigurationer för att låsa inkommande och utgående anslutning](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

En hanterad instans har en [dedikerad offentlig slut punkts adress](sql-database-managed-instance-find-management-endpoint-ip-address.md). I den utgående brand väggen på klient sidan och i reglerna för nätverks säkerhets grupper anger du den offentliga IP-adressen för den offentliga slut punkten för att begränsa utgående anslutning.

För att säkerställa att trafik till den hanterade instansen kommer från betrodda källor rekommenderar vi att du ansluter från källor med välkända IP-adresser. Använd en nätverks säkerhets grupp för att begränsa åtkomsten till den offentliga slut punkten för hanterade instanser på port 3342.

När klienter behöver initiera en anslutning från ett lokalt nätverk måste du kontrol lera att den ursprungliga adressen översätts till en välkänd uppsättning IP-adresser. Om du inte kan göra det (till exempel en mobil personal som är ett typiskt scenario) rekommenderar vi att du använder [punkt-till-plats-VPN-anslutningar och en privat slut punkt](sql-database-managed-instance-configure-p2s.md).

Om anslutningar startas från Azure rekommenderar vi att trafiken kommer från en välkänd, tilldelad [virtuell IP-adress](../virtual-network/virtual-networks-reserved-public-ip.md) (t. ex. en virtuell dator). Om du vill göra det enklare att hantera virtuella IP-adresser (VIP) kan du använda [offentliga IP-adressprefix](../virtual-network/public-ip-address-prefix.md).

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du konfigurerar en offentlig slut punkt för hanterings instanser: [Konfigurera offentlig slut punkt](sql-database-managed-instance-public-endpoint-configure.md)
