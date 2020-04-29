---
title: TLS-storskalig (citus) – Azure Database for PostgreSQL
description: Instruktioner och information om hur du konfigurerar Azure Database for PostgreSQL-storskalig (citus) och associerade program för att använda TLS-anslutningar på rätt sätt.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422325"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurera TLS i Azure Database for PostgreSQL-storskalig (citus)
Klient program anslutningar till den storskaliga (citus) koordinator noden kräver Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). Att framtvinga TLS-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

## <a name="enforcing-tls-connections"></a>Tvinga TLS-anslutningar
För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal är tvingande av TLS-anslutningar aktiverat som standard. 

På samma sätt inkluderar anslutnings strängar som är fördefinierade i inställningarna "anslutnings strängar" under servern i Azure Portal de nödvändiga parametrarna för vanliga språk för att ansluta till din databas server med TLS. TLS-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar
Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte TLS som standard under installationen. Om din PostgreSQL-Server framtvingar TLS-anslutningar men programmet inte har kon figurer ATS för TLS, kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Program som kräver certifikat verifiering för TLS-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en betrodd certifikat utfärdare (. cer) för att ansluta på ett säkert sätt. Certifikatet för att ansluta till en Azure Database for PostgreSQL-storskalig (citus) finns på https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem. Ladda ned certifikat filen och spara den på önskad plats.

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till din citus-koordinator-nod med hjälp av kommando rads verktyget psql. Använd inställningen `sslmode=verify-full` anslutnings sträng för att framtvinga verifiering av TLS-certifikat. Överför sökvägen till den lokala certifikat filen till `sslrootcert` -parametern.

Nedan visas ett exempel på anslutnings strängen psql:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="next-steps"></a>Nästa steg
Öka säkerheten ytterligare med [brand Väggs regler i Azure Database for PostgreSQL-storskalig (citus)](concepts-hyperscale-firewall-rules.md).
