---
title: 'Självstudiekurs: Konfigurera tillgänglighetsgrupp'
description: Den här självstudien visar hur du skapar en SQL Server Always On Availability Group på Virtuella Azure-datorer.
services: virtual-machines
documentationCenter: na
author: MikeRayMSFT
manager: craigg
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 08/30/2018
ms.author: mikeray
ms.custom: seo-lt-2019
ms.openlocfilehash: 426ba4c0ac84799b4d0e6bf9330508f928437fd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060184"
---
# <a name="tutorial-configure-availability-group-on-azure-sql-server-vm-manually"></a>Självstudiekurs: Konfigurera tillgänglighetsgrupp på Azure SQL Server VM manuellt

Den här självstudien visar hur du skapar en SQL Server Always On Availability Group på Virtuella Azure-datorer. Den fullständiga självstudien skapar en tillgänglighetsgrupp med en databasreplik på två SQL-servrar.

**Tidsuppskattning**: Tar cirka 30 minuter att slutföra när förutsättningarna är uppfyllda.

Diagrammet illustrerar vad du bygger i handledningen.

![Tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Krav

Självstudien förutsätter att du har en grundläggande förståelse för SQL Server Always On Availability Groups. Om du behöver mer information läser du [Översikt över ALLTID på tillgänglighetsgrupper (SQL Server)](https://msdn.microsoft.com/library/ff877884.aspx).

I följande tabell visas de förutsättningar som du måste slutföra innan du startar den här självstudien:

|  |Krav |Beskrivning |
|----- |----- |----- |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Två SQL-servrar | - I en Azure-tillgänglighetsuppsättning <br/> - I ett enda område <br/> - Med redundansklusterfunktionen installerad |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Filresurs för klustervittne |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|TJÄNSTKONTO FÖR SQL Server | Domänkonto |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|TJÄNSTKONTO FÖR SQL Server Agent | Domänkonto |  
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Brandväggsportar öppna | - SQL Server: **1433** för standardinstans <br/> - Slutpunkt för databasspegling: **5022** eller någon tillgänglig port <br/> - Tillgänglighet grupp belastningsutjämnare IP-adress hälsa sond: **59999** eller någon tillgänglig port <br/> - Klusterkärna belastningsutjämnad IP-adress hälsa sond: **58888** eller någon tillgänglig port |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Lägga till funktionen för redundanskluster | Båda SQL-servrarna kräver den här funktionen |
|![Square](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Installationsdomänkonto | - Lokal administratör på varje SQL Server <br/> - Medlem i SQL Server sysadmin fast serverroll för varje instans av SQL Server  |


Innan du påbörjar självstudien måste du slutföra förutsättningar för att [skapa alltid på tillgänglighetsgrupper i virtuella Azure-datorer](virtual-machines-windows-portal-sql-availability-group-prereq.md). Om dessa förutsättningar redan har slutförts kan du gå till [Skapa kluster](#CreateCluster).

  >[!NOTE]
  > Många av stegen i den här självstudien kan nu automatiseras med [Azure SQL VM CLI-](virtual-machines-windows-sql-availability-group-cli.md) och [Azure Quickstart-mallar](virtual-machines-windows-sql-availability-group-quickstart-template.md).


<!--**Procedure**: *This is the first "step". Make titles H2's and short and clear – H2's appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
## <a name="create-the-cluster"></a>Skapa klustret

När förutsättningarna har slutförts är det första steget att skapa ett Windows Server Redundanskluster som innehåller två SQL Severs och en vittnesserver.

1. RDP till den första SQL Server med ett domänkonto som är administratör på både SQL-servrar och vittnesservern.

   >[!TIP]
   >Om du har följt [förkunskapsdokumentet](virtual-machines-windows-portal-sql-availability-group-prereq.md)har du skapat ett konto som heter **CORP\Install**. Använd det här kontot.

2. Välj **Verktyg**på instrumentpanelen **i Serverhanteraren** och klicka sedan på **Redundansklusterhanteraren**.
3. Högerklicka på **Redundansklusterhanteraren**i den vänstra rutan och klicka sedan på **Skapa ett kluster**.
   ![Skapa kluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Skapa ett ennodskluster i guiden Skapa kluster genom att gå igenom sidorna med inställningarna i följande tabell:

   | Sida | Inställningar |
   | --- | --- |
   | Innan du börjar |Använd standardvärden |
   | Välj servrar |Skriv det första SQL Server-namnet i **Enter-servernamnet** och klicka på **Lägg till**. |
   | Varning för validering |Välj **Nr Jag behöver inte stöd från Microsoft för det här klustret och vill därför inte köra valideringstesterna. När jag klickar på Nästa fortsätter jag skapa klustret**. |
   | Åtkomstpunkt för administration av klustret |Skriv ett klusternamn, till exempel **SQLAGCluster1** i **Klusternamn**.|
   | Bekräftelse |Använd standardinställningar om du inte använder lagringsutrymmen. Se anteckningen efter den här tabellen. |

### <a name="set-the-windows-server-failover-cluster-ip-address"></a>Ange IP-adress för Windows-server-redundanskluster

  > [!NOTE]
  > På Windows Server 2019 skapar klustret ett **distribuerat servernamn** i stället för **namnet på klusternätverket**. Om du använder Windows Server 2019 hoppar du över alla steg som refererar till klusterkärnnamnet i den här självstudien. Du kan skapa ett klusternätverksnamn med [PowerShell](virtual-machines-windows-portal-sql-create-failover-cluster.md#windows-server-2019). Läs blogg [redundanskluster: Cluster Network Object](https://blogs.windows.com/windowsexperience/2018/08/14/announcing-windows-server-2019-insider-preview-build-17733/#W0YAxO8BfwBRbkzG.97) för mer information. 

1. Bläddra ned till **Klusterkärnresurser** i **Redundansklusterhanteraren**och expandera klusterinformationen. Du bör se både **namn-** och **IP-adressresurserna** i tillståndet **Misslyckades.** IP-adressresursen kan inte anslutas eftersom klustret tilldelas samma IP-adress som själva datorn, därför är det en dubblettadress.

2. Högerklicka på den misslyckade **IP-adressresursen** och klicka sedan på **Egenskaper**.

   ![Klusteregenskaper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Välj **Statisk IP-adress** och ange en tillgänglig adress från samma undernät som dina virtuella datorer.

4. Högerklicka på klusternamnet i avsnittet **Klusterresurser** och klicka på **Skapa online**. Vänta sedan tills båda resurserna är online. När klusternamnsresursen är online uppdateras DC-servern med ett nytt AD-datorkonto. Använd det här AD-kontot om du vill köra den klustrade tjänsten Tillgänglighetsgrupp senare.

### <a name="add-the-other-sql-server-to-cluster"></a><a name="addNode"></a>Lägga till den andra SQL Server i klustret

Lägg till den andra SQL Server i klustret.

1. Högerklicka på klustret i webbläsarträdet och klicka på **Lägg till nod**.

    ![Lägg till nod i klustret](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. Klicka på **Nästa** **i guiden Lägg till nod**. Lägg till den andra SQL Server på sidan **Välj servrar.** Skriv servernamnet i **Enter-servernamnet** och klicka sedan på **Lägg till**. När du är klar klickar du på **Nästa**.

1. På sidan **Valideringsvarning** klickar du på **Nej** (i ett produktionsscenario bör du utföra valideringstesterna). Klicka sedan på **Nästa**.

8. Avmarkera kryssrutan **Lägg till all kvalificerad lagring i klustret** på sidan **Bekräftelse** om du använder Lagringsutrymmen.

   ![Lägg till nodbekräftelse](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Om du använder Lagringsutrymmen och inte **avmarkerar Lägg till all kvalificerad lagring i klustret**kopplar Windows bort de virtuella diskarna under klustringsprocessen. Därför visas de inte i Diskhanteraren eller Utforskaren förrän lagringsutrymmena tas bort från klustret och återansluts med PowerShell. Lagringsutrymmen grupperar flera diskar i lagringspooler. Mer information finns i [Lagringsutrymmen](https://technet.microsoft.com/library/hh831739).

1. Klicka på **Nästa**.

1. Klicka på **Slutför**.

   Redundansklusterhanteraren visar att klustret har en ny nod och visar den i **noderbehållaren.**

10. Logga ut från fjärrskrivbordssessionen.

### <a name="add-a-cluster-quorum-file-share"></a>Lägga till en filresurs för klusterkvorum

I det här exemplet använder Windows-klustret en filresurs för att skapa ett klusterkvorum. Den här självstudien använder en nod och majoritetsbeslut för filresurs. Mer information finns i [Understanding Quorum Configurations in a Failover Cluster](https://technet.microsoft.com/library/cc731739.aspx) (Förstå kvorumkonfigurationer i ett redundanskluster).

1. Anslut till filresursens vittnesmedlemsserver med en fjärrskrivbordssession.

1. Klicka på **Verktyg**i **Serverhanteraren**. Öppna **datorhantering**.

1. Klicka på **Delade mappar**.

1. Högerklicka på **Resurser**och klicka på **Ny resurs...**.

   ![Ny aktie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **Guiden Skapa en delad mapp** för att skapa en resurs.

1. Klicka på **Bläddra** och leta upp eller skapa en sökväg för den delade mappen på **Mappsökväg.** Klicka på **Nästa**.

1. I **Namn, Beskrivning och Inställningar** verifieras resursnamnet och sökvägen. Klicka på **Nästa**.

1. På **behörigheter för delade mappar** anger du anpassa **behörigheter**. Klicka på **Anpassad...**.

1. Klicka **Customize Permissions**på Lägg **till...**.

1. Kontrollera att kontot som används för att skapa klustret har full kontroll.

   ![Ny aktie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klicka på **OK**.

1. Klicka på **Slutför**i **behörigheter för delade mappar.** Klicka på **Slutför** igen.  

1. Logga ut från servern

### <a name="configure-cluster-quorum"></a>Konfigurera klusterkvorum

Ange sedan klusterkvorumet.

1. Anslut till den första klusternoden med fjärrskrivbord.

1. Högerklicka på klustret i Klusterhanteraren för **växling,** peka på **Fler åtgärder**och klicka på Konfigurera **klusterkvoruminställningar...**.

   ![Ny aktie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. Klicka på **Nästa**i **guiden Konfigurera klusterkvorum**.

1. I **Välj kvorumkonfigurationsalternativ**väljer du **Välj kvorumvittne**och klickar på **Nästa**.

1. Klicka på Konfigurera ett vittne för **fildelning**i **Välj kvorum.**

   >[!TIP]
   >Windows Server 2016 stöder ett molnvittne. Om du väljer den här typen av vittne behöver du inget vittne om fildelning. Mer information finns i [Distribuera ett molnvittne för ett redundanskluster](https://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Den här självstudien använder ett filresursvittne, som stöds av tidigare operativsystem.

1. Skriv sökvägen för resursen du skapade på **Konfigurera filresursvittne.** Klicka på **Nästa**.

1. Verifiera inställningarna på **Bekräftelse**. Klicka på **Nästa**.

1. Klicka på **Slutför**.

Klusterkärnresurserna konfigureras med ett filresursvittne.

## <a name="enable-availability-groups"></a>Aktivera tillgänglighetsgrupper

Aktivera sedan funktionen **AlwaysOn-tillgänglighetsgrupper.** Gör dessa steg på båda SQL-servrarna.

1. Starta SQL Server **Configuration Manager**på **Startskärmen** .
2. Klicka på SQL **Server Services**i webbläsarträdet och högerklicka sedan på **TJÄNSTEN SQL Server (MSSQLSERVER)** och klicka på **Egenskaper**.
3. Klicka på fliken **Alltidpå hög tillgänglighet** och välj sedan **Aktivera alltidpå tillgänglighetsgrupper**enligt följande:

    ![Aktivera AlltidPå tillgänglighetsgrupper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klicka på **Använd**. Klicka på **OK** i popup-dialogrutan.

5. Starta om SQL Server-tjänsten.

Upprepa dessa steg på den andra SQL Server.

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

## <a name="create-a-database-on-the-first-sql-server"></a>Skapa en databas på den första SQL Server

1. Starta RDP-filen till den första SQL Server med ett domänkonto som är medlem i sysadmin fast serverroll.
1. Öppna SQL Server Management Studio och anslut till den första SQL Server.
7. Högerklicka på Databaser i **Objektutforskaren**och klicka på **Ny databas**. **Databases**
8. Skriv **MyDB1** **i Databasnamn**och klicka sedan på **OK**.

### <a name="create-a-backup-share"></a><a name="backupshare"></a>Skapa en delning för säkerhetskopiering

1. Klicka på **Verktyg**på den första SQL Server i **Serverhanteraren**. Öppna **datorhantering**.

1. Klicka på **Delade mappar**.

1. Högerklicka på **Resurser**och klicka på **Ny resurs...**.

   ![Ny aktie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **Guiden Skapa en delad mapp** för att skapa en resurs.

1. Klicka på **Bläddra** och leta upp eller skapa en sökväg för den delade mappen för säkerhetskopiering av databasen på **Mappsökväg.** Klicka på **Nästa**.

1. I **Namn, Beskrivning och Inställningar** verifieras resursnamnet och sökvägen. Klicka på **Nästa**.

1. På **behörigheter för delade mappar** anger du anpassa **behörigheter**. Klicka på **Anpassad...**.

1. Klicka **Customize Permissions**på Lägg **till...**.

1. Kontrollera att SQL Server- och SQL Server Agent-tjänstkontona för båda servrarna har full kontroll.

   ![Ny aktie](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klicka på **OK**.

1. Klicka på **Slutför**i **behörigheter för delade mappar.** Klicka på **Slutför** igen.  

### <a name="take-a-full-backup-of-the-database"></a>Gör en fullständig säkerhetskopia av databasen

Du måste säkerhetskopiera den nya databasen för att initiera loggkedjan. Om du inte gör en säkerhetskopia av den nya databasen kan den inte inkluderas i en tillgänglighetsgrupp.

1. Högerklicka på databasen i **Objektutforskaren,** peka på **Uppgifter,** klicka på **Säkerhetskopiera.**

1. Klicka på **OK** om du vill göra en fullständig säkerhetskopia till standardplatsen för säkerhetskopiering.

## <a name="create-the-availability-group"></a>Skapa tillgänglighetsgruppen
Du är nu redo att konfigurera en tillgänglighetsgrupp med hjälp av följande steg:

* Skapa en databas på den första SQL Server.
* Gör både en fullständig säkerhetskopiering och en säkerhetskopiering av databasen
* Återställa fullständiga säkerhetskopior och logga säkerhetskopior till den andra SQL Server med **NORECOVERY-alternativet**
* Skapa tillgänglighetsgruppen **(AG1)** med synkrona commit, automatisk redundans och läsbara sekundära repliker

### <a name="create-the-availability-group"></a>Skapa tillgänglighetsgruppen:

1. På fjärrskrivbordssession till den första SQL Server. Högerklicka på **AlwaysOn Hög tillgänglighet** i **Objektutforskaren** i SSMS och klicka på **Guiden Ny tillgänglighetsgrupp**.

    ![Guiden Starta en ny tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. Klicka på **Nästa**på sidan **Introduktion** . På sidan **Ange namn på tillgänglighetsgrupp** skriver du ett namn för tillgänglighetsgruppen, till exempel **AG1**, i **tillgänglighetsgruppnamn**. Klicka på **Nästa**.

    ![Ny AG-guide, ange AG-namn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. På sidan **Välj databaser** markerar du databasen och klickar på **Nästa**.

   >[!NOTE]
   >Databasen uppfyller förutsättningarna för en tillgänglighetsgrupp eftersom du har gjort minst en fullständig säkerhetskopia på den avsedda primära repliken.

   ![Ny AG-guide, Välj databaser](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. Klicka på Lägg till **replik**på sidan Ange **repliker** .

   ![Ny AG-guide, Ange repliker](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Dialogrutan **Anslut till server** visas. Skriv namnet på den andra servern i **Servernamn**. Klicka på **Anslut**.

   Tillbaka på sidan **Ange repliker** bör du nu se den andra servern som anges i **Tillgänglighetsrepliker**. Konfigurera replikerna enligt följande.

   ![Ny AG-guide, Ange repliker (slutförd)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klicka på **Slutpunkter** om du vill visa slutpunkten för databasspegling för den här tillgänglighetsgruppen. Använd samma port som du använde när du anger [brandväggsregeln för databasspeglingsslutpunkter](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Ny AG-guide, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. På sidan **Välj initial datasynkronisering** väljer du **Fullständig** och anger en delad nätverksplats. Använd den [säkerhetskopieringsresurs som du skapade](#backupshare)för platsen . I exemplet var det, ** \\ \\ \<\>Första\\SQL Server \Backup**. Klicka på **Nästa**.

   >[!NOTE]
   >Fullständig synkronisering kräver en fullständig säkerhetskopia av databasen på den första instansen av SQL Server och återställer den till den andra instansen. För stora databaser rekommenderas inte fullständig synkronisering eftersom det kan ta lång tid. Du kan minska den här tiden genom att manuellt göra `NO RECOVERY`en säkerhetskopia av databasen och återställa den med . Om databasen redan har `NO RECOVERY` återställts med på den andra SQL Server innan du konfigurerar tillgänglighetsgruppen väljer du **Endast gå med**. Om du vill ta säkerhetskopian efter att ha konfigurerat tillgänglighetsgruppen väljer du **Hoppa över inledande datasynkronisering**.

    ![Ny AG-guide, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. Klicka på **Nästa**på sidan **Validering** . Den här sidan ska se ut ungefär som följande bild:

    ![Ny AG-guide, validering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Det finns en varning för lyssnarkonfigurationen eftersom du inte har konfigurerat en lyssnare i tillgänglighetsgruppen. Du kan ignorera den här varningen eftersom du på virtuella Azure-datorer skapar lyssnaren när du har skapat Azure-belastningsutjämnaren.

10. Klicka på **Slutför**på sidan **Sammanfattning** och vänta sedan medan den nya tillgänglighetsgruppen konfigureras. På sidan **Förlopp** kan du klicka på **Mer information** om du vill visa de detaljerade förloppet. När guiden är klar bör du kontrollera sidan **Resultat** för att kontrollera att tillgänglighetsgruppen har skapats.

     ![Ny AG-guide, Resultat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Avsluta guiden genom att klicka på **Stäng** .

### <a name="check-the-availability-group"></a>Kontrollera tillgänglighetsgruppen

1. Expandera **AlwaysOn hög tillgänglighet i** **Objektutforskaren**och expandera sedan **tillgänglighetsgrupper**. Nu bör du se den nya tillgänglighetsgruppen i den här behållaren. Högerklicka på tillgänglighetsgruppen och klicka på **Visa instrumentpanel**.

   ![Visa AG-instrumentpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   **AlwaysOn-instrumentpanelen** ska se ut ungefär så här.

   ![AG-instrumentpanel](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Du kan se replikerna, redundansläget för varje replik och synkroniseringstillståndet.

2. Klicka på **klustret i Redundansklusterhanteraren.** Välj **Roller**. Namnet på tillgänglighetsgruppen som du använde är en roll i klustret. Den tillgänglighetsgruppen har ingen IP-adress för klientanslutningar, eftersom du inte har konfigurerat en lyssnare. Du konfigurerar lyssnaren när du har skapat en Azure-belastningsutjämningsfaktor.

   ![AG i Redundansklusterhanterare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Försök inte att växla över tillgänglighetsgruppen från Redundansklusterhanteraren. Alla redundansåtgärder ska utföras inifrån **AlwaysOn Dashboard** i SSMS. Mer information finns i [Begränsningar för hur du använder Redundansklusterhanteraren med tillgänglighetsgrupper](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Nu har du en tillgänglighetsgrupp med repliker på två instanser av SQL Server. Du kan flytta tillgänglighetsgruppen mellan instanser. Du kan inte ansluta till tillgänglighetsgruppen ännu eftersom du inte har en lyssnare. I virtuella Azure-datorer kräver lyssnaren en belastningsutjämnare. Nästa steg är att skapa belastningsutjämnaren i Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>skapa en Azure Load Balancer

På virtuella Azure-datorer kräver en SQL Server-tillgänglighetsgrupp en belastningsutjämnare. Belastningsutjämnaren innehåller IP-adresserna för lyssnarna i tillgänglighetsgruppen och Windows Server Redundanskluster. Det här avsnittet sammanfattar hur du skapar belastningsutjämnaren i Azure-portalen.

En Azure Load Balancer kan vara antingen en standard belastningsutjämning eller en grundläggande belastningsutjämnare. Standardbelastningsutjämnare har fler funktioner än basic Load Balancer. För en tillgänglighetsgrupp krävs standardbelastningsutjämnaren om du använder en tillgänglighetszon (i stället för en tillgänglighetsuppsättning). Mer information om skillnaden mellan belastningsutjämnaderna finns i [Jämförelse med belastningsutjämnare SKU](../../../load-balancer/concepts-limitations.md#skus).

1. Gå till resursgruppen där DINA SQL-servrar finns i Azure-portalen och klicka på **+ Lägg till**.
1. Sök efter **belastningsutjämnare**. Välj den belastningsutjämnare som publiceras av Microsoft.

   ![AG i Redundansklusterhanterare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1. Klicka på **Skapa**.
1. Konfigurera följande parametrar för belastningsutjämnaren.

   | Inställning | Field |
   | --- | --- |
   | **Namn** |Använd ett textnamn för belastningsutjämnaren, till exempel **sqlLB**. |
   | **Typ** |Intern |
   | **Virtuellt nätverk** |Använd namnet på det virtuella Azure-nätverket. |
   | **Undernät** |Använd namnet på det undernät som den virtuella datorn finns i.  |
   | **TILLDELNING AV IP-adress** |Statisk |
   | **IP-adress** |Använd en tillgänglig adress från undernätet. Använd den här adressen för din tillgänglighetsgrupplyssnare. Observera att detta skiljer sig från klustrets IP-adress.  |
   | **Prenumeration** |Använd samma prenumeration som den virtuella datorn. |
   | **Location** |Använd samma plats som den virtuella datorn. |

   Azure-portalbladet ska se ut så här:

   ![Skapa belastningsutjämnare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klicka på **Skapa**om du vill skapa belastningsutjämnaren.

Om du vill konfigurera belastningsutjämnaren måste du skapa en serverdpool, en avsökning och ange belastningsutjämningsregler. Gör dessa i Azure-portalen.

### <a name="add-backend-pool-for-the-availability-group-listener"></a>Lägga till backend pool för tillgänglighetsgrupplyssnaren

1. Gå till din tillgänglighetsgrupp i Azure-portalen. Du kan behöva uppdatera vyn för att se den nyskapade belastningsutjämnaren.

   ![Hitta belastningsutjämnare i resursgruppen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klicka på belastningsutjämnaren, klicka på **Backend-pooler**och klicka på **+Lägg till**.

1. Skriv ett namn på backend-poolen.

1. Associera backend-poolen med tillgänglighetsuppsättningen som innehåller de virtuella datorerna.

1. Under **Ip-konfigurationer**för målnätverk kontrollerar du **virtuell dator** och väljer båda de virtuella datorer som ska vara värdar för tillgänglighetsgrupprepliker. Ta inte med vittnesservern för filresurs.

   >[!NOTE]
   >Om båda virtuella datorer inte anges lyckas anslutningarna bara till den primära repliken.

1. Klicka på **OK** om du vill skapa backend-poolen.

### <a name="set-the-probe"></a>Ställ in sonden

1. Klicka på belastningsutjämnaren, klicka på **Hälsoavsökningar**och klicka på **+Lägg till**.

1. Ställ in lyssnarens hälsoavsökning enligt följande:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokollet** | Välj TCP | TCP |
   | **Port** | Oanvänd port | 59999 |
   | **Intervall**  | Tiden mellan sondförsök i sekunder |5 |
   | **Felfritt tröskelvärde** | Antalet på varandra följande avsökningsfel som måste uppstå för att en virtuell dator ska anses vara felfritt  | 2 |

1. Klicka på **OK** för att ställa in hälsoavsökningen.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. Klicka på belastningsutjämnaren, klicka på **Belastningsutjämningsregler**och klicka på **+Lägg till**.

1. Ställ in reglerna för utjämning av lyssnaren enligt följande.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointListener |
   | **IP-adress för klientdel** | Välj en adress |Använd adressen som du skapade när du skapade belastningsutjämnaren. |
   | **Protokollet** | Välj TCP |TCP |
   | **Port** | Använda porten för tillgänglighetsgruppavlyssnare | 1433 |
   | **Backend-port** | Det här fältet används inte när flytande IP är inställt för direkt serverretur | 1433 |
   | **Avsökning** |Namnet du angav för sonden | SQLAlwaysOnEndPointProbe |
   | **Sessions persistens** | Rullgardinsmenyn | **Inget** |
   | **Tidsgräns för inaktivitet** | Minuter för att hålla en TCP-anslutning öppen | 4 |
   | **Flytande IP (direkt serverretur)** | |Enabled |

   > [!WARNING]
   > Direkt serverretur ställs in när den skapas. Det kan inte ändras.

1. Klicka på **OK** om du vill ange reglerna för utjämning av lyssnaren.

### <a name="add-the-cluster-core-ip-address-for-the-windows-server-failover-cluster-wsfc"></a>Lägga till klusterkärn-IP-adressen för Windows Server Redundanskluster (WSFC)

WSFC IP-adressen måste också vara på belastningsutjämnaren.

1. Klicka på **Frontend IP-konfiguration** på samma Azure-belastningsutjämnare i portalen och klicka på **+Lägg till**. Använd den IP-adress som du har konfigurerat för WSFC i klusterkärnresurserna. Ange IP-adressen som statisk.

1. Klicka på **Hälsoavsökningar**på lastutjämnaren och klicka på **+Lägg till**.

1. Ange hälsoavsökningen för WSFC-klusterkärnor IP-adress enligt följande:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPointProbe |
   | **Protokollet** | Välj TCP | TCP |
   | **Port** | Oanvänd port | 58888 |
   | **Intervall**  | Tiden mellan sondförsök i sekunder |5 |
   | **Felfritt tröskelvärde** | Antalet på varandra följande avsökningsfel som måste uppstå för att en virtuell dator ska anses vara felfritt  | 2 |

1. Klicka på **OK** för att ställa in hälsoavsökningen.

1. Ställ in reglerna för belastningsutjämning. Klicka på **Regler för belastningsutjämning**och klicka på **+Lägg till**.

1. Ange reglerna för utjämning av klusterkärnor IP-adressbelastning enligt följande.

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | WSFCEndPoint |
   | **IP-adress för klientdel** | Välj en adress |Använd adressen som du skapade när du konfigurerade WSFC IP-adressen. Detta skiljer sig från lyssnarens IP-adress |
   | **Protokollet** | Välj TCP |TCP |
   | **Port** | Använd porten för klustrets IP-adress. Detta är en tillgänglig port som inte används för lyssnaren avsökningsporten. | 58888 |
   | **Backend-port** | Det här fältet används inte när flytande IP är inställt för direkt serverretur | 58888 |
   | **Avsökning** |Namnet du angav för sonden | WSFCEndPointProbe |
   | **Sessions persistens** | Rullgardinsmenyn | **Inget** |
   | **Tidsgräns för inaktivitet** | Minuter för att hålla en TCP-anslutning öppen | 4 |
   | **Flytande IP (direkt serverretur)** | |Enabled |

   > [!WARNING]
   > Direkt serverretur ställs in när den skapas. Det kan inte ändras.

1. Klicka på **OK** om du vill ange reglerna för belastningsutjämning.

## <a name="configure-the-listener"></a><a name="configure-listener"></a>Konfigurera lyssnaren

Nästa sak att göra är att konfigurera en tillgänglighetsgrupplyssnare i redundansklustret.

> [!NOTE]
> Den här självstudien visar hur du skapar en enda lyssnare - med en ILB IP-adress. Information om hur du skapar en eller flera lyssnare med en eller flera IP-adresser finns i [Skapa lyssnare och belastningsutjämnare | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ange lyssnarport

I SQL Server Management Studio ställer du in lyssnarporten.

1. Starta SQL Server Management Studio och anslut till den primära repliken.

1. Navigera till **AlwaysOn-tillgänglighetsgrupper** | **Availability Groups** | med hög**tillgänglighet.**

1. Du bör nu se lyssnarnamnet som du skapade i Redundansklusterhanteraren. Högerklicka på lyssnarens namn och klicka på **Egenskaper**.

1. I rutan **Port** anger du portnumret för lyssnaren i tillgänglighetsgruppen. 1433 är standard och klicka sedan på **OK**.

Du har nu en SQL Server-tillgänglighetsgrupp i virtuella Azure-datorer som körs i Resource Manager-läge.

## <a name="test-connection-to-listener"></a>Testa anslutning till lyssnaren

Så här testar du anslutningen:

1. RDP till en SQL Server som finns i samma virtuella nätverk, men som inte äger repliken. Du kan använda den andra SQL Server i klustret.

1. Använd **sqlcmd-verktyget** för att testa anslutningen. Följande skript upprättar till exempel en **sqlcmd-anslutning** till den primära repliken via lyssnaren med Windows-autentisering:

   ```cmd
   sqlcmd -S <listenerName> -E
   ```

   Om lyssnaren använder en annan port än standardporten (1433) anger du porten i anslutningssträngen. Följande sqlcmd-kommando ansluter till en lyssnare vid port 1435:

   ```cmd
   sqlcmd -S <listenerName>,1435 -E
   ```

SQLCMD-anslutningen ansluter automatiskt till den instans av SQL Server som är värd för den primära repliken.

> [!TIP]
> Kontrollera att porten du anger är öppen i brandväggen för båda SQL-servrarna. Båda servrarna kräver en inkommande regel för den TCP-port som du använder. Mer information finns i [Lägga till eller redigera brandväggsregel](https://technet.microsoft.com/library/cc753558.aspx).

## <a name="next-steps"></a>Nästa steg

- [Lägg till en IP-adress i en belastningsutjämnare för en andra tillgänglighetsgrupp](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
