---
title: Översikt över en Oracle-katastrofåterställning i Azure-miljön | Microsoft Docs
description: Haveriberedskap för en Oracle Database 12c-databas i Azure-miljön
services: virtual-machines-linux
documentationcenter: virtual-machines
author: romitgirdhar
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: rogirdh
ms.openlocfilehash: 09df1421d6deae6db305cef2a46d6c40d0c12ba3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60835897"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Haveriberedskap för en Oracle Database 12c-databas i en Azure-miljö

## <a name="assumptions"></a>Antaganden

- Du har en förståelse för Oracle Data Guard design- och Azure-miljöer.


## <a name="goals"></a>Mål
- Utforma topologi och konfiguration som uppfyller dina krav på disaster recovery (DR).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: Primära och DR-platser på Azure

En kund med en Oracle database set upp på den primära platsen. Det är en DR-plats i en annan region. Kunden använder Oracle Data Guard för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra användningsområden. 

### <a name="topology"></a>Topologi

Här följer en sammanfattning av installationsprogrammet för Azure:

- Två platser (en primär plats och en DR-plats)
- Två virtuella nätverk
- Två Oracle-databaser med Data Guard (primär och vänteläge)
- Två Oracle-databaser med guld Gate eller Data Guard (endast primär plats)
- Två programtjänster, en primär och en på DR-plats
- En *tillgänglighetsuppsättning,* som används för databas och ett program-tjänsten på den primära platsen
- En jumpbox på varje plats, som begränsar åtkomsten till det privata nätverket och tillåter endast logga in som administratör
- En jumpbox, programtjänsten, databas och VPN-gateway på olika undernät
- NSG som tillämpas på programmet och databasen undernät

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: Primär plats på lokalt och DR-webbplats på Azure

En kund har en lokal Oracle database-installation (primär plats). En DR-plats finns på Azure. Oracle Data Guard används för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra användningsområden. 

Det finns två metoder för den här installationen.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Metod 1: Direkta anslutningar mellan lokala och Azure, att kräva öppna TCP-portar i brandväggen 

Vi rekommenderar inte direkta anslutningar eftersom de exponera TCP-portar för allmänheten.

#### <a name="topology"></a>Topologi

Nedan följer en sammanfattning av installationsprogrammet för Azure:

- En DR-plats 
- Ett virtuellt nätverk
- En Oracle-databas med Data Guard (aktiva)
- Ett program-tjänsten på DR-plats
- En jumpbox, som begränsar åtkomsten till det privata nätverket och tillåter endast att logga in som administratör
- En jumpbox, programtjänsten, databas och VPN-gateway på olika undernät
- NSG som tillämpas på programmet och databasen undernät
- En NSG princip/regel som tillåter inkommande TCP-port 1521 (eller en användardefinierad port)
- En NSG/principregeln att begränsa endast IP-adress/adresser lokala (DB eller program) för att få åtkomst till det virtuella nätverket

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Metod 2: Plats-till-plats-VPN
Plats-till-plats-VPN är en bättre metod. Mer information om hur du konfigurerar en VPN-anslutning finns i [skapa ett virtuellt nätverk med en plats-till-plats-VPN-anslutning med CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologi

Nedan följer en sammanfattning av installationsprogrammet för Azure:

- En DR-plats 
- Ett virtuellt nätverk 
- En Oracle-databas med Data Guard (aktiva)
- Ett program-tjänsten på DR-plats
- En jumpbox, som begränsar åtkomsten till det privata nätverket och tillåter endast att logga in som administratör
- En jumpbox, programtjänsten, databas och VPN-gateway finns på olika undernät
- NSG som tillämpas på programmet och databasen undernät
- Plats-till-plats VPN-anslutning mellan lokala och Azure

![Skärmbild av sidan DR-topologi](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Ytterligare resurser

- [Utforma och implementera en Oracle-databas på Azure](oracle-design.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle guld Gate](configure-oracle-golden-gate.md)
- [Oracle-säkerhetskopiering och återställning](oracle-backup-recovery.md)


## <a name="next-steps"></a>Nästa steg

- [Självstudie: Skapa virtuella datorer med hög tillgänglighet](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)
