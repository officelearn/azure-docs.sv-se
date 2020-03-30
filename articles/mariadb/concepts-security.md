---
title: Säkerhet - Azure-databas för MariaDB
description: En översikt över säkerhetsfunktionerna i Azure Database för MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 8f41fe1005e96b428337bc73b9d468962a079596
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527834"
---
# <a name="security-in-azure-database-for-mariadb"></a>Säkerhet i Azure Database for MariaDB

Det finns flera säkerhetslager som är tillgängliga för att skydda data på din Azure-databas för MariaDB-server. I den här artikeln beskrivs dessa säkerhetsalternativ.

## <a name="information-protection-and-encryption"></a>Informationsskydd och kryptering

### <a name="in-transit"></a>Under transitering
Azure Database för MariaDB skyddar dina data genom att kryptera data under överföring med Transport Layer Security. Kryptering (SSL/TLS) tillämpas som standard.

### <a name="at-rest"></a>I vila
Azure Database for MariaDB-tjänsten använder FIPS 140-2-validerad kryptografisk modul för lagringskryptering av data i vila. Data, inklusive säkerhetskopior, krypteras på disk, med undantag för temporära filer som skapas när frågor körs. Tjänsten använder AES 256-bitars chiffer som ingår i Azure-lagringskryptering och nycklarna hanteras. Lagringskrypteringen är alltid igång och kan inte inaktiveras.


## <a name="network-security"></a>Nätverkssäkerhet
Anslutningar till en Azure-databas för MariaDB-server dirigeras först via en regional gateway. Gatewayen har en allmänt tillgänglig IP, medan serverns IP-adresser är skyddade. Mer information om gatewayen finns i [artikeln för anslutningsarkitektur](concepts-connectivity-architecture.md).  

En nyligen skapad Azure Database för MariaDB-server har en brandvägg som blockerar alla externa anslutningar. Även om de når gatewayen, är de inte tillåtet att ansluta till servern. 

### <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen
IP-brandväggsregler ger åtkomst till servrar baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i översikten över [brandväggsregler.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk
Slutpunkter för virtuella nätverkstjänster utökar din virtuella nätverksanslutning över Azure-stamnätet. Med hjälp av virtuella nätverksregler kan du aktivera din Azure Database för MariaDB-server för att tillåta anslutningar från valda undernät i ett virtuellt nätverk. Mer information finns i [slutpunktsöversikten för den virtuella nätverkstjänsten](concepts-data-access-security-vnet.md).


## <a name="access-management"></a>Åtkomsthantering

När du skapar Azure Database för MariaDB-servern anger du autentiseringsuppgifter för en administratörsanvändare. Den här administratören kan användas för att skapa ytterligare MariaDB-användare.


## <a name="threat-protection"></a>Hotskydd

Du kan välja [advanced threat protection](concepts-data-access-and-security-threat-protection.md) som identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja servrar.

[Granskningsloggning](concepts-audit-logs.md) är tillgängligt för att spåra aktivitet i dina databaser. 


## <a name="next-steps"></a>Nästa steg
- Aktivera brandväggsregler för [IP-adresser](concepts-firewall-rules.md) eller [virtuella nätverk](concepts-data-access-security-vnet.md)