---
title: Konfigurera SSL-anslutning i Azure Database för PostgreSQL – enskild Server
description: Anvisningar och information för att konfigurera Azure Database för PostgreSQL – enskild Server och associerade program att använda normalt SSL-anslutningar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: 686adfb2998eff10ef4b9f378163b164ba970c56
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461844"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurera SSL-anslutning i Azure Database för PostgreSQL – enskild Server
Azure Database för PostgreSQL föredrar ansluter dina klientprogram till PostgreSQL-tjänsten med hjälp av Secure Sockets Layer (SSL). Att framtvinga SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man-in-the-middle”-attacker genom att kryptera dataströmmen mellan servern och ditt program.

Som standard konfigureras PostgreSQL-databastjänst för att kräva SSL-anslutning. Du kan välja att inaktivera SSL-kravet om klientprogrammet inte har stöd för SSL-anslutning. 

## <a name="enforcing-ssl-connections"></a>Att framtvinga SSL-anslutningar
Tillämpning av SSL-anslutningar är aktiverat som standard för alla Azure Database for PostgreSQL-servrar som tillhandahålls genom Azure portal och CLI. 

På samma sätt innehåller anslutningssträngar som redan har definierats i inställningarna för ”anslutningssträngar” under din server i Azure-portalen de obligatoriska parametrarna för vanliga språk att ansluta till databasservern med SSL. SSL-parametern varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

## <a name="configure-enforcement-of-ssl"></a>Konfigurera tillämpning av SSL
Du kan också inaktivera framtvinga SSL-anslutning. Microsoft Azure rekommenderar att du alltid aktivera **framtvinga SSL-anslutning** för förbättrad säkerhet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal
Gå till din Azure Database for PostgreSQL-server och klicka på **anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera den **framtvinga SSL-anslutning** inställningen. Klicka sedan på **spara**. 

![Anslutningssäkerhet – inaktivera framtvinga SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Du kan bekräfta inställningen genom att visa den **översikt** och se den **framtvingad SSL-status** indikatorn.

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera den **ssl tvingande** parameter med hjälp av `Enabled` eller `Disabled` värden respektive i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller framework stöder SSL-anslutningar
Vissa programramverk med PostgreSQL för sina databastjänster Aktivera inte SSL som standard under installationen. Om din PostgreSQL-server använder SSL-anslutningar, men programmet har inte konfigurerats för SSL, kan programmet misslyckas att ansluta till databasservern. Dokumentationen för ditt program om du vill veta hur du aktiverar SSL-anslutningar.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikatet för SSL-anslutning
I vissa fall kan kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdare (CA) certifikatfil (.cer) att ansluta på ett säkert sätt. Certifikatet som ska ansluta till en Azure Database för PostgreSQL-server finns på https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem. Ladda ned certifikatfilen och spara den på din primära plats. 

### <a name="connect-using-psql"></a>Ansluta med psql
I följande exempel visas hur du ansluter till din PostgreSQL-server med psql-kommandoradsverktyget. Använd den `sslmode=verify-full` inställning för anslutningssträngen att framtvinga SSL-certifikatverifiering. Skicka lokala certifikatarkivet sökvägen till den `sslrootcert` parametern.

Nedan visas ett exempel på psql-anslutningssträng:
```
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar filsökvägen för det certifikatet som du sparade.


## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet i [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
