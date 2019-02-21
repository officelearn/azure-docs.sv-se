---
title: Konfigurera SSL-anslutning i Azure Database för PostgreSQL
description: Anvisningar och information för att konfigurera Azure Database för PostgreSQL och associerade program att använda normalt SSL-anslutningar.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 13a1ed626e7741c90cf902c9ed01911985ca8424
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453451"
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

### <a name="download-and-install-openssl-on-your-machine"></a>Hämta och installera OpenSSL på din dator 
Du måste installera OpenSSL på den lokala datorn för att avkoda certifikatfil som krävs för ditt program på ett säkert sätt ansluta till databasservern.

#### <a name="for-linux-os-x-or-unix"></a>För Linux, Unix eller OS X
OpenSSL-bibliotek finns i källkoden direkt från den [OpenSSL Software Foundation](https://www.openssl.org). Följande anvisningar beskriver hur du stegen för att installera OpenSSL, på din Linux-dator. Den här artikeln använder kommandon kända att arbeta med Ubuntu 12.04 och högre.

Öppna en terminalsession och ladda ned OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extrahera filerna från det Hämta paketet.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Ange katalogen där filerna har extraherats. Som standard ska den vara på följande sätt.

```bash
cd openssl-1.1.0e
```
Konfigurera OpenSSL genom att köra följande kommando. Om du vill filer i en mapp skiljer sig från /usr/local/openssl, se till att ändra enligt följande.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Nu när OpenSSL har konfigurerats korrekt, måste du kompilera den för att konvertera ditt certifikat. För att kompilera, kör du följande kommando:

```bash
make
```
När kompilering är klar är du redo att installera OpenSSL, som en körbar fil genom att köra följande kommando:
```bash
make install
```
För att bekräfta att du har installerat OpenSSL på datorn, kör du följande kommando och kontroll för att kontrollera att du får samma utdata.

```bash
/usr/local/openssl/bin/openssl version
```
Om detta lyckas visas följande meddelande.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>För Windows
Installera OpenSSL på en Windows-dator kan du göra detta på följande sätt:
1. **(Rekommenderas)**  Med hjälp av den inbyggda Bash för Windows-funktionen i Windows 10 och senare, OpenSSL installeras som standard. Anvisningar om hur du aktiverar Bash för Windows-funktioner i Windows 10 finns [här](https://msdn.microsoft.com/commandline/wsl/install_guide).
2. Genom att ladda ned ett Win32/64-program som tillhandahålls av communityn. Även om OpenSSL Software Foundation inte stöder eller godkänner eventuella specifika Windows-installationsprogram, tillhandahåller de en lista över tillgängliga installationsprogram [här](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Avkoda certifikatfil
Den hämta rot-CA-filen är i krypterat format. Använd OpenSSL för att avkoda certifikatfilen. Du gör detta genom att köra det här OpenSSL-kommandot:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Ansluter till Azure Database för PostgreSQL med SSL-certifikatautentisering
Nu när du har har avkodas ditt certifikat, kan du nu ansluta till databasservern på ett säkert sätt via SSL. Om du vill tillåta serververifiering för certifikat måste certifikatet placeras i filen ~/.postgresql/root.crt i användarens arbetskatalog. (På Microsoft Windows filen heter % APPDATA%\postgresql\root.crt.). Följande innehåller instruktioner för att ansluta till Azure Database för PostgreSQL.

#### <a name="using-psql-command-line-utility"></a>Med psql-kommandoradsverktyget
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

#### <a name="using-pgadmin-gui-tool"></a>Med hjälp av pgAdmin GUI-verktyget
Konfigurera pgAdmin 4 för att ansluta på ett säkert sätt via SSL måste du ange den `SSL mode = Verify-CA` eller `SSL mode = Verify-Full` på följande sätt:

![Skärmbild av pgAdmin - connection - SSL-läge kräver](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet efter [anslutningsbibliotek för Azure Database for PostgreSQL](concepts-connection-libraries.md).
