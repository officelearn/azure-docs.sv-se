---
title: "SQL-servertillgänglighet grupper - virtuella datorer i Azure - kursen | Microsoft Docs"
description: "Den här kursen visar hur du skapar en SQL Server alltid på tillgänglighetsgrupp på Azure Virtual Machines."
services: virtual-machines
documentationCenter: na
authors: MikeRayMSFT
manager: jhubbard
editor: monicar
tags: azure-service-management
ms.assetid: 08a00342-fee2-4afe-8824-0db1ed4b8fca
ms.service: virtual-machines-sql
ms.devlang: na
ms.custom: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/09/2017
ms.author: mikeray
ms.openlocfilehash: 228ca9ca5fddc493d27bfd6a40df5ee7306d6aa9
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="configure-always-on-availability-group-in-azure-vm-manually"></a>Konfigurera alltid på Tillgänglighetsgruppen i Azure VM manuellt

Den här kursen visar hur du skapar en SQL Server alltid på tillgänglighetsgrupp på Azure Virtual Machines. Fullständig kursen skapar en tillgänglighetsgrupp med en databasreplik på två SQL-servrar.

**Tid uppskattning**: tar cirka 30 minuter för att slutföra när alla krav är uppfyllda.

Diagrammet visar hur du skapar under kursen.

![Tillgänglighetsgruppen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/00-EndstateSampleNoELB.png)

## <a name="prerequisites"></a>Krav

Kursen förutsätter att du har en grundläggande förståelse för SQL Server alltid på Tillgänglighetsgrupper. Om du behöver mer information, se [översikt över alltid på Tillgänglighetsgrupper (SQL Server)](http://msdn.microsoft.com/library/ff877884.aspx).

I följande tabell visas de krav som du måste utföra innan du påbörjar den här kursen:

|  |Krav |Beskrivning |
|----- |----- |----- |
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png) | Två SQL-servrar | – I en Azure tillgänglighetsuppsättning <br/> – I en domän <br/> -Med redundanskluster installerat |
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)| Windows Server | Filresurs för klustret vittne |  
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|SQL Server-tjänstkontot | Domänkonto |
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Tjänstkontot för SQL Server Agent | Domänkonto |  
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Öppna portar i brandväggen | -SQL Server: **1433** för standardinstans <br/> -Slutpunkten för databasspegling: **5022** eller alla tillgängliga portar <br/> -Azure belastningsutjämningsavsökning: **59999** eller alla tillgängliga portar |
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Lägga till Redundansklusterfunktionen | Både SQL-servrar kräver den här funktionen |
|![Ruta](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/square.png)|Domänkonto för installation | -Lokal administratör på varje SQL Server <br/> -Medlem i SQL Server fasta serverrollen sysadmin för varje instans av SQL Server  |


Innan du påbörjar självstudiekursen måste du [slutföra förutsättningar för att skapa Always On-Tillgänglighetsgrupper i Azure Virtual Machines](virtual-machines-windows-portal-sql-availability-group-prereq.md). Om dessa krav har redan slutförts, kan du hoppa till [Skapa kluster](#CreateCluster).


<!--**Procedure**: *This is the first “step”. Make titles H2’s and short and clear – H2’s appear in the right pane on the web page and are important for navigation.*-->

<a name="CreateCluster"></a>
##Skapa klustret

När förutsättningarna har slutförts, är det första steget att skapa ett redundanskluster för Windows-Server som innehåller två SQL-servrar och en vittnesserver som.  

1. RDP till den första SQL-servern med ett domänkonto som är administratör på både SQL-servrar och vittnesservern.

   >[!TIP]
   >Om du har följt den [förutsättningsdokumentet](virtual-machines-windows-portal-sql-availability-group-prereq.md), du har skapat ett konto som heter **CORP\Install**. Använd det här kontot.

2. I den **Serverhanteraren** instrumentpanelen, väljer **verktyg**, och klicka sedan på **Klusterhanteraren**.
3. I det vänstra fönstret högerklickar du på **Klusterhanteraren**, och klicka sedan på **skapa ett kluster**.
   ![Skapa kluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/40-createcluster.png)
4. Skapa ett kluster med en nod i guiden Skapa kluster genom att gå igenom sidorna med inställningarna i följande tabell:

   | Sida | Inställningar |
   | --- | --- |
   | Innan du börjar |Använd standardvärden |
   | Välj servrar |Ange det första SQL Server-namnet i **ange servernamnet** och på **Lägg till**. |
   | Verifieringsvarning |Välj **Nej jag inte behöver support från Microsoft för det här klustret och därför inte vill köra verifieringstesterna. När jag klickar på nästa fortsätter att skapa klustret**. |
   | Åtkomstpunkt för administration av klustret |Skriv ett namn för klustret, till exempel **SQLAGCluster1** i **klusternamnet**.|
   | Bekräftelse |Använd standardvärden om du inte använder lagringsutrymmen. Se fotnoten efter denna tabell. |

### <a name="set-the-cluster-ip-address"></a>Ange klustrets IP-adress

1. I **Klusterhanteraren**, rulla ned till **klustrets kärnresurser** och expandera klustret information. Du bör se både den **namn** och **IP-adress** resurser i den **misslyckades** tillstånd. IP-adressresurs kunde inte försättas online eftersom klustret tilldelas samma IP-adress som dator själva, därför är det en dubblett-adress.

2. Högerklicka på den misslyckade **IP-adress** resursen och klickar sedan på **egenskaper**.

   ![Egenskaper för klustret](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/42_IPProperties.png)

3. Välj **statisk IP-adress** och ange en tillgänglig adress från undernätet där SQL Server finns i textrutan adress. Klicka på **OK**.
4. I den **klustrets kärnresurser** avsnittet, högerklickar du på klusternamnet och klickar på **Anslut**. Vänta tills båda resurser är online. När namnet klusterresursen är online uppdaterar DC-servern med ett nytt AD-datorkonto. Använda AD-kontot för att köra tjänsten Availability Group klustrade senare.

### <a name="addNode"></a>Lägga till SQL Server i klustret

Lägg till SQL Server i klustret.

1. I webbläsarträdet högerklickar du på klustret och klickar på **Lägg till nod**.

    ![Lägg till nod i klustret](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/44-addnode.png)

1. I den **guiden Lägg till nod**, klickar du på **nästa**. I den **Välj servrar** lägger du till den andra SQL-servern. Skriv servernamnet i **ange servernamnet** och klicka sedan på **Lägg till**. När du är klar klickar du på **nästa**.

1. I den **verifieringsvarning** klickar du på **nr** (i en form av produktionsscenario du bör utföra verifieringstesterna). Klicka sedan på **Nästa**.

8. I den **bekräftelse** sidan om du använder lagringsutrymmen, avmarkera kryssrutan **lägga till alla tillgängliga lagringsenheter i klustret.**

   ![Lägg till nod bekräftelse](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/46-addnodeconfirmation.png)

    >[!WARNING]
   >Om du använder lagringsutrymmen och inte avmarkera **lägga till alla tillgängliga lagringsenheter i klustret**, Windows tar bort de virtuella diskarna under klustringsprocessen. Därför kan de visas inte i Diskhantering eller Explorer tills lagringsutrymmen tas bort från klustret och anbringas på nytt med hjälp av PowerShell. Lagringsutrymmen grupper flera diskar i lagringspooler. Mer information finns i [lagringsutrymmen](https://technet.microsoft.com/library/hh831739).

1. Klicka på **Nästa**.

1. Klicka på **Slutför**.

   Hanteraren för redundanskluster visar att klustret har en ny nod och visar den i den **noder** behållare.

10. Logga ut från fjärrskrivbords-sessionen.

### <a name="add-a-cluster-quorum-file-share"></a>Lägg till en filresurs för klustrets kvorum

I det här exemplet använder en filresurs i Windows-kluster för att skapa en klustrets kvorum. Den här kursen använder en nod och filresursmajoritet kvorum. Mer information finns i [Om kvorumkonfigurationer i ett Failover-kluster](http://technet.microsoft.com/library/cc731739.aspx).

1. Anslut till filresurs vittne medlem filservern med en fjärrskrivbordssession.

1. På **Serverhanteraren**, klickar du på **verktyg**. Öppna **Datorhantering**.

1. Klicka på **delade mappar**.

1. Högerklicka på **resurser**, och klicka på **ny resurs...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **guiden Skapa en delad mapp** att skapa en resurs.

1. På **mappsökväg**, klickar du på **Bläddra** och hitta eller skapa en sökväg för den delade mappen. Klicka på **Nästa**.

1. I **namn, beskrivning och inställningar** Kontrollera namn och sökväg. Klicka på **Nästa**.

1. På **delade mappbehörigheter** ange **anpassa behörigheter**. Klicka på **anpassad...** .

1. På **anpassa behörigheter**, klickar du på **Lägg till...** .

1. Se till att det konto som används för att skapa klustret har fullständig behörighet.

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/50-filesharepermissions.png)

1. Klicka på **OK**.

1. I **delade mappbehörigheter**, klickar du på **Slutför**. Klicka på **Slutför** igen.  

1. Logga ut från servern

### <a name="configure-cluster-quorum"></a>Konfigurera klustrets kvorumdisk

Ange sedan klustrets kvorumdisk.

1. Ansluta till den första noden i klustret med fjärrskrivbord.

1. I **Klusterhanteraren**, högerklicka på klustret, peka på **fler åtgärder**, och klicka på **konfigurera inställningarna för klusterkvorum...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/52-configurequorum.png)

1. I **guiden Konfigurera klusterkvorum**, klickar du på **nästa**.

1. I **Välj alternativ för kvorumkonfiguration**, Välj **Välj kvorumvittnet**, och klicka på **nästa**.

1. På **Välj Kvorumvittne**, klickar du på **konfigurera ett filresursvittne**.

   >[!TIP]
   >Windows Server 2016 stöder ett vittne i molnet. Om du väljer den här typen av vittne du behöver inte en fil dela vittne. Mer information finns i [distribuera ett moln vittne för ett redundanskluster](http://technet.microsoft.com/windows-server-docs/failover-clustering/deploy-cloud-witness). Den här kursen använder ett filresursvittne som stöds av tidigare operativsystem.

1. På **konfigurera filresursvittne**, ange sökvägen till den resurs du skapat. Klicka på **Nästa**.

1. Kontrollera inställningarna på **bekräftelse**. Klicka på **Nästa**.

1. Klicka på **Slutför**.

Klustrets kärnresurser är konfigurerade med ett filresursvittne.

## <a name="enable-availability-groups"></a>Aktivera Tillgänglighetsgrupper

Aktivera sedan den **AlwaysOn Availability Groups** funktion. Utföra de här stegen på både SQL-servrar.

1. Från den **starta** skärmen, starta **SQL Server Configuration Manager**.
2. I webbläsarträdet klickar du på **SQL Server Services**, högerklicka på den **SQL Server (MSSQLSERVER)** tjänsten och klickar på **egenskaper**.
3. Klicka på den **AlwaysOn hög tillgänglighet** fliken och markera sedan **aktivera AlwaysOn Availability Groups**enligt följande:

    ![Aktivera AlwaysOn-Tillgänglighetsgrupper](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/54-enableAlwaysOn.png)

4. Klicka på **Använd**. Klicka på **OK** i popup-fönstret.

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
7. I **Object Explorer**, högerklicka på **databaser** och på **ny databas**.
8. I **databasnamnet**, typen **MyDB1**, klicka på **OK**.

### <a name="backupshare"></a>Skapa en säkerhetskopia

1. På den första SQL-servern i **Serverhanteraren**, klickar du på **verktyg**. Öppna **Datorhantering**.

1. Klicka på **delade mappar**.

1. Högerklicka på **resurser**, och klicka på **ny resurs...** .

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/48-newshare.png)

   Använd **guiden Skapa en delad mapp** att skapa en resurs.

1. På **mappsökväg**, klickar du på **Bläddra** och hitta eller skapa en sökväg för delad mapp för säkerhetskopiering. Klicka på **Nästa**.

1. I **namn, beskrivning och inställningar** Kontrollera namn och sökväg. Klicka på **Nästa**.

1. På **delade mappbehörigheter** ange **anpassa behörigheter**. Klicka på **anpassad...** .

1. På **anpassa behörigheter**, klickar du på **Lägg till...** .

1. Kontrollera att SQL Server och SQL Server Agent-tjänstkontona för båda servrarna har fullständig behörighet.

   ![Ny resurs](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/68-backupsharepermission.png)

1. Klicka på **OK**.

1. I **delade mappbehörigheter**, klickar du på **Slutför**. Klicka på **Slutför** igen.  

### <a name="take-a-full-backup-of-the-database"></a>Ta en fullständig säkerhetskopiering av databasen

Du måste säkerhetskopiera den nya databasen att initiera loggkedjan. Om du inte gör en säkerhetskopia av den nya databasen kan inte ingå i en tillgänglighetsgrupp.

1. I **Object Explorer**, högerklicka på databasen, peka på **aktiviteter...** , klickar du på **säkerhetskopiera**.

1. Klicka på **OK** göra en fullständig säkerhetskopiering till standardplatsen för säkerhetskopiering.

## <a name="create-the-availability-group"></a>Skapa Tillgänglighetsgruppen
Du är nu redo att konfigurera en tillgänglighetsgrupp med följande steg:

* Skapa en databas på den första SQL-servern.
* Ta både en fullständig säkerhetskopia och en transaktion säkerhetskopia av databasen
* Återställa hela och loggsäkerhetskopior till andra SQL-servern med den **NORECOVERY** alternativet
* Skapa Tillgänglighetsgruppen (**AG1**) med synkront genomförande och automatisk redundans läsbara sekundära repliker

### <a name="create-the-availability-group"></a>Skapa Tillgänglighetsgruppen:

1. På fjärrskrivbords-session till den första SQL-servern. I **Object Explorer** i SSMS, högerklickar du på **AlwaysOn hög tillgänglighet** och på **guiden Ny tillgänglighetsgrupp**.

    ![Starta guiden Ny tillgänglighetsgrupp](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/56-newagwiz.png)

2. I den **introduktion** klickar du på **nästa**. I den **ange tillgänglighetsgruppens namn** , ange ett namn för Tillgänglighetsgruppen, till exempel **AG1**i **tillgänglighetsgruppens namn**. Klicka på **Nästa**.

    ![Guiden Ny AG, ange AG-namn](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/58-newagname.png)

3. I den **Välj databaser** väljer du databasen och klicka på **nästa**.

   >[!NOTE]
   >Databasen uppfyller kraven för en tillgänglighetsgrupp eftersom du har utfört en fullständig säkerhetskopiering på den avsedda primära repliken.

   ![Guiden Ny AG, Välj databaser](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/60-newagselectdatabase.png)
4. I den **ange repliker** klickar du på **lägga till replik**.

   ![Guiden Ny AG, ange repliker](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/62-newagaddreplica.png)
5. Den **Anslut till Server** dialogrutan som öppnas. Skriv namnet på den andra servern i **servernamn**. Klicka på **Anslut**.

   I den **ange repliker** sidan du bör nu se den andra servern i **Tillgänglighetsrepliker**. Konfigurera replikerna på följande sätt.

   ![Guiden Ny AG, ange repliker (fullständig)](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/64-newagreplica.png)

6. Klicka på **slutpunkter** att se slutpunkten för databasspegling för den här Tillgänglighetsgruppen. Använd samma port som du använde när du ställer in den [brandväggsregel för slutpunkter för databasspegling](virtual-machines-windows-portal-sql-availability-group-prereq.md#endpoint-firewall).

    ![Guiden Ny AG, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/66-endpoint.png)

8. I den **Välj inledande datasynkronisering** väljer **fullständig** och ange en plats i nätverket. Plats, använder den [säkerhetskopieringsresursen du skapade](#backupshare). I exemplet som den var **\\\\\<första SQL Server\>\Backup\**. Klicka på **Nästa**.

   >[!NOTE]
   >Fullständig synkronisering tar en fullständig säkerhetskopiering av databasen på den första instansen av SQL Server och återställer det till den andra instansen. För stora databaser rekommenderas fullständig synkronisering inte eftersom det kan ta lång tid. Du kan minska nu genom att manuellt ta en säkerhetskopia av databasen och återställer dem med `NO RECOVERY`. Om databasen är redan återställd med `NO RECOVERY` på andra SQL Server innan du konfigurerar Tillgänglighetsgruppen, Välj **Anslut endast**. Om du vill säkerhetskopiera när du har konfigurerat Tillgänglighetsgruppen, Välj **hoppa över inledande datasynkronisering**.

    ![Guiden Ny AG, Välj inledande datasynkronisering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/70-datasynchronization.png)

9. I den **validering** klickar du på **nästa**. Den här sidan bör likna följande bild:

    ![Guiden Ny AG, validering](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/72-validation.png)

    >[!NOTE]
    >Det finns en varning för Lyssnarkonfigurationen eftersom du inte har konfigurerat en tillgänglighetsgruppens lyssnare. Du kan ignorera den här varningen eftersom på Azure virtual machines lyssnaren skapa när du har skapat Azure belastningsutjämnare.

10. I den **sammanfattning** klickar du på **Slutför**, vänta medan guiden konfigurerar nya Tillgänglighetsgruppen. I den **förlopp** sidan som du kan klicka på **mer** att visa detaljerad förloppet. När guiden har slutförts kan du granska den **resultat** sidan för att kontrollera att Tillgänglighetsgruppen har skapats.

     ![Guiden för ny AG resultat](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/74-results.png)
11. Klicka på **Stäng** vill avsluta guiden.

### <a name="check-the-availability-group"></a>Kontrollera Tillgänglighetsgruppen

1. I **Object Explorer**, expandera **AlwaysOn hög tillgänglighet**, expandera **Tillgänglighetsgrupper**. Du bör nu se nya Tillgänglighetsgruppen i den här behållaren. Högerklicka på Tillgänglighetsgruppen och på **visa instrumentpanelen**.

   ![Visa AG-instrumentpanelen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/76-showdashboard.png)

   Din **AlwaysOn instrumentpanelen** bör se ut ungefär så här.

   ![AG-instrumentpanelen](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/78-agdashboard.png)

   Du kan se replikerna, redundansläge för varje replik- och tillstånd för synkronisering.

2. I **Klusterhanteraren**, klicka på ditt kluster. Välj **roller**. Tillgänglighetsgruppens namn som du använde är en roll i klustret. Att Tillgänglighetsgruppen inte har en IP-adress för klientanslutningar, eftersom du inte konfigurerar en lyssnare. Du konfigurerar lyssnaren när du har skapat en Azure belastningsutjämnare.

   ![AG i hanteraren för redundanskluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/80-clustermanager.png)

   > [!WARNING]
   > Försök inte att redundansväxla Tillgänglighetsgruppen från hanteraren för redundanskluster. Alla redundansåtgärder ska utföras inifrån **AlwaysOn instrumentpanelen** i SSMS. Mer information finns i [begränsningar på med hjälp av i hanteraren för redundanskluster med Tillgänglighetsgrupper](https://msdn.microsoft.com/library/ff929171.aspx).
    >

Du har nu en tillgänglighetsgrupp med repliker på två instanser av SQL Server. Du kan flytta Tillgänglighetsgruppen mellan instanser. Du kan inte ansluta till Tillgänglighetsgruppen ännu eftersom du inte har en lyssnare. I Azure-datorer kräver lyssnaren en belastningsutjämnare. Nästa steg är att skapa belastningsutjämnaren i Azure.

<a name="configure-internal-load-balancer"></a>

## <a name="create-an-azure-load-balancer"></a>Skapa en Azure belastningsutjämnare

På Azure virtual machines kräver en belastningsutjämnare i en SQL Server-tillgänglighetsgrupp. Belastningsutjämnaren innehåller IP-adressen för tillgänglighetsgruppens lyssnare. Det här avsnittet beskrivs hur du skapar belastningsutjämnaren i Azure-portalen.

1. I Azure-portalen går du till resursgruppen där din SQL-servrar är och på **+ Lägg till**.
2. Sök efter **belastningsutjämnare**. Välj belastningsutjämnaren som publicerats av Microsoft.

   ![AG i hanteraren för redundanskluster](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/82-azureloadbalancer.png)

1.  Klicka på **Skapa**.
3. Konfigurera följande parametrar för belastningsutjämnaren.

   | Inställning | Fält |
   | --- | --- |
   | **Namn** |Använda ett namn för belastningsutjämnaren, till exempel **sqlLB**. |
   | **Typ** |internt |
   | **Virtuellt nätverk** |Använd namnet på virtuella Azure-nätverket. |
   | **Undernät** |Använd namnet på det undernät som den virtuella datorn finns i.  |
   | **IP-adresstilldelning** |Statisk |
   | **IP-adress** |Använd en tillgänglig adress från undernätet. |
   | **Prenumeration** |Använd samma prenumeration som den virtuella datorn. |
   | **Plats** |Använd samma plats som den virtuella datorn. |

   Azure portalbladet bör se ut så här:

   ![Skapa belastningsutjämnare](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/84-createloadbalancer.png)

1. Klicka på **skapa**, för att skapa belastningsutjämnaren.

Om du vill konfigurera belastningsutjämnaren måste du skapa en serverdelspool, en avsökning och ange regler för belastningsutjämning. Gör följande i Azure-portalen.

### <a name="add-backend-pool"></a>Lägg till serverdelspoolen

1. Gå till tillgänglighetsgruppen i Azure-portalen. Du kan behöva uppdatera vyn för att se nyligen skapade belastningsutjämnaren.

   ![Hitta belastningsutjämnare i resursgruppen.](./media/virtual-machines-windows-portal-sql-availability-group-tutorial/86-findloadbalancer.png)

1. Klicka på belastningsutjämnaren, **serverdelspooler**, och klicka på **+ Lägg till**. Ange serverdelspoolen på följande sätt:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Skriv ett namn | SQLLBBE
   | **Tillhör** | Välj från listan | Tillgänglighetsuppsättning
   | **Tillgänglighetsuppsättning** | Använd ett namn på tillgänglighetsuppsättning som din SQL Server-datorer finns i | sqlAvailabilitySet |
   | **Virtuella datorer** |Två Azure SQL Server-VM-namn | SQLServer-0, sqlserver-1

1. Ange namn för backend-adresspool.

1. Klicka på **+ Lägg till en virtuell dator**.

1. För tillgänglighetsuppsättningen, Välj tillgängligheten att SQL-servrar finns i.

1. För virtuella datorer, ta med både SQL-servrar. Inkludera inte vittne för servern.

1. Klicka på **OK** att skapa serverdelspoolen.

### <a name="set-the-probe"></a>Ange avsökningen

1. Klicka på belastningsutjämnaren, **hälsoavsökning**, och klicka på **+ Lägg till**.

1. Ange hälsoavsökningen enligt följande:

   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointProbe |
   | **Protokoll** | Välj TCP | TCP |
   | **Port** | Alla oanvända portar | 59999 |
   | **Intervall**  | Tidslängd mellan avsökningsförsök i sekunder |5 |
   | **Tröskelvärde för ohälsosamt värde** | Antalet avsökningsfel som måste vidtas för en virtuell dator för att ses som ohälsosamt  | 2 |

1. Klicka på **OK** att ange avsökningen hälsa.

### <a name="set-the-load-balancing-rules"></a>Ange regler för belastningsutjämning

1. Klicka på belastningsutjämnaren, **belastningsutjämningsregler**, och klicka på **+ Lägg till**.

1. Ange belastningsutjämning regler på följande sätt.
   | Inställning | Beskrivning | Exempel
   | --- | --- |---
   | **Namn** | Text | SQLAlwaysOnEndPointListener |
   | **Frontend-IP-adress** | Välj en adress |Använd den adress som du skapade när du skapade belastningsutjämnaren. |
   | **Protokoll** | Välj TCP |TCP |
   | **Port** | Använd port för SQL Server-instansen | 1433 |
   | **Backend-Port** | Det här fältet används inte när flytande IP är inställd för direkta servern returnerade | 1433 |
   | **Avsökningen** |Det namn du angav för avsökningen | SQLAlwaysOnEndPointProbe |
   | **Persistence för session** | Listrutan | **Ingen** |
   | **Inaktivitetstid** | Minuter att öppna en TCP-anslutning | 4 |
   | **Flytande IP (direkt serverreturnering)** | |Enabled |

   > [!WARNING]
   > Direkt serverreturnering anges när du skapar. Det går inte att ändra.

1. Klicka på **OK** att ange regler för belastningsutjämning.

## <a name="configure-listener"></a>Konfigurera lyssnaren

Nästa du behöver göra är att konfigurera en Availability Group-lyssnare för redundanskluster.

> [!NOTE]
> Den här kursen visar hur du skapar en enda lyssnare - med en ILB IP-adress. För att skapa en lyssnare med hjälp av en eller flera IP-adresser, se [Create Availability Group listener och belastningsutjämnare | Azure](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
>
>

[!INCLUDE [ag-listener-configure](../../../../includes/virtual-machines-ag-listener-configure.md)]

## <a name="set-listener-port"></a>Ange lyssningsport

Ange porten för lyssnare i SQL Server Management Studio.

1. Starta SQL Server Management Studio och Anslut till den primära repliken.

1. Gå till **AlwaysOn hög tillgänglighet** | **Tillgänglighetsgrupper** | **tillgänglighetsgruppens lyssnare**.

1. Du bör nu se lyssnare namnet som du skapade i hanteraren för redundanskluster. Högerklicka på grupplyssnarens namn och på **egenskaper**.

1. I den **Port** anger du portnumret för tillgänglighetsgruppens lyssnare med hjälp av $EndpointPort du använde tidigare (1433 var standard), klicka på **OK**.

Nu har du en SQL Server-tillgänglighetsgrupp i Azure virtuella datorer som körs i läget Resource Manager.

## <a name="test-connection-to-listener"></a>Testa anslutning till lyssnare

Att testa anslutningen:

1. RDP till en SQL-Server som är i samma virtuella nätverk, men inte äger repliken. Du kan använda SQL Server i klustret.

1. Använd **sqlcmd** verktyg för att testa anslutningen. Till exempel följande skript upprättar en **sqlcmd** anslutning till den primära repliken via lyssnaren med Windows-autentisering:

    ```
    sqlcmd -S <listenerName> -E
    ```

    Om lyssnaren använder en annan port än standard port (1433), ange porten i anslutningssträngen. Till exempel ansluter kommandot sqlcmd till en lyssnare på port 1435:

    ```
    sqlcmd -S <listenerName>,1435 -E
    ```

SQLCMD anslutning ansluter automatiskt till instansen av SQL Server är värd för den primära repliken.

> [!TIP]
> Kontrollera att den port som du anger är öppen i brandväggen för både SQL-servrar. Båda servrarna kräver en inkommande regel för TCP-porten som du använder. Mer information finns i [Lägg till eller redigera brandväggsregel](http://technet.microsoft.com/library/cc753558.aspx).
>
>



<!--**Notes**: *Notes provide just-in-time info: A Note is “by the way” info, an Important is info users need to complete a task, Tip is for shortcuts. Don’t overdo*.-->


<!--**Procedures**: *This is the second “step." They often include substeps. Again, use a short title that tells users what they’ll do*. *("Configure a new web project.")*-->

<!--**UI**: *Note the format for documenting the UI: bold for UI elements and arrow keys for sequence. (Ex. Click **File > New > Project**.)*-->

<!--**Screenshot**: *Screenshots really help users. But don’t include too many since they’re difficult to maintain. Highlight areas you are referring to in red.*-->

<!--**No. of steps**: *Make sure the number of steps within a procedure is 10 or fewer. Seven steps is ideal. Break up long procedure logically.*-->


<!--**Next steps**: *Reiterate what users have done, and give them interesting and useful next steps so they want to go on.*-->

## <a name="next-steps"></a>Nästa steg

- [Lägg till en IP-adress till en belastningsutjämnare för en andra tillgänglighetsgruppen](virtual-machines-windows-portal-sql-ps-alwayson-int-listener.md#Add-IP).
