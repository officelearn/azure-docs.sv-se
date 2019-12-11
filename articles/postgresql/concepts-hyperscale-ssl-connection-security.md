---
title: SSL-storskalig (citus) – Azure Database for PostgreSQL
description: Instruktioner och information om hur du konfigurerar Azure Database for PostgreSQL-storskalig (citus) och associerade program för att använda SSL-anslutningar på rätt sätt.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/10/2019
ms.locfileid: "74973993"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurera SSL i Azure Database for PostgreSQL-storskalig (citus)
Klient program anslutningar till den storskaliga (citus) koordinator noden kräver Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

## <a name="enforcing-ssl-connections"></a>Tvinga SSL-anslutningar
För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal, är tvång av SSL-anslutningar aktiverat som standard. 

På samma sätt inkluderar anslutnings strängar som är fördefinierade i inställningarna "anslutnings strängar" under servern i Azure Portal de nödvändiga parametrarna för vanliga språk för att ansluta till databas servern med hjälp av SSL. SSL-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller ramverk stöder SSL-anslutningar
Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte SSL som standard under installationen. Om din PostgreSQL-Server upprätthåller SSL-anslutningar men programmet inte har kon figurer ATS för SSL, kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar SSL-anslutningar finns i programmets dokumentation.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikat verifiering för SSL-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en betrodd certifikat utfärdare (. cer) för att ansluta på ett säkert sätt. Certifikatet för att ansluta till en Azure Database for PostgreSQL-storskalig (citus) finns på https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Ladda ned certifikat filen och spara den på önskad plats.

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till din citus-koordinator-nod med hjälp av kommando rads verktyget psql. Använd inställningen `sslmode=verify-full` anslutnings sträng för att framtvinga verifiering av SSL-certifikat. Överför sökvägen till den lokala certifikat filen till `sslrootcert`-parametern.

Nedan visas ett exempel på anslutnings strängen psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="next-steps"></a>Nästa steg
Öka säkerheten ytterligare med [brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](concepts-hyperscale-firewall-rules.md).
