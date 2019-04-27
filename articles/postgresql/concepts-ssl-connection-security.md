---
title: Konfigurera SSL-anslutning i Azure Database för PostgreSQL
description: Anvisningar och information för att konfigurera Azure Database för PostgreSQL och associerade program att använda normalt SSL-anslutningar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/12/2019
ms.openlocfilehash: 5a0fc99052b18dc1fa837147aa914a473d27d832
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60871425"
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Konfigurera SSL-anslutning i Azure Database för PostgreSQL
Azure Database för PostgreSQL föredrar ansluter dina klientprogram till PostgreSQL-tjänsten med hjälp av Secure Sockets Layer (SSL). Framtvingande av SSL-anslutningar mellan databasservern och klientprogrammen hjälper till att skydda mot ”man in the middle”-attacker genom att kryptera dataströmmen mellan servern och programmet.

Som standard konfigureras PostgreSQL-databastjänst för att kräva SSL-anslutning. Du kan också inaktivera att kräva SSL för att ansluta till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning. 

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
Många vanliga programramverk med PostgreSQL för sina databastjänster, till exempel Drupal och Django, aktivera inte SSL som standard under installationen. Aktivera SSL-anslutning måste göras efter installationen eller via CLI-kommandon som är specifika för programmet. Om din PostgreSQL-server är att framtvinga SSL-anslutningar och det associerade programmet är inte korrekt konfigurerad, kan programmet misslyckas att ansluta till databasservern. Dokumentationen för ditt program om du vill veta hur du aktiverar SSL-anslutningar.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikatet för SSL-anslutning
I vissa fall kan kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdare (CA) certifikatfil (.cer) att ansluta på ett säkert sätt. Se följande steg för att hämta .cer-filen, avkoda certifikatet och binda det till ditt program.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Ladda ned certifikatfilen från den certifikatutfärdaren (CA) 
Det certifikat som krävs för kommunikation via SSL med din Azure Database för PostgreSQL-servern finns [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Ladda ned certifikatfilen lokalt.

### <a name="install-a-cert-decoder-on-your-machine"></a>Installera en certifikat-avkodare på din dator 
Du kan använda [OpenSSL](https://github.com/openssl/openssl) att avkoda certifikatfil som krävs för ditt program på ett säkert sätt ansluta till databasservern. Om du vill lära dig mer om att installera OpenSSL, se den [OpenSSL Installationsinstruktioner](https://github.com/openssl/openssl/blob/master/INSTALL). 


### <a name="decode-your-certificate-file"></a>Avkoda certifikatfil
Den hämta rot-CA-filen är i krypterat format. Använd OpenSSL för att avkoda certifikatfilen. Du gör detta genom att köra det här OpenSSL-kommandot:

```
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Ansluter till Azure Database för PostgreSQL med SSL-certifikatautentisering
Nu när du har har avkodas ditt certifikat, kan du nu ansluta till databasservern på ett säkert sätt via SSL. Om du vill tillåta serververifiering för certifikat måste certifikatet placeras i filen ~/.postgresql/root.crt i användarens arbetskatalog. (På Microsoft Windows filen heter % APPDATA%\postgresql\root.crt.). 

#### <a name="connect-using-psql"></a>Ansluta med psql
I följande exempel visar hur du ansluta till din PostgreSQL-server med psql-kommandoradsverktyget. Använd den `root.crt` filen som skapades och `sslmode=verify-ca` eller `sslmode=verify-full` alternativet.

Med hjälp av PostgreSQL-kommandoradsgränssnittet, kör du följande kommando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mydemoserver.postgres.database.azure.com dbname=postgres user=mylogin@mydemoserver"
```
Om detta lyckas visas följande utdata:
```bash
Password for user mylogin@mydemoserver:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet efter [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
