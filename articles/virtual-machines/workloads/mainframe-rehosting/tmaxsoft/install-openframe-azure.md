---
title: Installera TmaxSoft OpenFrame på Azure Virtual Machines
description: Ange ny värd för dina IBM z/OS stordatorprogram arbetsbelastningar med hjälp av TmaxSoft OpenFrame miljö på Azure Virtual Machines (VM).
services: virtual-machines-linux
documentationcenter: ''
author: njray
ms.author: larryme
ms.date: 04/02/2019
ms.topic: article
ms.service: virtual-machines-linux
ms.openlocfilehash: 78a8b5e7a1c5512f81315519210bc7759dd15342
ms.sourcegitcommit: 48a41b4b0bb89a8579fc35aa805cea22e2b9922c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/15/2019
ms.locfileid: "59578483"
---
# <a name="install-tmaxsoft-openframe-on-azure"></a>Installera TmaxSoft OpenFrame på Azure

Lär dig hur du ställer in en OpenFrame miljö på Azure som är lämplig för utveckling, demonstrationer, testning eller produktionsarbetsbelastningar. Den här självstudien vägleder dig igenom varje steg.

OpenFrame innehåller flera komponenter som skapar stordatorprogram emulering miljö på Azure. Till exempel OpenFrame onlinetjänster Ersätt stordatorprogram mellanprogram, till exempel IBM kundens Information kontroll System (CICS) och OpenFrame Batch med dess TJES-komponenten ersätter IBM-stordatorer jobbet post undersystem (JES).

OpenFrame fungerar med alla relationsdatabas, inklusive Oracle Database, Microsoft SQL Server, IBM Db2 och MySQL. Den här installationen av OpenFrame använder TmaxSoft Tibero relationsdatabas. Både OpenFrame och Tibero som körs på en Linux-operativsystem. Den här självstudien installerar CentOS 7.3, men du kan använda andra Linux-distributioner som stöds. Programservern OpenFrame och Tibero databasen är installerade på en virtuell dator (VM).

Kursen vägleder dig genom installationen av programsvitkomponenterna OpenFrame. Vissa måste installeras separat.

Main OpenFrame komponenter:

- Installationspaket som krävs.
- Tibero databas.
- Open Database Connectivity (ODBC) används av program i OpenFrame för att kommunicera med Tibero-databasen.
- OpenFrame grundläggande, mellanprogram som hanterar hela systemet.
- OpenFrame Batch, den lösning som ersätter den stordatorprogram batch-system.
- TACF, en tjänst-modul som styr användarnas åtkomst till system och resurser.
- ProSort ett sortera verktyg för Batchtransaktioner.
- OFCOBOL en kompilator som tolkar det stordatorprogram COBOL program.
- OFASM en kompilator som tolkar det stordatorprogram assembler program.
- OpenFrame Server typ C (OSC), den lösning som ersätter stordatorprogram mellanprogram och IBM CICS.
- Java Enterprise användaren lösning (JEUS), en webbserver för program som är certifierad för Java Enterprise Edition 6.
- OFGW OpenFrame gateway-komponenten som tillhandahåller en 3270 lyssnare.
- OFManager, en lösning som erbjuder Openframes åtgärden och hanteringsfunktioner i webbmiljö.

Andra nödvändiga OpenFrame komponenter:

- OSI den lösning som ersätter stordatorprogram mellanprogram och IMS DC.
- TJES lösningen som innehåller den stordatorprogram JES miljö.
- OFTSAM lösning som gör att (V) SAM-filer som ska användas i öppna system.
- OFHiDB, som ersätter stordatorprogram lösningen användarens IMS DB.
- OFPLI, en kompilator som tolkar stordatorprogram användarens PL / jag program.
- PROTRIEVE, en lösning som kör stordatorprogram språk CA-Easytrieve.
- OFMiner, en lösning som analyserar stordatorer tillgångar och migrerar dem till Azure.

## <a name="architecture"></a>Arkitektur

Bilden nedan ger en översikt över de OpenFrame 7.0 strukturella komponenter installerade i den här självstudien:

![OpenFrame komponenter](media/openframe-02.png)

## <a name="azure-system-requirements"></a>Azure systemkrav

I följande tabell visas kraven för installation på Azure.
<!-- markdownlint-disable MD033 -->

<table>
<thead>
    <tr><th>Krav</th><th>Beskrivning</th></tr>
</thead>
<tbody>
<tr><td>Linux-distributioner som stöds på Azure
</td>
<td>
Linux x86 2.6 (32-bitars, 64-bitars)<br/>
Red Hat 7.x<br/>
CentOS 7.x<br/>
</td>
</tr>
<tr><td>Maskinvara
</td>
<td>Kärnor: 2 (minimum)<br/>
Minne: 4 GB (minst)<br/>
Växlingsutrymme: 1 GB (minimum)<br/>
Hårddisk: 100 GB (minimum)<br/>
</td>
</tr>
<tr><td>Valfri programvara för Windows-användare
</td>
<td>PuTTY: Används i den här guiden för att konfigurera VM-funktioner<br/>
WinSCP: En populär SFTP-klienten och FTP-klient som du kan använda<br/>
Eclipse för Windows: En utvecklingsplattform som stöds av TmaxSoft<br/>
(Microsoft Visual Studio stöds inte just nu)
</td>
</tr>
</tbody>
</table>

<!-- markdownlint-enable MD033 -->

## <a name="prerequisites"></a>Nödvändiga komponenter

Planera på utgifter några få dagar för att samla ihop alla nödvändiga program och slutför de manuella processerna.

Innan du har kommit igång kan du göra följande:

- Hämta installationsmediet OpenFrame från TmaxSoft. Om du är en befintlig TmaxSoft-kund kan du kontakta din TmaxSoft-representant för en licensierad version. Annars kan begära en utvärderingsversion från [TmaxSoft](http://www.tmaxsoft.com/contact/).

- Begära OpenFrame dokumentationen genom att skicka e-postmeddelande till <support@tmaxsoft.com>.

- Få en Azure-prenumeration om du inte redan har ett. Du kan också skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

- Valfri. Konfigurera en plats-till-plats VPN-tunnel eller en jumpbox som begränsar åtkomsten till virtuella Azure-datorer till tillåtna användare i din organisation. Det här steget krävs inte, men det är en bra idé.

## <a name="set-up-a-vm-on-azure-for-openframe-and-tibero"></a>Konfigurera en virtuell dator på Azure för OpenFrame och Tibero

Du kan ställa in OpenFrame miljön med hjälp av olika distribution mönster, men följande procedur visar hur du distribuerar OpenFrame programservern och Tibero databasen på en virtuell dator. I större miljöer och för ansenliga arbetsbelastningar är bästa praxis att distribuera databasen separat på en egen virtuell dator för bättre prestanda.

**Skapa en virtuell dator**

1. Gå till Azure-portalen på <http://portal.azure.com> och logga in på ditt konto.

2. Klicka på **virtuella datorer**.

    ![Lista över resurser i Azure-portalen](media/vm-01.png)

3. Klicka på **Lägg till**.

    ![Alternativet Lägg till i Azure-portalen](media/vm-02.png)

4. Till höger om **operativsystem**, klickar du på **mer**.

     ![Fler alternativ i Azure-portalen](media/vm-03.png)

5. Klicka på **CentOS-baserade 7.3** att följa den här genomgången exakt, eller så kan du välja en annan stöd för Linux-distribution.

     ![Alternativen för operativsystemet i Azure-portalen](media/vm-04.png)

6. I den **grunderna** inställningar, ange **namn**, **användarnamn**, **autentiseringstyp**, **prenumeration** (Betala per användning är AWS-format av betalning), och **resursgrupp** (Använd en befintlig eller skapa en TmaxSoft).

7. När du är klar (inklusive offentligt/privat nyckelpar för **autentiseringstyp**), klickar du på **skicka**.

> [!NOTE]
> Om du använder en offentlig SSH-nyckel för **autentiseringstyp**, finns i stegen i nästa avsnitt för att generera offentligt/privat nyckelpar och återuppta de här stegen.

### <a name="generate-a-publicprivate-key-pair"></a>Generera ett offentligt/privat nyckelpar

Om du använder ett Windows-operativsystem, måste PuTTYgen att generera ett offentligt/privat nyckelpar.

Den offentliga nyckeln kan delas fritt, men den privata nyckeln ska hållas hemliga helt och hållet och bör aldrig delas med någon annan part. När du har genererat nycklarna måste du klistra in den **offentlig SSH-nyckel** i konfigurationen – gäller, överföra den till Linux-VM. Den lagras inuti behörighet\_nycklar inom den \~/.ssh directory i användarens arbetskatalog. Linux-VM är sedan kunna känna igen och verifiera att anslutningen fungerar när du har angett den associerade **privata SSH-nyckeln** i SSH-klient (i vårt fall PuTTY).

När du ger nya enskilda användare åtkomst till den virtuella datorn: 

- Varje ny person genererar sina egna offentliga och privata nycklar med hjälp av PuTTYgen.
- Enskilda användare lagra sina egna privata nycklar separat och skicka informationen om offentliga nyckeln till administratören för den virtuella datorn.
- Administratören klistrar in innehållet i den offentliga nyckeln till den \~/.ssh/authorized\_nyckelfil.
- Personen som nya ansluter via PuTTY.

**Att generera ett offentligt/privat nyckelpar**

1.  Hämta PuTTYgen från <https://www.putty.org/> och installera den med hjälp av standardinställningarna.

2.  Öppna PuTTYgen genom att leta upp PuTTY installationskatalogen i C:\\programfiler\\PuTTY.

    ![PuTTY-gränssnitt](media/puttygen-01.png)

3.  Klicka på **generera**.

    ![PuTTY-Nyckelgenerator-dialogrutan](media/puttygen-02.png)

4.  När du generering, sparar du både den offentliga och privata nyckeln. Klistra in innehållet i den offentliga nyckeln i den **offentlig SSH-nyckel** delen av den **Skapa virtuell dator \> grunderna** fönstret (visas i steg 6 och 7 i föregående avsnitt).

    ![PuTTY-Nyckelgenerator-dialogrutan](media/puttygen-03.png)

### <a name="configure-vm-features"></a>Konfigurera VM-funktioner

1. Azure-portalen i den **väljer du en storlek** bladet välj Linux-dator maskinvaruinställningar du vill. Den *minsta* kraven för att installera både Tibero och OpenFrame är 2 processorer och 4 GB RAM-minne som du ser i exemplet installationen:

    ![Skapa virtuell dator - grunderna](media/create-vm-01.png)

2. Klicka på **3 inställningar** och Använd standardinställningarna för att konfigurera valfria funktioner.
3. Granska din betalningsinformation.

    ![Skapa virtuell dator - köp](media/create-vm-02.png)

4. Skicka dina val. Azure börjar distribuera den virtuella datorn. Den här processen tar vanligtvis några minuter.

5. När den virtuella datorn har distribuerats visas dess instrumentpanel med alla inställningar som valdes under konfigurationen. Anteckna den **offentliga IP-adressen**.

    ![Tmax på Azure-instrumentpanelen](media/create-vm-03.png)

6. Öppna PuTTY.

7. För **värdnamn**skriver du ditt användarnamn och den offentliga IP-adressen du kopierade. Till exempel **användarnamn\@publicip**.

    ![Dialogrutan för puTTY-konfiguration](media/putty-01.png)

8. I den **kategori** klickar du på **anslutning \> SSH \> Auth**. Ange sökvägen till din **privata nyckeln** fil.

    ![Dialogrutan för puTTY-konfiguration](media/putty-02.png)

9. Klicka på **öppna** att starta fönstret PuTTY. Om detta lyckas kan du är ansluten till din nya CentOS VM som körs på Azure.

10. Om du vill logga in som rotanvändare, Skriv **sudo bash**.

    ![Rotanvändarinloggning i kommandofönstret](media/putty-03.png)

## <a name="set-up-the-environment-and-packages"></a>Konfigurera miljön och paket

Nu när den virtuella datorn har skapats och du har loggat in, måste du utföra några steg i installationen och installera de nödvändiga preinstallation paketen.

1. Mappa namnet **ofdemo** till den lokala IP-adressen med hjälp av vi för att redigera värdfilen (`vi /etc/hosts`). Om vi antar att vår IP är 192.168.96.148 ofdemo, detta är före ändringen:

    ```vi
    127.0.0.1   localhost localhost.localdomain localhost4 localhost4.localdomain4 
    ::1              localhost localhost.localdomain localhost6 localhost6.localdomain 
    <IP Address>    <your hostname>
    ```

     Det här är efter ändringen:

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

3. Ändra lösenordet för användaren oframe7:

    ```vi
    New password: 
    Retype new password: 
    passwd: all authentication tokens updated successfully.
    ```

4. Uppdatera kernel-parametrarna i /etc/sysctl.conf:

    ```vi
    [root@ofdemo ~]# vi /etc/sysctl.conf
    kernel.shmall = 7294967296 
    kernel.sem = 10000 32000 10000 10000
    ```

5. Uppdatera parametrarna kernel dynamiskt utan omstart:

    ```vi
    [root@ofdemo ~]# /sbin/sysctl -p
    ```

6. Hämta de nödvändiga paketen: Kontrollera att servern är ansluten till Internet, hämta följande paket och sedan installera dem:

     - dos2unix
     - glibc
     - glibc.i686 glibc.x86\_64
     - libaio
     - ncurses

          > [!NOTE]
          > När du har installerat paketet ncurses, skapar du följande symboliska länkar:
         ```
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so
         ln -s /usr/lib64/libncurses.so.5.9 /usr/lib/libtermcap.so.2
         ```

     - gcc
     - gcc-c++
     - libaio-devel.x86\_64
     - strace
     - ltrace
     - gdb

7. Vid Java RPM-installationen, gör du följande:

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

## <a name="install-the-tibero-database"></a>Installera databasen för Tibero

Tibero innehåller flera viktiga funktioner i OpenFrame miljön i Azure:

- Tibero används som OpenFrame internt datalager för olika systemfunktioner.
- VSAM filer, inklusive KSDS och RRDS ESDS, använder internt Tibero-databas för lagring av data.
- Datalager TACF lagras i Tibero.
- Kataloginformation OpenFrame lagras i Tibero.
- Tibero databasen kan användas som en ersättning för IBM Db2 för att lagra programdata.

**Installera Tibero**

1. Kontrollera att det finns Tibero binära installationsfilen och granska versionsnumret.
2. Kopiera Tibero programvaran till Tibero användarkonto (oframe). Exempel:

    ```
    [oframe7@ofdemo ~]$ tar -xzvf tibero6-bin-6_rel_FS04-linux64-121793-opt-tested.tar.gz 
    [oframe7@ofdemo ~]$ mv license.xml /opt/tmaxdb/tibero6/license/
    ```

3. Öppna .bash\_profil i vi (`vi .bash_profile`) och klistra in följande i den:

    ```
    # Tibero6 ENV
    export TB_HOME=/opt/tmaxdb/tibero6 
    export TB_SID=TVSAM export TB_PROF_DIR=$TB_HOME/bin/prof 
    export LD_LIBRARY_PATH=$TB_HOME/lib:$TB_HOME/client/lib:$LD_LIBRARY_PATH 
    export PATH=$TB_HOME/bin:$TB_HOME/client/bin:$PATH
    ```

4. Att köra bash-profilen vid kommandotolken skriver:

    ```
    source .bash_profile
    ```

5. Generera filen tips (en konfigurationsfil för Tibero), och sedan öppna den i vi. Exempel:

    ```
    [oframe7@ofdemo ~]$ sh $TB_HOME/config/gen_tip.sh
    [oframe7@ofdemo ~]$ vi $TB_HOME/config/$TB_SID.tip
    ```

6. Ändra \$TB\_HOME/client/config/tbdsn.tbr och placera 127.0.0.1 i stället oflocalhost enligt:

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

8. För att återanvända Tibero först stänga av med hjälp av den `tbdown` kommando. Exempel:

    ```
    [oframe7@ofdemo ~]$$ tbdown 
    Tibero instance terminated (NORMAL mode).
    ```

9. Nu startar Tibero med `tbboot`. Exempel:

    ```
    [oframe7@ofdemo ~]$ tbboot
    Change core dump dir to /opt/tmaxdb/tibero6/bin/prof. Listener port = 8629

    Tibero 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Tibero instance started up (NORMAL mode).
    ```

10. Om du vill skapa en registerutrymme, få åtkomst till databasen med SYS användaren (sys/tmax), sedan skapa nödvändiga registerutrymmet för standard-volym och TACF:

    ```
    [oframe7@ofdemo ~]$ tbsql tibero/tmax
    tbSQL 6  
    TmaxData Corporation Copyright (c) 2008-. All rights reserved.
    Connected to Tibero.
    ```

11. Nu skriver du följande SQL-kommandon:

    ```
    SQL> create tablespace "DEFVOL" datafile 'DEFVOL.dbf' size 500M autoextend on; create tablespace "TACF00" datafile 'TACF00.dbf' size 500M autoextend on; create tablespace "OFM_REPOSITORY" datafile 'ofm_repository.dbf' size 300M autoextend on;
    SQL> Tablespace 'DEFVOL' created.
    SQL> Tablespace 'TACF00' created.
    SQL> Tablespace ' OFM_REPOSITORY ' created.
    SQL> SQL> Disconnected.
    ```

12. Starta Tibero och kontrollera att Tibero processer körs:

    ```
    [oframe7@ofdemo ~]$ tbboot 
    ps -ef | egrep tbsvr
    ```

Utdata:

![Tibero utdata](media/tibero-01.png)

## <a name="install-odbc"></a>Installera ODBC

Program i OpenFrame kommunicerar med Tibero databasen med hjälp av ODBC-API som tillhandahålls av öppen källkod unixODBC-projektet.

Så här installerar ODBC:

1. Kontrollera att det finns unixODBC 2.3.4.tar.gz installer-fil eller Använd den `wget unixODBC-2.3.4.tar.gz` kommando. Exempel:

     ```
     [oframe7@ofdemo ~]$ wget ftp://ftp.unixodbc.org/pub/unixODBC/unixODBC-2.3.4.tar.gz
     ```

2. Packa upp den binära filen. Exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf unixODBC-2.3.4.tar.gz
     ```

3. Gå till katalogen för unixODBC 2.3.4 och generera en make-filen med hjälp av informationen om kontrollerar datorn. Exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ ./configure --prefix=/opt/tmaxapp/unixODBC/ --sysconfdir=/opt/tmaxapp/unixODBC/etc
     ```

     Som standard unixODBC är installerat i usr/local, så `--prefix` skickar ett värde om du vill ändra platsen. På samma sätt konfigurationsfiler är installerade på/etc som standard så `--sysconfdir` skickar värdet för önskad plats.

4. Kör make-fil: `[oframe7@ofdemo unixODBC-2.3.4]$ make`

5. Kopiera den körbara filen i katalogen program efter kompilering. Exempel:

     ```
     [oframe7@ofdemo unixODBC-2.3.4]$ make install
     ```

6. Använd vi för att redigera bash-profil (`vi ~/.bash_profile`) och Lägg till följande:

     ```
     # UNIX ODBC ENV 
     export ODBC_HOME=$HOME/unixODBC 
     export PATH=$ODBC_HOME/bin:$PATH 
     export LD_LIBRARY_PATH=$ODBC_HOME/lib:$LD_LIBRARY_PATH 
     export ODBCINI=$HOME/unixODBC/etc/odbc.ini 
     export ODBCSYSINI=$HOME
     ```

7. Tillämpa ODBC. Redigera följande filer i enlighet med detta. Exempel:

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

8. Skapa en symbolisk länk och validera databasanslutningen Tibero:

     ```
     [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbc.so $ODBC_HOME/lib/libodbc.so.1 [oframe7@ofdemo ~]$ ln $ODBC_HOME/lib/libodbcinst.so 
     $ODBC_HOME/lib/libodbcinst.so.1

     [oframe7@ofdemo lib]$ isql TVSAM tibero tmax
     ```

Följande utdata visas:

![ODBC-utdata som visar ansluten till SQL](media/odbc-01.png)

## <a name="install-openframe-base"></a>Installationsbas OpenFrame

Grundläggande application server har installerats innan de enskilda tjänster som OpenFrame använder för att hantera system på Azure, inklusive transaktionen hantering av serverprocesser.

**Installera OpenFrame Base**

1. Kontrollera att Tibero installationen är klar, och sedan kontrollera att följande OpenFrame\_kostnad för original 7\_0\_Linux\_x86\_64. bin installer-fil och base.properties konfigurationsfilen finns.

2. Uppdatera bash-profil med följande Tibero-specifik information:

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

3. Kör bash-profil:`[oframe7@ofdemo ~]$ . .bash_profile`
4. Se till att Tibero processer körs. Exempel:

     ```linux
     [oframe7@ofdemo ~]$ ps -ef|grep tbsvr
     ```

    ![Bas](media/base-01.png)

     > [!IMPORTANT]
     > Kontrollera att du startar Tibero före installationen.

5. Generera licens på [technet.tmaxsoft.com](https://technet.tmaxsoft.com/en/front/main/main.do) och PLACERA OpenFrame bas, Batch, TACF OSC licenser i mappen:

     ```
     [oframe7@ofdemo ~]$ cp license.dat /opt/tmaxapp/OpenFrame/core/license/
     [oframe7@ofdemo ~]$ cp lictjes.dat lictacf.dat licosc.dat $OPENFRAME_HOME/license/
     ```

6. Hämta OpenFrame Base binära och base.properties filer:

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

7. Kör installationsprogrammet med hjälp av base.properties-filen. Exempel:

    ```
    [oframe7@ofdemo ~]$ chmod a+x OpenFrame_Base7_0_Linux_x86_64.bin 
    [oframe7@ofdemo ~]$ ./OpenFrame_Base7_0_Linux_x86_64.bin -f base.properties
    ```

    När du är klar är installationen hela meddelandet visas.

8. Kontrollera OpenFrame Base directory strukturen använder den `ls -ltr` kommando. Exempel:

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

10. Kontrollera status för processen är klar med hjälp av kommandot tmadmin i si. RDY visas i den **status** kolumn för alla processer:

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

## <a name="install-openframe-batch"></a>Installera OpenFrame Batch

OpenFrame Batch består av flera komponenter som simulerar stordatorprogram batch miljöer och används för att köra batch-jobb på Azure.

**Att installera Batch**

1. Se till att den grundläggande installationen har slutförts och sedan kontrollera att OpenFrame\_Batch7\_0\_Fix2\_MVS\_Linux\_x86\_64. bin installer-fil och batch.Properties konfigurationsfilen finns:

2. I Kommandotolken, Skriv `vi batch.properties` redigera filen batch.properties använder vi.

3. Ändra parametrarna på följande sätt:

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

4. Att köra batch-installationsprogrammet kommandotolk skriver:

     ```
     ./OpenFrame_Batch7_0_Fix2_MVS_Linux_x86_64.bin -f batch.properties
     ```

5. När installationen är klar, startar de installera OpenFrame som genom att skriva `tmboot` i Kommandotolken.

    ![tmboot utdata](media/tmboot-01.png)

6. Typ `tmadmin` vid Kommandotolken för att kontrollera OpenFrame-processen.

    ![Tmax Admin-skärmen](media/tmadmin-01.png)

7. Kör följande kommandon:

     ```
     $$2 NODE1 (tmadm): quit 
     ADM quit for node (NODE1)
     ```

8. Använd den `tmdown` kommando för att starta och stänga av Batch:

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

TACF Manager är en OpenFrame tjänstemodulen som styr användarnas åtkomst till system och resurser via RACF säkerhet.

**Installera TACF**

1. Kontrollera att OpenFrame\_Tacf7\_0\_Fix2\_Linux\_x86\_64. bin installer-fil och tacf.properties konfigurationsfilen finns.
2. Kontrollera att Batch-installationen har slutförts, och sedan använder vi för att öppna filen tacf.properties (`vi tacf.properties`).
3. Ändra parametrarna TACF:

     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame 
     USE_OS_AUTH=NO 
     TACF_USERNAME=tibero 
     TACF_PASSWORD=tmax 
     TACF_DATABASE=oframe 
     TACF_TABLESPACE=TACF00 
     TACF_TABLE_CREATE=YES
     ```

4. När du har slutfört TACF installer gäller TACF miljövariabler. Skriv följande i kommandotolken:

     ```
     source \~/.bash\_profile
     ```

5. Köra installationsprogrammet TACF. Skriv följande i kommandotolken:

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

6. I Kommandotolken, Skriv `tmboot` att starta om OpenFrame. Utdata ser ut ungefär så här:

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

7. Kontrollera att processens status är klar med `tmadmin` i den `si` kommando. Exempel:

     ```
     [oframe7\@ofdemo \~]\$ tmadmin
     ```

     I den **status** kolumnen RDY visas:

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

9. Stänga av servern med hjälp av den `tmdown` kommando. Utdata ser ut ungefär så här:

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

ProSort är ett verktyg som används i Batchtransaktioner vid sortering av data.

**Installera ProSort**

1. Kontrollera att Batch-installationen har lyckats och kontrollera att den **prosort är bin-prosort\_2sp3-linux64-2123-opt.tar.gz** installer-fil finns.

2. Kör installationsprogrammet med hjälp av egenskaper-filen. Skriv följande i kommandotolken:

     ```
     tar -zxvf prosort-bin-prosort\_2sp3-linux64-2123-opt.tar.gz
     ```

3. Flytta katalogen prosort till hemnätverk plats. Skriv följande i kommandotolken:

     ```
     mv prosort /opt/tmaxapp/prosort
     ```

4. Skapa en underkatalog licens och kopiera licensfilen. Exempel:

     ```
     cd /opt/tmaxapp/prosort 
     mkdir license 
     cp /opt/tmaxsw/oflicense/prosort/license.xml /opt/tmaxapp/prosort/license
     ```

5. Öppna bash.profile i vi (`vi .bash_profile`) och uppdatera den på följande sätt:

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

6. För att köra bash-profil i Kommandotolken, skriver du: `. .bash_profile`

7. Skapa konfigurationsfilen. Exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/prosort/config 
     oframe@oframe7: ./gen_tip.sh 
     Using PROSORT_SID "gbg"
      /home/oframe7/prosort/config/gbg.tip generated
     ```

8. Skapa den symboliska länken. Exempel:

     ```
     oframe@oframe7: cd /opt/tmaxapp/OpenFrame/util/ 
     oframe@oframe7home/oframe7/OpenFrame/util :  ln -s DFSORT SORT
     ```

9. Verifiera ProSort installationen genom att köra den `prosort -h` kommando. Exempel:

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

OFCOBOL är OpenFrame kompilatorn som tolkar det stordatorprogram COBOL program. 

**Installera OFCOBOL**

1. Se till att Batch/Online-installationen har slutförts och sedan kontrollera att OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64. bin installer-fil finns.

2. För att köra installationsprogrammet för OFCOBOL i Kommandotolken, skriver du:

     ```
      ./OpenFrame\_COBOL3\_0\_40\_Linux\_x86\_64.bin
     ```

3. Läs licensavtalet och tryck på RETUR för att fortsätta.

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

5. Öppna bash-profil i vi (`vi .bash_profile`) och kontrollera som uppdateras med OFCOBOL variabler.
6. Kör bash-profil. Skriv följande i kommandotolken:

     ```
      source ~/.bash_profile
     ```

7. Kopiera OFCOBOL-licens till mappen installerade. Exempel:
     ```
     mv licofcob.dat $OFCOB_HOME/license
     ```
8. Gå till konfigurationsfilen OpenFrame tjclrun.conf och öppna den i vi. Exempel:
     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

   Här är SYSLIB avsnittet före ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib
     ```
   Här är avsnittet SYSLIB efter ändringen:
     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bin LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${COBDIR}/lib:/ usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib :${ODBC_HOME}/lib 
     :${OFCOB_HOME}/lib
     ```
9. Granska OpenFrame\_COBOL\_InstallLog.log i vi och kontrollera att det inte finns några fel. Exempel:
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
10. Använd den `ofcob --version` kommandot och granska versionsnumret för att verifiera installationen. Exempel:

     ```
     [oframe7@ofdemo ~]$ ofcob --version 
     OpenFrame COBOL Compiler 3.0.54 
     CommitTag:: 645f3f6bf7fbe1c366a6557c55b96c48454f4bf
     ```

11. Starta om OpenFrame med hjälp av den `tmdown/tmboot` kommando.

## <a name="install-ofasm"></a>Installera OFASM

OFASM är OpenFrame kompilatorn som tolkar det stordatorprogram assembler program.

**Installera OFASM**

1. Se till att Batch/Online-installationen har slutförts och sedan kontrollera att OpenFrame\_ASM3\_0\_Linux\_x86\_64. bin installer-fil finns.

2. Kör installationsprogrammet. Exempel:

     ```
     [oframe7@ofdemo ~]$ ./OpenFrame_ASM3_0_Linux_x86_64.bin
     ```

3. Läs licensavtalet och tryck på RETUR för att fortsätta.
4. Acceptera licensavtalet.
5. Kontrollera bash-profil uppdateras med OFASM variabler. Exempel:

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

6. Öppna konfigurationsfilen OpenFrame tjclrun.conf i vi och redigera den på följande sätt:

     ```
     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/config 
     [oframe7@ofdemo ~]$ vi tjclrun.conf
     ```

     Här är avsnittet [SYSLIB] *innan* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib
     ```

     Här är avsnittet [SYSLIB] *när* ändringen:

     ```
     [SYSLIB] BIN_PATH=${OPENFRAME_HOME}/bin:${OPENFRAME_HOME}/util:${COBDIR}/bin:/usr/local/bin:/bi n:${OPENFRAME_HOME}/volume_default/SYS1.LOADLIB LIB_PATH=${OPENFRAME_HOME}/lib:${OPENFRAME_HOME}/core/lib:${TB_HOME}/client/lib:${CO BDIR}/lib:/usr/lib:/lib:/lib/i686:/usr/local/lib:${PROSORT_HOME}/lib:/opt/FSUNbsort/lib:${OFCOB_HOM E}/lib:${ODBC_HOME}/lib:${OFPLI_HOME}/lib:${OFASM_HOME}/lib
     ```

7. Öppna OpenFrame\_ASM\_InstallLog.log i vi och kontrollera att det inte finns några fel. Exempel:

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

8. Starta om OpenFrame genom att utfärda ett av följande kommandon:

     ```
     tmdown / tmboot
     ```

     – eller –

     ```
     oscdown / oscboot
     ```

## <a name="install-osc"></a>Installera OSC

OSC är OpenFrame miljön liknar IBM CICS som har stöd för höghastighetsnätverk OLTP-transaktioner och andra hanteringsfunktioner.

**Installera OSC**

1. Se till att den grundläggande installationen har slutförts och sedan kontrollera att OpenFrame\_OSC7\_0\_Fix2\_Linux\_x86\_64. bin installer-fil och osc.properties konfigurationsfilen är finns.
2. Redigera följande parametrar i filen osc.properties:
     ```
     OPENFRAME_HOME=/opt/tmaxapp/OpenFrame OSC_SYS_OSC_NCS_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_NCS OSC_APP_OSC_TC_PATH=/opt/tmaxapp/OpenFrame/temp/OSC_TC
     ```

3. Kör installationsprogrammet med hjälp av egenskaper-filen som visas:

     ```
     [oframe7@ofdemo ~]$ chmod a+x OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin [oframe7@ofdemo ~]$ ./OpenFrame_OSC7_0_Fix2_Linux_x86_64.bin -f osc.properties
     ```

     När du är klar visas meddelandet ”Installation Complete”.

4. Kontrollera att bash-profil uppdateras med OSC variabler.
5. Granska OpenFrame\_OSC7\_0\_Fix2\_InstallLog.log fil. Det bör se ut ungefär så här:

     ```
     Summary 
     ------ 
     Installation: Successful.

     233 Successes
     0 Warnings
     0 NonFatalErrors
     0 FatalError
     ```

6. Använd vi för att öppna konfigurationsfilen ofsys.seq. Exempel:

     ```
     vi $OPENFRAME_HOME/config/ofsys.seq
     ```

7. I den \#BASE och \#BATCH-avsnitt, redigera parametrarna som visas.

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

8. Kopiera licensfilen. Exempel:

     ```
     [oframe7@ofdemo ~]$ cp /home/oframe7/oflicense/ofonline/licosc.dat 

     $OPENFRAME_HOME/license

     [oframe7@ofdemo ~]$ cd $OPENFRAME_HOME/license 
     oframe@oframe7/OpenFrame/license / ls -l 
     -rwxr-xr-x. 1 oframe mqm 80 Sep 12 01:37 licosc.dat 
     -rwxr-xr-x. 1 oframe mqm 80 Sep  8 09:40 lictacf.dat 
     -rwxrwxr-x. 1 oframe mqm 80 Sep  3 11:54 lictjes.da
     ```

9. För att starta och stänga av OSC, initiera CICS region delat minne genom att skriva `osctdlinit OSCOIVP1` i Kommandotolken.

10. Kör `oscboot` att starta OSC. Utdata ser ut ungefär så här:

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

11. Kontrollera att processens status är klar genom att använda den `tmadmin` i si. Alla processer ska visa RDY i den **status** kolumn.

    ![Processer som visar RDY](media/tmadmin-02.png)

12. Stäng OSC med hjälp av den `oscdown` kommando.

## <a name="install-jeus"></a>Installera JEUS

JEUS (Java Enterprise användaren lösning) ger presentationslagret för OpenFrame web application server.

Installera paketet Apache Ant, som innehåller de bibliotek och kommandoradsverktyg som krävs för att installera JEUS innan du installerar JEUS.

**Installera Apache Ant**

1. Ladda ned Ant binära med hjälp av den `wget` kommando. Exempel:

     ```
     wget http://apache.mirror.cdnetworks.com/ant/binaries/apacheant-1.9.7-bin.tar.gz
     ```

2. Använd den `tar` verktyg för att extrahera den binära filen och flytta den till en lämplig plats. Exempel:

     ```
     tar -xvzf apache-ant-1.9.7-bin.tar.gz
     ```

3. Skapa en symbolisk länk för effektivitet:

     ```
     ln -s apache-ant-1.9.7 ant
     ```

4. Öppna bash-profil i vi (`vi .bash_profile`) och uppdatera den med följande variabler:

     ```
     # Ant ENV
     export ANT_HOME=$HOME/ant 
     export PATH=$HOME/ant/bin:$PATH
     ```

5.  Tillämpa ändrade miljövariabeln. Exempel:

     ```
     [oframe7\@ofdemo \~]\$ source \~/.bash\_profile
     ```

**Installera JEUS**

1. Expandera installationsprogrammet med hjälp av den `tar` verktyget. Exempel:

     ```
     [oframe7@ofdemo ~]$ tar -zxvf jeus704.tar.gz
     ```

2. Skapa en **jeus** mapp (`mkdir jeus7`) och packa upp den binära filen.
3. Ändra till den **installationsprogrammet** directory (eller Använd parametern JEUS för din egen miljö). Exempel:

     ```
     [oframe7@ofdemo ~]$ cd jeus7/setup/
     ```

4. Köra `ant clean-all` innan du utför versionen. Utdata ser ut ungefär så här:

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

5.  Gör en säkerhetskopia av domän-config-template.properties-filen. Exempel:

     ```
     [oframe7@ofdemo ~]$ cp domain-config-template.properties domain-configtemplate.properties.bkp
     ```

6. Öppna filen domän-config-template.properties i vi:

     ```
     [oframe7\@ofdemo setup]\$ vi domain-config-template.properties
     ```

7. Ändra `jeus.password=jeusadmin nodename=Tmaxsoft` till `jeus.password=tmax1234 nodename=ofdemo`

8. Kör den `ant install` kommando för att skapa JEUS.
9.  Uppdatera .bash\_profilfil med JEUS variabler som visas:

     ```
     # JEUS ENV 
     export JEUS_HOME=/opt/tmaxui/jeus7 PATH="/opt/tmaxui/jeus7/bin:/opt/tmaxui/jeus7/lib/system:/opt/tmaxui/jeus7/webserver/bin:$ {PATH}" 
     export PATH
     ```

10. Kör bash-profil. Exempel:

     ```
     [oframe7@ofdemo setup]$ . .bash_profile
     ```

11. *Valfritt*. Skapa ett alias för enkelt avstängning och start av JEUS komponenter:

     ```     
     # JEUS alias

     alias dsboot='startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin'
     alias msboot='startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin' 
     alias msdown=‘jeusadmin -u administrator -p tmax1234 "stop-server server1“’ 
     alias dsdown=‘jeusadmin -domain jeus_domain -u administrator -p tmax1234 "local-shutdown“’
     ```

12. Verifiera installationen genom att starta administratörsserver domän enligt:

     ```
     [oframe7@ofdemo ~]$ startDomainAdminServer -domain jeus_domain -u administrator -p jeusadmin
     ```

13. Bekräfta genom webbinloggning med syntax:

     ```
     http://<IP>:<port>/webadmin/login
     ```

     Till exempel <http://192.168.92.133:9736/webadmin/login.> inloggningsskärmen visas:
    
     ![JEUS WebAdmin inloggningsskärmen](media/jeus-01.png)

     > [!NOTE]
     > Om du får problem med portsäkerhet öppna port 9736 eller inaktivera brandväggen (`systemctl stop firewall`).

14. Klicka för att ändra värdnamnet för server1 **låsa r & edigera**, klicka sedan på **server1**. I fönstret Server att ändra värdnamnet på följande sätt:

    1.  Ändra **nodnamn** till **ofdemo**.
    2.  Klicka på **OK** på höger sida av fönstret.
    3.  Klicka på **tillämpa ändringarna** på nere till vänster i fönstret och beskrivning, anger *värdnamn ändra*.

    ![JEUS WebAdmin skärmen](media/jeus-02.png)

15. Kontrollera att konfigurationen har genomförts på bekräftelseskärmen.

    ![jeus_domain Server screen](media/jeus-03.png)

16. Starta processen hanterad server ”server1” med följande kommando:

     ```
     [oframe7@ofdemo ~]$ startManagedServer -domain jeus_domain -server server1 -u administrator -p jeusadmin
     ```

## <a name="install-ofgw"></a>Installera OFGW

OFGW är OpenFrame gateway som har stöd för kommunikation mellan 3270 terminalemulator och OSI-base och hanterar sessioner mellan terminalemulator och OSI.

**Installera OFGW**

1. Se till att JEUS har installerats och sedan kontrollera att OFGW7\_0\_1\_Generic.bin installer-fil finns.
2. Kör installationsprogrammet. Exempel:

     ```
     [oframe7@ofdemo ~]$ ./OFGW7_0_1_Generic.bin
     ````

3. Använd följande platser för motsvarande anvisningarna:
     -   Arbetskatalog för JEUS
     -   JEUS domännamn
     -   JEUS servernamn
     -   Tibero Driver
     -   Ofdemo Tmax nod-ID

4. Acceptera resten av standardinställningarna och tryck RETUR för att avsluta installationsprogrammet.

5. Kontrollera att URL: en för OFGW fungerar som förväntat:

     ```
     Type URL 
     http://192.168.92.133:8088/webterminal/ and press enter
      < IP >               :8088/webterminal/
     ```

     Följande skärm visas:

    ![OpenFrame WebTerminal](media/ofgw-01.png)

## <a name="install-ofmanager"></a>Installera OFManager

OFManager tillhandahåller åtgärden och hanteringsfunktioner för OpenFrame i webbmiljö.

**Installera OFManager**

1. Kontrollera att OFManager7\_Generic.bin installer-fil finns.
2. Kör installationsprogrammet. Exempel:

     ```
     OFManager7_Generic.bin
     ```

3.  Tryck på RETUR för att fortsätta och godkänner licensavtalet.
4.  Välj installationsmappen.
5.  Acceptera alla standardinställningar.
6.  Välj Tibero som databasen.
7.  Tryck på RETUR för att avsluta installationsprogrammet.
8.  Kontrollera att URL: en för OFManager fungerar som förväntat:

     ```
     Type URL http://192.168.92.133:8088/ofmanager and press enter <  IP >  : < PORT >  ofmanager Enter ID:   ROOT 
     Password: SYS1
     ```

Start-skärmen visas:

![Tmax OpenFrame Manager inloggningsskärmen](media/ofmanager-01.png)

Det Slutför installationen av OpenFrame-komponenter.

## <a name="next-steps"></a>Nästa steg

Om du funderar på att en stordatormigrering, är vårt växande partnerekosystem som hjälper dig. För detaljerad information om hur du väljer en partnerlösning som avser den [plattform modernisering Alliance](https://www.platformmodernization.org/pages/mainframe.aspx).

-   [Kom igång med Azure](https://docs.microsoft.com/azure/)
-   [Dokumentation för värd-Integration (HIS)](https://docs.microsoft.com/host-integration-server/)
-   [Azure Virtual Datacenter Lift and Shift-Guide](https://blogs.msdn.microsoft.com/azurecat/2018/03/12/new-whitepaper-azure-virtual-datacenter-lift-and-shift-guide/)
