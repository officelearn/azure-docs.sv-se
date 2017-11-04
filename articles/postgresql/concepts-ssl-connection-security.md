---
title: "Konfigurera SSL-anslutning i Azure-databas för PostgreSQL | Microsoft Docs"
description: "Anvisningar och information för att konfigurera Azure-databas för PostgreSQL och associerade program ska använda SSL-anslutningar."
services: postgresql
author: JasonMAnderson
ms.author: janders
editor: jasonwhowell
manager: jhubbard
ms.service: postgresql
ms.custom: 
ms.topic: article
ms.date: 11/01/2017
ms.openlocfilehash: 3173964f0315559b0839fd7e659f8f3bd2c30b2a
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2017
---
# <a name="configure-ssl-connectivity-in-azure-database-for-postgresql"></a>Konfigurera SSL-anslutning i Azure-databas för PostgreSQL
Azure-databas för PostgreSQL föredrar ansluta ditt klientprogram till PostgreSQL-tjänsten med hjälp av Secure Sockets Layer (SSL). Framtvinga SSL-anslutningar mellan databasservern och ditt klientprogram hjälper dig att skydda mot attacker som ”man i mitten” genom att kryptera dataströmmen mellan servern och ditt program.

Som standard konfigureras tjänsten PostgreSQL-databas för att kräva SSL-anslutning. Du kan också kan du inaktivera att kräva SSL för att ansluta till databastjänsten om klientprogrammet inte har stöd för SSL-anslutning. 

## <a name="enforcing-ssl-connections"></a>Att framtvinga SSL-anslutningar
Tillämpning av SSL-anslutningar är aktiverad som standard för alla Azure-databas för PostgreSQL-servrar som etablerats via Azure portal och CLI. 

På samma sätt innehåller anslutningssträngar som har fördefinierats i inställningarna för ”anslutningssträngar” under din server i Azure portal de obligatoriska parametrarna för vanliga språk att ansluta till databasservern med SSL. Parametern SSL varierar beroende på anslutningen, till exempel ”ssl = true” eller ”sslmode = kräver” eller ”sslmode = krävs” eller andra ändringar.

## <a name="configure-enforcement-of-ssl"></a>Konfigurera tvingande av SSL
Alternativt kan du inaktivera att framtvinga SSL-anslutning. Microsoft Azure rekommenderar att du alltid aktivera **framtvinga SSL-anslutning** för ökad säkerhet.

### <a name="using-the-azure-portal"></a>Använda Azure Portal
Besök din Azure-databas för PostgreSQL-servern och på **anslutningssäkerhet**. Använd växlingsknappen för att aktivera eller inaktivera den **framtvinga SSL-anslutning** inställningen. Klicka på **spara**. 

![Anslutningssäkerhet - inaktivera framtvinga SSL](./media/concepts-ssl-connection-security/1-disable-ssl.png)

Du kan bekräfta inställningen genom att visa den **översikt** sidan för att visa den **SSL genomdriva status** indikatorn.

### <a name="using-azure-cli"></a>Använda Azure CLI
Du kan aktivera eller inaktivera den **ssl tvingande** parameter med hjälp av `Enabled` eller `Disabled` värden i Azure CLI.

```azurecli
az postgres server update --resource-group myresourcegroup --name mypgserver-20170401 --ssl-enforcement Enabled
```

## <a name="ensure-your-application-or-framework-supports-ssl-connections"></a>Se till att ditt program eller framework stöder SSL-anslutningar
Många vanliga ramverk för programmet som använder PostgreSQL för sin databastjänster, till exempel Drupal och Django, aktiverar inte SSL som standard under installationen. Aktivera SSL-anslutning måste göras efter installationen eller via CLI-kommandon som är specifika för programmet. Om servern PostgreSQL tvingar SSL-anslutningar och associerade program är inte korrekt konfigurerad, kan programmet misslyckas att ansluta till databasservern. Dokumentationen för ditt program om du vill veta hur du aktiverar SSL-anslutningar.


## <a name="applications-that-require-certificate-verification-for-ssl-connectivity"></a>Program som kräver certifikatverifiering för SSL-anslutning
I vissa fall kan kräver program en lokal certifikatfil som genereras från en betrodd certifikatutfärdare (CA)-certifikatfil (.cer) att ansluta på ett säkert sätt. Se följande steg för att hämta den .cer-fil, avkoda certifikatet och bind det till programmet.

### <a name="download-the-certificate-file-from-the-certificate-authority-ca"></a>Hämta certifikatfilen från certifikatutfärdaren (CA) 
Det certifikat som behövs för att kommunicera via SSL med din Azure-databas för PostgreSQL-servern är belägen [här](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt). Hämta certifikatfilen lokalt.

### <a name="download-and-install-openssl-on-your-machine"></a>Hämta och installera OpenSSL på din dator 
Du måste installera OpenSSL på den lokala datorn för att avkoda certifikatfilen som behövs för ditt program att på ett säkert sätt ansluta till databasservern.

#### <a name="for-linux-os-x-or-unix"></a>För Linux, OS X eller Unix
OpenSSL-bibliotek finns i källkoden direkt från den [OpenSSL Software Foundation](http://www.openssl.org). Följande instruktioner leder dig genom stegen för att installera OpenSSL på Linux-datorn. Den här artikeln använder kommandon kända att arbeta med Ubuntu 12.04 och högre.

Öppna en terminalsession och hämta OpenSSL.
```bash
wget http://www.openssl.org/source/openssl-1.1.0e.tar.gz
``` 
Extrahera filerna från det Hämta paketet.
```bash
tar -xvzf openssl-1.1.0e.tar.gz
```
Ange katalogen där filerna har extraherats. Som standard ska det vara på följande sätt.

```bash
cd openssl-1.1.0e
```
Konfigurera OpenSSL genom att köra följande kommando. Om du vill filer i en mapp skiljer sig /usr/local/openssl, se till att ändra enligt följande.

```bash
./config --prefix=/usr/local/openssl --openssldir=/usr/local/openssl
```
Nu när OpenSSL är korrekt konfigurerad, behöver du kompileras om du vill konvertera ditt certifikat. Kompilera genom att köra följande kommando:

```bash
make
```
När kompileringen är klar, är du redo att installera OpenSSL som en körbar fil genom att köra följande kommando:
```bash
make install
```
För att bekräfta att du har installerat OpenSSL på datorn, kör du följande kommando och kontrollera att se till att du får samma utdata.

```bash
/usr/local/openssl/bin/openssl version
```
Du bör se följande meddelande om det lyckas.
```bash
OpenSSL 1.1.0e 7 Apr 2014
```

#### <a name="for-windows"></a>För Windows
Installera OpenSSL på en Windows-dator kan du göra detta på följande sätt:
1. **(Rekommenderas)**  Använder inbyggda Bash för Windows-funktioner i Windows 10 och senare, OpenSSL installeras som standard. Anvisningar om hur du aktiverar Bash för Windows-funktioner i Windows 10 finns [här](https://msdn.microsoft.com/en-us/commandline/wsl/install_guide).
2. Genom att hämta ett Win32/64-program som tillhandahålls av gruppen. Medan OpenSSL Software Foundation inte stöder eller stödja en specifik Windows-installationsprogram, ger en lista över tillgängliga installationsprogram [här](https://wiki.openssl.org/index.php/Binaries).

### <a name="decode-your-certificate-file"></a>Avkoda certifikatfil
Den hämta filen rot-CA är i krypterat format. Använd OpenSSL för att avkoda certifikatfilen. Om du vill göra det, kör du kommandot OpenSSL:

```dos
openssl x509 -inform DER -in BaltimoreCyberTrustRoot.crt -text -out root.crt
```

### <a name="connecting-to-azure-database-for-postgresql-with-ssl-certificate-authentication"></a>Ansluter till Azure-databas för PostgreSQL med SSL-certifikat-autentisering
Nu när du har har avkodas certifikatet, kan du nu ansluta till databasservern på ett säkert sätt via SSL. Om du vill tillåta serververifiering certifikat måste certifikatet placeras i filen ~/.postgresql/root.crt i användarens hemkatalog. (För Microsoft Windows filen heter % APPDATA%\postgresql\root.crt.). Följande innehåller instruktioner för att ansluta till Azure-databas för PostgreSQL.

> [!NOTE]
> För närvarande finns ett känt problem om du använder ”sslmode = Kontrollera fullständig” i din anslutning till tjänsten anslutningen misslyckas med följande fel: _servercertifikat för ”&lt;region&gt;. Control.Database.Windows.NET ”(och 7 namn) inte matchar värdnamnet”&lt;servername&gt;. postgres.database.azure.com ”._
> Om ”sslmode = Kontrollera fullständig” är krävs, Använd Namngivningskonventionen för servrar  **&lt;servername&gt;. database.windows.net** som värdnamn i anslutningssträngen. Vi planerar att ta bort den här begränsningen i framtiden. Anslutningar med andra [SSL lägen](https://www.postgresql.org/docs/9.6/static/libpq-ssl.html#LIBPQ-SSL-SSLMODE-STATEMENTS) ska fortsätta att använda önskade värden namngivningskonvention  **&lt;servername&gt;. postgres.database.azure.com**.

#### <a name="using-psql-command-line-utility"></a>Med hjälp av kommandoradsverktyget psql
I följande exempel visas hur för att ansluta till servern med hjälp av kommandoradsverktyget psql PostgreSQL. Använd den `root.crt` fil som har skapats och `sslmode=verify-ca` eller `sslmode=verify-full` alternativet.

Med hjälp av kommandoradsgränssnittet PostgreSQL, kör du följande kommando:
```bash
psql "sslmode=verify-ca sslrootcert=root.crt host=mypgserver-20170401.postgres.database.azure.com dbname=postgres user=mylogin@mypgserver-20170401"
```
Om detta lyckas visas följande utdata:
```bash
Password for user mylogin@mypgserver-20170401:
psql (9.6.2)
WARNING: Console code page (437) differs from Windows code page (1252)
     8-bit characters might not work correctly. See psql reference
     page "Notes for Windows users" for details.
SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-SHA384, bits: 256, compression: off)
Type "help" for help.

postgres=>
```

#### <a name="using-pgadmin-gui-tool"></a>Verktyget pgAdmin GUI
Konfigurera pgAdmin 4 på ett säkert sätt ansluta via SSL måste du ange den `SSL mode = Verify-CA` eller `SSL mode = Verify-Full` på följande sätt:

![Skärmbild av pgAdmin - anslutning – SSL-läge kräver](./media/concepts-ssl-connection-security/2-pgadmin-ssl.png)

## <a name="next-steps"></a>Nästa steg
Granska olika anslutningsalternativ för programmet efter [anslutningsbibliotek för Azure-databas för PostgreSQL](concepts-connection-libraries.md).
