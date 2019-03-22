---
title: SQL Server Business Intelligence | Microsoft Docs
description: Det här avsnittet använder resurser som har skapats med den klassiska distributionsmodellen och beskriver funktionerna för Business Intelligence (BI) som är tillgängliga för SQL Server som körs på Azure Virtual Machines (VM).
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: c681e7a7-eeda-48aa-bc35-6277f4828244
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 05/30/2017
ms.author: maghan
ms.openlocfilehash: 29e851772e665b4130ee58b04c264d55bcd54523
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/21/2019
ms.locfileid: "58317790"
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence på Azure Virtuella datorer
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller som används för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Microsoft Azure-dator-galleriet innehåller avbildningar som innehåller SQL Server-installationer. SQL Server-versioner som stöds i galleriavbildningar är samma installationsfilerna som du kan installera på lokala datorer och virtuella datorer. Det här avsnittet sammanfattas de SQL Server Business Intelligence (BI)-funktioner som installeras på bilderna och konfigurationssteg som krävs när en virtuell dator har etablerats. Det här avsnittet beskriver också distributionstopologier som stöds för BI-funktioner och bästa praxis.

## <a name="license-considerations"></a>Överväganden för licens
Det finns två sätt att licens för SQL Server i Microsoft Azure Virtual Machines:

1. License mobility-förmånerna som är del av Software Assurance. Mer information finns i [License Mobility genom Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Betala per timme av Azure-datorer med SQL Server installerat. Se avsnittet ”SQL Server” i [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Mer information om licensiering och aktuella priser finns i [virtuella datorer vanliga frågor om licensiering](https://azure.microsoft.com/pricing/licensing-faq/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server-avbildningar i galleriet för Azure virtuella datorer
Microsoft Azure-dator-galleriet innehåller flera avbildningar som innehåller Microsoft SQL Server. Program som installeras på virtuella-bilder varierar beroende på version av operativsystemet och versionen av SQL Server. Lista över avbildningarna i Azure VM-galleriet ändras ofta.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL-avbildning i Virtuella Azure-galleriet](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Följande PowerShell-skriptet returnerar listan över Azure-avbildningar som innehåller ”SQL Server” i avbildningen:

    # assumes you have already uploaded a management certificate to your Microsoft Azure Subscription. View the thumbprint value from the "Subscriptions" menu in Azure portal.

    $subscriptionID = ""    # REQUIRED: Provide your subscription ID.
    $subscriptionName = "" # REQUIRED: Provide your subscription name.
    $thumbPrint = "" # REQUIRED: Provide your certificate thumbprint.
    $certificate = Get-Item cert:\currentuser\my\$thumbPrint # REQUIRED: If your certificate is in a different store, provide it here.-Ser  store is the one specified with the -ss parameter on MakeCert

    Set-AzureSubscription -SubscriptionName $subscriptionName -Certificate $certificate -SubscriptionID $subscriptionID

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2016"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2016*"} | select imagename,category, location, label, description

    Write-Host -foregroundcolor green "List of available gallery images where imagename contains 2014"
    Write-Host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
    get-azurevmimage | where {$_.ImageName -Like "*SQL-Server-2014*"} | select imagename,category, location, label, description

Mer information om versioner och funktioner som stöds av SQL Server finns i följande:

* [SQL Server-versioner](https://www.microsoft.com/sql-server/sql-server-2017-editions)
* [Funktioner som stöds i olika utgåvor av SQLServer 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI funktioner som installeras på de SQL Server-avbildningarna för galleriet för virtuella datorer
I följande tabell sammanfattas de Business Intelligence-funktioner som installeras på de vanliga galleriavbildningar för Microsoft Azure-dator för SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| SQL Server BI-funktion | Installerad på galleriet bilden | Anteckningar |
| --- | --- | --- |
| **Reporting Services enhetligt läge** |Ja |Installerad men kräver konfiguration, inklusive URL: en för report manager. Se avsnittet [konfigurera Reporting Services](#configure-reporting-services). |
| **Reporting Services SharePoint Mode** |Nej |Microsoft Azure-dator galleriet bilden inte innehåller SharePoint- eller SharePoint installationsfilerna. <sup>1</sup> |
| **Flerdimensionella Analysis Services och Data-utvinning (OLAP)** |Ja |Installerat och konfigurerat som standard Analysis Services-instans |
| **Analysis Services Tabular** |Nej |Stöds i SQL Server 2012, installeras 2014 och 2016-avbildningar, men det inte som standard. Installera en annan instans av Analysis Services. Finns i avsnittet installera andra SQL Server-tjänster och funktioner i det här avsnittet. |
| **Analysis Services PowerPivot för SharePoint** |Nej |Microsoft Azure-dator galleriet bilden inte innehåller SharePoint- eller SharePoint installationsfilerna. <sup>1</sup> |

<sup>1</sup> ytterligare information om SharePoint och Azure-datorer finns i [Microsoft Azure-arkitekturer för SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) och [SharePoint-distributionen på Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Kör följande PowerShell-kommando för att hämta en lista över installerade produkter som innehåller ”SQL” i namnet på tjänsten.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Allmänna rekommendationer och bästa praxis
* Minst rekommenderade storleken för en virtuell dator är **A3** när du använder SQL Server Enterprise Edition. Den **A4** VM-storlek som rekommenderas för SQL Server BI distributioner av Analysis Services och Reporting Services.
  
    Information om de aktuella storlekarna för virtuella datorer finns i [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Bästa praxis för Diskhantering är att lagra data, logg och säkerhetskopierade filer på enheter än **C**: och **D**:. Till exempel skapa datadiskar **E**: och **F**:.
  
  * Enheten Cachelagringsprincip för standardenheten **C**: är inte optimalt för att arbeta med data.
  * Den **D**: enhet är en tillfällig enhet som används främst för växlingsfilen. Den **D**: enheten är inte beständiga och sparas inte i blob storage. Återställ storleken på hanteringsuppgifter, till exempel en ändring av den virtuella datorn i **D**: enhet. Det rekommenderas att **inte** använder den **D**: enhet för databasfiler, inklusive tempdb.
    
    Läs mer om att skapa och koppla diskar [hur du ansluter en datadisk till en virtuell dator](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Stoppa eller avinstallera tjänster som du inte tänker använda. För exempel om den virtuella datorn endast används för Reporting Services, stoppa eller avinstallera Analysis Services och SQL Server Integration Services. Följande bild är ett exempel på de tjänster som startas som standard.
  
    ![SQL Server-tjänster](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > SQL Server database engine krävs i de BI-scenarierna som stöds. I en enda server VM-topologi krävs database engine för att köras på samma virtuella dator.
  
    Mer information finns i följande: [Avinstallera Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) och [avinstallera en instans av Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Kontrollera **Windows Update** för nya ”viktiga uppdateringar”. Microsoft Azure VM-avbildningar uppdateras ofta; dock viktiga uppdateringar kan bli tillgänglig från **Windows Update** när avbildningen senast uppdaterades.

## <a name="example-deployment-topologies"></a>Exempeltopologier för distribution
Följande är exempeldistributioner som använder Microsoft Azure Virtual Machines. Topologier i dessa diagram är endast några av de möjliga topologier som du kan använda med SQL Server BI-funktioner och Microsoft Azure Virtual Machines.

### <a name="single-virtual-machine"></a>Enstaka virtuell dator
Analysis Services, Reporting Services, SQL Server Database Engine och datakällor på en virtuell dator.

![BI IAS-scenario med 1 virtuell dator](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Två virtuella datorer
* Analysis Services, Reporting Services och SQL Server Database Engine på en virtuell dator. Den här distributionen omfattar databaser för rapportservern.
* Datakällor på en andra virtuell dator. Den andra virtuella datorn innehåller SQL Server Database Engine som en datakälla.

![BI iaas-scenario med 2 virtuella datorer](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Blandade Azure – data på Azure SQL-databas
* Analysis Services, Reporting Services och SQL Server Database Engine på en virtuell dator. Den här distributionen omfattar databaser för rapportservern.
* Datakällan är Azure SQL-databas.

![virtuell dator BI iaas-scenarier och AzureSQL som datakälla](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrid – data lagras lokalt
* I den här exempeldistributionen Analysis Services körs Reporting Services, SQL Server Database Engine på en virtuell dator. Den virtuella datorn är värd för rapportserverdatabaserna. Den virtuella datorn är ansluten till en lokal domän via Azure virtuellt nätverk eller några andra VPN-tunnel lösning.
* Datakällan är på plats.

![BI iaas scenarier vm och lokala datakällor](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Konfiguration av Reporting Services enhetligt läge
Bild för VM-galleriet för SQL Server innehåller Reporting Services i enhetligt läge installerat, men rapportservern inte har konfigurerats. Stegen i det här avsnittet Konfigurera Reporting Services-rapportserver. Mer detaljerad information om hur du konfigurerar Reporting Services i enhetligt läge finns i [installera Reporting Services interna Mode Report Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Liknande innehåll som använder Windows PowerShell-skript för att konfigurera rapportservern, se [Använd PowerShell för att skapa en Azure virtuell dator med ett Native Mode Report Server](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Anslut till den virtuella datorn och starta konfigurationshanteraren för Reporting Services
Det finns två vanliga arbetsflöden för att ansluta till en Azure-dator:

* Att ansluta klickar du på namnet på den virtuella datorn och klicka sedan på **Connect**. En anslutning till fjärrskrivbord öppnas och datornamnet fylls i automatiskt.
  
    ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Anslut till den virtuella datorn med Windows anslutning till fjärrskrivbord. I användargränssnittet för fjärrskrivbordet:
  
  1. Skriv den **molntjänstnamnet** som datornamn.
  2. Skriv kolon (:) och det offentliga portnumret som har konfigurerats för TCP fjärrskrivbordsslutpunkt.
     
      Myservice.cloudapp.net:63133
     
      Mer information finns i [vad är en molnbaserad tjänst?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Starta Reporting Services Configuration Manager**

I **Windows Server 2012/2016**:

1. Från den **starta** skärmen, Skriv **Reporting Services** att se en lista över appar.
2. Högerklicka på **Konfigurationshanteraren för Reporting Services** och klicka på **kör som administratör**.

I **Windows Server 2008 R2**:

1. Klicka på **starta**, och klicka sedan på **alla program**.
2. Klicka på **Microsoft SQL Server 2016**.
3. Klicka på **konfigurationsverktyg**.
4. Högerklicka på **Konfigurationshanteraren för Reporting Services** och klicka på **kör som administratör**.

Eller:

1. Klicka på **Starta**.
2. I den **Sök program och filer** dialogrutan skriver **Rapporteringstjänster**. Om den virtuella datorn kör Windows Server 2012, Skriv **Rapporteringstjänster** på startskärmen i Windows Server 2012.
3. Högerklicka på **Konfigurationshanteraren för Reporting Services** och klicka på **kör som administratör**.
   
    ![Sök efter ssrs configuration manager](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurera Reporting Services
**Tjänstkontot och webbtjänst-URL:**

1. Kontrollera den **servernamn** är den lokala servernamn och klicka på **Connect**.
2. Observera du tom **Report Server-databasnamn**. Databasen skapas när konfigurationen är klar.
3. Kontrollera den **Report Server Status** är **startad**. Om du vill kontrollera tjänsten i Windows Server Manager, är tjänsten den **SQL Server Reporting Services** Windows-tjänsten.
4. Klicka på **tjänstkonto** och ändrar kontot efter behov. Om den virtuella datorn används i en icke-domänansluten domänmiljö, inbyggt **ReportServer** konto räcker. Mer information om kontot finns i [tjänstkonto](https://msdn.microsoft.com/library/ms189964.aspx).
5. Klicka på **Webbtjänstadress** i den vänstra rutan.
6. Klicka på **tillämpa** konfigurera standardvärden.
7. Obs den **rapportera Server webbadresser till tjänsten**. Observera att TCP-standardporten är 80 och är en del av URL: en. I ett senare steg skapar du en Microsoft Azure VM-slutpunkt för porten.
8. I den **resultat** rutan kontrollerar du de åtgärder som har slutförts.

**Databas:**

1. Klicka på **databasen** i den vänstra rutan.
2. Klicka på **ändra databasen**.
3. Kontrollera **skapa en ny rapportserverdatabas** är markerad och klicka sedan på Nästa.
4. Kontrollera **servernamn** och klicka på **Testanslutningen**.
5. Om resultatet är **Anslutningstestet lyckades**, klickar du på **OK** och klicka sedan på **nästa**.
6. Observera att databasnamnet är **ReportServer** och **Report Server mode** är **interna** klickar **nästa**.
7. Klicka på **nästa** på den **autentiseringsuppgifter** sidan.
8. Klicka på **nästa** på den **sammanfattning** sidan.
9. Klicka på **nästa** på den **vidare och slutför** sidan.

**Web Portal-URL eller URL för Report Manager för 2012 och 2014:**

1. Klicka på **webbportalens URL**, eller **URL för Report Manager** för 2014 och 2012 i den vänstra rutan.
2. Klicka på **Verkställ**.
3. I den **resultat** rutan kontrollerar du de åtgärder som har slutförts.
4. Klicka på **avsluta**.

Information om report server-behörigheter finns i [bevilja behörigheter på en Native Mode Report Server](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Bläddra till lokala Report Manager
Kontrollera konfigurationen genom att bläddra till report manager på den virtuella datorn.

1. Starta Internet Explorer med administratörsbehörighet på den virtuella datorn.
2. Bläddra till http: \/ /localhost/rapporter på den virtuella datorn.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Att ansluta till fjärranslutna webbportal eller Report Manager för 2014 och 2012
Om du vill ansluta till webbportalen eller Report Manager för 2014 och 2012 på den virtuella datorn från en fjärrdator, kan du skapa en ny virtuell dator TCP-slutpunkt. Som standard rapportservern lyssnar efter HTTP-begäranden på **port 80**. Om du konfigurerar report server URL: er för att använda en annan port måste du ange det portnummer i följande anvisningar.

1. Skapa en slutpunkt för den virtuella datorn på TCP-Port 80. Mer information finns i, [virtuella datorers slutpunkter och brandväggsportar](#virtual-machine-endpoints-and-firewall-ports) i det här dokumentet.
2. Öppna port 80 i brandväggen för den virtuella datorn.
3. Bläddra till webbportalen, eller rapportera manager, med hjälp av Azure-dator **DNS-namnet** som servernamn i URL: en. Exempel:
   
    **Rapportservern**: http://uebi.cloudapp.net/reportserver  **Webbportalen**: http://uebi.cloudapp.net/reports
   
    [Konfigurera en brandvägg för Reportserveråtkomst](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Skapa och publicera rapporter till Azure-dator
I följande tabell sammanfattas några av de tillgängliga alternativ för att publicera befintliga rapporter från en lokal dator till rapportservern finns på Microsoft Azure-dator:

* **Report Builder**: Den virtuella datorn innehåller klicka-en gång version av Microsoft SQL Server Report Builder för SQL 2014 och 2012. Starta Report builder först gången på den virtuella datorn med SQL 2016:
  
  1. Starta din webbläsare med administrativa privilegier.
  2. Bläddra till webbportalen, på den virtuella datorn och välj den **hämta** ikonen i det övre högra hörnet.
  3. Välj **Report Builder**.
     
     Mer information finns i [starta Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM:  SQL Server Data Tools är installerat på den virtuella datorn och kan användas för att skapa **Report Server-projekt** och rapporter på den virtuella datorn. SQL Server Data Tools kan publicera rapporter till rapportservern på den virtuella datorn.
* **SQL Server Data Tools: Remote**:  Skapa en Reporting Services-projekt i SQL Server Data Tools som innehåller Reporting Services-rapporter på din lokala dator. Konfigurera projektet för att ansluta till webbtjänstens URL.
  
    ![SSDT Projektegenskaper för SSRS-projekt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Skapa en. VHD-hårddisk som innehåller rapporter över och sedan ladda upp och ansluta enheten.
  
  1. Skapa en. VHD-hårddisk på den lokala datorn som innehåller dina rapporter.
  2. Skapa och installera ett certifikat.
  3. Överför VHD-filen till Azure med hjälp av cmdleten Add-AzureVHD [skapa och ladda upp en Windows Server VHD till Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Koppla disken till den virtuella datorn.

## <a name="install-other-sql-server-services-and-features"></a>Installera andra SQL Server-tjänster och funktioner
Om du vill installera ytterligare SQL Server-tjänster, till exempel Analysis Services i tabelläge, kör du installationsguiden för SQL server. Installationsfilerna finns på den virtuella datorns hårddisk.

1. Klicka på **starta** och klicka sedan på **alla program**.
2. Klicka på **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** eller **Microsoft SQL Server 2012** och klicka sedan på **konfigurationsverktyg** .
3. Klicka på **SQL Server Installationscenter**.

Eller kör C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe eller C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Första gången du kör installationsprogrammet för SQL Server, flera filer för installationsprogrammet kan laddas ned och kräver en omstart av den virtuella datorn och en omstart av installationsprogrammet för SQL Server.
> 
> Om du vill anpassa upprepade gånger den avbildning som valt från Microsoft Azure-dator kan du skapa din egen SQL Server-avbildning. Analysis Services SysPrep funktioner har aktiverats med SQL Server 2012 SP1 CU2. Mer information finns i [överväganden för att installera SQL Server med hjälp av SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) och [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Om du vill installera Analysis Services Tabular-läge
Stegen i det här avsnittet **sammanfatta** installationen av Analysis Services tabular-läge. Mer information finns i följande:

* [Installera Analysis Services i tabelläge](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabellmodellering (självstudiekurs för Adventure Works)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Så här installerar Analysis Services Tabular-läge:**

1. I installationsguiden för SQL Server, klickar du på **Installation** i den vänstra rutan och klicka sedan på **fristående nya SQL server-installation eller Lägg till funktioner till en befintlig installation**.
   
   * Om du ser den **Bläddra efter mapp**bläddrar du till c:\SQLServer_13.0_full, c:\SQLServer_12.0_full eller c:\SQLServer_11.0_full och klicka sedan på **Ok**.
2. Klicka på **nästa** på produktsidan för uppdateringar.
3. På den **installationstyp** väljer **utför en ny installation av SQL Server** och klicka på **nästa**.
4. På den **konfigurera roll** klickar du på **Installation av SQL Server-funktioner**.
5. På den **Funktionsurval** klickar du på **Analysis Services**.
6. På den **instanskonfiguration** anger du ett beskrivande namn, till exempel **Tabelluppgraderingen** till **med namnet instans** och **instans-Id** textrutor .
7. På den **Analysis Services-konfiguration** väljer **tabelläge**. Lägga till den aktuella användaren i listan med administrativa behörigheter.
8. Slutför och Stäng guiden Installera SQL Server.

## <a name="analysis-services-configuration"></a>Analysis Services-konfiguration
### <a name="remote-access-to-analysis-services-server"></a>Fjärråtkomst till Analysis Services-servern
Analysis Services-servern stöder endast windows-autentisering. Om du vill få åtkomst till Analysis Services via en fjärranslutning från klientprogram, till exempel SQL Server Management Studio eller SQL Server Data Tools, måste den virtuella datorn är ansluten till din lokala domän, med hjälp av Azure virtuella nätverk. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

En **standardinstansen** av Analysis Services som lyssnar på TCP-port **2383**. Öppna porten i brandväggen för virtuella datorer. En klustrad namngiven instans av Analysis Services också lyssnar på port **2383**.

För en **namngiven instans** av Analysis Services, SQL Server Browser-tjänsten som krävs för att hantera åtkomst till port. Standardkonfigurationen för SQL Server Browser är port **2382**.

Öppna port i brandväggen för virtuella datorer, **2382** och skapa en statisk Analysis Services-namngiven instans port.

1. Kontrollera portar som redan används på den virtuella datorn och vilken process som använder portarna med följande kommando med administratörsbehörighet:
   
        netstat /ao
2. Använd SQL Server Management Studio för att skapa en statisk Analysis Services namngivna instans port genom att uppdatera ”Port” värdet i tabellform AS instans allmänna egenskaper. Mer information finns i ”använda en fast port för en standard eller namngiven instans” i [konfigurera Windows-brandväggen för att tillåta Analysis Services-åtkomst](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Tjänsten Analysis Services tabular-instansen startas om.

Mer information finns i, **virtuella datorers slutpunkter och brandväggsportar** i det här dokumentet.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtuella datorers slutpunkter och brandväggsportar
Det här avsnittet sammanfattas Microsoft Azure VM-slutpunkter för att skapa och portar som ska öppnas i VM-brandväggar. I följande tabell sammanfattas de **TCP** portar för att skapa slutpunkter för och portarna som ska öppnas i brandväggen för virtuella datorer.

* Om du använder en enda virtuell dator och följande poster är true, du behöver inte skapa VM-slutpunkter och du behöver inte öppna portar i brandväggen på den virtuella datorn.
  
  * Du inte fjärransluta till SQL Server-funktioner på den virtuella datorn. Etablera en fjärrskrivbordsanslutning till den virtuella datorn och få åtkomst till SQL Server-funktioner lokalt på den virtuella datorn anses inte en anslutning till SQL Server-funktioner.
  * Den virtuella datorn Anslut inte till en lokal domän via Azure virtuellt nätverk eller en annan lösning för VPN-tunnlar.
* Om den virtuella datorn inte är ansluten till en domän, men du vill via fjärranslutning kan du ansluta till SQL Server-funktioner på den virtuella datorn:
  
  * Öppna portar i brandväggen på den virtuella datorn.
  * Skapa virtuella datorers slutpunkter för meddelanden portar (*).
* Om den virtuella datorn är ansluten till en domän med en VPN-tunnel, till exempel Azure virtuellt nätverk, sedan är slutpunkterna inte obligatoriska. Men öppna portarna i brandväggen på den virtuella datorn.
  
  | Port | Type | Beskrivning |
  | --- | --- | --- |
  | **80** |TCP |Rapportserver fjärråtkomst (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Detta krävs när den virtuella datorn i ansluten till en domän. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Standardinstansen för SQL Server Analysis Services och klustrade namngivna instanser. |
  | **användardefinierad** |TCP |Skapa en statisk Analysis Services-namngiven instans port för ett portnummer som du väljer och avblockera portnumret i brandväggen. |

Mer information om hur du skapar slutpunkter finns i:

* Skapa slutpunkter:[så här konfigurerar du slutpunkter till en virtuell dator](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQL Server: Se avsnittet ”hela konfigurationen steg för att ansluta till den virtuella datorn med hjälp av SQL Server Management Studio” i [etablera en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Följande diagram illustrerar portarna som ska öppnas i VM-brandväggen att tillåta fjärråtkomst till funktioner och komponenter på den virtuella datorn.

![portar som ska öppnas för bi program i virtuella Azure-datorer](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Resurser
* Supportpolicyn för Microsoft-serverprogramvara i Azure VM-miljön. Följande avsnitt sammanfattar stödet för exempelvis BitLocker, redundanskluster och Utjämning av nätverksbelastning. [Microsofts serverprogramsupport för Azure Virtual Machines](https://support.microsoft.com/kb/2721672).
* [SQLServer på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Etablera en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Hur du ansluter en datadisk till en virtuell dator](../classic/attach-disk-classic.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrera en databas till SQLServer på en virtuell Azure-dator](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Fastställa serverläge av en Analysis Services-instans](https://msdn.microsoft.com/library/gg471594.aspx)
* [Flerdimensionella modeller (självstudiekurs för Adventure Works)](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure Documentation Center](https://azure.microsoft.com/documentation/)
* [Med Powerbi i en Hybridmiljö](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Skicka feedback och kontaktuppgifter genom att ansluta till Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Community-innehåll
* [Hantering av Azure SQL Database med PowerShell](https://azure.microsoft.com/blog/windows-azure-sql-database-management-with-powershell/)

