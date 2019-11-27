---
title: Säkerhet i Azure Database for MySQL-enskild server
description: En översikt över säkerhetsfunktionerna i Azure Database for MySQL-enskild server.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 11/25/2019
ms.openlocfilehash: 06f895d2e8cd7936141c9f35c5ed0c81fb7e3e15
ms.sourcegitcommit: 8cf199fbb3d7f36478a54700740eb2e9edb823e8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2019
ms.locfileid: "74485060"
---
# <a name="security-in-azure-database-for-mysql---single-server"></a>Säkerhet i Azure Database for MySQL-enskild server

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


## <a name="access-management"></a>Åtkomsthantering

När du skapar Azure Database for MySQL-servern anger du autentiseringsuppgifter för en administratörs användare. Den här administratören kan användas för att skapa ytterligare MySQL-användare.


## <a name="threat-protection"></a>Hotskydd

Du kan välja att använda [Avancerat skydd](concepts-data-access-and-security-threat-protection.md) som identifierar avvikande aktiviteter som visar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja servrar.

[Gransknings loggning](concepts-audit-logs.md) är tillgängligt för att spåra aktiviteter i dina databaser. 


## <a name="next-steps"></a>Nästa steg
- Aktivera brand Väggs regler för [IP-adresser](concepts-firewall-rules.md) eller [virtuella nätverk](concepts-data-access-and-security-vnet.md)