---
title: SSL – Azure Database for PostgreSQL-enskild server
description: Instruktioner och information om hur du konfigurerar SSL-anslutning för Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 4f7fd3ea1c83e1bf5183aedf4fe894809884414c
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903511"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurera SSL-anslutning i Azure Database for PostgreSQL-enskild server
Azure Database for PostgreSQL föredrar att ansluta dina klient program till PostgreSQL-tjänsten med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

Som standard är PostgreSQL-databas tjänsten konfigurerad att kräva SSL-anslutning. Du kan välja att inaktivera krav på SSL om klient programmet inte stöder SSL-anslutning. 

## <a name="enforcing-ssl-connections"></a>Tvinga SSL-anslutningar
För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal och CLI är tvingande av SSL-anslutningar aktiverat som standard. 

På samma sätt inkluderar anslutnings strängar som är fördefinierade i inställningarna "anslutnings strängar" under servern i Azure Portal de nödvändiga parametrarna för vanliga språk för att ansluta till databas servern med hjälp av SSL. SSL-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

## <a name="configure-enforcement-of-ssl"></a>Konfigurera tvingande av SSL
Du kan också inaktivera tvingande SSL-anslutning. Microsoft Azure rekommenderar att du alltid aktiverar alternativet **FRAMTVINGA SSL-anslutning** för förbättrad säkerhet.

> [!NOTE]
> TLS-versionen som stöds för Azure Database for PostgreSQL är TLS 1,0, TLS 1,1, TLS 1,2.

### <a name="using-the-azure-portal"></a>Använda Azure Portal
Besök Azure Database for PostgreSQL-servern och klicka på **anslutnings säkerhet**. Använd växlings knappen för att aktivera eller inaktivera inställningen **FRAMTVINGA SSL-anslutning** . Klicka sedan på **Spara**. 

![Anslutnings säkerhet – inaktivera framtvinga SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Du kan bekräfta inställningen genom att visa **översikts** sidan för att se status indikatorn för **SSL-framtvingande** .

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera parametern **SSL-tvång** med `Enabled` eller `Disabled` värden i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller ramverk stöder SSL-anslutningar
Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte SSL som standard under installationen. Om din PostgreSQL-Server upprätthåller SSL-anslutningar men programmet inte har kon figurer ATS för SSL, kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar SSL-anslutningar finns i programmets dokumentation.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikat verifiering för SSL-anslutning
I vissa fall kräver program en lokal certifikat fil som genereras från en betrodd certifikat utfärdare (. cer) för att ansluta på ett säkert sätt. Certifikatet för att ansluta till en Azure Database for PostgreSQL-Server finns på https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Ladda ned certifikat filen och spara den på önskad plats. 

### <a name="connect-using-psql"></a>Anslut med psql
I följande exempel visas hur du ansluter till PostgreSQL-servern med hjälp av kommando rads verktyget psql. Använd inställningen `sslmode=verify-full` anslutnings sträng för att framtvinga verifiering av SSL-certifikat. Överför sökvägen till den lokala certifikat filen till `sslrootcert`-parametern.

Nedan visas ett exempel på anslutnings strängen psql:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.


## <a name="next-steps"></a>Nästa steg
Granska olika program anslutnings alternativ i [anslutnings bibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
