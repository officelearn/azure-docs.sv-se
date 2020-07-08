---
title: SSL/TLS – Azure Database for PostgreSQL-enskild server
description: Instruktioner och information om hur du konfigurerar TLS-anslutning för Azure Database for PostgreSQL-enskild server.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: 6660c5d40ffb8ecb338dd9cdf53f24cfe2911713
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86043843"
---
# <a name="configure-tls-connectivity-in-azure-database-for-postgresql---single-server"></a>Konfigurera TLS-anslutning i Azure Database for PostgreSQL-enskild server

Azure Database for PostgreSQL föredrar att ansluta dina klient program till PostgreSQL-tjänsten med hjälp av Transport Layer Security (TLS), tidigare kallat Secure Sockets Layer (SSL). Att framtvinga TLS-anslutningar mellan din databas server och dina klient program hjälper till att skydda mot "man-in-the-Middle"-attacker genom att kryptera data strömmen mellan servern och ditt program.

Som standard är PostgreSQL-databas tjänsten konfigurerad att kräva TLS-anslutning. Du kan välja att inaktivera krav på TLS om klient programmet inte stöder TLS-anslutning.

## <a name="enforcing-tls-connections"></a>Tvinga TLS-anslutningar

För alla Azure Database for PostgreSQL-servrar som tillhandahålls via Azure Portal och CLI är tvingande av TLS-anslutningar aktiverat som standard. 

På samma sätt inkluderar anslutnings strängar som är fördefinierade i inställningarna "anslutnings strängar" under servern i Azure Portal de nödvändiga parametrarna för vanliga språk för att ansluta till din databas server med TLS. TLS-parametern varierar beroende på anslutningen, till exempel "SSL = true" eller "sslmode = Kräv" eller "sslmode = required" och andra variationer.

## <a name="configure-enforcement-of-tls"></a>Konfigurera verk ställande av TLS

Du kan också inaktivera tvingande TLS-anslutning. Microsoft Azure rekommenderar att du alltid aktiverar alternativet **FRAMTVINGA SSL-anslutning** för förbättrad säkerhet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal

Besök Azure Database for PostgreSQL-servern och klicka på **anslutnings säkerhet**. Använd växlings knappen för att aktivera eller inaktivera inställningen **FRAMTVINGA SSL-anslutning** . Klicka sedan på **Spara**.

![Anslutnings säkerhet – inaktivera framtvinga TLS/SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Du kan bekräfta inställningen genom att visa **översikts** sidan för att se status indikatorn för **SSL-framtvingande** .

### <a name="using-azure-cli"></a>Använda Azure CLI

Du kan aktivera eller inaktivera **SSL-tvångs** parametern med `Enabled` respektive `Disabled` värden i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mydemoserver --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-tls-connections"></a>Se till att ditt program eller ramverk stöder TLS-anslutningar

Vissa program ramverk som använder PostgreSQL för sina databas tjänster aktiverar inte TLS som standard under installationen. Om din PostgreSQL-Server framtvingar TLS-anslutningar men programmet inte har kon figurer ATS för TLS, kan det hända att programmet inte kan ansluta till din databas server. Mer information om hur du aktiverar TLS-anslutningar finns i programmets dokumentation.

## <a name="applications-that-require-certificate-verification-for-tls-connectivity"></a>Program som kräver certifikat verifiering för TLS-anslutning

I vissa fall kräver program en lokal certifikat fil som genereras från en certifikat utfärdare för betrodda certifikat utfärdare för att ansluta säkert. Certifikatet för att ansluta till en Azure Database for PostgreSQL-Server finns på https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem . Ladda ned certifikat filen och spara den på önskad plats. (Se följande länkar för certifikat för servrar i suveräna moln: [Azure Government](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem), [Azure Kina](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)och [Azure Germany](https://www.d-trust.net/cgi-bin/D-TRUST_Root_Class_3_CA_2_2009.crt).) 

### <a name="connect-using-psql"></a>Anslut med psql

I följande exempel visas hur du ansluter till PostgreSQL-servern med hjälp av kommando rads verktyget psql. Använd `sslmode=verify-full` inställningen för anslutnings strängen för att framtvinga verifiering av TLS/SSL-certifikat. Överför sökvägen till den lokala certifikat filen till- `sslrootcert` parametern.

Följande kommando är ett exempel på anslutnings strängen psql:

```shell
psql "sslmode=verify-full sslrootcert=BaltimoreCyberTrustRoot.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=myusern@mydemoserver"
```

> [!TIP]
> Bekräfta att värdet som skickas till `sslrootcert` matchar fil Sök vägen för det certifikat som du har sparat.

## <a name="tls-enforcement-in-azure-database-for-postgresql-single-server"></a>TLS-tvång i Azure Database for PostgreSQL enskild server

Azure Database for PostgreSQL-enskild server stöder kryptering för klienter som ansluter till din databas server med hjälp av Transport Layer Security (TLS). TLS är ett bransch standard protokoll som garanterar säkra nätverks anslutningar mellan din databas server och klient program, så att du kan följa kraven för efterlevnad.

### <a name="tls-settings"></a>TLS-inställningar

Azure Database for PostgreSQL enskild server ger möjlighet att genomdriva TLS-versionen för klient anslutningarna. Om du vill framtvinga TLS-versionen använder du inställningen **minsta TLS-version** . Följande värden är tillåtna för den här alternativ inställningen:

|  Minsta TLS-inställning             | Klientens TLS-version stöds                |
|:---------------------------------|-------------------------------------:|
| TLSEnforcementDisabled (standard) | Ingen TLS krävs                      |
| TLS1_0                           | TLS 1,0, TLS 1,1, TLS 1,2 och högre |
| TLS1_1                           | TLS 1,1, TLS 1,2 och högre          |
| TLS1_2                           | TLS version 1,2 och högre           |


Om du till exempel anger den här lägsta TLS-inställningen version till TLS 1,0 betyder det att servern tillåter anslutningar från klienter som använder TLS 1,0, 1,1 och 1.2 +. Du kan också ställa in detta på 1,2 innebär att du bara tillåter anslutningar från klienter som använder TLS 1.2 + och alla anslutningar med TLS 1,0 och TLS 1,1 kommer att avvisas.

> [!Note] 
> Som standard tillämpar Azure Database for PostgreSQL inte en lägsta TLS-version (inställningen `TLSEnforcementDisabled` ).
>
> När du har tillverkat en lägsta TLS-version kan du inte senare inaktivera den lägsta version som tillämpas.

Information om hur du anger TLS-inställningen för Azure Database for PostgreSQL enskild server finns i [Konfigurera TLS-inställningen](howto-tls-configurations.md).

## <a name="next-steps"></a>Nästa steg

Granska olika program anslutnings alternativ i [anslutnings bibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).

- Lär dig hur du [konfigurerar TLS](howto-tls-configurations.md)