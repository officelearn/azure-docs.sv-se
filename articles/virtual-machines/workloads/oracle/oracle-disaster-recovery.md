---
title: Översikt över ett scenario med oracle-haveriberedskap i din Azure-miljö | Microsoft-dokument
description: Ett katastrofåterställningsscenario för en Oracle Database 12c-databas i din Azure-miljö
services: virtual-machines-linux
documentationcenter: virtual-machines
author: DavidCBerry13
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 08/02/2018
ms.author: daberry
ms.openlocfilehash: 527c4cfdad3fc371bddd42388aa432c27a2877df
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81272975"
---
# <a name="disaster-recovery-for-an-oracle-database-12c-database-in-an-azure-environment"></a>Haveriberedskap för en Oracle Database 12c-databas i en Azure-miljö

## <a name="assumptions"></a>Antaganden

- Du har en förståelse för Oracle Data Guard-design och Azure-miljöer.


## <a name="goals"></a>Mål
- Utforma topologin och konfigurationen som uppfyller dina DR-krav (Disaster Recovery).

## <a name="scenario-1-primary-and-dr-sites-on-azure"></a>Scenario 1: Primära och DR-platser på Azure

En kund har en Oracle-databas konfigurerad på den primära platsen. En DR-plats finns i en annan region. Kunden använder Oracle Data Guard för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra användningsområden. 

### <a name="topology"></a>Topologi

Här är en sammanfattning av Azure-installationen:

- Två platser (en primär plats och en DR-plats)
- Två virtuella nätverk
- Två Oracle-databaser med Data Guard (primär och standby)
- Två Oracle-databaser med Golden Gate eller Data Guard (endast primär plats)
- Två programtjänster, en primär och en på DR-platsen
- En *tillgänglighetsuppsättning* som används för databas- och programtjänst på den primära platsen
- En jumpbox på varje plats, vilket begränsar åtkomsten till det privata nätverket och endast tillåter inloggning av en administratör
- En jumpbox, programtjänst, databas och VPN-gateway på separata undernät
- NSG tillämpas på program och databas undernät

![Skärmbild av dr-topologisidan](./media/oracle-disaster-recovery/oracle_topology_01.png)

## <a name="scenario-2-primary-site-on-premises-and-dr-site-on-azure"></a>Scenario 2: Primär plats lokalt och DR-plats på Azure

En kund har en lokal Oracle-databaskonfiguration (primär plats). En DR-plats finns på Azure. Oracle Data Guard används för snabb återställning mellan dessa platser. Den primära platsen har också en sekundär databas för rapportering och andra användningsområden. 

Det finns två metoder för den här inställningen.

### <a name="approach-1-direct-connections-between-on-premises-and-azure-requiring-open-tcp-ports-on-the-firewall"></a>Metod 1: Direkta anslutningar mellan lokala och Azure, vilket kräver öppna TCP-portar i brandväggen 

Vi rekommenderar inte direkta anslutningar eftersom de exponerar TCP-portarna för omvärlden.

#### <a name="topology"></a>Topologi

Följande är en sammanfattning av Azure-installationen:

- En DR-plats 
- Ett virtuellt nätverk
- En Oracle-databas med Data Guard (aktiv)
- En programtjänst på DR-platsen
- En jumpbox, som begränsar åtkomsten till det privata nätverket och endast tillåter inloggning av en administratör
- En jumpbox, programtjänst, databas och VPN-gateway på separata undernät
- NSG tillämpas på program och databas undernät
- En NSG-princip/regel som tillåter inkommande TCP-port 1521 (eller en användardefinierad port)
- En NSG-princip/regel för att begränsa endast IP-adressen/adresserna lokalt (DB eller program) för att komma åt det virtuella nätverket

![Skärmbild av dr-topologisidan](./media/oracle-disaster-recovery/oracle_topology_02.png)

### <a name="approach-2-site-to-site-vpn"></a>Metod 2: VPN från plats till plats
Site-to-site VPN är ett bättre tillvägagångssätt. Mer information om hur du konfigurerar ett VPN finns i [Skapa ett virtuellt nätverk med en VPN-anslutning från plats till plats med CLI](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-cli).

#### <a name="topology"></a>Topologi

Följande är en sammanfattning av Azure-installationen:

- En DR-plats 
- Ett virtuellt nätverk 
- En Oracle-databas med Data Guard (aktiv)
- En programtjänst på DR-platsen
- En jumpbox, som begränsar åtkomsten till det privata nätverket och endast tillåter inloggning av en administratör
- En jumpbox, programtjänst, databas och VPN-gateway finns på separata undernät
- NSG tillämpas på program och databas undernät
- Plats-till-plats-VPN-anslutning mellan lokalt och Azure

![Skärmbild av dr-topologisidan](./media/oracle-disaster-recovery/oracle_topology_03.png)

## <a name="additional-reading"></a>Ytterligare läsning

- [Designa och implementera en Oracle-databas på Azure](oracle-design.md)
- [Konfigurera Oracle Data Guard](configure-oracle-dataguard.md)
- [Konfigurera Oracle Golden Gate](configure-oracle-golden-gate.md)
- [Oracle säkerhetskopiering och återställning](oracle-backup-recovery.md)


## <a name="next-steps"></a>Nästa steg

- [Självstudiekurs: Skapa virtuella datorer med hög tillgång](../../linux/create-cli-complete.md)
- [Utforska Azure CLI-exempel för VM-distribution](../../linux/cli-samples.md)
