---
title: TLS - Hyperskala (Citus) - Azure-databas för PostgreSQL
description: Instruktioner och information för att konfigurera Azure Database för PostgreSQL - Hyperscale (Citus) och associerade program för att korrekt använda TLS-anslutningar.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/30/2020
ms.openlocfilehash: 8a691b6d3f28800ab1cb38a72f124e950d485084
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422325"
---
# <a name="configure-tls-in-azure-database-for-postgresql---hyperscale-citus"></a>Konfigurera TLS i Azure Database för PostgreSQL - Hyperskala (Citus)
Klientprogramanslutningar till citus-koordinatornoden Hyperscale (Citus) kräver TLS (Transport Layer Security), som tidigare kallades SSL (Secure Sockets Layer). Genom att tillämpa TLS-anslutningar mellan databasservern och klientprogrammen kan du skydda mot "man-in-the-middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program.

## <a name="enforcing-tls-connections"></a>Tvinga TLS-anslutningar
För alla Azure-databas för PostgreSQL-servrar som etablerats via Azure-portalen aktiveras tvingande TLS-anslutningar som standard. 

På samma sätt innehåller anslutningssträngar som är fördefinierade i inställningarna för anslutningssträngar under servern i Azure-portalen de parametrar som krävs för att vanliga språk ska ansluta till databasservern med TLS. TLS-parametern varierar beroende på kopplingen, till exempel "ssl=true" eller "sslmode=require" eller "sslmode=required" och andra variationer.

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar
Vissa programramverk som använder PostgreSQL för sina databastjänster aktiverar inte TLS som standard under installationen. Om PostgreSQL-servern framtvingar TLS-anslutningar men programmet inte är konfigurerat för TLS kan det hända att programmet inte ansluter till databasservern. Läs programmets dokumentation om du vill lära dig hur du aktiverar TLS-anslutningar.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Program som kräver certifikatverifiering för TLS-anslutning
I vissa fall kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdarcertifikatfil (.cer) för att ansluta säkert. Certifikatet för att ansluta till en Azure-databas för PostgreSQL https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem- Hyperscale (Citus) finns på . Hämta certifikatfilen och spara den på önskad plats.

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till cus-koordinatornoden Hyperscale (Citus) med hjälp av kommandoradsverktyget psql. Använd `sslmode=verify-full` inställningen för anslutningssträng för att framtvinga TLS-certifikatverifiering. Skicka sökvägen till den `sslrootcert` lokala certifikatfilen till parametern.

Nedan är ett exempel på psql-anslutningssträngen:
```
psql "sslmode=verify-full sslrootcert=DigiCertGlobalRootCA.crt.pem host=mydemoserver.postgres.database.azure.com dbname=citus user=citus password=your_pass"
```
> [!TIP]
> Bekräfta att värdet `sslrootcert` som skickades till matchar filsökvägen för certifikatet som du sparade.

## <a name="next-steps"></a>Nästa steg
Öka säkerheten ytterligare med [brandväggsregler i Azure Database för PostgreSQL - Hyperscale (Citus)](concepts-hyperscale-firewall-rules.md).
