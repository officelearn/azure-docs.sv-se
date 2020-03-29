---
title: SSL - Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Instruktioner och information för att konfigurera Azure Database för PostgreSQL - Hyperscale (Citus) och associerade program för att korrekt använda SSL-anslutningar.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/17/2019
ms.openlocfilehash: 3e4ef5d2d6db3a3d4f8923f47079f2484639a751
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74973993"
---
# <a name="configure-ssl-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurera SSL i Azure Database för PostgreSQL - Hyperskala (Citus)
Klientprogramanslutningar till citus-koordinatornoden Hyperscale (Citus) kräver SSL (Secure Sockets Layer). Genom att tillämpa SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda mot "man-in-the-middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program.

## <a name="enforcing-ssl-connections"></a>Tvinga SSL-anslutningar
För alla Azure-databas för PostgreSQL-servrar som etablerats via Azure-portalen aktiveras tvingande SSL-anslutningar som standard. 

På samma sätt innehåller anslutningssträngar som är fördefinierade i inställningarna för anslutningssträngar under servern i Azure-portalen de parametrar som krävs för att vanliga språk ska ansluta till databasservern med SSL. SSL-parametern varierar beroende på kopplingen, till exempel "ssl=true" eller "sslmode=require" eller "sslmode=required" och andra variationer.

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller ramverk stöder SSL-anslutningar
Vissa programramverk som använder PostgreSQL för sina databastjänster aktiverar inte SSL som standard under installationen. Om PostgreSQL-servern framtvingar SSL-anslutningar men programmet inte är konfigurerat för SSL kan det hända att programmet inte ansluter till databasservern. Läs programmets dokumentation om du vill lära dig hur du aktiverar SSL-anslutningar.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikatverifiering för SSL-anslutning
I vissa fall kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdarcertifikatfil (.cer) för att ansluta säkert. Certifikatet för att ansluta till en Azure-databas för PostgreSQL https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem- Hyperscale (Citus) finns på . Hämta certifikatfilen och spara den på önskad plats.

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till cus-koordinatornoden Hyperscale (Citus) med hjälp av kommandoradsverktyget psql. Använd `sslmode=verify-full` inställningen för anslutningssträng för att framtvinga SSL-certifikatverifiering. Skicka sökvägen till den `sslrootcert` lokala certifikatfilen till parametern.

Nedan är ett exempel på psql-anslutningssträngen:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Bekräfta att värdet `sslrootcert` som skickades till matchar filsökvägen för certifikatet som du sparade.

## <a name="next-steps"></a>Nästa steg
Öka säkerheten ytterligare med [brandväggsregler i Azure Database för PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
