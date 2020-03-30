---
title: Installera TmaxSoft OpenFrame på virtuella Azure-datorer
description: Rehost din IBM z / OS stordator arbetsbelastningar med TmaxSoft OpenFrame miljö på Azure Virtual Machines (VIRTUELLA datorer).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 1ad6e52c421d9cfec4640d3a330b5507d6ed3e9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "72436046"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installera TmaxSoft OpenFrame på Azure

Lär dig hur du konfigurerar en OpenFrame-miljö på Azure som lämpar sig för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar. Den här självstudien går igenom varje steg.

OpenFrame innehåller flera komponenter som skapar stordatoremuleringsmiljön på Azure. OpenFrame-onlinetjänster ersätter till exempel stordatorns mellanprogram som IBM Customer Information Control System (CICS) och OpenFrame Batch, med TJES-komponenten, ersätter IBM-stordatorns undersystem för jobbinmatning (JES).

OpenFrame fungerar med alla relationsdatabaser, inklusive Oracle Database, Microsoft SQL Server, IBM Db2 och MySQL. Denna installation av OpenFrame använder TmaxSoft Tibero relationsdatabas. Både OpenFrame och Tibero körs på ett Linux-operativsystem. Den här självstudien installerar CentOS 7.3, även om du kan använda andra Linux-distributioner som stöds. OpenFrame-programservern och Tibero-databasen installeras på en virtuell dator (VM).

Självstudiekursen går igenom installationen av OpenFrame-paketkomponenterna. Vissa måste installeras separat.

Main OpenFrame-komponenter:

- Nödvändiga installationspaket.
- Tibero databas.
- Open Database Connectivity (ODBC) används av program i OpenFrame för att kommunicera med Tibero-databasen.
- OpenFrame Base, mellanmaterialet som hanterar hela systemet.
- OpenFrame Batch, lösningen som ersätter stordatorns batchsystem.
- TACF, en tjänstmodul som styr användarnas åtkomst till system och resurser.
- ProSort, ett sorteringsverktyg för batchtransaktioner.
- OFCOBOL, en kompilator som tolkar stordatorns COBOL-program.
- OFASM, en kompilator som tolkar stordatorns assemblerprogram.
- OpenFrame Server Type C (OSC ), lösningen som ersätter stordatorns mellanprogram och IBM CICS.
- Java Enterprise User Solution (JEUS ), en webbprogramserver som är certifierad för Java Enterprise Edition 6.
- OFGW, OpenFrame gateway-komponenten som ger en 3270-lyssnare.
- OFManager, en lösning som tillhandahåller OpenFrames drift- och hanteringsfunktioner i webbmiljön.

Andra nödvändiga OpenFrame-komponenter:

- OSI, den lösning som ersätter stordatorn mellanprogram och IMS DC.
- TJES, lösningen som tillhandahåller stordatorns JES-miljö.
- OFTSAM, lösningen som gör att (V)SAM-filer kan användas i det öppna systemet.
- OFHiDB, den lösning som ersätter stordatorns IMS DB.
- OFPLI, en kompilator som tolkar stordatorns PL/I-program.
- PROTRIEVE, en lösning som kör stordatorspråket CA-Easytrieve.
- OFMiner, en lösning som analyserar stordatortillgångarna och sedan migrerar dem till Azure.

## <a name="architecture"></a>Arkitektur

Följande bild ger en översikt över de arkitekturkomponenter i OpenFrame 7.0 som är installerade i den här självstudien:

![OpenFrame-komponenter](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure-systemkrav

I följande tabell visas kraven för installationen på Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Krav</th><th>Beskrivning</th></tr>
</thead>
<tbody>
<tr><td>Linuxdistributioner som stöds på Azure
</td>
<td>
Linux x86 2,6 (32-bitars, 64-bitars)<br/>
Röd hatt 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Maskinvara
</td>
<td>Kärnor: 2 (minimum)<br/>
Minne: 4 GB (minimum)<br/>
Byt utrymme: 1 GB (minimum)<br/>
Hårddisk: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Valfri programvara för Windows-användare
</td>
<td>PuTTY: Används i den här guiden för att konfigurera vm-funktioner<br/>
WinSCP: En populär SFTP-klient och FTP-klient som du kan använda<br/>
Eclipse för Windows: En utvecklingsplattform som stöds av TmaxSoft<br/>
(Microsoft Visual Studio stöds inte just nu)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Krav

Planera att spendera några dagar för att montera all nödvändig programvara och slutföra alla manuella processer.

Gör följande innan du börjar:

- Hämta OpenFrame-installationsmedia från TmaxSoft. Om du är en befintlig TmaxSoft-kund kontaktar du din TmaxSoft-representant för en licensierad kopia. Annars begär du en testversion från [TmaxSoft](https://www.tmaxsoft.com/contact/).

- Begär OpenFrame-dokumentationen genom <support@tmaxsoft.com>att skicka e-post till .

- Skaffa en Azure-prenumeration om du inte redan har en. Du kan också skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Valfri. Konfigurera en VPN-tunnel för plats till plats eller en jumpbox som begränsar åtkomsten till den virtuella Azure-datorn till tillåtna användare i organisationen. Det här steget krävs inte, men det är en bästa praxis.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurera en virtuell dator på Azure för OpenFrame och Tibero

Du kan ställa in OpenFrame-miljön med hjälp av olika distributionsmönster, men följande procedur visar hur du distribuerar OpenFrame-programservern och Tibero-databasen på en virtuell dator. I större miljöer och för stora arbetsbelastningar är en bästa praxis att distribuera databasen separat på sin egen virtuella dator för bättre prestanda.

**Så här skapar du en virtuell dator**

1. Gå till Azure-portalen och logga in på <https://portal.azure.com> ditt konto.

2. Klicka på **Virtuella datorer**.

    ![Resurslista i Azure-portalen](media/vm-01.png)

3. Klicka på **Lägg till**.

    ![Alternativet Lägg till i Azure-portalen](media/vm-02.png)

4. Till höger om **operativsystem**klickar du på **Mer**.

     ![Fler alternativ i Azure-portalen](media/vm-03.png)

5. Klicka på **CentOS-baserade 7.3** om du vill följa den här genomstuningen exakt, eller så kan du välja en annan Linux-distribution som stöds.

     ![Operativsystemalternativ i Azure-portalen](media/vm-04.png)

6. I **inställningarna för Grunderna** anger du **Namn**, **Användarnamn**, **Autentiseringstyp**, **Prenumeration** (Pay-As-You-Go är AWS-betalningsstilen) och **resursgrupp** (använd en befintlig eller skapa en TmaxSoft-grupp).

7. När det är klart (inklusive det offentliga/privata nyckelparet för **autentiseringstyp**) klickar du på **Skicka**.

> [!NOTE]
> Om du använder en offentlig SSH-nyckel för **autentiseringstyp**läser du stegen i nästa avsnitt för att generera det offentliga/privata nyckelparet och återupptar sedan stegen här.

### <a name="generate-a-publicprivate-key-pair"></a>Generera ett offentligt/privat nyckelpar

Om du använder ett Windows-operativsystem behöver du PuTTYgen för att generera ett offentligt/privat nyckelpar.

Den offentliga nyckeln kan delas fritt, men den privata nyckeln bör hållas helt hemlig och får aldrig delas med en annan part. När du har genererat nycklarna måste du klistra in **den offentliga SSH-nyckeln** i konfigurationen – i själva verket laddar du upp den till Linux-datorn. Den lagras inuti\_auktoriserade \~nycklar i katalogen /.ssh i användarkontots arbetskatalog. Linux-vm kan sedan känna igen och validera anslutningen när du tillhandahåller den associerade **SSH-privata nyckeln** i SSH-klienten (i vårt fall PuTTY).

När du ger nya personer åtkomst till den virtuella datorn: 

- Varje ny individ genererar sina egna offentliga / privata nycklar med PuTTYgen.
- Enskilda personer lagrar sina egna privata nycklar separat och skickar information om offentlig nyckel till administratören för den virtuella datorn.
- Administratören klistrar in innehållet i \~den offentliga nyckeln i\_filen /.ssh/authorized keys.
- Den nya personen ansluter via PuTTY.

**Så här skapar du ett offentligt/privat nyckelpar**

1.  Ladda ner PuTTYgen från <https://www.putty.org/> och installera den med alla standardinställningar.

2.  Om du vill öppna PuTTYgen letar du\\upp\\installationskatalogen för PuTTY i C: Program\-filar PuTTY.

    ![PuTTY-gränssnitt](media/puttygen-01.png)

3.  Klicka på **Generera**.

    ![Dialogrutan PuTTY-nyckel generera](media/puttygen-02.png)

4.  Spara både den offentliga nyckeln och den privata nyckeln efter generation. Klistra in innehållet i den offentliga nyckeln i avsnittet **SSH public key** i fönstret Skapa grundläggande enheter för **virtuella datorer \> ** (visas i steg 6 och 7 i föregående avsnitt).

    ![Dialogrutan PuTTY-nyckel generera](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurera vm-funktioner

1. I Azure-portalen **Choose a size** väljer du önskade vilka maskinvaruinställningar för Linux-datorn du vill ha i Azure-portalen. *Minimikraven* för installation av både Tibero och OpenFrame är 2 processorer och 4 GB RAM som visas i den här exempelinstallationen:

    ![Skapa virtuell dator - Grunderna](media/create-vm-01.png)

2. Klicka på **3 Inställningar** och använd standardinställningarna för att konfigurera valfria funktioner.
3. Granska dina betalningsuppgifter.

    ![Skapa virtuell dator - Inköp](media/create-vm-02.png)

4. Skicka in dina val. Azure börjar distribuera den virtuella datorn. Den här processen tar vanligtvis några minuter.

5. När den virtuella datorn distribueras visas instrumentpanelen som visar alla inställningar som har valts under konfigurationen. Anteckna den **offentliga IP-adressen**.

    ![tmax på Azure-instrumentpanelen](media/create-vm-03.png)

6. Öppna PuTTY.

7. För **Värdnamn**skriver du ditt användarnamn och den offentliga IP-adressen som du kopierade. Användarnamn **\@publicip**.

    ![Dialogrutan PuTTY-konfiguration](media/putty-01.png)

8. Klicka på **Anslutning \> \> SSH-auth**i rutan **Kategori** . Ange sökvägen till din **privata nyckelfil.**

    ![Dialogrutan PuTTY-konfiguration](media/putty-02.png)

9. Klicka på **Öppna** för att starta PuTTY-fönstret. Om det lyckas är du ansluten till din nya CentOS VM som körs på Azure.

10. Om du vill logga in som rotanvändare skriver **du sudo bash**.

    ![Root-användare inloggning i kommandofönstret](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Ställa in miljö och paket

Nu när den virtuella datorn har skapats och du är inloggad måste du utföra några installationssteg och installera de nödvändiga förinstallationspaketen.

1. Mappa namnet **pådemo** till den lokala IP-adressen med`vi /etc/hosts`hjälp av vi för att redigera hosts-filen ( ). Förutsatt att vår IP är 192.168.96.148 ofdemo, är detta före förändringen:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Detta är efter förändringen:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    192.168.96.148   ofdemo
    ```

2. Skapa grupper och användare:

    ```vi
    [root@ofdemo ~]# adduser -d /home/oframe7 oframe7 
    [root@ofdemo ~]# passwd oframe7
    ```

3. Ändra lösenordet för användarsarmen7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Uppdatera kärnparametrarna i /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Uppdatera kärnparametrarna dynamiskt utan omstart:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Hämta de paket som krävs: Kontrollera att servern är ansluten till Internet, hämta följande paket och installera dem sedan:

     - dos2unix (dos2unix)
     - glibc
     - glibc.i686 glibc.x86\_64
     - Libaio
     - Ncurses

          > [!NOTE]
          > När du har installerat ncurses-paketet skapar du följande symboliska länkar:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - Gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - Strace
     - Ltrace
     - Gdb

7. Vid Java RPM-installation gör du följande:

```
root@ofdemo ~]# rpm -ivh jdk-7u79-linux-x64.rpm
[root@ofdemo ~]# vi .bash_profile

# JAVA ENV
export JAVA_HOME=/usr/java/jdk1.7.0_79/
export PATH=$JAVA_HOME/bin:$PATH
export CLASSPATH=$CLASSPATH:$JAVA_HOME/jre/lib/ext:$JAVA_HOME/lib/tools.jar

[root@ofdemo ~]# source /etc/profile
[root@ofdemo ~]# java –version

java version "1.7.0_79"
Java(TM) SE Runtime Environment (build 1.7.0_79-b15)
Java HotSpot(TM) 64-Bit Server VM (build 24.79-b02, mixed mode)

[root@ofdemo ~]# echo $JAVA_HOME /usr/java/jdk1.7.0_79/
```

## <a name="install-the-tibero-database"></a>Installera Tibero-databasen

Tibero tillhandahåller flera nyckelfunktioner i OpenFrame-miljön på Azure:

- Tibero används som OpenFrame internt datalager för olika systemfunktioner.
- VSAM-filer, inklusive KSDS, RRDS och ESDS, använder Tibero-databasen internt för datalagring.
- TACF-datadatabasen lagras i Tibero.
- OpenFrame-kataloginformationen lagras i Tibero.
- Tibero-databasen kan användas som ersättning för IBM Db2 för att lagra programdata.

**Så här installerar du Tibero**

1. Kontrollera att den binära Tiber-installationsfilen finns och granska versionsnumret.
2. Kopiera Tibero-programvaran till Tibero-användarkontot (oframe). Ett exempel:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Öppna .bash-profilen\_`vi .bash_profile`i vi ( ) och klistra in följande i den:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Om du vill köra bash-profilen, vid kommandotolkenstyp:

    ```
    source .bash_profile
    ```

5. Generera tipsfilen (en konfigurationsfil för Tibero) och öppna den sedan i vi. Ett exempel:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Ändra \$\_TB HOME/client/config/tbdsn.tbr och sätt 127.0.0.1 istället förlocalhost som visas:

    ```
    TVSAM=( 
    (INSTANCE=(HOST=127.0.0.1)
        (PT=8629)
    (DB_NAME=TVSAM)
          )
     )
    ```

7. Skapa databasen. Följande utdata visas:

    ```
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NOMOUNT mode).
     /--------------------- newmount sql ------------------------/
    create database character set MSWIN949 national character set UTF16;
    /-----------------------------------------------------------/
    Database created.
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof.
    Listener port = 8629
    Tibero 6
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    /opt/tmaxdb/tibero6/bin/tbsvr
    ………………………..
    Creating agent table...
    Done.
    For details, check /opt/tmaxdb/tibero6/instance/TVSAM/log/system_init.log.
    ************************************************** 
    * Tibero Database TVSAM is created successfully on Fri Aug 12 19:10:43 UTC 2016.
    *     Tibero home directory ($TB_HOME) =
    *         /opt/tmaxdb/tibero6
    *     Tibero service ID ($TB_SID) = TVSAM
    *     Tibero binary path =
    *         /opt/tmaxdb/tibero6/bin:/opt/tmaxdb/tibero6/client/bin
    *     Initialization parameter file =
    *         /opt/tmaxdb/tibero6/config/TVSAM.tip
    * 
    * Make sure that you always set up environment variables $TB_HOME and
    * $TB_SID properly before you run Tibero.
     ******************************************************************************
    ```

8. Om du vill återvinna Tibero stänger `tbdown` du först av den med kommandot. Ett exempel:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nu starta Tibero med `tbboot`. Ett exempel:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Om du vill skapa ett tabellutrymme öppnar du databasen med SYS-användare (sys/tmax) och skapar sedan det nödvändiga tabellutrymmet för standardvolymen och TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Skriv nu följande SQL-kommandon:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Starta Tibero och kontrollera att Tibero-processerna körs:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Resultat:

![Tibero utgång](media/tibero-01.png)

## <a name="install-odbc"></a>Installera ODBC

Program i OpenFrame kommunicerar med Tibero-databasen med hjälp av ODBC API som tillhandahålls av unixODBC-projektet med öppen källkod.

Så här installerar du ODBC:

1. Kontrollera att installationsfilen för installationsprogrammet unixODBC-2.3.4.tar.gz `wget unixODBC-2.3.4.tar.gz` finns eller använd kommandot. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Packa upp den binära. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Navigera till katalogen unixODBC-2.3.4 och generera Makefile med hjälp av kontrollmaskinsinformationen. Ett exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Som standard installeras unixODBC i /usr `--prefix` /local, så skickar ett värde för att ändra platsen. På samma sätt installeras konfigurationsfiler i `--sysconfdir` /etc som standard, så passerar värdet för önskad plats.

4. Kör Makefile:`[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopiera den körbara filen i programkatalogen efter kompilering. Ett exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Använd vi för att`vi ~/.bash_profile`redigera bash-profilen ( ) och lägg till följande:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Ansök ODBC. Redigera följande filer därefter. Ett exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ source ~/.bash_profile

     [oframe7@ofdemo ~]$ cd

     [oframe7@ofdemo ~]$ odbcinst -j unixODBC 2.3.4
     DRIVERS............: /home/oframe7/odbcinst.ini
     SYSTEM DATA SOURCES: /home/oframe7/odbc.ini
     FILE DATA SOURCES..: /home/oframe7/ODBCDataSources
     USER DATA SOURCES..: /home/oframe7/unixODBC/etc/odbc.ini
     SQLULEN Size.......: 8
     SQLLEN Size........: 8
     SQLSETPOSIROW Size.: 8

     [oframe7@ofdemo ~]$ vi odbcinst.ini

     [Tibero]
     Description = Tibero ODBC driver for Tibero6
     Driver = /opt/tmaxdb/tibero6/client/lib/libtbodbc.so
     Setup = 
     FileUsage = 
     CPTimeout = 
     CPReuse = 
     Driver Logging = 7

     [ODBC]
     Trace = NO 
     TraceFile = /home/oframe7/odbc.log 
     ForceTrace = Yes 
     Pooling = No 
     DEBUG = 1

     [oframe7@ofdemo ~]$ vi odbc.ini

     [TVSAM]
     Description = Tibero ODBC driver for Tibero6 
     Driver = Tibero 
     DSN = TVSAM 
     SID = TVSAM 
     User = tibero 
     password = tmax
     ```

8. Skapa en symbolisk länk och validera Tibero-databasanslutningen:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Följande utdata visas:

![ODBC-utgång som visar ansluten till SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installera OpenFrame Base

Basprogramservern installeras före de enskilda tjänster som OpenFrame använder för att hantera systemet på Azure, inklusive transaktionshanteringsserverprocesser.

**Så här installerar du OpenFrame Base**

1. Kontrollera att Tibero-installationen lyckades och kontrollera\_sedan\_att\_det\_finns\_följande Konfigurationsfil för Installationsprogrammet för OpenFrame Base7 0 Linux x86 64.bin och konfigurationsfilen för base.properties.

2. Uppdatera bash-profilen med följande Tibero-specifik information:

     ```bash
     alias ofhome='cd $OPENFRAME_HOME'
     alias ulog='cd $OPENFRAME_HOME/log/tmax/ulog'
     alias sysjcl='cd $OPENFRAME_HOME/volume_default/SYS1.JCLLIB'
     alias sysload='cd $OPENFRAME_HOME/volume_default/SYS1.LOADLIB'
     alias sysproc='cd $OPENFRAME_HOME/volume_default/SYS1.PROCLIB'
     alias oscsrc='cd $OPENFRAME_HOME/osc/oivp'
     alias osisrc='cd $OPENFRAME_HOME/osi/oivp'
     alias defvol='cd $OPENFRAME_HOME/volume_default'
     ```

3. Kör bash-profilen:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Se till att Tibero-processerna körs. Ett exempel:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Base](media/base-01.png)

     > [!IMPORTANT]
     > Se till att du startar Tibero före installationen.

5. Generera licens på [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) och placera OpenFrame Base-, Batch-, TACF-, OSC-licenserna i lämplig mapp:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Hämta filerna OpenFrame Base binary and base.properties:

     ```
     [oframe7@ofdemo ~]$ vi base.properties
     OPENFRAME_HOME= <appropriate location for installation> ex. /opt/tmaxapp/OpenFrame TP_HOST_NAME=<your IP Hostname> ex. ofdemo 
     TP_HOST_IP=<your IP Address> ex. 192.168.96.148 
     TP_SHMKEY=63481 
     TP_TPORTNO=6623 
     TP_UNBLOCK_PORT=6291 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     MASCAT_NAME=SYS1.MASTER.ICFCAT 
     MASCAT_CREATE=YES 
     DEFAULT_VOLSER=DEFVOL 
     VOLADD_DEFINE=YES TSAM_USERNAME=tibero 
     TSAM_PASSWORD=tmax 
     TSAM_DATABASE=oframe 
     DATASET_SHMKEY=63211 
     DSLOCK_DATA=SYS1.DSLOCK.DATA 
     DSLOCK_LOG=SYS1.DSLOCK.LOG 
     DSLOCK_SEQ=dslock_seq.dat 
     DSLOCK_CREATE=YES 
     OPENFRAME_LICENSE_PATH=/opt/tmaxapp/license/OPENFRAME TMAX_LICENSE_PATH=/opt/tmaxapp/license/TMAX
     ```

7. Kör installationsprogrammet med filen base.properties. Ett exempel:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    När det är klart är meddelandet Slutförd för installation.

8. Verifiera OpenFrame Base-katalogstrukturen `ls -ltr` med kommandot. Ett exempel:

     ```
     [oframe7@ofdemo OpenFrame]$ ls -ltr
     total 44

     drwxrwxr-x. 4 oframe7 oframe7 61 Nov 30 16:57 UninstallerData 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 bin 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 cpm drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 data 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:57 include 
     drwxrwxr-x. 2 oframe7 oframe7 8192 Nov 30 16:57 lib 
     drwxrwxr-x. 6 oframe7 oframe7 48 Nov 30 16:57 log 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 profile 
     drwxrwxr-x. 7 oframe7 oframe7 62 Nov 30 16:57 sample 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 schema 
     drwxrwxr-x. 2 oframe7 oframe7 6 Nov 30 16:57 temp 
     drwxrwxr-x. 3 oframe7 oframe7 16 Nov 30 16:57 shared 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 license 
     drwxrwxr-x. 23 oframe7 oframe7 4096 Nov 30 16:58 core 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 config 
     drwxrwxr-x. 2 oframe7 oframe7 4096 Nov 30 16:58 scripts 
     drwxrwxr-x. 2 oframe7 oframe7 25 Nov 30 16:58 volume_default
     ```

9. Starta OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ cp /usr/lib/libtermcap.so.2 $TMAXDIR/lib
     Startup Tmax Server
     [oframe7@ofdemo ~]$ tmboot
     ```

     ![tmboot kommandoutdata](media/base-02.png)

10. Kontrollera att processstatusen är klar med kommandot tmadmin i si. RDY visas i **statuskolumnen** för var och en av processerna:

     ![tmadmin kommandoutdata](media/base-03.png)

11. Stäng av OpenFrame Base:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(vtammgr:43) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLH downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: CLL downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TLM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMM downed: Wed Sep  7 15:37:21 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-openframe-batch"></a>Installera OpenFrame-batch

OpenFrame Batch består av flera komponenter som simulerar stordatorbatchsmiljöer och används för att köra batchjobb på Azure.

**Så här installerar du Batch**

1. Kontrollera att basinstallationen lyckades och kontrollera\_sedan\_att\_konfigurationsfilen OpenFrame Batch7 0 Fix2\_MVS\_Linux\_x86 64.bin\_och konfigurationsfilen batch.properties finns:

2. Skriv om du `vi batch.properties` redigerar filen batch.properties i kommandotolken med vi.

3. Ändra parametrarna enligt följande:

     ```
     OPENFRAME_HOME = /opt/tmaxapp/OpenFrame
     DEFAULT_VOLSER=DEFVOL 
     TP_NODE_NAME=NODE1 
     TP_NODE_LIST=NODE1 
     RESOURCE_SHMKEY=66991 
     #JOBQ_DATASET_CREATE=YES 
     #OUTPUTQ_DATASET_CREATE=YES 
     DEFAULT_JCLLIB_CREATE=YES 
     DEFAULT_PROCLIB_CREATE=YES 
     DEFAULT_USERLIB_CREATE=YES 
     TJES_USERNAME=tibero 
     TJES_PASSWORD=tmax 
     TJES_DATABASE=oframe 
     BATCH_TABLE_CREATE=YES
     ```

4. Så här kör du batchinstallationsprogrammet vid kommandotolken:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. När installationen är klar startar du de installerade `tmboot` OpenFrame-paketen genom att skriva i kommandotolken.

    ![tmboot utgång](media/tmboot-01.png)

6. Skriv `tmadmin` vid kommandotolken för att kontrollera OpenFrame-processen.

    ![Tmax Admin-skärm](media/tmadmin-01.png)

7. Kör följande kommandon:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Använd `tmdown` kommandot för att starta och stänga av Batch:

     ```
     [oframe7@ofdemo ~]$tmdown
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:44) downed: Wed Sep  7 16:01:46 2016
     TMDOWN: SERVER(vtammgr: 43) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:45) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:46) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjinit:55) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:49) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:51) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:52) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjmsvr:53) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofruisvr:41) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: SERVER(ofrsmlog:42) downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLL downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TLM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: CLH downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMM downed: Wed Sep  7 16:01:46 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-tacf"></a>Installera TACF

TACF Manager är en OpenFrame-tjänstmodul som styr användarnas åtkomst till system och resurser via RACF-säkerhet.

**Så här installerar du TACF**

1. \_Kontrollera att konfigurationsfilen\_OpenFrame\_Tacf7 0 Fix2\_Linux\_x86 64.bin\_och konfigurationsfilen tacf.properties finns.
2. Kontrollera att batchinstallationen lyckades och använd sedan vi för`vi tacf.properties`att öppna filen tacf.properties ( ).
3. Ändra TACF-parametrarna:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. När du har slutfört TACF-installationsprogrammet använder du TACF-miljövariablerna. Skriv följande i kommandotolken:

     ```
     source \~/.bash\_profile
     ```

5. Kör TACF-installationsprogrammet. Skriv följande i kommandotolken:

     ```
     ./OpenFrame_Tacf7_0_Fix2_Linux_x86_64.bin -f tacf.properties
     ```

     Utdata ser ut ungefär så här:

     ```
     Wed Dec 07 17:36:42 EDT 2016
     Free Memory: 18703 kB 
     Total Memory: 28800 kB

     4 Command Line Args: 
     0:  -f 1:  tacf.properties 
     2:  -m 
     3:  SILENT 
     java.class.path: 
     /tmp/install.dir.41422/InstallerData 
     /tmp/install.dir.41422/InstallerData/installer.zip 
     ZGUtil.CLASS_PATH: 
     /tmp/install.dir.41422/InstallerData 
     tmp/install.dir.41422/InstallerData/installer.zip 
     sun.boot.class.path: 
     /tmp/install.dir.41422/Linux/resource/jre/lib/resources.jar /tmp/install.dir.41422/Linux/resource/jre/lib/rt.jar /tmp/install.dir.41422/Linux/resource/jre/lib/sunrsasign.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jsse.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jce.jar /tmp/install.dir.41422/Linux/resource/jre/lib/charsets.jar /tmp/install.dir.41422/Linux/resource/jre/lib/jfr.jar /tmp/install.dir.41422/Linux/resource/jre/classes
     ```

6. Skriv `tmboot` om OpenFrame i kommandotolken. Utdata ser ut ungefär så här:

     ```
     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/7 
     TMBOOT: TMM is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLL is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: CLH is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: TLM(tlm) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsasvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrlhsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrdsedt) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrcmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofruisvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrsmlog) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(vtammgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjschd) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjinit) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjhist) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmjspbk) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(ofrpmsvr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(obmtsmgr) is starting: Wed Sep  7 17:48:53 2016 
     TMBOOT: SVR(tmsvr) is starting: Wed Sep  7 17:48:53 2016
     ```

7. Kontrollera att processstatusen `tmadmin` är `si` klar i kommandot. Ett exempel:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     I **statuskolumnen** visas RDY:

    ![RDY i statuskolumnen](media/tmboot-02.png)

8. Kör följande kommandon:

     ```
     $$2 NODE1 (tmadm): quit 
     DM quit for node (NODE1)

     [oframe7@ofdemo ~]$ tacfmgr 
     Input USERNAME  : ROOT 
     Input PASSWORD  : SYS1

     TACFMGR: TACF MANAGER START!!!
     QUIT TACFMGR: TACF MANAGER END!!!

     [oframe7@ofdemo ~]$ tmdow
     ```

9. Stäng av servern `tmdown` med kommandot. Utdata ser ut ungefär så här:

     ```
     [oframe7@ofdemo ~]$ tmdown 
     Do you really want to down whole Tmax? (y : n): y

     TMDOWN for node(NODE1) is starting: 
     TMDOWN: SERVER(ofrlhsvr:37) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdsedt:39) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjschd:54) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:47) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:48) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrdmsvr:38) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjmsvr:50) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjhist:56) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrsasvr:36) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrcmsvr:40) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmjspbk:57) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(tmsvr:60) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(ofrpmsvr:58) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: SERVER(obmtsmgr:59) downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLL downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: CLH downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TLM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMM downed: Wed Sep  7 17:50:50 2016 
     TMDOWN: TMAX is down
     ```

## <a name="install-prosort"></a>Installera ProSort

ProSort är ett verktyg som används i batchtransaktioner för sortering av data.

**Så här installerar du ProSort**

1. Kontrollera att batchinstallationen lyckades och kontrollera sedan att **installationsfilen\_prosort-bin-prosort 2sp3-linux64-2123-opt.tar.gz** finns.

2. Kör installationsprogrammet med hjälp av egenskapsfilen. Skriv följande i kommandotolken:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Flytta prosortkatalogen till hemplatsen. Skriv följande i kommandotolken:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Skapa en licensunderkatalog och kopiera licensfilen där. Ett exempel:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Öppna bash.profile i`vi .bash_profile`vi ( ) och uppdatera den på följande sätt:

     ```bash
     #       PROSORT

     PROSORT_HOME=/opt/tmaxapp/prosort 
     PROSORT_SID=gbg 
     PATH=$PATH:$PROSORT_HOME/bin LD_LIBRARY_PATH=$PROSORT_HOME/lib:$LD_LIBRARY_PATH LIBPATH$PROSORT_HOME/lib:$LIBPATH 
     export PROSORT_HOME PROSORT_SID 
     PATH LD_LIBRARY_PATH LIBPATH 
     PATH=$PATH:$OPENFRAME_HOME/shbin 
     export PATH
     ```

6. Om du vill köra bash-profilen skriver du i kommandotolken:`. .bash_profile`

7. Skapa konfigurationsfilen. Ett exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Skapa den symboliska länken. Ett exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifiera ProSort-installationen genom `prosort -h` att köra kommandot. Ett exempel:

     ```
     oframe@oframe7: prosort -h

     Usage: prosort [options] [sort script files]
     options ------
     -h             Display this information 
     -v             Display version information 
     -s             Display state information 
     -j             Display profile information 
     -x             Use SyncSort compatible mode
     ```

## <a name="install-ofcobol"></a>Installera OFCOBOL

OFCOBOL är OpenFrame-kompilatorn som tolkar stordatorns COBOL-program. 

**Så här installerar du OFCOBOL**

1. Kontrollera att batch/online-installationen lyckades och kontrollera\_sedan att\_installationsfilen för\_OpenFrame\_COBOL3 0\_40\_Linux x86 64.bin finns.

2. Om du vill köra OFCOBOL-installationsprogrammet skriver du:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Läs licensavtalet och tryck på Retur för att fortsätta.

4. Acceptera licensavtalet. När installationen är klar visas följande:

     ```
     Choose Install Folder 
     --------------------
     Where would you like to install?
     Default Install Folder: /home/oframe7/OFCOBOL

     ENTER AN ABSOLUTE PATH, OR PRESS <ENTER> TO ACCEPT THE DEFAULT : /opt/tmaxapp/OFCOBOL

     INSTALL FOLDER IS: /opt/tmaxapp/OFCOBOL 
     IS THIS CORRECT? (Y/N): Y[oframe7@ofdemo ~]$ vi .bash_profile

     ============================================================================ Installing... 
     ------------
     [==================|==================|==================|==================] 
     [------------------|------------------|------------------|------------------]

     =============================================================================== Installation Complete 
     --------------------
     Congratulations. OpenFrame_COBOL has been successfully installed
     PRESS <ENTER> TO EXIT THE INSTALLER
     ```

5. Öppna bash-profilen i`vi .bash_profile`vi ( ) och kontrollera att den uppdateras med OFCOBOL-variabler.
6. Kör bash-profilen. Skriv följande i kommandotolken:

     ```
      source ~/.bash_profile
     ```

7. Kopiera OFCOBOL-licensen till den installerade mappen. Ett exempel:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Gå till konfigurationsfilen För OpenFrame tjclrun.conf och öppna den i vi. Ett exempel:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Här är SYSLIB-avsnittet före ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Här är SYSLIB-avsnittet efter ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Granska filen\_OpenFrame\_COBOL InstallLog.log in vi och kontrollera att det inte finns några fel. Ett exempel:
     ```
     [oframe7@ofdemo ~]$ vi $OFCOB_HOME/UninstallerData/log/OpenFrame_COBOL_InstallLog.log 
     …….. 
     Summary 
     ------
     Installation: Successful. 
     131 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalError
     ```
10. Använd `ofcob --version` kommandot och granska versionsnumret för att verifiera installationen. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Starta om OpenFrame med `tmdown/tmboot` kommandot.

## <a name="install-ofasm"></a>Installera OFASM

OFASM är OpenFrame-kompilatorn som tolkar stordatorns monteringsprogram.

**Så här installerar du OFASM**

1. Kontrollera att batch/online-installationen lyckades och kontrollera\_sedan att\_installationsfilen för OpenFrame ASM3 0\_Linux\_x86\_64.bin finns.

2. Kör installationsprogrammet. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Läs licensavtalet och tryck på Retur för att fortsätta.
4. Acceptera licensavtalet.
5. Kontrollera att bash-profilen har uppdaterats med OFASM-variabler. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ source .bash_profile
     [oframe7@ofdemo ~]$ ofasm --version 
     # TmaxSoft OpenFrameAssembler v3 r328 
     (3ff35168d34f6e2046b96415bbe374160fcb3a34)

     [oframe7@ofdemo OFASM]$ vi .bash_profile

     # OFASM ENV 
     export OFASM_HOME=/opt/tmaxapp/OFASM 
     export OFASM_MACLIB=$OFASM_HOME/maclib/free_macro 
     export PATH="${PATH}:$OFASM_HOME/bin:" 
     export LD_LIBRARY_PATH="./:$OFASM_HOME/lib:$LD_LIBRARY_PATH"
     ```

6. Öppna konfigurationsfilen För OpenFrame tjclrun.conf i vi och redigera den på följande sätt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Här är avsnittet [SYSLIB] *före* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Här är [SYSLIB] avsnittet *efter* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Öppna filen\_OpenFrame\_ASM InstallLog.log i vi och kontrollera att det inte finns några fel. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ vi 
     $OFASM_HOME/UninstallerData/log/OpenFrame_ASM_InstallLog.log 
     …….. 
     Summary 
     ------

     Installation: Successful.

     55 Successes 
     0 Warnings 
     0 NonFatalErrors 
     0 FatalErrors
     ```

8. Starta om OpenFrame genom att utfärda något av följande kommandon:

     ```
     tmdown / tmboot
     ```

     —eller—

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installera OSC

OSC är OpenFrame-miljön som liknar IBM CICS som stöder höghastighets-OLTP-transaktioner och andra hanteringsfunktioner.

**Så här installerar du OSC**

1. Kontrollera att basinstallationen lyckades och kontrollera\_sedan\_att\_konfigurationsfilen\_OpenFrame\_OSC7 0 Fix2\_Linux x86 64.bin och konfigurationsfilen för osc.properties finns.
2. Redigera följande parametrar i filen osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Kör installationsprogrammet med hjälp av egenskapsfilen som visas:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     När du är klar visas meddelandet "Slutför installation".

4. Kontrollera att bash-profilen är uppdaterad med OSC-variabler.
5. Granska filen\_OpenFrame\_OSC7\_\_0 Fix2 InstallLog.log. Det bör se ut ungefär så här:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Använd vi för att öppna konfigurationsfilen ofsys.seq. Ett exempel:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. Redigera \#parametrarna \#som visas i avsnitten BASE och BATCH.

     ```
     Before changes
     #BASE
     ofrsasvr
     ofrlhsvr
     ofrdmsvr
     ofrdsedt
     ofrcmsvr
     ofruisvr
     ofrsmlog
     vtammgr
     TPFMAGENT

     #BATCH 
     #BATCH#obmtsmgr
     #BATCH#ofrpmsvr
     #BATCH#obmjmsvr
     #BATCH#obmjschd
     #BATCH#obmjinit
     #BATCH#obmjhist
     #BATCH#obmjspbk
     #TACF #TACF#tmsvr

     After changes  #BATCH
     #BASE          obmtsmgr 
     ofrsasvr       ofrpmsvr
     ofrlhsvr       obmjmsvr
     ofrdmsvr       obmjschd
     ofrdsedt       obmjinit
     ofrcmsvr       obmjhist
     ofruisvr       obmjspbk
     ofrsmlog
     vtammgr        #TACF
     TPFMAGENT      tmsvr
    ```

8. Kopiera licensfilen. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. Om du vill starta och stänga av OSC initierar `osctdlinit OSCOIVP1` du det delade minnet för CICS-regionen genom att skriva i kommandotolken.

10. Kör `oscboot` för att starta upp OSC. Utdata ser ut ungefär så här:

     ```
     OSCBOOT : pre-processing       [ OK ]

     TMBOOT for node(NODE1) is starting: 
     Welcome to Tmax demo system: it will expire 2016/11/4 
     Today: 2016/9/12 
          TMBOOT: TMM is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLL is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: CLH is starting: Mon Sep 12 01:40:25 2016 
          TMBOOT: TLM(tlm) is starting: Mon Sep 12 01:40:25 2016 
     ```

11. Om du vill kontrollera att processstatusen är klar använder du `tmadmin` kommandot i si. Alla processer ska visa RDY i statuskolumnen. **status**

    ![Processer som visar RDY](media/tmadmin-02.png)

12. Stäng av OSC `oscdown` med kommandot.

## <a name="install-jeus"></a>Installera JEUS

JEUS (Java Enterprise User Solution) tillhandahåller presentationslagret på OpenFrame-webbprogramservern.

Innan du installerar JEUS installerar du Apache Ant-paketet, som tillhandahåller de bibliotek och kommandoradsverktyg som behövs för att installera JEUS.

**Så här installerar du Apache Ant**

1. Ladda ner Ant `wget` binary med kommandot. Ett exempel:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Använd `tar` verktyget för att extrahera den binära filen och flytta den till en lämplig plats. Ett exempel:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Skapa en symbolisk länk för effektivitet:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Öppna bash-profilen i`vi .bash_profile`vi ( )och uppdatera den med följande variabler:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Använd den ändrade miljövariabeln. Ett exempel:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Så här installerar du JEUS**

1. Expandera installationsprogrammet med `tar` hjälp av verktyget. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Skapa en **jeus** mapp (`mkdir jeus7`) och packa upp den binära.
3. Ändra till **inställningskatalogen** (eller använd JEUS-parametern för din egen miljö). Ett exempel:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Kör `ant clean-all` innan du utför versionen. Utdata ser ut ungefär så här:

     ```
     Buildfile: /home/oframe7jeus7/setup/build.xml

     clean-bin:
     delete-domain:
     [echo] Deleting a domain configuration: domain = jeus_domain
     delete-nodesxml:
     clean-config:
     clean-all:
     BUILD SUCCESSFUL
     Total time: 0 seconds
     ```

5.  Gör en säkerhetskopia av filen domain-config-template.properties. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Öppna filen domain-config-template.properties i vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Ändra `jeus.password=jeusadmin nodename=Tmaxsoft` till`jeus.password=tmax1234 nodename=ofdemo`

8. Kör `ant install` kommandot för att bygga JEUS.
9.  Uppdatera .bash-profilfilen\_med JEUS-variablerna som visas:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Kör bash-profilen. Ett exempel:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Valfritt*. Skapa ett alias för enkel avstängning och uppstart av JEUS-komponenter:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Om du vill verifiera installationen startar du domänadministratörsservern enligt bilden:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Verifiera genom webbinloggning med syntaxen:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     <http://192.168.92.133:9736/webadmin/login.> Inloggningsskärmen visas till exempel:
    
     ![JEUS WebAdmin-inloggningsskärm](media/jeus-01.png)

     > [!NOTE]
     > Om du får problem med portsäkerhet öppnar du port 9736 eller inaktiverar brandväggen (`systemctl stop firewall`).

14. Om du vill ändra värdnamnet för server1 klickar du på **Lås & Redigera**och sedan på **server1**. Ändra värdnamnet på följande sätt i serverfönstret:

    1.  Ändra **nodnamn** till **ofdemo**.
    2.  Klicka på **OK** till höger i fönstret.
    3.  Klicka på **Använd ändringar** längst ned till vänster i fönstret och ange ändring av *värdnamn*för beskrivning.

    ![JEUS WebAdmin-skärm](media/jeus-02.png)

15. Kontrollera att konfigurationen lyckas på bekräftelseskärmen.

    ![skärmen jeus_domain server](media/jeus-03.png)

16. Starta den hanterade serverprocessen "server1" med följande kommando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installera OFGW

OFGW Är OpenFrame-gatewayen som stöder kommunikation mellan 3270-terminalemulatorn och OSI-basen och hanterar sessionerna mellan terminalemulatorn och OSI.

**Så här installerar du OFGW**

1. Kontrollera att JEUS har installerats och kontrollera sedan\_att\_installationsfilen\_OFGW7 0 1 Generic.bin finns.
2. Kör installationsprogrammet. Ett exempel:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Använd följande platser för motsvarande uppmaningar:
     -   Jeus-arbetskatalog
     -   JEUS-domännamn
     -   JEUS-servernamn
     -   Tibero-drivrutin
     -   Tmax nod ID ofdemo

4. Acceptera resten av standardinställningarna och tryck sedan på Retur för att avsluta installationsprogrammet.

5. Kontrollera att URL:en för OFGW fungerar som förväntat:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Följande skärm visas:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installera OFManager

OFManager tillhandahåller drift- och hanteringsfunktioner för OpenFrame i webbmiljön.

**Så här installerar du OFManager**

1. Kontrollera att installationsfilen OFManager7\_Generic.bin finns.
2. Kör installationsprogrammet. Ett exempel:

     ```
     OFManager7_Generic.bin
     ```

3.  Tryck på Retur för att fortsätta och acceptera sedan licensavtalet.
4.  Välj installationsmappen.
5.  Acceptera alla standardinställningar.
6.  Välj Tibero som databas.
7.  Tryck på Retur för att avsluta installationsprogrammet.
8.  Kontrollera att URL:en för OFManager fungerar som förväntat:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Startskärmen visas:

![Inloggningsskärmen för Tmax OpenFrame Manager](media/ofmanager-01.png)

Som slutför installationen av OpenFrame-komponenterna.

## <a name="next-steps"></a>Nästa steg

Om du funderar på en stordatormigrering är vårt växande partnerekosystem tillgängligt för att hjälpa dig. Detaljerad vägledning om hur du väljer en partnerlösning finns i [Plattformmoderniseringsalliansen](https://datamigration.microsoft.com/).

-   [Kom igång med Azure](https://docs.microsoft.com/azure/)
-   [Dokumentation för Host Integration Server (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Azure Virtual Data Center Lift-and-Shift Guide](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
