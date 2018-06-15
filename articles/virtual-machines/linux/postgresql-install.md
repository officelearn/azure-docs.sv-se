---
title: Ställ in PostgreSQL på en Linux-VM | Microsoft Docs
description: Lär dig hur du installerar och konfigurerar PostgreSQL på en Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: ''
author: iainfoulds
manager: jeconnoc
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: iainfou
ms.openlocfilehash: 7741f861c5697da1e453c0d613b4b762511cf555
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2018
ms.locfileid: "30241019"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Installera och konfigurera PostgreSQL på Azure
PostgreSQL är en avancerad liknande Oracle och DB2 databas med öppen källkod. Den innehåller funktioner för enterprise-redo som fullständig ACID kompatibilitet, tillförlitlig transaktionell bearbetning och flera version samtidighetskontroll. Det stöder också standarder, till exempel ANSI SQL och SQL/MED (inklusive externa data omslutningar för Oracle, MySQL, MongoDB och många andra). Det är mycket utökningsbart med stöd för över 12 procedurmässig språk, GIN och GiST index, stöd för spatial data och flera NoSQL-liknande funktioner för JSON- eller key-värde-baserade program.

I den här artikeln får du lära dig hur du installerar och konfigurerar PostgreSQL på en Azure-dator som kör Linux.

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="install-postgresql"></a>Installera PostgreSQL
> [!NOTE]
> Du måste redan ha en Azure-dator som kör Linux för att kunna slutföra den här kursen. Om du vill skapa och konfigurera en Linux-VM innan du fortsätter, finns det [virtuella Azure Linux-datorn kursen](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

I det här fallet Använd port 1999 som PostgreSQL-port.  

Ansluta till Linux VM som du skapade via PuTTY. Om du använder en Azure Linux VM Se [hur du använder SSH med Linux på Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) information om hur du använder PuTTY för att ansluta till en Linux-VM.

1. Kör följande kommando för att växla till roten (admin):
   
        # sudo su -
2. Vissa distributioner har beroenden som måste installeras innan du installerar PostgreSQL. Kontrollera om din distro i listan och köra ett kommando:
   
   * Red Hat grundläggande Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debian grundläggande Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Hämta PostgreSQL i rotkatalogen och packa upp paketet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Detta är ett exempel. Du kan hitta mer detaljerad hämta adressen i den [Index/pub/källa /](https://ftp.postgresql.org/pub/source/).
4. Du börjar bygga genom att köra följande kommandon:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Om du vill skapa allt som kan byggas, inklusive dokumentation (HTML och man sidor) och ytterligare moduler (contrib), kör du följande kommando:
   
        # gmake install-world
   
    Du får följande bekräftelsemeddelande:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurera PostgreSQL
1. (Valfritt) Skapa en symbolisk länk för att förkorta PostgreSQL-referens för att inte inkludera versionsnumret:
   
        # ln -s /opt/pgsql9.3.5 /opt/pgsql
2. Skapa en katalog för databasen:
   
        # mkdir -p /opt/pgsql_data
3. Skapa en rotanvändare och ändra användarens profil. Växla sedan till den nya användaren (kallas *postgres* i vårt exempel):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Av säkerhetsskäl använder en rotanvändare PostgreSQL för att initiera, starta eller stänga av databasen.
   > 
   > 
4. Redigera den *bash_profile* filen genom att ange nedanstående kommandon. Dessa rader läggs till i slutet av den *bash_profile* fil:
   
        cat >> ~/.bash_profile <<EOF
        export PGPORT=1999
        export PGDATA=/opt/pgsql_data
        export LANG=en_US.utf8
        export PGHOME=/opt/pgsql
        export PATH=\$PATH:\$PGHOME/bin
        export MANPATH=\$MANPATH:\$PGHOME/share/man
        export DATA=`date +"%Y%m%d%H%M"`
        export PGUSER=postgres
        alias rm='rm -i'
        alias ll='ls -lh'
        EOF
5. Köra den *bash_profile* fil:
   
        $ source .bash_profile
6. Verifiera installationen med hjälp av följande kommando:
   
        $ which psql
   
    Om installationen lyckas visas följande meddelande:
   
        /opt/pgsql/bin/psql
7. Du kan också kontrollera PostgreSQL-version:
   
        $ psql -V
8. Initiera databasen:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Du bör få följande utdata:

![Bild](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Ställ in PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Kör följande kommandon:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Ändra två variabler i filen /etc/init.d/postgresql. Prefixet är inställd på installationssökvägen för PostgreSQL: **/opt/pgsql**. PGDATA är inställd på datasökväg för lagring av PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![Bild](./media/postgresql-install/no2.png)

Ändra så att den körbara filen:

    # chmod +x /etc/init.d/postgresql

Starta PostgreSQL:

    # /etc/init.d/postgresql start

Kontrollera om slutpunkten för PostgreSQL finns på:

    # netstat -tunlp|grep 1999

Du bör se följande utdata:

![Bild](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ansluta till databasen Postgres
Växla till postgres användaren igen:

    # su - postgres

Skapa en Postgres databas:

    $ createdb events

Anslut till databasen händelser som du just har skapat:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Skapa och ta bort en Postgres tabell
Nu när du har anslutit till databasen kan skapa du tabeller i den.

Till exempel skapa en ny exempel Postgres tabell med hjälp av följande kommando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Du har nu konfigurerat en tabell med fyra kolumner med följande kolumnnamn och begränsningar:

1. Kolumnen ”namn” begränsade av kommandot VARCHAR ska vara mindre än 20 tecken.
2. Kolumnen ”mat” anger mat-objekt som kommer att få varje person. VARCHAR begränsar texten för att vara under 30 tecken.
3. Kolumnen ”bekräftad” innehåller information om personen som har svarat på knytkalas. Godkända värden är ”Y” och ”N”.
4. I ”dag” kolumnen visas när de registrerar sig för händelsen. Postgres kräver att skrivas datum som åååå-mm-dd.

Om din tabell har skapats bör du se följande:

![Bild](./media/postgresql-install/no4.png)

Du kan också kontrollera tabellstrukturen med hjälp av följande kommando:

![Bild](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Lägg till data i en tabell
Börja lägga till information i en rad:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Du bör se dessa utdata:

![Bild](./media/postgresql-install/no6.png)

Du kan lägga till några fler personer tabellen. Här följer några alternativ eller skapa egna:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Visa tabeller
Använd följande kommando om du vill visa en tabell:

    select * from potluck;

Utdata är:

![Bild](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Ta bort data i en tabell
Använd följande kommando för att ta bort data i en tabell:

    delete from potluck where name=’John’;

Detta tar bort all information på raden ”John”. Utdata är:

![Bild](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Uppdatera data i en tabell
Använd följande kommando för att uppdatera data i en tabell. För den här har Sandy bekräftat att hon deltar, så vi ändrar sin RSVP från ”N” till ”Y”:

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Mer information om PostgreSQL
Nu när du har slutfört installationen av PostgreSQL i en Azure Linux-dator kan få du med i Azure. Mer information om PostgreSQL finns i [PostgreSQL webbplats](http://www.postgresql.org/).

