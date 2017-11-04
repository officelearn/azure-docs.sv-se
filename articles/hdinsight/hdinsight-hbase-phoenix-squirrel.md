---
title: "Använda Apache Phoenix och SQuirreL med Windows-baserade Azure HDInsight | Microsoft Docs"
description: "Lär dig hur du använder Apache Phoenix i HDInsight och hur du installerar och konfigurerar SQuirreL på din arbetsstation för att ansluta till ett HBase-kluster i HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: 1a756e98-75c1-44cd-a178-c5119683b7b7
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/25/2017
ms.author: jgao
ROBOTS: NOINDEX
ms.openlocfilehash: 64a4c5b158ebe0119f2f0133587a743fd2dbf0ff
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="use-apache-phoenix-and-squirrel-with-windows-based-hbase-clusters-in-hdinsight"></a>Använda Apache Phoenix och SQuirreL med Windows-baserade HBase-kluster i HDInsight
Lär dig hur du använder [Apache Phoenix](http://phoenix.apache.org/) i HDInsight och hur du installerar och konfigurerar SQuirreL på din arbetsstation för att ansluta till ett HBase-kluster i HDInsight. Läs mer om Phoenix [Phoenix i 15 minuter eller mindre](http://phoenix.apache.org/Phoenix-in-15-minutes-or-less.html). Phoenix-grammatik finns [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

> [!NOTE]
> Phoenix versionsinformation i HDInsight, se [vad är nytt i Hadoop-klusterversioner som tillhandahålls av HDInsight?](hdinsight-component-versioning.md).
>

> [!IMPORTANT]
> Stegen i det här dokumentet fungerar endast för Windows-baserade HDInsight-kluster. HDInsight är endast tillgängligt i Windows för versioner som är lägre än HDInsight 3.4. Linux är det enda operativsystemet som används med HDInsight version 3.4 och senare. Mer information finns i [HDInsight-avveckling på Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement). Information om hur du använder Phoenix på Linux-baserat HDInsight finns i [använda Apache Phoenix med Linux-baserade HBase-kluster i HDInsight](hbase/apache-hbase-phoenix-squirrel-linux.md).
>



## <a name="use-sqlline"></a>Använd SQLLine
[SQLLine](http://sqlline.sourceforge.net/) är ett kommandoradsverktyg för att köra SQL.

### <a name="prerequisites"></a>Krav
Innan du kan använda SQLLine, måste du ha följande:

* **En HBase-kluster i HDInsight**. Mer information om etablera HBase-kluster, se [Kom igång med Apache HBase i HDInsight][hdinsight-hbase-get-started].
* **Ansluta till HBase-kluster via remote desktop protocol**. Instruktioner finns i [hantera Hadoop-kluster i HDInsight med hjälp av den klassiska Azure-portalen][hdinsight-manage-portal].

**Ta reda på värdnamn**

1. Öppna **Hadoop kommandoraden** från skrivbordet.
2. Kör följande kommando för att hämta DNS-suffix:

        ipconfig

    Skriv ned **anslutningsspecifika DNS-suffixet**. Till exempel *myhbasecluster.f5.internal.cloudapp.net*. När du ansluter till ett HBase-kluster behöver du ansluta till en Zookeepers med FQDN. Varje HDInsight-kluster har 3 Zookeepers. De är *zookeeper0*, *zookeeper1*, och *zookeeper2*. FQDN är något som liknar *zookeeper2.myhbasecluster.f5.internal.cloudapp.net*.

**Att använda SQLLine**

1. Öppna **Hadoop kommandoraden** från skrivbordet.
2. Kör följande kommandon för att öppna SQLLine:

        cd %phoenix_home%\bin
        sqlline.py [The FQDN of one of the Zookeepers]

    ![HDInsight hbase phoenix sqlline][hdinsight-hbase-phoenix-sqlline]

    De kommandon som används i exemplet:

        CREATE TABLE Company (COMPANY_ID INTEGER PRIMARY KEY, NAME VARCHAR(225));

        !tables

        UPSERT INTO Company VALUES(1, 'Microsoft');

        SELECT * FROM Company;

Mer information finns i [SQLLine manuell](http://sqlline.sourceforge.net/#manual) och [Phoenix grammatik](http://phoenix.apache.org/language/index.html).

## <a name="use-squirrel"></a>Använd SQuirreL
[SQuirreL SQL Client](http://squirrel-sql.sourceforge.net/) är ett grafiskt Java-program som gör att du kan visa strukturen för en JDBC-kompatibel databas, bläddra data i tabeller, utfärda SQL-kommandon osv. Det kan användas för att ansluta till Apache Phoenix på HDInsight.

Det här avsnittet visar hur du installerar och konfigurerar SQuirreL på din arbetsstation för att ansluta till ett HBase-kluster i HDInsight via VPN.

### <a name="prerequisites"></a>Krav
Innan du följer procedurerna, måste du ha följande:

* Ett HBase-kluster som distribueras till en Azure-nätverk med en virtuell dator i DNS.  Instruktioner finns i [skapa HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet].

* Hämta HBase-kluster klustret anslutningsspecifika DNS-suffix. Att hämta det RDP i klustret, och sedan köra IPConfig.  DNS-suffix är ungefär:

        myhbase.b7.internal.cloudapp.net
* Hämta och installera [Microsoft Visual Studio Express för Windows-skrivbordet](https://www.visualstudio.com/products/visual-studio-express-vs.aspx) på arbetsstationen. Du behöver makecert från paketet för att skapa certifikatet.  
* Hämta och installera [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html) på arbetsstationen.  SQuirreL SQL klientversion 3.0 och senare kräver JRE version 1.6 eller senare.  

### <a name="configure-a-point-to-site-vpn-connection-to-the-azure-virtual-network"></a>Konfigurera en punkt-till-plats VPN-anslutning till Azure-nätverket
Ingår 3 steg hur du konfigurerar en punkt-till-plats VPN-anslutning:

1. [Konfigurera ett virtuellt nätverk och en dynamisk routning gateway](#Configure-a-virtual-network-and-a-dynamic-routing-gateway)
2. [Skapa certifikat](#Create-your-certificates)
3. [Konfigurera VPN-klienten](#Configure-your-VPN-client)

Se [konfigurerar en punkt-till-plats VPN-anslutning till ett Azure Virtual Network](../vpn-gateway/vpn-gateway-point-to-site-create.md) för mer information.

#### <a name="configure-a-virtual-network-and-a-dynamic-routing-gateway"></a>Konfigurera ett virtuellt nätverk och en dynamisk routning gateway
Garantera att du har etablerat ett HBase-kluster i Azure-nätverk (se krav för det här avsnittet). Nästa steg är att konfigurera en punkt-till-plats-anslutning.

**Så här konfigurerar du punkt-till-plats-anslutning**

1. Logga in på den [klassiska Azure-portalen][azure-portal].
2. Klicka på vänster, **nätverk**.
3. Klicka på det virtuella nätverket som du har skapat (se [etablera HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet]).
4. Klicka på **konfigurera** från början.
5. I den **punkt-till-plats-anslutning** väljer **konfigurera punkt-till-plats-anslutning**.
6. Konfigurera **första IP-** och **CIDR** att ange den IP-adressintervall från vilka dina VPN-klienter tar emot en IP-adress vid anslutning. Intervallet får inte överlappa med ett intervall som finns på ditt lokala nätverk och virtuella Azure-nätverket måste du ansluta till. Till exempel. Om du har valt 10.0.0.0/20 för det virtuella nätverket kan du välja 10.1.0.0/24 för klient-adressutrymmet. Finns det [punkt-till-plats-anslutning] [ vnet-point-to-site-connectivity] för mer information.
7. I området virtuellt nätverk blanksteg klickar du på **Lägg till gateway-undernätet**.
8. Klicka på **spara** längst ned på sidan.
9. Klicka på **Ja** att bekräfta ändringen. Vänta tills datorn är klar med ändringen innan du går vidare till nästa procedur.

**Att skapa en dynamisk routning gateway**

1. I den klassiska Azure-portalen klickar du på **INSTRUMENTPANELEN** från sidans överkant.
2. Klicka på **skapa GATEWAY** längst ned på sidan.
3. Klicka på **Ja** att bekräfta. Vänta tills gatewayen har skapats.
4. Klicka på **INSTRUMENTPANELEN** från början.  Ett visual diagram i det virtuella nätverket visas:

    ![Virtuella Azure-nätverket punkt-till-plats-diagram][img-vnet-diagram]

    Diagrammet visar 0-klientanslutningar. När du upprättar en anslutning till det virtuella nätverket uppdateras numret till en.

#### <a name="create-your-certificates"></a>Skapa certifikat
Ett sätt att skapa ett X.509-certifikat är med hjälp av certifikat skapas (makecert.exe) som medföljer [Microsoft Visual Studio Express för Windows-skrivbordet](https://www.visualstudio.com/products/visual-studio-express-vs.aspx).

**Skapa ett självsignerat rotcertifikat**

1. Öppna Kommandotolken från din arbetsstation.
2. Navigera till verktygsmappen för Visual Studio.
3. Följande kommando i exemplet nedan skapar och installera ett rotcertifikat i det personliga certifikatarkivet på din arbetsstation och även skapa en .cer-fil som du kommer senare överföring till den klassiska Azure-portalen.

        makecert -sky exchange -r -n "CN=HBaseVnetVPNRootCertificate" -pe -a sha1 -len 2048 -ss My "C:\Users\JohnDole\Desktop\HBaseVNetVPNRootCertificate.cer"

    Ändra till den katalog som du vill .cer-filen ska finnas i, där HBaseVnetVPNRootCertificate är det namn som du vill använda för certifikatet.

    Stäng inte Kommandotolken.  Du behöver det i nästa procedur.

   > [!NOTE]
   > Eftersom du har skapat ett rotcertifikat som genererar klientcertifikat, kan du vilja exportera certifikatet tillsammans med sin privata nyckel och spara det på en säker plats där det kan återställas.
   >
   >

**Så här skapar du ett klientcertifikat**

* Från kommandoraden i samma (det måste finnas på samma dator där du skapade rotcertifikatet. Klientcertifikatet måste skapas från rotcertifikatet), kör du följande kommando:

          makecert.exe -n "CN=HBaseVnetVPNClientCertificate" -pe -sky exchange -m 96 -ss My -in "HBaseVnetVPNRootCertificate" -is my -a sha1

    HBaseVnetVPNRootCertificate är namnet på rot.  Den måste matcha namnet på rot.  

    Både rotcertifikatet och klientcertifikatet lagras i ditt personliga certifikatarkiv på datorn. Använda certmgr.msc för att verifiera.

    ![Punkt-till-plats VPN-certifikat för virtuella Azure-nätverket][img-certificate]

    Ett klientcertifikat måste installeras på varje dator som du vill ansluta till det virtuella nätverket. Vi rekommenderar att du skapar unika klientcertifikat för varje dator som du vill ansluta till det virtuella nätverket. Om du vill exportera klientcertifikat, Använd certmgr.msc.

**Att överföra rotcertifikatet till den klassiska Azure-portalen**

1. I den klassiska Azure-portalen klickar du på **nätverk** till vänster.
2. Klicka på det virtuella nätverket där HBase-kluster har distribuerats till.
3. Klicka på **certifikat** från början.
4. Klicka på **överför** längst ned, och ange på rotcertifikatfilen som du har skapat i proceduren före senaste. Vänta tills certifikatet har importerats.
5. Klicka på **INSTRUMENTPANELEN** högst upp.  Virtuella diagram visar status.

#### <a name="configure-your-vpn-client"></a>Konfigurera VPN-klienten
**Hämta och installera VPN-klientpaketet**

1. Klicka på antingen från sidan INSTRUMENTPANELEN i det virtuella nätverket i avsnittet snabböversikten **hämta 64-bitars VPN-klientpaketet** eller **hämta 32-bitars VPN-klientpaketet** baserat på din arbetsstation OS version.
2. Klicka på **kör** att installera paketet.
3. Säkerhetsmeddelande, klickar du på **mer info**, och klicka sedan på **kör ändå**.
4. Klicka på **Ja** två gånger.

**Att ansluta till VPN**

1. Klicka på ikonen nätverk i Aktivitetsfältet på skrivbordet på din arbetsstation. En VPN-anslutning visas med namn på din virtuella nätverk.
2. Klicka på namnet på VPN-anslutningen.
3. Klicka på **Anslut**.

**Att testa namnmatchning för VPN-anslutning och domän**

* Öppna en kommandotolk från den här arbetsstationen och ping något av följande namn anges HBase-kluster DNS-suffix är myhbase.b7.internal.cloudapp.net:

        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        zookeeper0.myhbase.b7.internal.cloudapp.net
        headnode0.myhbase.b7.internal.cloudapp.net
        headnode1.myhbase.b7.internal.cloudapp.net
        workernode0.myhbase.b7.internal.cloudapp.net

### <a name="install-and-configure-squirrel-on-your-workstation"></a>Installera och konfigurera SQuirreL på din arbetsstation
**Så här installerar du SQuirreL**

1. Hämta den SQuirreL SQL jar-filen från [http://squirrel-sql.sourceforge.net/#installation](http://squirrel-sql.sourceforge.net/#installation).
2. Öppna/köra jar-filen. Det krävs av [Java Runtime Environment](http://www.oracle.com/technetwork/java/javase/downloads/jre7-downloads-1880261.html).
3. Klicka på **nästa** två gånger.
4. Ange en sökväg där du har skrivbehörighet och klicka sedan på **nästa**.

  > [!NOTE]
  > Standardinstallationsmappen finns i mappen C:\Program Files\squirrel-sql-3,6.  Installationsprogrammet måste ha beviljats behörigheten administratör för att kunna skriva till den här sökvägen. Du kan öppna en kommandotolk som administratör, gå till Java's bin-mappen och kör sedan:
  >
  >     Java.exe-jar [sökvägen SQuirreL jar-fil]
5. Klicka på **OK** att bekräfta att skapa målkatalogen.
6. Standardinställningen är att installera bas- och Standard paket.  Klicka på **Nästa**.
7. Klicka på **nästa** två gånger, och klicka sedan på **klar**.

**Phoenix drivrutin**

Phoenix drivrutinen jar-filen finns på HBase-kluster. Sökvägen är liknar följande baserat på versionerna:

    C:\apps\dist\phoenix-4.0.0.2.1.11.0-2316\phoenix-4.0.0.2.1.11.0-2316-client.jar
Du måste kopiera den till din arbetsstation under [SQuirreL installationsmappen] / lib sökväg.  Det enklaste sättet är att RDP till klustret och sedan använda filen kopiera och klistra in (CTRL + C och CTRL + V) för att kopiera den till din arbetsstation.

**Att lägga till en drivrutin för Phoenix SQuirreL**

1. Öppna SQuirreL SQL-klienten från din arbetsstation.
2. Klicka på den **drivrutinen** fliken till vänster.
3. Från den **drivrutiner** -menyn klickar du på **ny drivrutin**.
4. Ange följande information:

   * **Namnet**: Phoenix
   * **Exempel-URL**: jdbc:phoenix:zookeeper2.contoso-hbase-eu.f5.internal.cloudapp.net
   * **Klassnamn**: org.apache.phoenix.jdbc.PhoenixDriver

     > [!WARNING]
     > Användaren alla gemen i exempel-URL. Du kan använda de fullständiga zookeeper kvorum om en av dem är nere.  Värdnamnen är zookeeper0, zookeeper1 och zookeeper2.
     >
     >

     ![HDInsight HBase Phoenix SQuirreL drivrutin][img-squirrel-driver]
5. Klicka på **OK**.

**Skapa ett alias till HBase-kluster**

1. SQuirreL, klicka på den **alias** fliken till vänster.
2. Från den **alias** -menyn klickar du på **nytt Alias**.
3. Ange följande information:

   * **Namnet**: namnet på HBase-kluster eller ett namn som du föredrar.
   * **Drivrutinen**: Phoenix.  Detta måste matcha namnet på drivrutinen som du skapade i föregående procedur.
   * **URL: en**: URL: en kopieras från konfigurationen av drivrutinen. Kontrollera att användaren alla gemen.
   * **Användarnamnet**: Det kan vara text.  Eftersom du använder VPN-anslutningar här används inte användarnamnet alls.
   * **Lösenordet**: Det kan vara text.

     ![HDInsight HBase Phoenix SQuirreL drivrutin][img-squirrel-alias]
4. Klicka på **Test**.
5. Klicka på **Anslut**. När den gör anslutningen SQuirreL ser ut som:

    ![HBase Phoenix SQuirreL][img-squirrel]

**Ett test**

1. Klicka på den **SQL** fliken höger bredvid den **objekt** fliken.
2. Kopiera och klistra in följande kod:

        CREATE TABLE IF NOT EXISTS us_population (state CHAR(2) NOT NULL, city VARCHAR NOT NULL, population BIGINT  CONSTRAINT my_pk PRIMARY KEY (state, city))
3. Klicka på knappen Kör.

    ![HBase Phoenix SQuirreL][img-squirrel-sql]
4. Växla tillbaka till den **objekt** fliken.
5. Expandera aliasnamnet och expandera sedan **tabellen**.  Du bör se den nya tabellen visas under.

## <a name="next-steps"></a>Nästa steg
I den här artikeln har du lärt dig hur du använder Apache Phoenix i HDInsight.  Läs mer i

* [HDInsight HBase-översikt][hdinsight-hbase-overview]: HBase är en NoSQL-databas av Apachetyp med öppen källkod som bygger på Hadoop och som ger direktåtkomst och stark konsekvens för stora mängder ostrukturerade och semistrukturerade data.
* [Etablera HBase-kluster i Azure Virtual Network][hdinsight-hbase-provision-vnet]: med virtuell nätverksintegration HBase-kluster kan bara distribueras till samma virtuella nätverk som dina program så att program kan kommunicera med HBase direkt.
* [Konfigurera HBase-replikering i HDInsight](hbase/apache-hbase-replication.md): Lär dig att konfigurera HBase-replikering mellan två Azure-datacenter.


[azure-portal]: https://portal.azure.com
[vnet-point-to-site-connectivity]: https://msdn.microsoft.com/library/azure/09926218-92ab-4f43-aa99-83ab4d355555#BKMK_VNETPT

[hdinsight-versions]: hdinsight-component-versioning.md
[hdinsight-hbase-get-started]: hdinsight-hbase-tutorial-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md#connect-to-clusters-using-rdp
[hdinsight-hbase-provision-vnet]:hbase/apache-hbase-provision-vnet.md
[hdinsight-hbase-overview]:hbase/apache-hbase-overview.md

[hdinsight-hbase-phoenix-sqlline]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-phoenix-sqlline.png
[img-certificate]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vpn-certificate.png
[img-vnet-diagram]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-vnet-point-to-site.png
[img-squirrel-driver]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-driver.png
[img-squirrel-alias]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-alias.png
[img-squirrel]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel.png
[img-squirrel-sql]: ./media/hdinsight-hbase-phoenix-squirrel/hdinsight-hbase-squirrel-sql.png
