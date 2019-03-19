---
title: Tillgänglighet för SQL Server grupper – Azure Virtual Machines – självstudier | Microsoft Docs
description: Den här självstudien visar hur du skapar en SQL Server alltid tillgänglighetsgrupp på Azure Virtual Machines.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.openlocfilehash: d86538fca907f7181bf58ff236bba8de186641fb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003447"
---
# <a name="tutorial-configure-always-on-availability-group-in-azure-vm-manually"></a>Självstudier: Konfigurera Always On Availability Group i virtuella Azure-datorer manuellt

Den här självstudien visar hur du skapar en SQL Server alltid tillgänglighetsgrupp på Azure Virtual Machines. Fullständig genomgång skapar en tillgänglighetsgrupp med en databasreplik på två SQL-servrar.

**Uppskattad tidsåtgång**: Tar cirka 30 minuter för att slutföra när alla krav är uppfyllda.

Diagrammet visar vad du skapar under kursen.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Förutsättningar

Självstudien förutsätter att du har grundläggande kunskaper om SQL Server Always On-Tillgänglighetsgrupper. Om du behöver mer information finns i [översikt över alltid på Tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

I följande tabell visas de krav som du måste utföra innan du påbörjar den här självstudiekursen:

|  |Krav |Beskrivning |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Två SQL-servrar | – I en Azure-tillgänglighetsuppsättning <br/> – I en enda domän <br/> -Med funktionen för redundanskluster installerat |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Filresurs för klustervittne |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server-tjänstkontot | Domänkonto |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Tjänstkontot för SQL Server Agent | Domänkonto |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Öppna portar i brandväggen | -SQL-Server: **1433** för standardinstans <br/> -Slutpunkten för databasspegling: **5022** eller alla tillgängliga portar <br/> -Tillgänglighetsgruppen läsa in hälsoavsökning för belastningsutjämnaren IP-adress: **59999** eller alla tillgängliga portar <br/> -Klustrets core IP-adress hälsoavsökning för belastningsutjämnaren: **58888** eller alla tillgängliga portar |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Lägg till funktionen för Redundansklustring | Både SQL-servrar kräver den här funktionen |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Domänkonto för installation | – Lokal administratör på varje SQL Server <br/> -Medlem i SQL Server fasta serverrollen sysadmin för varje instans av SQL Server  |


Innan du påbörjar självstudiekursen måste du [Slutför stegen för att skapa ständigt aktiverade Tillgänglighetsgrupper i Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Om dessa krav har redan slutförts, kan du hoppa till [Skapa kluster](#CreateCluster).

  >[!NOTE]
  > Många av stegen i den här självstudien kan nu automatiseras med [Azure SQL-dator med CLI](virtual-machines-windows-sql-availability-group-cli.md) och [Azure-Snabbstartsmallar](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Skapa klustret

När kraven har slutförts, är det första steget att skapa ett redundanskluster för Windows-Server som innehåller två SQL-servrar och en witness-server.

1. RDP till den första SQL-Server med ett domänkonto som har administratörsbehörighet på både SQL-servrar och vittnesservern.

   >[!TIP]
   >Om du har följt den [förutsättningsdokumentet](virtual-machines-windows-portal-sql-availability-group-prereq.md), du har skapat ett konto med namnet **CORP\Install**. Använd det här kontot.

2. I den **Serverhanteraren** instrumentpanelen, väljer **verktyg**, och klicka sedan på **Klusterhanteraren**.
3. I det vänstra fönstret högerklickar du på **Klusterhanteraren**, och klicka sedan på **skapa ett kluster**.
   ![Skapa kluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Skapa ett kluster med en nod i guiden Skapa kluster genom att stega dig igenom sidorna med inställningarna i tabellen nedan:

   | Sidan | Inställningar |
   | --- | --- |
   | Innan du börjar |Använd standard |
   | Välj servrar |Skriv namnet på första SQL Server i **RETUR servernamn** och klicka på **Lägg till**. |
   | Verifieringsvarning |Välj **Nej. jag inte behöver support från Microsoft för detta kluster och därför inte vill köra verifieringstesterna. När jag klickar på Nästa kommer fortsätta att skapa klustret**. |
   | Åtkomstpunkt för administration av klustret |Skriv ett namn för klustret, till exempel **SQLAGCluster1** i **klusternamnet**.|
   | Bekräftelse |Använd standardinställningarna om du inte använder lagringsutrymmen. Se fotnoten efter denna tabell. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ställ in Windows server failover-kluster IP-adress

1. I **Klusterhanteraren**, rulla ned till **Klusterkärnresurser** och expandera klusterinformation. Du bör se både den **namn** och **IP-adress** resurser i den **misslyckades** tillstånd. IP-adressresurs kunde inte försättas online eftersom klustret tilldelas samma IP-adress som datorn, därför är det en dubblett-adress.

2. Högerklicka på den **IP-adress** resursen och klicka sedan på **egenskaper**.

   ![Egenskaper för klustret](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Välj **statisk IP-adress** och ange en tillgänglig adress från samma undernät som virtuella datorer.

4. I den **Klusterkärnresurser** avsnittet, högerklickar du på klusternamnet och klickar på **Anslut**. Vänta tills båda resurserna är online. När namnet klusterresursen är online uppdaterar DC-servern med ett nytt AD-datorkonto. Använd den här AD-konto för att köra tjänsten Availability Group klustrade senare.

### <a name="addNode"></a>Lägg till SQL Server i klustret

Lägg till SQL Server i klustret.

1. I webbläsarträdet, högerklicka på klustret och klicka på **Lägg till nod**.

    ![Lägg till nod i klustret](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. I den **guiden Lägg till nod**, klickar du på **nästa**. I den **Välj servrar** lägger du till den andra SQL-servern. Skriv servernamnet i **RETUR servernamn** och klicka sedan på **Lägg till**. När du är klar klickar du på **nästa**.

1. I den **Validation Warning** klickar du på **nr** (i ett produktionsscenario bör du utföra verifieringstesterna). Klicka sedan på **Nästa**.

8. I den **bekräftelse** sidan om du använder lagringsutrymmen, avmarkera kryssrutan **lägga till alla tillgängliga lagringsenheter i klustret.**

   ![Lägg till nod-bekräftelse](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Om du använder lagringsutrymmen och inte avmarkera **lägga till alla tillgängliga lagringsenheter i klustret**, Windows kopplar från de virtuella diskarna under klustringsprocessen. Därför kan de visas inte i Diskhanteraren eller Explorer förrän lagringsutrymmen tas bort från klustret och återansluta med hjälp av PowerShell. Lagringsutrymmen grupperar flera diskar i lagringspooler. Mer information finns i [lagringsutrymmen](https://technet.microsoft.com/library/hh831739).

1. Klicka på **Nästa**.

1. Klicka på **Slutför**.

   Hanteraren för redundanskluster visar att klustret har en ny nod och visar den i den **noder** behållare.

10. Logga ut från fjärrskrivbordssessionen.

### <a name="add-a-cluster-quorum-file-share"></a>Lägg till en filresurs för klustret kvorum

I det här exemplet använder en filresurs i Windows-kluster för att skapa en klustrets kvorum. Den här självstudien används en nod och filresursmajoritet kvorum. Mer information finns i [Om kvorumkonfigurationer i ett redundanskluster](https://technet.microsoft.com/library/cc731739.aspx).

1. Anslut till resursen vittne medlem filservern med en fjärrskrivbordssession.

1. På **Serverhanteraren**, klickar du på **verktyg**. Öppna **Datorhantering**.

1. Klicka på **delade mappar**.

1. Högerklicka på **resurser**, och klicka på **ny resurs...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **guiden Skapa en delad mapp** att skapa en resurs.

1. På **mappsökväg**, klickar du på **Bläddra** och hitta eller skapa en sökväg för den delade mappen. Klicka på **Nästa**.

1. I **namn, beskrivning och inställningar** Kontrollera resursens namn och sökväg. Klicka på **Nästa**.

1. På **delade mappbehörigheter** ange **anpassa behörigheter**. Klicka på **anpassad...** .

1. På **anpassa behörigheter**, klickar du på **Lägg till...** .

1. Se till att det konto som används för att skapa klustret har fullständig behörighet.

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klicka på **OK**.

1. I **delade mappbehörigheter**, klickar du på **Slutför**. Klicka på **Slutför** igen.  

1. Logga ut från servern

### <a name="configure-cluster-quorum"></a>Konfigurera klustrets kvorumdisk

Nu ska vi Konfigurera klustrets kvorum.

1. Ansluta till den första noden i klustret via ett fjärrskrivbord.

1. I **Klusterhanteraren**, högerklickar du på klustret, peka på **fler åtgärder**, och klicka på **konfigurera inställningarna för klusterkvorum...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. I **guiden Konfigurera klusterkvorum**, klickar du på **nästa**.

1. I **Välj alternativ för kvorumkonfiguration**, Välj **Välj kvorumvittnet**, och klicka på **nästa**.

1. På **Välj Kvorumvittne**, klickar du på **konfigurera ett filresursvittne**.

   >[!TIP]
   >Windows Server 2016 har stöd för ett molnvittne. Om du väljer den här typen av vittne, behöver du inte en fil dela vittne. Mer information finns i [distribuera ett molnvittne för ett redundanskluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Den här självstudien används ett filresursvittne som stöds av tidigare operativsystem.

1. På **konfigurera filresursvittne**, ange sökvägen för den resurs som du skapade. Klicka på **Nästa**.

1. Granska inställningarna på **bekräftelse**. Klicka på **Nästa**.

1. Klicka på **Slutför**.

Klustrets kärnresurser är konfigurerade med ett filresursvittne.

## <a name="enable-availability-groups"></a>Aktivera Tillgänglighetsgrupper

Aktivera sedan den **AlwaysOn Availability Groups** funktionen. Utför dessa steg på både SQL-servrar.

1. Från den **starta** skärmen, starta **SQL Server Configuration Manager**.
2. I webbläsarträdet klickar du på **SQL Server Services**, högerklicka på den **SQL Server (MSSQLSERVER)** tjänsten och klicka på **egenskaper**.
3. Klicka på den **AlwaysOn hög tillgänglighet** och sedan välja **aktivera AlwaysOn Availability Groups**, enligt följande:

    ![Aktivera AlwaysOn-Tillgänglighetsgrupper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klicka på **Verkställ**. Klicka på **OK** i en dialogruta.

5. Starta om SQL Server-tjänsten.

Upprepa dessa steg på SQL Server.

<!-----------------
## <a name="endpoint-firewall"></a>Open firewall for the database mirroring endpoint

Each instance of SQL Server that participates in an Availability Group requires a database mirroring endpoint. This endpoint is a TCP port for the instance of SQL Server that is used to synchronize the database replicas in the Availability Groups on that instance.

On both SQL Servers, open the firewall for the TCP port for the database mirroring endpoint.

1. On the first SQL Server **Start** screen, launch **Windows Firewall with Advanced Security**.
2. In the left pane, select **Inbound Rules**. On the right pane, click **New Rule**.
3. For **Rule Type**, choose **Port**.
1. For the port, specify TCP and choose an unused TCP port number. For example, type *5022* and click **Next**.

   >[!NOTE]
   >For this example, we're using TCP port 5022. You can use any available port.

5. In the **Action** page, keep **Allow the connection** selected and click **Next**.
6. In the **Profile** page, accept the default settings and click **Next**.
7. In the **Name** page, specify a rule name, such as **Default Instance Mirroring Endpoint** in the **Name** text box, then click **Finish**.

Repeat these steps on the second SQL Server.
-------------------------->

## <a name="create-a-database-on-the-first-sql-server"></a>Skapa en databas på den första SQL-servern

1. Starta RDP-filen till den första SQL-servern med ett domänkonto som är medlem i den fasta serverrollen sysadmin.
1. Öppna SQL Server Management Studio och Anslut till den första SQL-servern.
7. I **Object Explorer**, högerklicka på **databaser** och klicka på **ny databas**.
8. I **databasnamn**, typ **MyDB1**, klicka sedan på **OK**.

### <a name="backupshare"></a> Skapa en säkerhetskopiering

1. På den första SQL-servern i **Serverhanteraren**, klickar du på **verktyg**. Öppna **Datorhantering**.

1. Klicka på **delade mappar**.

1. Högerklicka på **resurser**, och klicka på **ny resurs...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **guiden Skapa en delad mapp** att skapa en resurs.

1. På **mappsökväg**, klickar du på **Bläddra** och hitta eller skapa en sökväg för delad mapp för säkerhetskopiering. Klicka på **Nästa**.

1. I **namn, beskrivning och inställningar** Kontrollera resursens namn och sökväg. Klicka på **Nästa**.

1. På **delade mappbehörigheter** ange **anpassa behörigheter**. Klicka på **anpassad...** .

1. På **anpassa behörigheter**, klickar du på **Lägg till...** .

1. Se till att SQL Server och SQL Server Agent-tjänstkonton för båda servrarna har fullständig behörighet.

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klicka på **OK**.

1. I **delade mappbehörigheter**, klickar du på **Slutför**. Klicka på **Slutför** igen.  

### <a name="take-a-full-backup-of-the-database"></a>Ta en fullständig säkerhetskopia av databasen

Du måste säkerhetskopiera den nya databasen att initiera loggkedjan. Om du inte vidtar en säkerhetskopia av den nya databasen kan inte tas med i en tillgänglighetsgrupp.

1. I **Object Explorer**, högerklicka på databasen, peka på **uppgifter...** , klickar du på **säkerhetskopiera**.

1. Klicka på **OK** att ta en fullständig säkerhetskopiering till standardplatsen för säkerhetskopior.

## <a name="create-the-availability-group"></a>Skapa Tillgänglighetsgruppen
Du är nu redo att konfigurera en tillgänglighetsgrupp med följande steg:

* Skapa en databas på den första SQL-servern.
* Ta både en fullständig säkerhetskopia och en säkerhetskopia av databasen log
* Återställ fullständiga säkerhetskopior och loggsäkerhetskopior till andra SQL Server med den **NORECOVERY** alternativet
* Skapa Availability-gruppen (**AG1**) med synkront genomförande, automatisk växling vid fel och läsbara sekundära repliker

### <a name="create-the-availability-group"></a>Skapa Tillgänglighetsgruppen:

1. På fjärrskrivbordssessionen för den första SQL-servern. I **Object Explorer** i SSMS, högerklickar du på **AlwaysOn hög tillgänglighet** och klicka på **guiden Ny tillgänglighetsgrupp**.

    ![Starta guiden Ny tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. I den **introduktion** klickar du på **nästa**. I den **ange tillgänglighetsgruppens namn** anger du ett namn för Tillgänglighetsgruppen, till exempel **AG1**i **tillgänglighetsgruppens namn**. Klicka på **Nästa**.

    ![Guiden för ny AG, ange AG-namn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. I den **Välj databaser** väljer du databasen och klicka på **nästa**.

   >[!NOTE]
   >Databasen uppfyller kraven för en tillgänglighetsgrupp eftersom du har utfört minst en fullständig säkerhetskopiering på den avsedda primära repliken.

   ![Guiden för ny AG, Välj databaser](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. I den **ange repliker** klickar du på **lägga till repliken**.

   ![Guiden för ny AG, ange repliker](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Den **Anslut till Server** dialogrutan som öppnas. Skriv namnet på den andra servern i **servernamn**. Klicka på **Anslut**.

   I den **ange repliker** du bör nu se den andra servern som anges i **Tillgänglighetsrepliker**. Konfigurera replikerna på följande sätt.

   ![Guiden för ny AG, ange repliker (slutförd)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klicka på **slutpunkter** att se slutpunkten för databasspegling för den här Tillgänglighetsgruppen. Använd samma port som du använde när du ställer in den [brandväggsregel för slutpunkter för databasspegling](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Guiden för ny AG, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. I den **Välj inledande datasynkronisering** väljer **fullständig** och ange en delad nätverksplats. För plats, använder den [säkerhetskopieringsresursen du skapade](#backupshare). I det här exemplet var,  **\\ \\ \<första SQLServer\>\Backup\\**. Klicka på **Nästa**.

   >[!NOTE]
   >Fullständig synkronisering tar en fullständig säkerhetskopiering av databasen på den första instansen av SQL Server och återställer det till den andra instansen. Fullständig synkronisering rekommenderas inte för stora databaser, eftersom det kan ta lång tid. Du kan minska nu genom att manuellt gör en säkerhetskopia av databasen och återställer dem med `NO RECOVERY`. Om databasen har redan återställts med `NO RECOVERY` på andra SQL Server innan du konfigurerar Availability-gruppen, Välj **Anslut bara**. Om du vill säkerhetskopiera när du har konfigurerat Availability-gruppen, Välj **hoppa över inledande datasynkronisering**.

    ![Guiden för ny AG, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. I den **verifiering** klickar du på **nästa**. Den här sidan bör likna följande bild:

    ![Guiden Ny AG, verifiering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Det finns en varning för Lyssnarkonfigurationen eftersom du inte har konfigurerat en lyssningsfunktion. Du kan ignorera den här varningen eftersom på Azure virtual machines lyssnaren skapa när du har skapat Azure load balancer.

10. I den **sammanfattning** klickar du på **Slutför**, vänta medan guiden konfigurerar nya Tillgänglighetsgruppen. I den **förlopp** sidan som du kan klicka på **mer** att visa detaljerad förloppet. När guiden har slutförts kan du granska den **resultat** sidan för att kontrollera att Tillgänglighetsgruppen har skapats.

     ![Guiden för ny AG resultat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klicka på **Stäng** vill avsluta guiden.

### <a name="check-the-availability-group"></a>Kontrollera Tillgänglighetsgruppen

1. I **Object Explorer**, expandera **AlwaysOn hög tillgänglighet**, expandera sedan **Tillgänglighetsgrupper**. Du bör nu se nya Availability-gruppen i den här behållaren. Högerklicka på Tillgänglighetsgruppen och klicka på **visa instrumentpanelen**.

   ![Visa AG-instrumentpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Din **AlwaysOn instrumentpanelen** bör se ut ungefär så här.

   ![AG-instrumentpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Du kan se replikerna, redundansläge för varje replik och tillstånd för synkronisering.

2. I **Klusterhanteraren**, klickar du på klustret. Välj **roller**. Availability Group-namn som du använde är en roll i klustret. Den Tillgänglighetsgruppen har inte en IP-adress för klientanslutningar, eftersom du inte konfigurerar en lyssnare. När du har skapat en Azure belastningsutjämnare ska du konfigurera lyssnaren.

   ![AG i hanteraren för redundanskluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Försök inte att redundansväxla Tillgänglighetsgruppen från Klusterhanteraren. Alla redundans åtgärder ska utföras inifrån **AlwaysOn instrumentpanelen** i SSMS. Mer information finns i [begränsningar på med hjälp av i hanteraren för redundanskluster med Tillgänglighetsgrupper](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Nu har du en tillgänglighetsgrupp med repliker på två instanser av SQL Server. Du kan flytta Tillgänglighetsgruppen mellan instanser. Du kan inte ansluta till Tillgänglighetsgruppen ännu eftersom du inte har en lyssnare. I Azure-datorer kräver lyssnaren en belastningsutjämnare. Nästa steg är att skapa belastningsutjämnaren i Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>skapa en Azure Load Balancer

På Azure-datorer kräver en belastningsutjämnare i en SQL Server-tillgänglighetsgrupp. Belastningsutjämnaren innehåller IP-adresser för tillgänglighetsgruppens lyssnare och Windows Server-redundanskluster. Det här avsnittet sammanfattas hur du skapar belastningsutjämnaren i Azure-portalen.

En Azure Load Balancer kan vara antingen en Standard Load Balancer eller en grundläggande belastningsutjämnare. Standard Load Balancer har fler funktioner än den grundläggande belastningsutjämnaren. För en tillgänglighetsgrupp är Standard Load Balancer krävs om du använder en Tillgänglighetszon (i stället för en Tillgänglighetsuppsättning). Mer information om skillnaden mellan load balancer-typer finns i [Load Balancers SKU jämförelse](../../../load-balancer/load-balancer-overview.md#skus).

1. I Azure-portalen går du till resursgruppen där din SQL-servrar är och på **+ Lägg till**.
1. Sök efter **belastningsutjämnare**. Välj belastningsutjämnare som publicerats av Microsoft.

   ![AG i hanteraren för redundanskluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klicka på **Skapa**.
1. Konfigurera följande parametrar för belastningsutjämnaren.

   | Inställning | Fält |
   | --- | --- |
   | **Namn** |Använda ett namn för belastningsutjämnaren, till exempel **sqlLB**. |
   | **Typ** |Intern |
   | **Virtuellt nätverk** |Använd namnet på Azure-nätverket. |
   | **Undernät** |Använd namnet på det undernät som den virtuella datorn är i.  |
   | **IP-adresstilldelning** |Statisk |
   | **IP-adress** |Använd en tillgänglig adress från undernätet. Använd den här adressen för din tillgänglighetsgruppens lyssnare. Observera att detta skiljer sig från klustrets IP-adress.  |
   | **Prenumeration** |Använd samma prenumeration som den virtuella datorn. |
   | **Plats** |Använd samma plats som den virtuella datorn. |

   Azure portalbladet bör se ut så här:

   ![Skapa belastningsutjämnare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klicka på **skapa**, för att skapa belastningsutjämnaren.

Om du vill konfigurera belastningsutjämnaren måste du skapa en backend-adresspool, en avsökning och ange regler för belastningsutjämning. Gör följande i Azure-portalen.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Lägg till serverdelspool för tillgänglighetsgruppens lyssnare

1. I Azure-portalen går du till tillgänglighetsgruppen. Du kan behöva uppdatera vyn för att se den nyligen skapade belastningsutjämnaren.

   ![Hitta belastningsutjämnare i resursgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klicka på belastningsutjämnaren, **serverdelspooler**, och klicka på **+ Lägg till**.

1. Skriv ett namn för serverdelspoolen.

1. Koppla serverdelspoolen tillgänglighetsuppsättningen som innehåller de virtuella datorerna.

1. Under **rikta IP-konfigurationer**, kontrollera **VM** och välja båda de virtuella datorerna som är värd för tillgänglighetsgrupprepliker. Omfattar inte vittne för servern.

   >[!NOTE]
   >Om båda de virtuella datorerna inte har angetts, lyckas bara anslutningar till den primära repliken.

1. Klicka på **OK** skapa backend-poolen.

### <a name="set-the-probe"></a>Ange avsökningen

1. Klicka på belastningsutjämnaren, **hälsoavsökningar**, och klicka på **+ Lägg till**.

1. Ange hälsoavsökningen lyssnaren på följande sätt:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokoll** | Välj TCP | TCP |
   | **Port** | Alla oanvända portar | 59999 |
   | **Intervall**  | Tiden mellan avsökningsförsöken i sekunder |5 |
   | **Tröskelvärde för ej felfri** | Antalet avsökningsfel som måste inträffa för en virtuell dator för att anses vara defekt  | 2 |

1. Klicka på **OK** att ställa in hälsoavsökningen.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. Klicka på belastningsutjämnaren, **belastningsutjämningsregler**, och klicka på **+ Lägg till**.

1. Ange lyssnare reglerna för belastningsutjämning på följande sätt.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointListener |
   | **Frontend-IP-adress** | Välj en adress |Använd den adress som du skapade när du skapade belastningsutjämnaren. |
   | **Protokoll** | Välj TCP |TCP |
   | **Port** | Använd port för tillgänglighetsgruppens lyssnare | 1433 |
   | **Serverdelsport** | Det här fältet används inte när flytande IP har ställts in för direct server returnerade | 1433 |
   | **Avsökningen** |Det namn du angav för avsökningen | SQLAlwaysOnEndPointProbe |
   | **Sessionspermanens** | Nedrullningsbar listruta | **Ingen** |
   | **Timeout för inaktivitet** | Minuter för att öppna en TCP-anslutning | 4 |
   | **Flytande IP (direkt serverreturnering)** | |Enabled |

   > [!WARNING]
   > Direkt serverreturnering anges när du skapar. Det kan inte ändras.

1. Klicka på **OK** att ställa in lyssnare-regler för belastningsutjämning.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Lägg till klustrets core IP-adress för den Windows Server-redundanskluster (WSFC)

WSFC-IP-adressen måste också vara på belastningsutjämnaren.

1. Klicka i portalen på samma Azure-belastningsutjämnaren, **Frontend-IP-konfiguration** och klicka på **+ Lägg till**. Använd IP-adress som du har konfigurerat för WSFC i klustrets kärnresurser. Ange IP-adressen som statisk.

1. Klicka på belastningsutjämnaren, **hälsoavsökningar**, och klicka på **+ Lägg till**.

1. Ange hälsoavsökningen för WSFC-klustret core IP-adress på följande sätt:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPointProbe |
   | **Protokoll** | Välj TCP | TCP |
   | **Port** | Alla oanvända portar | 58888 |
   | **Intervall**  | Tiden mellan avsökningsförsöken i sekunder |5 |
   | **Tröskelvärde för ej felfri** | Antalet avsökningsfel som måste inträffa för en virtuell dator för att anses vara defekt  | 2 |

1. Klicka på **OK** att ställa in hälsoavsökningen.

1. Ange regler för belastningsutjämning. Klicka på **belastningsutjämningsregler**, och klicka på **+ Lägg till**.

1. Ange kluster core IP-adress reglerna för belastningsutjämning på följande sätt.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPoint |
   | **Frontend-IP-adress** | Välj en adress |Använd den adress som du skapade när du har konfigurerat WSFC-IP-adress. Detta skiljer sig från IP-adressen för lyssnaren |
   | **Protokoll** | Välj TCP |TCP |
   | **Port** | Använda porten för klustrets IP-adress. Det här är en tillgänglig port som inte används för avsökning lyssningsport. | 58888 |
   | **Serverdelsport** | Det här fältet används inte när flytande IP har ställts in för direct server returnerade | 58888 |
   | **Avsökningen** |Det namn du angav för avsökningen | WSFCEndPointProbe |
   | **Sessionspermanens** | Nedrullningsbar listruta | **Ingen** |
   | **Timeout för inaktivitet** | Minuter för att öppna en TCP-anslutning | 4 |
   | **Flytande IP (direkt serverreturnering)** | |Enabled |

   > [!WARNING]
   > Direkt serverreturnering anges när du skapar. Det kan inte ändras.

1. Klicka på **OK** att ange regler för belastningsutjämning.

## <a name="configure-listener"></a> Konfigurera lyssnaren

Nästa sak att göra är att konfigurera en lyssningsfunktion för redundanskluster.

> [!NOTE]
> Den här självstudiekursen visar hur du skapar en enda lyssnare - med en ILB IP-adress. För att skapa en eller flera lyssnare med hjälp av en eller flera IP-adresser, se [Create Availability Group-lyssnare och load balancer | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Set-lyssnarporten

Ange porten för lyssnare i SQL Server Management Studio.

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

1. Gå till **AlwaysOn hög tillgänglighet** | **Tillgänglighetsgrupper** | **tillgänglighetsgruppens lyssnare**.

1. Du bör nu se namnet på lyssnare som du skapade i hanteraren för redundanskluster. Högerklicka på namnet på lyssnare och på **egenskaper**.

1. I den **Port** skriver du portnumret för tillgänglighetsgruppens lyssnare. 1433 är standardinställningen och klicka sedan på **OK**.

Nu har du en SQL Server-tillgänglighetsgrupp i Azure virtuella datorer i Resource Manager-läge.

## <a name="test-connection-to-listener"></a>Testa anslutning till lyssnare

Så här testar du anslutningen:

1. RDP till en SQL-Server som är i samma virtuella nätverk, men inte äger repliken. Du kan använda SQL Server i klustret.

1. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Om lyssnaren använder en annan port än standardvärdet standardporten (1433), ange porten i anslutningssträngen. Till exempel ansluter kommandot sqlcmd till en lyssnare på port 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD-anslutning ansluter automatiskt till instansen av SQL Server är värd för den primära repliken.

> [!TIP]
> Se till att den port som du anger är öppen i brandväggen för både SQL-servrar. Båda servrarna kräver en regel för inkommande trafik för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brandväggsregel](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Nästa steg

- [Lägg till en IP-adress till en belastningsutjämnare för en andra tillgänglighetsgruppen](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
