---
title: SSL/TLS-anslutning – Azure Database for MySQL
description: Information om hur du konfigurerar Azure Database for MySQL och associerade program för att använda SSL-anslutningar korrekt
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 2421f8a9396b47d04db35a7cad843f6baa6f6177
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84416111"
---
# <a name="ssltls-connectivity-in-azure-database-for-mysql"></a>SSL/TLS-anslutning i Azure Database for MySQL

Azure Database for MySQL stöder anslutning av databas servern till klient program med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

## <a name="ssl-default-settings"></a>Standardinställningar för SSL

Som standard ska databas tjänsten konfigureras för att kräva SSL-anslutningar vid anslutning till MySQL.  Vi rekommenderar att du inte inaktiverar SSL-alternativet när det är möjligt.

När du konfigurerar en ny Azure Database for MySQL-server via Azure Portal och CLI är tvingande av SSL-anslutningar aktiverat som standard. 

Anslutnings strängar för olika programmeringsspråk visas i Azure Portal. Dessa anslutnings strängar innehåller de SSL-parametrar som krävs för att ansluta till databasen. I Azure Portal väljer du servern. Under rubriken **Inställningar** väljer du **anslutnings strängarna**. SSL-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

Information om hur du aktiverar eller inaktiverar SSL-anslutning när du utvecklar program finns i [så här konfigurerar du SSL](howto-configure-ssl.md).

## <a name="tls-enforcement-in-azure-database-for-mysql"></a>TLS-tvång i Azure Database for MySQL

Azure Database for MySQL stöder kryptering för klienter som ansluter till din databas server med hjälp av Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar säkra nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

### <a name="tls-settings"></a>TLS-inställningar

Azure Database for MySQL ger möjlighet att genomdriva TLS-versionen för klient anslutningarna. Om du vill framtvinga TLS-versionen använder du inställningen **minsta TLS-version** . Följande värden är tillåtna för den här alternativ inställningen:

|  Minsta TLS-inställning             | Klientens TLS-version stöds                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standard) | Ingen TLS krävs                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 och högre           |
| TLS1_1                           | TLS 1,1, TLS 1,2 och högre                   |
| TLS1_2                           | TLS version 1,2 och högre                     |


Om du till exempel anger värdet för lägsta TLS-inställnings version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. Du kan också ställa in detta på 1,2 innebär att du bara tillåter anslutningar från klienter som använder TLS 1.2 + och alla anslutningar med TLS 1,0 och TLS 1,1 kommer att avvisas.

> [!Note] 
> Azure Database for MySQL standardvärdet för TLS inaktive ras för alla nya servrar.
>
> För närvarande är de TLS-versioner som stöds av Azure Database for MySQL TLS 1,0, 1,1 och 1,2. När du har tvingat fram en speciell lägsta TLS-version kan du inte ändra den till inaktive rad.

Information om hur du anger TLS-inställningen för Azure Database for MySQL finns i [Konfigurera TLS-inställningen](howto-tls-configurations.md).

## <a name="next-steps"></a>Nästa steg

- [Anslutnings bibliotek för Azure Database for MySQL](concepts-connection-libraries.md)
- Lär dig hur du [konfigurerar SSL](howto-configure-ssl.md)
- Lär dig hur du [konfigurerar TLS](howto-tls-configurations.md)
