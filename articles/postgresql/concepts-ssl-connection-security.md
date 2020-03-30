---
title: SSL - Azure-databas för PostgreSQL - Single Server
description: Instruktioner och information om hur du konfigurerar SSL-anslutning för Azure Database för PostgreSQL - Single Server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 303da4dcb68a79e69254f6610afc0003bf0aa22c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79477008"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurera SSL-anslutning i Azure Database för PostgreSQL - Single Server

Azure Database for PostgreSQL föredrar att ansluta dina klientprogram till PostgreSQL-tjänsten med secure sockets layer (SSL). Genom att tillämpa SSL-anslutningar mellan databasservern och klientprogrammen kan du skydda mot "man-in-the-middle"-attacker genom att kryptera dataströmmen mellan servern och ditt program.

Som standard är PostgreSQL-databastjänsten konfigurerad för att kräva SSL-anslutning. Du kan välja att inaktivera krav på SSL om klientprogrammet inte stöder SSL-anslutning.

## <a name="enforcing-ssl-connections"></a>Tvinga SSL-anslutningar

För alla Azure-databas för PostgreSQL-servrar som etablerats via Azure-portalen och CLI aktiveras tvingande SSL-anslutningar som standard. 

På samma sätt innehåller anslutningssträngar som är fördefinierade i inställningarna för anslutningssträngar under servern i Azure-portalen de parametrar som krävs för att vanliga språk ska ansluta till databasservern med SSL. SSL-parametern varierar beroende på kopplingen, till exempel "ssl=true" eller "sslmode=require" eller "sslmode=required" och andra variationer.

## <a name="configure-enforcement-of-ssl"></a>Konfigurera tvingande efterlevnad av SSL

Du kan också inaktivera tvingande SSL-anslutning. Microsoft Azure rekommenderar att du alltid aktiverar **tvingande SSL-anslutningsinställning** för förbättrad säkerhet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Besök din Azure-databas för PostgreSQL-server och klicka på **Anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera inställningen **Framtvinga SSL-anslutning.** Klicka sedan på **Spara**.

![Anslutningssäkerhet - Inaktivera Tvinga SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Du kan bekräfta inställningen genom att visa sidan **Översikt** för att se statusindikatorn för **SSL-intvingning.**

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan aktivera eller inaktivera **parametern ssl-enforcement** med respektive `Enabled` `Disabled` värden i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller ramverk stöder SSL-anslutningar

Vissa programramverk som använder PostgreSQL för sina databastjänster aktiverar inte SSL som standard under installationen. Om PostgreSQL-servern framtvingar SSL-anslutningar men programmet inte är konfigurerat för SSL kan det hända att programmet inte ansluter till databasservern. Läs programmets dokumentation om du vill lära dig hur du aktiverar SSL-anslutningar.

## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikatverifiering för SSL-anslutning

I vissa fall kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdarcertifikatfil (.cer) för att ansluta säkert. Certifikatet för att ansluta till en Azure-databas för https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pemPostgreSQL-server finns på . Hämta certifikatfilen och spara den på önskad plats.

### <a name="connect-using-psql"></a>Anslut med psql

I följande exempel visas hur du ansluter till Din PostgreSQL-server med hjälp av kommandoradsverktyget psql. Använd `sslmode=verify-full` inställningen för anslutningssträng för att framtvinga SSL-certifikatverifiering. Skicka sökvägen till den `sslrootcert` lokala certifikatfilen till parametern.

Följande kommando är ett exempel på psql-anslutningssträngen:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Bekräfta att värdet `sslrootcert` som skickades till matchar filsökvägen för certifikatet som du sparade.

## <a name="next-steps"></a>Nästa steg

Granska olika programanslutningsalternativ i [anslutningsbibliotek för Azure Database för PostgreSQL](concepts-connection-libraries.md).
