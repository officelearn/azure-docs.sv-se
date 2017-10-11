---
title: SQL Server Business Intelligence | Microsoft Docs
description: "Det här avsnittet använder resurser som har skapats med den klassiska distributionsmodellen och beskriver funktionerna för Business Intelligence (BI) som är tillgängliga för SQL Server som körs på Azure Virtual Machines (virtuella datorer)."
services: virtual-machines-windows
documentationcenter: na
author: guyinacube
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
ms.author: asaxton
ms.openlocfilehash: 65bada117e7d005362b0ac0ce7cc5336a92e0889
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="sql-server-business-intelligence-in-azure-virtual-machines"></a>SQL Server Business Intelligence på Azure Virtuella datorer
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln täcker den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Microsoft Azure Virtual Machine-galleriet innehåller bilder som innehåller SQL Server-installationer. SQL Server-versioner som stöds i galleriavbildningar är samma installationsfiler som du kan installera på lokala datorer och virtuella datorer. Det här avsnittet sammanfattas de SQL Server Business Intelligence (BI) funktioner installeras på bilderna och konfigurationssteg som krävs när en virtuell dator har etablerats. Det här avsnittet beskriver också distributionstopologier som stöds för BI-funktionerna och bästa praxis.

## <a name="license-considerations"></a>Licens-överväganden
Det finns två sätt att licens SQL Server i Microsoft Azure-datorer:

1. Licens mobility fördelar som ingår i Software Assurance. Mer information finns i [Licensmobilitet via Software Assurance på Azure](https://azure.microsoft.com/pricing/license-mobility/).
2. Betala per timme av Azure virtuella datorer med SQL Server installerad. Se avsnittet ”SQL Server” i [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Mer information om licensiering och aktuella kurser finns [virtuella datorer vanliga frågor om licensiering](https://azure.microsoft.com/pricing/licensing-faq/%20/).

## <a name="sql-server-images-available-in-azure-virtual-machine-gallery"></a>SQL Server-avbildningar finns i Azure galleriet för virtuella datorer
Microsoft Azure Virtual Machine-galleriet innehåller flera avbildningar som innehåller Microsoft SQL Server. Programvaran på virtuella datoravbildningar varierar beroende på vilken version av operativsystemet och versionen av SQL Server. Listan över tillgängliga avbildningar i galleriet för virtuella Azure-datorn ändras ofta.

<!--![SQL image in azure VM gallery](./media/virtual-machines-windows-classic-ps-sql-bi/IC741367.png)-->
![SQL-avbildningen i Virtuella Azure-galleriet](./media/virtual-machines-windows-classic-ps-sql-bi/vm-sql-images.png)

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Följande PowerShell-skriptet returnerar listan över Azure-avbildningar som innehåller ”SQL Server” i avbildning:

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

Mer information om versioner och funktioner som stöds av SQL Server finns i:

* [SQL Server-versioner](https://www.microsoft.com/server-cloud/products/sql-server-editions/#fbid=Zae0-E6r5oh)
* [Funktioner som stöds i olika utgåvor av SQLServer 2016](https://msdn.microsoft.com/library/cc645993.aspx)

### <a name="bi-features-installed-on-the-sql-server-virtual-machine-gallery-images"></a>BI funktioner som är installerade på SQL Server-galleriet för virtuella datorer avbildningar
I följande tabell sammanfattas de Business Intelligence-funktioner som installeras på de vanliga Microsoft Azure-dator galleriavbildningar för SQL Server:

* SQL Server 2016 SP1 Enterprise
* SQL Server 2016 SP1 Standard
* SQL Server 2014 SP2 Enterprise
* SQL Server 2014 SP2 Standard
* SQL Server 2012 SP3 Enterprise
* SQL Server 2012 SP3 Standard

| Funktionen för SQL Server BI | Installerad på bild galleriet | Anteckningar |
| --- | --- | --- |
| **Reporting Services enhetligt läge** |Ja |Installerats men måste konfigurationen, inklusive URL: en för report manager. Se avsnittet [konfigurera Reporting Services](#configure-reporting-services). |
| **Reporting Services SharePoint-läge** |Nej |Bild för virtuell dator i Microsoft Azure-galleriet inte innehåller SharePoint- eller SharePoint installationsfilerna. <sup>1</sup> |
| **Analysis Services flerdimensionella och Data utvinningsmodellen (OLAP)** |Ja |Installeras och konfigureras som standard Analysis Services-instans |
| **Analysis Services-tabell** |Nej |Stöds i SQL Server 2012, installeras 2014 2016 bilder, men den inte som standard. Installera en annan instans av Analysis Services. Se avsnittet installera andra SQL Server-tjänster och funktioner i det här avsnittet. |
| **Analysis Services Power Pivot för SharePoint** |Nej |Bild för virtuell dator i Microsoft Azure-galleriet inte innehåller SharePoint- eller SharePoint installationsfilerna. <sup>1</sup> |

<sup>1</sup> ytterligare information om Azure virtuella datorer och SharePoint finns [Microsoft Azure arkitekturer för SharePoint 2013](https://technet.microsoft.com/library/dn635309.aspx) och [SharePoint-distributionen på Microsoft Azure Virtual Machines](https://www.microsoft.com/download/details.aspx?id=34598).

![PowerShell](./media/virtual-machines-windows-classic-ps-sql-bi/IC660119.gif) Kör följande PowerShell-kommando för att hämta en lista över installerade produkter som innehåller ”SQL” i namnet på tjänsten.

    get-service | Where-Object{ $_.DisplayName -like '*SQL*' } | Select DisplayName, status, servicetype, dependentservices | format-Table -AutoSize

## <a name="general-recommendations-and-best-practices"></a>Allmänna rekommendationer och bästa praxis
* Minst rekommenderade storleken för en virtuell dator är **A3** när du använder SQL Server Enterprise Edition. Den **A4** storlek för virtuell dator rekommenderas för SQL Server BI distributioner av Analysis Services och Reporting Services.
  
    Mer information om de aktuella storlekarna för VM finns [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Bästa praxis för Diskhantering är att lagra data och loggfilen säkerhetskopior på enheter än **C**: och **D**:. Till exempel skapa datadiskar **E**: och **F**:.
  
  * Enhetens princip för standardenheten för cachelagring av **C**: är inte optimalt för att arbeta med data.
  * Den **D**: enheten är en temporär enhet som används främst för växlingsfilen. Den **D**: enheten är inte beständiga och sparas inte i blob storage. Hanteringsuppgifter, till exempel en ändring av den virtuella datorn storlek Återställ den **D**: enheten. Det rekommenderas att **inte** använder den **D**: enheten för databasfilerna, inklusive tempdb.
    
    Mer information om hur du skapar och bifogar diskar finns [hur du kopplar en datadisk till en virtuell dator](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* Stoppa eller avinstallera tjänster som du inte tänker använda. För exempel om den virtuella datorn bara används för Reporting Services, stoppar eller avinstallerar Analysis Services och SQL Server Integration Services. Följande bild är ett exempel på de tjänster som startas som standard.
  
    ![SQL Server-tjänster](./media/virtual-machines-windows-classic-ps-sql-bi/IC650107.gif)
  
  > [!NOTE]
  > SQL Server-databasmotorn krävs i BI-scenarier som stöds. I en enda server VM-topologi måste databasmotorn köras på samma virtuella dator.
  
    Mer information finns i följande: [avinstallera Reporting Services](https://msdn.microsoft.com/library/hh479745.aspx) och [avinstallera en instans av Analysis Services](https://msdn.microsoft.com/library/ms143687.aspx).
* Kontrollera **Windows Update** för nya 'viktiga uppdateringar ”. Microsoft Azure Virtual Machine-avbildningar uppdateras ofta; dock viktiga uppdateringar bli tillgängliga från **Windows Update** när VM-avbildning senast uppdaterades.

## <a name="example-deployment-topologies"></a>Exempel distributionstopologier
Följande är exempeldistributioner som använder Microsoft Azure-datorer. Topologier i dessa diagram är endast en del av möjliga topologier som du kan använda med SQL Server BI funktioner och virtuella datorer i Microsoft Azure.

### <a name="single-virtual-machine"></a>Enskild virtuell dator
Analysis Services, Reporting Services, SQL Server Database Engine och datakällor på en enskild virtuell dator.

![BI IAS-scenario med 1 virtuell dator](./media/virtual-machines-windows-classic-ps-sql-bi/IC650108.gif)

### <a name="two-virtual-machines"></a>Två virtuella datorer
* Analysis Services Reporting Services och SQL Server Database Engine på en enskild virtuell dator. Den här distributionen innehåller report server-databaser.
* Datakällor på en andra virtuell dator. Den andra virtuella datorn innehåller SQL Server Database Engine som en datakälla.

![BI iaas-scenario med 2 virtuella datorer](./media/virtual-machines-windows-classic-ps-sql-bi/IC650109.gif)

### <a name="mixed-azure--data-on-azure-sql-database"></a>Blandat Azure – data på Azure SQL-databas
* Analysis Services Reporting Services och SQL Server Database Engine på en enskild virtuell dator. Den här distributionen innehåller report server-databaser.
* Datakällan är Azure SQL-databas.

![BI iaas scenarier vm och AzureSQL som datakälla](./media/virtual-machines-windows-classic-ps-sql-bi/IC650110.gif)

### <a name="hybrid-data-on-premises"></a>Hybrid – data lokalt
* I den här exempeldistributionen Analysis Services köras Reporting Services och SQL Server Database Engine på en enskild virtuell dator. Den virtuella datorn är värd för report server-databaser. Den virtuella datorn är ansluten till en lokal domän via Azure-virtuellt nätverk eller några andra VPN-tunnel lösning.
* Datakällan är lokalt.

![BI iaas scenarier virtuella och lokala datakällor](./media/virtual-machines-windows-classic-ps-sql-bi/IC654384.gif)

## <a name="reporting-services-native-mode-configuration"></a>Reporting Services-konfiguration för enhetligt läge
Bild för virtuell dator-galleriet för SQL Server innehåller Reporting Services enhetligt läge installerat, men rapportservern inte har konfigurerats. Stegen i det här avsnittet konfigurerar Reporting Services-rapportservern. Mer detaljerad information om hur du konfigurerar Reporting Services enhetligt läge finns [installera Reporting Services enhetligt läge Report Server (SSRS)](https://msdn.microsoft.com/library/ms143711.aspx).

> [!NOTE]
> Liknande innehåll som använder Windows PowerShell-skript för att konfigurera rapportservern finns [Använd PowerShell för att skapa en Azure VM med ett enhetligt läge rapportserver](../classic/ps-sql-report.md).

### <a name="connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager"></a>Ansluta till den virtuella datorn och starta konfigurationshanteraren för Reporting Services
Det finns två vanliga arbetsflöden för att ansluta till en virtuell dator i Azure:

* Ansluta klickar du på namnet på den virtuella datorn och klicka sedan på **Anslut**. En anslutning till fjärrskrivbord öppnas och datornamnet fylls i automatiskt.
  
    ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-bi/IC650112.gif)
* Ansluta till den virtuella datorn med anslutning till fjärrskrivbord. I användargränssnittet för fjärrskrivbordet:
  
  1. Typ av **molntjänstnamnet** som datornamn.
  2. Ange kolon (:) och det offentliga portnumret som har konfigurerats för TCP skrivbord fjärrslutpunkten.
     
      Myservice.cloudapp.NET:63133
     
      Mer information finns i [vad är en molnbaserad tjänst?](https://azure.microsoft.com/manage/services/cloud-services/what-is-a-cloud-service/).


**Starta Reporting Services Configuration Manager**

I **Windows Server 2012/2016**:

1. Från den **starta** skriver **Reporting Services** att se en lista över appar.
2. Högerklicka på **Reporting Services Configuration Manager** och på **kör som administratör**.

I **Windows Server 2008 R2**:

1. Klicka på **starta**, och klicka sedan på **alla program**.
2. Klicka på **Microsoft SQL Server 2016**.
3. Klicka på **konfigurationsverktyg**.
4. Högerklicka på **Reporting Services Configuration Manager** och på **kör som administratör**.

Eller:

1. Klicka på **starta**.
2. I den **Sök program och filer** dialogrutan typen **reporting services**. Om den virtuella datorn kör Windows Server 2012, Skriv **reporting services** på skärmen för att starta om Windows Server 2012.
3. Högerklicka på **Reporting Services Configuration Manager** och på **kör som administratör**.
   
    ![Sök efter ssrs configuration manager](./media/virtual-machines-windows-classic-ps-sql-bi/IC650113.gif)

### <a name="configure-reporting-services"></a>Konfigurera Reporting Services
**Tjänstkonto och webbtjänst-URL:**

1. Kontrollera den **servernamn** är den lokala servernamnet och klicka på **Anslut**.
2. Observera tomt **Report Server-databasnamn**. Databasen skapas när konfigurationen är klar.
3. Kontrollera den **Report Server Status** är **igång**. Om du vill kontrollera tjänsten i Windows Server Manager, är tjänsten den **SQL Server Reporting Services** Windows-tjänst.
4. Klicka på **tjänstkonto** och ändra kontot efter behov. Om den virtuella datorn används i en icke-domänanslutna domänmiljö kan inbyggt **ReportServer** kontot är tillräckliga. Mer information om kontot finns [tjänstkonto](https://msdn.microsoft.com/library/ms189964.aspx).
5. Klicka på **URL för webbtjänsten** i den vänstra rutan.
6. Klicka på **tillämpa** konfigurera standardvärden.
7. Observera den **Report Server webbadresser till tjänsten**. Observera att TCP-standardporten är 80 och är en del av URL: en. I ett senare steg kan du skapa en Microsoft Azure Virtual Machine-slutpunkt för porten.
8. I den **resultat** rutan kontrollerar du de åtgärder som har slutförts.

**Databas:**

1. Klicka på **databasen** i den vänstra rutan.
2. Klicka på **ändra databasen**.
3. Kontrollera **skapa en ny rapportserverdatabas** är markerad och klicka sedan på Nästa.
4. Kontrollera **servernamn** och på **Testanslutningen**.
5. Om resultatet är **testet av anslutningen lyckades**, klickar du på **OK** och klicka sedan på **nästa**.
6. Observera att databasnamnet är **ReportServer** och **Report Server mode** är **interna** Klicka **nästa**.
7. Klicka på **nästa** på den **autentiseringsuppgifter** sidan.
8. Klicka på **nästa** på den **sammanfattning** sidan.
9. Klicka på **nästa** på den **utvecklas och slutför** sidan.

**Web Portal-URL eller URL för Report Manager för 2012 och 2014:**

1. Klicka på **URL för Web Portal**, eller **URL för Report Manager** för 2014 och 2012 i den vänstra rutan.
2. Klicka på **Använd**.
3. I den **resultat** rutan kontrollerar du de åtgärder som har slutförts.
4. Klicka på **avsluta**.

Information om server-behörighet för rapporten finns [bevilja behörigheter på en rapportserver för enhetligt läge](https://msdn.microsoft.com/library/ms156014.aspx).

### <a name="browse-to-the-local-report-manager"></a>Bläddra till lokala Report Manager
Verifiera konfigurationen genom att bläddra till report manager på den virtuella datorn.

1. Starta Internet Explorer med administratörsbehörighet på den virtuella datorn.
2. Bläddra till http://localhost/reports på den virtuella datorn.

### <a name="to-connect-to-remote-web-portal-or-report-manager-for-2014-and-2012"></a>Att ansluta till Remote web-portalen eller Report Manager för 2014 och 2012
Om du vill ansluta till web-portalen eller Report Manager för 2014 och 2012 på den virtuella datorn från en fjärrdator, kan du skapa en ny virtuell dator TCP-slutpunkt. Som standard rapportservern lyssnar efter HTTP-begäranden på **port 80**. Om du konfigurerar URL för rapporten om du vill använda en annan port måste du ange det portnumret i följande instruktioner.

1. Skapa en slutpunkt för den virtuella datorn av TCP-Port 80. Mer information finns i [virtuella slutpunkter och brandväggsportar](#virtual-machine-endpoints-and-firewall-ports) i det här dokumentet.
2. Öppna port 80 i brandväggen för den virtuella datorn.
3. Bläddra till webbportalen eller report manager, med hjälp av Azure virtuella **DNS-namnet** som servernamnet i URL: en. Exempel:
   
    **Rapportservern**: http://uebi.cloudapp.net/reportserver **webbportalen**: http://uebi.cloudapp.net/reports
   
    [Konfigurera en brandvägg för Report Server Access](https://msdn.microsoft.com/library/bb934283.aspx)

### <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Att skapa och publicera rapporter för den virtuella Azure-datorn
I följande tabell sammanfattas några av de tillgängliga alternativ för att publicera befintliga rapporter från en lokal dator till rapportservern finns på Microsoft Azure-dator:

* **Report Builder**: den virtuella datorn innehåller klicka – en gång version av Microsoft SQL Server Report Builder för SQL 2014 och 2012. Starta Report builder första gången på den virtuella datorn med SQL 2016:
  
  1. Starta din webbläsare med administratörsbehörighet.
  2. Bläddra till webbportalen på den virtuella datorn och markera den **hämta** ikonen i det övre högra hörnet.
  3. Välj **Report Builder**.
     
     Mer information finns i [starta Report Builder](https://msdn.microsoft.com/library/ms159221.aspx).
* **SQL Server Data Tools**: VM: SQL Server Data Tools är installerat på den virtuella datorn och kan användas för att skapa **Report Server-projekt** och rapporter på den virtuella datorn. SQL Server Data Tools kan publicera rapporterna till rapportservern på den virtuella datorn.
* **SQL Server Data Tools: Remote**: skapa en Reporting Services-projekt i SQL Server Data Tools som innehåller Reporting Services-rapporter på den lokala datorn. Konfigurera projektet för att ansluta till URL för webbtjänsten.
  
    ![SSDT projektegenskaperna för SSRS-projekt](./media/virtual-machines-windows-classic-ps-sql-bi/IC650114.gif)
* Skapa en. VHD-hårddisk som innehåller rapporter och sedan ladda upp och ansluta enheten.
  
  1. Skapa en. VHD-hårddisk på den lokala datorn som innehåller dina rapporter.
  2. Skapa och installera ett certifikat.
  3. Överföra VHD-filen till Azure med hjälp av cmdlet Add-AzureVHD [skapa och ladda upp en Windows Server VHD till Azure](../classic/createupload-vhd.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
  4. Ansluta disken till den virtuella datorn.

## <a name="install-other-sql-server-services-and-features"></a>Installera andra SQL Server-tjänster och funktioner
Om du vill installera ytterligare SQL Server-tjänster, till exempel Analysis Services i tabelläge, kör du installationsguiden för SQL server. Installationsfilerna finns på den virtuella datorns hårddisk.

1. Klicka på **starta** och klicka sedan på **alla program**.
2. Klicka på **Microsoft SQL Server 2016**, **Microsoft SQL Server 2014** eller **Microsoft SQL Server 2012** och klicka sedan på **konfigurationsverktyg** .
3. Klicka på **SQL Server Installationscenter**.

Eller kör C:\SQLServer_13.0_full\setup.exe, C:\SQLServer_12.0_full\setup.exe eller C:\SQLServer_11.0_full\setup.exe

> [!NOTE]
> Första gången du kör installationsprogrammet för SQL Server, flera filer kan hämtas och kräver en omstart av den virtuella datorn och startar om installationen av SQL Server.
> 
> Om du behöver anpassa upprepade gånger bilden är markerad från Microsoft Azure-dator kan du överväga att skapa en egen SQL Server-avbildning. Analysis Services SysPrep-funktioner har aktiverats med SQL Server 2012 SP1 CU2. Mer information finns i [överväganden för installation av SQL Server med hjälp av SysPrep](https://msdn.microsoft.com/library/ee210754.aspx) och [Sysprep-stöd för serverroller](https://msdn.microsoft.com/windows/hardware/commercialize/manufacture/desktop/sysprep-support-for-server-roles).
> 
> 

### <a name="to-install-analysis-services-tabular-mode"></a>Om du vill installera Analysis Services i tabelläge
Stegen i det här avsnittet **sammanfatta** installationen av Analysis Services tabelläge. Mer information finns i följande:

* [Installera Analysis Services i tabelläge](https://msdn.microsoft.com/library/hh231722.aspx)
* [Tabell modellering (Adventure Works självstudier)](https://msdn.microsoft.com/library/140d0b43-9455-4907-9827-16564a904268)

**Om du vill installera Analysis Services i tabelläge:**

1. I installationsguiden av SQL Server klickar du på **Installation** i den vänstra rutan och klicka sedan på **fristående ny SQL server-installation eller Lägg till funktioner i en befintlig installation**.
   
   * Om du ser den **Bläddra efter mapp**, bläddra till c:\SQLServer_13.0_full, c:\SQLServer_12.0_full eller c:\SQLServer_11.0_full och klicka sedan på **Ok**.
2. Klicka på **nästa** på sidan uppdateringar.
3. På den **installationstyp** väljer **utför en ny installation av SQL Server** och på **nästa**.
4. På den **konfigurera roll** klickar du på **Installation av SQL Server-funktioner**.
5. På den **Funktionsurval** klickar du på **Analysis Services**.
6. På den **instanskonfiguration** Skriv ett beskrivande namn, **Tabellmetadata** till **namngivna instansen** och **instans-Id** textrutor .
7. På den **Analysis Services-konfiguration** väljer **tabelläge**. Lägg till den aktuella användaren i listan med administrativ behörighet.
8. Slutför och Stäng guiden Installera SQL Server.

## <a name="analysis-services-configuration"></a>Analysis Services-konfiguration
### <a name="remote-access-to-analysis-services-server"></a>Fjärråtkomst till Analysis Services-Server
Analysis Services-server stöder endast windows-autentisering. För att fjärransluta till Analysis Services från klientprogram, till exempel SQL Server Management Studio eller SQL Server Data Tools, måste den virtuella datorn är ansluten till den lokala domänen med hjälp av Azure virtuella nätverk. Mer information finns i [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md).

En **standardinstansen** av Analysis Services som lyssnar på TCP-port **2383**. Öppna porten i brandväggen för virtuella datorer. En klustrad namngivna instansen av Analysis Services också lyssnar på port **2383**.

För en **namngivna instansen** av Analysis Services, SQL Server Browser-tjänsten krävs för att hantera port åtkomst. Standardkonfigurationen för SQL Server Browser är port **2382**.

Öppna port i brandväggen för virtuella datorer, **2382** och skapa en statisk Analysis Services namngivna instansen port.

1. Kör följande kommando för att verifiera portar som redan används på den virtuella datorn och vilken process som använder portarna med administratörsbehörighet:
   
        netstat /ao
2. Använd SQL Server Management Studio för att skapa en statisk Analysis Services med namnet instans porten genom att uppdatera ”Port” värdet i tabellform AS instansen allmänna egenskaper. Mer information finns i ”använda en fast port för standard och namngivna instanser” i [konfigurera Windows-brandväggen att tillåta åtkomst för Analysis Services](https://msdn.microsoft.com/library/ms174937.aspx#bkmk_fixed).
3. Tjänsten Analysis Services tabular-instansen startas om.

Mer information finns i **virtuella slutpunkter och brandväggsportar** i det här dokumentet.

## <a name="virtual-machine-endpoints-and-firewall-ports"></a>Virtuella slutpunkter och portar i brandväggen
Det här avsnittet sammanfattas Microsoft Azure virtuella slutpunkter för att skapa och portar som ska öppnas i de virtuella datorn brandväggarna. I följande tabell sammanfattas de **TCP** portar för att skapa slutpunkter för och portar som ska öppnas i brandväggen för virtuella datorer.

* Om du använder en enda virtuell dator och följande poster är true, du behöver inte skapa VM-slutpunkter och du behöver inte öppna portar i brandväggen på den virtuella datorn.
  
  * Du inte ansluta till SQL Server-funktioner på den virtuella datorn via en fjärranslutning. Etablera en fjärrskrivbordsanslutning till den virtuella datorn och ansluter till SQL Server-funktioner lokalt på den virtuella datorn inte betraktas som en anslutning till SQL Server-funktioner.
  * Du Anslut inte den virtuella datorn till en lokal domän via Azure-virtuellt nätverk eller en annan lösning för VPN-tunnel.
* Om den virtuella datorn inte är ansluten till en domän, men du vill från en annan dator att ansluta till SQL Server-funktioner på den virtuella datorn:
  
  * Öppna portar i brandväggen på den virtuella datorn.
  * Skapa virtuella slutpunkter för portarna som anges (*).
* Slutpunkter är inte obligatoriska om den virtuella datorn är ansluten till en domän med hjälp av en VPN-tunnel som virtuella Azure-nätverk. Men öppna portar i brandväggen på den virtuella datorn.
  
  | Port | Typ | Beskrivning |
  | --- | --- | --- |
  | **80** |TCP |Rapportserver fjärråtkomst (*). |
  | **1433** |TCP |SQL Server Management Studio (*). |
  | **1434** |UDP |SQL Server Browser. Detta krävs när den virtuella datorn i ansluten till en domän. |
  | **2382** |TCP |SQL Server Browser. |
  | **2383** |TCP |Standardinstansen för SQL Server Analysis Services och klustrade namngivna instanser. |
  | **Användardefinierade** |TCP |Skapa en statisk Analysis Services namngiven instans port efter ett portnummer som du väljer och avblockera portnumret i brandväggen. |

Mer information om hur du skapar slutpunkter finns i:

* Skapa slutpunkter:[konfigurera slutpunkter till en virtuell dator](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
* SQLServer: Se avsnittet ”klar konfigurationsstegen för att ansluta till den virtuella datorn med hjälp av SQL Server Management Studio” i [etablering av en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md).

Följande diagram illustrerar portarna som ska öppnas i brandväggen för VM att tillåta fjärråtkomst till funktioner och komponenter på den virtuella datorn.

![portar som ska öppnas för bi program i virtuella Azure-datorer](./media/virtual-machines-windows-classic-ps-sql-bi/IC654385.gif)

## <a name="resources"></a>Resurser
* Granska Supportpolicy för Microsoft server-programvara som används i virtuella Azure-miljö. Följande avsnitt sammanfattar stöd för funktioner som BitLocker, redundanskluster och Utjämning av nätverksbelastning. [Support för Microsoft server-programvara för Azure Virtual Machines](http://support.microsoft.com/kb/2721672).
* [SQLServer på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md)
* [Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Etablering av en SQL Server-dator på Azure](../sql/virtual-machines-windows-portal-sql-server-provision.md)
* [Så här kopplar du en datadisk till en virtuell dator](../classic/attach-disk.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)
* [Migrera en databas till SQLServer på en virtuell dator i Azure](../sql/virtual-machines-windows-migrate-sql.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fsqlclassic%2ftoc.json)
* [Fastställa serverläge för en Analysis Services-instans](https://msdn.microsoft.com/library/gg471594.aspx)
* [Flerdimensionella modellering (Adventure Works självstudier)](https://technet.microsoft.com/library/ms170208.aspx)
* [Azure Documentation Center](https://azure.microsoft.com/documentation/)
* [Med Powerbi i en Hybridmiljö](https://msdn.microsoft.com/library/dn798994.aspx)

> [!NOTE]
> [Skicka feedback och kontaktuppgifter genom att ansluta till Microsoft SQL Server](https://connect.microsoft.com/SQLServer/Feedback)

### <a name="community-content"></a>Community-innehåll
* [Hantering av Azure SQL-databas med PowerShell](http://blogs.msdn.com/b/windowsazure/archive/2013/02/07/windows-azure-sql-database-management-with-powershell.aspx)

