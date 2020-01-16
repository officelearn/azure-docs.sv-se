---
title: Säkerhet – Azure Database for MySQL
description: En översikt över säkerhetsfunktionerna i Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: fb0a71a650a8c36d4da962adaf3f1f314c30d4c2
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979997"
---
# <a name="security-in-azure-database-for-mysql"></a>Säkerhet i Azure Database for MySQL

Det finns flera säkerhets lager som är tillgängliga för att skydda data på din Azure Database for MySQL-server. Den här artikeln beskriver de här säkerhets alternativen.

## <a name="information-protection-and-encryption"></a>Informations skydd och kryptering

### <a name="in-transit"></a>Under överföring
Azure Database for MySQL skyddar dina data genom att kryptera data under överföring med Transport Layer Security. Kryptering (SSL/TLS) tillämpas som standard.

### <a name="at-rest"></a>I vila
Tjänsten Azure Database for MySQL använder FIPS 140-2-validerade kryptografisk modul för lagrings kryptering av data i vila. Data, inklusive säkerhets kopior, krypteras på disk, med undantag för tillfälliga filer som skapas vid körning av frågor. Tjänsten använder AES 256-bit-chiffer som ingår i Azure Storage-kryptering och nycklarna hanteras av systemet. Lagringskrypteringen är alltid igång och kan inte inaktiveras.


## <a name="network-security"></a>Nätverkssäkerhet
Anslutningar till en Azure Database for MySQL-server dirigeras först via en regional Gateway. Gatewayen har en offentligt tillgänglig IP-adress, medan serverns IP-adresser är skyddade. Mer information om gatewayen finns i artikeln om [anslutnings arkitektur](concepts-connectivity-architecture.md).  

En nyligen skapad Azure Database for MySQL-server har en brand vägg som blockerar alla externa anslutningar. Även om de når gatewayen, tillåts de inte ansluta till servern. 

### <a name="ip-firewall-rules"></a>Regler för IP-brandvägg
Regler för IP-brandvägg ger åtkomst till servrar baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i [Översikt över brand Väggs regler](concepts-firewall-rules.md) .

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk
Tjänst slut punkter i virtuella nätverk utökar din virtuella nätverks anslutning via Azure-stamnätet. Med hjälp av regler för virtuella nätverk kan du aktivera Azure Database for MySQL servern för att tillåta anslutningar från valda undernät i ett virtuellt nätverk. Mer information finns i [Översikt över Virtual Network Service-slutpunkt](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privat IP
Med privat länk kan du ansluta till din Azure Database for MySQL i Azure via en privat slut punkt. Azures privata länk placerar Azure-tjänster i ditt privata Virtual Network (VNet). PaaS-resurser kan nås med hjälp av den privata IP-adressen precis som vilken annan resurs som helst i VNet. Mer information finns i [Översikt över privata länkar](concepts-data-access-security-private-link.md)

## <a name="access-management"></a>Åtkomsthantering

När du skapar Azure Database for MySQL-servern anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan användas för att skapa ytterligare MySQL-användare.


## <a name="threat-protection"></a>Hotskydd

Du kan välja att använda [Avancerat skydd](concepts-data-access-and-security-threat-protection.md) som identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja servrar.

[Gransknings loggning](concepts-audit-logs.md) är tillgängligt för att spåra aktiviteter i dina databaser. 


## <a name="next-steps"></a>Nästa steg
- Aktivera brand Väggs regler för [IP-adresser](concepts-firewall-rules.md) eller [virtuella nätverk](concepts-data-access-and-security-vnet.md)