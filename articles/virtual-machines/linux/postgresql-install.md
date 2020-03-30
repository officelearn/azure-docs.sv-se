---
title: Konfigurera PostgreSQL på en Virtuell Linux-dator
description: Lär dig hur du installerar och konfigurerar PostgreSQL på en virtuell Linux-dator i Azure
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: ''
tags: azure-resource-manager,azure-service-management
ms.assetid: 1a747363-0cc5-4ba3-9be7-084dfeb04651
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/01/2016
ms.author: cynthn
ms.openlocfilehash: bbfad994de663881e3aa03292fc0d0611a0d0933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75747794"
---
# <a name="install-and-configure-postgresql-on-azure"></a>Installera och konfigurera PostgreSQL på Azure
PostgreSQL är en avancerad öppen källkod databas som liknar Oracle och DB2. Den innehåller företagsfärdiga funktioner som fullständig ACID-efterlevnad, tillförlitlig transaktionsbearbetning och samtidighetskontroll i flera versioner. Den stöder också standarder som ANSI SQL och SQL /MED (inklusive utländska dataomslag för Oracle, MySQL, MongoDB och många andra). Det är mycket utökningsbart med stöd för över 12 procedurspråk, GIN- och GiST-index, stöd för rumsliga data och flera NoSQL-liknande funktioner för JSON eller nyckelvärdesbaserade program.

I den här artikeln får du lära dig hur du installerar och konfigurerar PostgreSQL på en virtuell Azure-dator som kör Linux.


## <a name="install-postgresql"></a>Installera PostgreSQL
> [!NOTE]
> Du måste redan ha en virtuell Azure-dator som kör Linux för att slutföra den här självstudien. Information om hur du skapar och konfigurerar en virtuell Linux-dator innan du fortsätter läser du [azure linux-vm-självstudien](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
> 
> 

Använd i så fall port 1999 som PostgreSQL-port.  

Anslut till den virtuella Linux-datorn som du skapade via PuTTY. Om det här är första gången du använder en Virtuell Azure Linux läser du Så här använder du [SSH med Linux på Azure](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) för att lära dig hur du använder PuTTY för att ansluta till en Virtuell Linux-dator.

1. Kör följande kommando för att växla till roten (admin):
   
        # sudo su -
2. Vissa distributioner har beroenden som du måste installera innan du installerar PostgreSQL. Kontrollera om din distributioner i den här listan och kör lämpligt kommando:
   
   * Red Hat bas Linux:
     
           # yum install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
   * Debianbas Linux:
     
            # apt-get install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam libxslt-devel tcl-devel python-devel -y  
   * SUSE Linux:
     
           # zypper install readline-devel gcc make zlib-devel openssl openssl-devel libxml2-devel pam-devel pam  libxslt-devel tcl-devel python-devel -y  
3. Ladda ner PostgreSQL till rotkatalogen och packa sedan upp paketet:
   
        # wget https://ftp.postgresql.org/pub/source/v9.3.5/postgresql-9.3.5.tar.bz2 -P /root/
   
        # tar jxvf  postgresql-9.3.5.tar.bz2
   
    Ovanstående är ett exempel. Du hittar den mer detaljerade nedladdningsadressen i [index för /pub/source/](https://ftp.postgresql.org/pub/source/).
4. Om du vill starta versionen kör du följande kommandon:
   
        # cd postgresql-9.3.5
   
        # ./configure --prefix=/opt/postgresql-9.3.5
5. Om du vill bygga allt som kan byggas, inklusive dokumentation (HTML`contrib`och man sidor) och ytterligare moduler ( ), kör följande kommando istället:
   
        # gmake install-world
   
    Du bör få följande bekräftelsemeddelande:
   
        PostgreSQL, contrib, and documentation successfully made. Ready to install.

## <a name="configure-postgresql"></a>Konfigurera PostgreSQL
1. (Valfritt) Skapa en symbolisk länk för att förkorta PostgreSQL-referensen till att inte inkludera versionsnumret:
   
        # ln -s /opt/postgresql-9.3.5 /opt/pgsql
2. Skapa en katalog för databasen:
   
        # mkdir -p /opt/pgsql_data
3. Skapa en icke-rotanvändare och ändra användarens profil. Byt sedan till den nya användaren (kallas *postgres* i vårt exempel):
   
        # useradd postgres
   
        # chown -R postgres.postgres /opt/pgsql_data
   
        # su - postgres
   
   > [!NOTE]
   > Av säkerhetsskäl använder PostgreSQL en icke-rotanvändare för att initiera, starta eller stänga av databasen.
   > 
   > 
4. Redigera *bash_profile* filen genom att ange kommandona nedan. Dessa rader läggs till i *bash_profile* slutet av bash_profile-filen:
   
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
5. Kör *bash_profile-filen:*
   
        $ source .bash_profile
6. Validera installationen med följande kommando:
   
        $ which psql
   
    Om installationen lyckas visas följande svar:
   
        /opt/pgsql/bin/psql
7. Du kan också kontrollera PostgreSQL-versionen:
   
        $ psql -V

8. Initiera databasen:
   
        $ initdb -D $PGDATA -E UTF8 --locale=C -U postgres -W
   
    Du bör få följande utdata:

![image](./media/postgresql-install/no1.png)

## <a name="set-up-postgresql"></a>Konfigurera PostgreSQL
<!--    [postgres@ test ~]$ exit -->

Kör följande kommandon:

    # cd /root/postgresql-9.3.5/contrib/start-scripts

    # cp linux /etc/init.d/postgresql

Ändra två variabler i filen /etc/init.d/postgresql. Prefixet är inställt på installationssökvägen för PostgreSQL: **/opt/pgsql**. PGDATA är inställt på datalagringssökvägen för PostgreSQL: **/opt/pgsql_data**.

    # sed -i '32s#usr/local#opt#' /etc/init.d/postgresql

    # sed -i '35s#usr/local/pgsql/data#opt/pgsql_data#' /etc/init.d/postgresql

![image](./media/postgresql-install/no2.png)

Ändra filen så att den blir körbar:

    # chmod +x /etc/init.d/postgresql

Starta PostgreSQL:

    # /etc/init.d/postgresql start

Kontrollera om slutpunkten för PostgreSQL är på:

    # netstat -tunlp|grep 1999

Du bör se följande utdata:

![image](./media/postgresql-install/no3.png)

## <a name="connect-to-the-postgres-database"></a>Ansluta till Postgres-databasen
Växla till postgres användaren igen:

    # su - postgres

Skapa en Postgres-databas:

    $ createdb events

Anslut till händelsedatabasen som du just skapade:

    $ psql -d events

## <a name="create-and-delete-a-postgres-table"></a>Skapa och ta bort en Postgres-tabell
Nu när du har anslutit till databasen kan du skapa tabeller i den.

Skapa till exempel ett nytt exempel på Postgres-tabellen med följande kommando:

    CREATE TABLE potluck (name VARCHAR(20),    food VARCHAR(30),    confirmed CHAR(1), signup_date DATE);

Du har nu skapat en tabell med fyra kolumner med följande kolumnnamn och begränsningar:

1. Kolumnen "namn" har begränsats av kommandot VARCHAR för att vara under 20 tecken långt.
2. Kolumnen "mat" anger det livsmedel som varje person kommer att föra med sig. VARCHAR begränsar texten till att vara under 30 tecken.
3. Kolumnen "bekräftad" registrerar om personen har OSA:s till knytkalasna. De godtagbara värdena är "Y" och "N".
4. Kolumnen "datum" visar när de registrerade sig för händelsen. Postgres kräver att datum skrivs som yyyy-mm-dd.

Du bör se följande om tabellen har skapats:

![image](./media/postgresql-install/no4.png)

Du kan också kontrollera tabellstrukturen med hjälp av följande kommando:

![image](./media/postgresql-install/no5.png)

### <a name="add-data-to-a-table"></a>Lägga till data i en tabell
Infoga först information i en rad:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('John', 'Casserole', 'Y', '2012-04-11');

Du bör se dessa utdata:

![image](./media/postgresql-install/no6.png)

Du kan också lägga till ett par personer i tabellen. Här är några alternativ, eller så kan du skapa egna:

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Sandy', 'Key Lime Tarts', 'N', '2012-04-14');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES ('Tom', 'BBQ','Y', '2012-04-18');

    INSERT INTO potluck (name, food, confirmed, signup_date) VALUES('Tina', 'Salad', 'Y', '2012-04-18');

### <a name="show-tables"></a>Visa tabeller
Använd följande kommando för att visa en tabell:

    select * from potluck;

Utdata ser ut så här:

![image](./media/postgresql-install/no7.png)

### <a name="delete-data-in-a-table"></a>Ta bort data i en tabell
Använd följande kommando för att ta bort data i en tabell:

    delete from potluck where name=’John’;

Detta tar bort all information i raden "John". Utdata ser ut så här:

![image](./media/postgresql-install/no8.png)

### <a name="update-data-in-a-table"></a>Uppdatera data i en tabell
Använd följande kommando för att uppdatera data i en tabell. För den här har Sandy bekräftat att de deltar, så vi kommer att ändra OSA från "N" till "Y":

     UPDATE potluck set confirmed = 'Y' WHERE name = 'Sandy';


## <a name="get-more-information-about-postgresql"></a>Få mer information om PostgreSQL
Nu när du har slutfört installationen av PostgreSQL i en Virtuell Azure Linux kan du njuta av att använda den i Azure. Om du vill veta mer om PostgreSQL, besök [PostgreSQL webbplats](https://www.postgresql.org/).

