---
title: Säkerhet i Azure Database för PostgreSQL - Single Server
description: En översikt över säkerhetsfunktionerna i Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/22/2019
ms.openlocfilehash: 02dc9e1ad9ee46b1a400e44b6ef737e70571a17a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972587"
---
# <a name="security-in-azure-database-for-postgresql---single-server"></a>Säkerhet i Azure Database för PostgreSQL - Single Server

Det finns flera säkerhetslager som är tillgängliga för att skydda data på din Azure-databas för PostgreSQL-server. I den här artikeln beskrivs dessa säkerhetsalternativ.

## <a name="information-protection-and-encryption"></a>Informationsskydd och kryptering

### <a name="in-transit"></a>Under transitering
Azure Database for PostgreSQL skyddar dina data genom att kryptera data under överföring med Transport Layer Security. Kryptering (SSL/TLS) tillämpas som standard.

### <a name="at-rest"></a>I vila
Azure Database for PostgreSQL-tjänsten använder FIPS 140-2-validerad kryptografisk modul för lagringskryptering av data i vila. Data, inklusive säkerhetskopior, krypteras på disk, med undantag för temporära filer som skapas när frågor körs. Tjänsten använder AES 256-bitars chiffer som ingår i Azure-lagringskryptering och nycklarna hanteras. Lagringskrypteringen är alltid igång och kan inte inaktiveras.


## <a name="network-security"></a>Nätverkssäkerhet
Anslutningar till en Azure-databas för PostgreSQL-server dirigeras först via en regional gateway. Gatewayen har en allmänt tillgänglig IP, medan serverns IP-adresser är skyddade. Mer information om gatewayen finns i [artikeln för anslutningsarkitektur](concepts-connectivity-architecture.md).  

En nyligen skapad Azure Database för PostgreSQL-server har en brandvägg som blockerar alla externa anslutningar. Även om de når gatewayen, är de inte tillåtet att ansluta till servern. 

### <a name="ip-firewall-rules"></a>REGLER för IP-brandväggen
IP-brandväggsregler ger åtkomst till servrar baserat på den ursprungliga IP-adressen för varje begäran. Mer information finns i översikten över [brandväggsregler.](concepts-firewall-rules.md)

### <a name="virtual-network-firewall-rules"></a>Brandväggsregler för virtuella nätverk
Slutpunkter för virtuella nätverkstjänster utökar din virtuella nätverksanslutning över Azure-stamnätet. Med hjälp av virtuella nätverksregler kan du aktivera din Azure-databas för PostgreSQL-server för att tillåta anslutningar från valda undernät i ett virtuellt nätverk. Mer information finns i [slutpunktsöversikten för den virtuella nätverkstjänsten](concepts-data-access-and-security-vnet.md).

### <a name="private-ip"></a>Privat IP
Med Private Link kan du ansluta till din Azure-databas för PostgreSQL Single server i Azure via en privat slutpunkt. Azure Private Link ger i huvudsak Azure-tjänster i ditt privata virtuella nätverk (VNet). PaaS-resurserna kan nås med hjälp av den privata IP-adressen precis som alla andra resurser i det virtuella nätverket. Mer information finns i [översikten över den privata länken](concepts-data-access-and-security-private-link.md)


## <a name="access-management"></a>Åtkomsthantering

När du skapar Azure Database för PostgreSQL-server anger du autentiseringsuppgifter för en administratörsroll. Den här administratörsrollen kan användas för att skapa ytterligare [PostgreSQL-roller](https://www.postgresql.org/docs/current/user-manag.html).

Du kan också ansluta till servern med [Azure Active Directory (AAD) autentisering](concepts-aad-authentication.md).


## <a name="threat-protection"></a>Hotskydd

Du kan välja [advanced threat protection](concepts-data-access-and-security-threat-protection.md) som identifierar avvikande aktiviteter som indikerar ovanliga och potentiellt skadliga försök att komma åt eller utnyttja servrar.

[Granskningsloggning](concepts-audit.md) är tillgängligt för att spåra aktivitet i dina databaser. 


## <a name="next-steps"></a>Nästa steg
- Aktivera brandväggsregler för [IP-adresser](concepts-firewall-rules.md) eller [virtuella nätverk](concepts-data-access-and-security-vnet.md)
- Lär dig mer om [Azure Active Directory-autentisering](concepts-aad-authentication.md) i Azure Database för PostgreSQL
