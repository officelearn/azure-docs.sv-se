---
title: Översikt över en Oracle-katastrofåterställning i Azure-miljön | Microsoft Docs
description: En katastrofåterställning för en Oracle-databas 12c-databas i Azure-miljön
services: virtual-machines-linux
documentationcenter: virtual-machines
author: v-shiuma
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 6/2/2017
ms.author: rclaus
ms.openlocfilehash: bb319c4ba9bbfba584803b35a0db0763fcf97b86
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657869"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Haveriberedskap för en Oracle-databas 12c-databas i en Azure-miljö

## <a name="assumptions"></a>Antaganden

- Du har en förståelse av Oracle Data Guard design och miljöer i Azure.


## <a name="goals"></a>Mål
- Utforma topologi och konfiguration som uppfyller dina krav för disaster recovery (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: Den primära servern och DR platser på Azure

En kund har en Oracle databasen set in på den primära platsen. En DR-plats finns i en annan region. Kunden använder Oracle Data Guard för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra ändamål. 

### <a name="topology"></a>Topologi

Här följer en sammanfattning av Azure installationen:

- Två platser (en primär plats och en DR-plats)
- Två virtuella nätverk
- Två Oracle-databaser med Data Guard (primär och vänteläge)
- Två Oracle-databaser med guld Gate eller Data Guard (endast primär plats)
- Två programtjänster, en primär och en på DR-plats
- En *tillgänglighetsuppsättning* som används för databasen och program på den primära platsen
- En jumpbox på varje plats, vilket begränsar åtkomsten till det privata nätverket och kan bara logga in som administratör
- En jumpbox programtjänsten, databas och VPN-gateway på separata undernät
- NSG tillämpas på programmet och databasen undernät

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: Primär plats på anläggningar och DR-plats på Azure

En kund har en lokal Oracle database-installation (primär plats). En DR-plats finns på Azure. Oracle Data Guard används för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra ändamål. 

Det finns två tillvägagångssätt för den här installationen.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Metod 1: Direkta anslutningar mellan lokala och Azure, som kräver öppna TCP-portar i brandväggen 

Vi rekommenderar inte direkta anslutningar eftersom de exponera TCP-portar för allmänheten.

#### <a name="topology"></a>Topologi

Nedan följer en sammanfattning av Azure installationen:

- En DR-plats 
- Ett virtuellt nätverk
- En Oracle-databas med Data Guard (aktiv)
- Ett program-tjänsten på DR-plats
- En jumpbox som begränsar åtkomsten till det privata nätverket och kan bara logga in som administratör
- En jumpbox programtjänsten, databas och VPN-gateway på separata undernät
- NSG tillämpas på programmet och databasen undernät
- En NSG princip/regel som tillåter inkommande TCP-port 1521 (eller en användardefinierad port)
- En NSG/principregeln att begränsa endast IP-adress/adresser lokalt (DB eller program) för att få åtkomst till det virtuella nätverket

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Metod 2: Plats-till-plats VPN
Plats-till-plats VPN är en bättre metod. Mer information om hur du konfigurerar en VPN-anslutning finns [skapa ett virtuellt nätverk med en plats-till-plats VPN-anslutning med hjälp av CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologi

Nedan följer en sammanfattning av Azure installationen:

- En DR-plats 
- Ett virtuellt nätverk 
- En Oracle-databas med Data Guard (aktiv)
- Ett program-tjänsten på DR-plats
- En jumpbox som begränsar åtkomsten till det privata nätverket och kan bara logga in som administratör
- En jumpbox programtjänsten, databas och VPN-gateway finns på separata undernät
- NSG tillämpas på programmet och databasen undernät
- Plats-till-plats VPN-anslutning mellan Azure och lokalt

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Ytterligare resurser

- [Utforma och implementera en Oracle-databas på Azure](oracle-design.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle guld Gate](configure-oracle-golden-gate.md)
- [Oracle-säkerhetskopiering och återställning](oracle-backup-recovery.md)


## <a name="next-steps"></a>Nästa steg

- [Självstudier: Skapa högtillgängliga virtuella datorer](../../linux/create-cli-complete.md)
- [Utforska VM distribution Azure CLI-exempel](../../linux/cli-samples.md)
