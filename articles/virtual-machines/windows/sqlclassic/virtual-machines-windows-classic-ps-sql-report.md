---
title: Använd PowerShell för att skapa en virtuell dator med en rapportserver i enhetligt läge | Microsoft Docs
description: 'Det här avsnittet beskriver och vägleder dig genom distributionen och konfigurationen av en rapportserver för SQL Server Reporting Services enhetligt läge i en Azure virtuell dator. '
services: virtual-machines-windows
documentationcenter: na
author: markingmyname
manager: erikre
editor: monicar
tags: azure-service-management
ms.assetid: 553af55b-d02e-4e32-904c-682bfa20fa0f
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/11/2017
ms.author: maghan
ms.openlocfilehash: 32be46fb0c41909ce8a8014b13843970555d366f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "42057022"
---
# <a name="use-powershell-to-create-an-azure-vm-with-a-native-mode-report-server"></a>Använd PowerShell för att skapa en virtuell Azure-dator med en rapportserver i enhetligt läge
> [!IMPORTANT] 
> Azure har två olika distributionsmodeller för att skapa och arbeta med resurser: [Resource Manager och klassisk](../../../azure-resource-manager/resource-manager-deployment-model.md). Den här artikeln beskriver den klassiska distributionsmodellen. Microsoft rekommenderar att de flesta nya distributioner använder Resource Manager-modellen.

Det här avsnittet beskriver och vägleder dig genom distributionen och konfigurationen av en rapportserver för SQL Server Reporting Services enhetligt läge i en Azure virtuell dator. Stegen i det här dokumentet använder en kombination av manuella steg för att skapa den virtuella datorn och ett Windows PowerShell-skript för att konfigurera Reporting Services på den virtuella datorn. Av konfigurationsskriptet innehåller att öppna en brandväggsport för HTTP eller HTTPs.

> [!NOTE]
> Om du inte behöver **HTTPS** på rapportservern, **hoppa över steg 2**.
> 
> När du har skapat den virtuella datorn i steg 1, går du till avsnittet Använd skript för att konfigurera report server- och HTTP. När du har kört skriptet är klart att användas på rapportservern.

## <a name="prerequisites-and-assumptions"></a>Förhandskrav och antaganden
* **Azure-prenumeration**: Kontrollera antalet tillgängliga kärnor i din Azure-prenumeration. Om du skapar den rekommenderade storleken på **A3**, behöver du **4** tillgängliga kärnor. Om du använder en VM-storlek för **A2**, behöver du **2** tillgängliga kärnor.
  
  * Klicka på inställningar i den vänstra rutan och sedan klicka på användning på den översta menyn för att verifiera kärngräns för din prenumeration i Azure-portalen.
  * Om du vill öka kärnkvoten Kontakta [Azure-supporten](https://azure.microsoft.com/support/options/). VM-Storleksinformation finns i [storlekar för virtuella datorer för Azure](../sizes.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* **Windows PowerShell-skript**: ämnet förutsätter att du har grundläggande kunskaper om Windows PowerShell. Mer information om hur du använder Windows PowerShell finns i följande:
  
  * [Starta Windows PowerShell på Windows Server](https://docs.microsoft.com/powershell/scripting/setup/starting-windows-powershell)
  * [Komma igång med Windows PowerShell](https://technet.microsoft.com/library/hh857337.aspx)

## <a name="step-1-provision-an-azure-virtual-machine"></a>Steg 1: Etablera en Azure virtuell dator
1. Bläddra till Azure-portalen.
2. Klicka på **virtuella datorer** i den vänstra rutan.
   
    ![Microsoft azure-datorer](./media/virtual-machines-windows-classic-ps-sql-report/IC660124.gif)
3. Klicka på **Ny**.
   
    ![knappen Ny](./media/virtual-machines-windows-classic-ps-sql-report/IC692019.gif)
4. Klicka på **från galleriet**.
   
    ![ny virtuell dator från galleriet](./media/virtual-machines-windows-classic-ps-sql-report/IC692020.gif)
5. Klicka på **SQL Server 2014 RTM Standard – Windows Server 2012 R2** och klicka sedan på pilen för att fortsätta.
   
    ![nästa](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
   
    Om du behöver Reporting Services-prenumerationer funktionen för datadriven väljer **SQL Server 2014 RTM Enterprise, Windows Server 2012 R2**. Mer information om SQL Server-versioner och funktioner som stöds finns i [funktioner stöds av utgåvor av SQL Server 2012](https://msdn.microsoft.com/library/cc645993.aspx#Reporting).
6. På den **konfiguration av virtuell dator** , redigera följande fält:
   
   * Om det finns fler än en **VERSION LANSERINGSDATUM**, Välj den senaste versionen.
   * **Namn på virtuell dator**: namnet på datorn används också på konfigurationssidan nästa som standard Cloud Service DNS-namn. DNS-namnet måste vara unikt i Azure-tjänsten. Överväg att konfigurera den virtuella datorn med ett datornamn som beskriver vad den virtuella datorn används för. Till exempel ssrsnativecloud.
   * **Nivån**: Standard
   * **Storlek: A3** rekommenderade VM-storleken för SQL Server-arbetsbelastningar. Om en virtuell dator används endast som en rapportserver, räcker en VM-storleken för A2, såvida inte rapportservern påträffar en stor belastning. VM information om priser, se [prissättning för Virtual Machines](https://azure.microsoft.com/pricing/details/virtual-machines/).
   * **Nytt användarnamn**: namnet som du anger skapas som en administratör på den virtuella datorn.
   * **Nytt lösenord** och **Bekräfta**. Det här lösenordet används för administratörskontot och bör du använda ett starkt lösenord.
   * Klicka på **Nästa**. ![Nästa](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
7. På nästa sida redigerar du följande fält:
   
   * **Molntjänst**: Välj **skapa en ny molntjänst**.
   * **DNS-namn i molnet**: Detta är det offentliga DNS-namnet på den molntjänst som är associerad med den virtuella datorn. Standardnamnet är namnet du angav i för VM-namnet. Om i senare steg i avsnittet om du skapar ett betrott SSL-certifikat och DNS-namnet används för värdet för den ”**utfärdat till**” för certifikatet.
   * **Region/tillhörighet grupp/virtuella nätverk**: Välj regionen som är närmast dina slutanvändare.
   * **Storage-konto**: använda en automatiskt genererad storage-konto.
   * **Tillgänglighetsuppsättning**: Ingen.
   * **SLUTPUNKTER** hålla den **fjärrskrivbord** och **PowerShell** slutpunkter och sedan lägga till en HTTP- eller HTTPS-slutpunkt, beroende på din miljö.
     
     * **HTTP**: de offentliga och privata portarna som standard är **80**. Observera att om du använder en privat port än 80, ändra **$HTTPport = 80** i http-skriptet.
     * **HTTPS**: de offentliga och privata portarna som standard är **443**. Det är en bra säkerhetsrutin att ändra den privata porten och konfigurera din brandvägg och rapportservern för att använda den privata porten. Läs mer på slutpunkter [så ange kommunikation med en virtuell dator](../classic/setup-endpoints.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json). Observera att om du använder en annan port än 443, ändra parametern **$HTTPsport = 443** i HTTPS-skriptet.
   * Klicka på Nästa. ![nästa](./media/virtual-machines-windows-classic-ps-sql-report/IC692021.gif)
8. På den sista sidan i guiden, behåller du standardvärdet **installera VM-agenten** valda. Stegen i det här avsnittet använder inte VM-agenten, men om du planerar att behålla den här virtuella datorn VM-agenten och tillägg kan du förbättra han CM.  Mer information om VM-agenten finns i [VM-agenten och tillägg – del 1](https://azure.microsoft.com/blog/2014/04/11/vm-agent-and-extensions-part-1/). En av standard tillägg installerade ad som körs är tillägget ”BGINFO” som visas på den virtuella datorns skrivbord, Systeminformation som intern IP-adress och ledigt diskutrymme.
9. Klicka på klar. ![Ok](./media/virtual-machines-windows-classic-ps-sql-report/IC660122.gif)
10. Den **Status** för den virtuella datorn visas som **startar (etablering)** under processen för etablering och sedan visar som **kör** när den virtuella datorn är allokerade och redo för användning.

## <a name="step-2-create-a-server-certificate"></a>Steg 2: Skapa ett certifikat
> [!NOTE]
> Om du inte behöver HTTPS på rapportservern, kan du **hoppa över steg 2** och gå till avsnittet **använda skript för att konfigurera report server- och HTTP-**. Använd HTTP-skript för att snabbt konfigurera rapportservern och rapportservern är redo att användas.

Du behöver ett betrott SSL-certifikat för att kunna använda HTTPS på den virtuella datorn. Beroende på ditt scenario kan du använda något av följande två metoder:

* Ett giltigt SSL-certifikat utfärdat av en certifikatutfärdare (CA) och betrodd av Microsoft. Rotcertifikat för CA: N behöver distribueras via Microsoft Root Certificate Program. Mer information om det här programmet finns i [Windows och Windows Phone 8 SSL Root Certificate Program (medlem CAs)](http://social.technet.microsoft.com/wiki/contents/articles/14215.windows-and-windows-phone-8-ssl-root-certificate-program-member-cas.aspx) och [introduktion till Microsoft Root Certificate Program](http://social.technet.microsoft.com/wiki/contents/articles/3281.introduction-to-the-microsoft-root-certificate-program.aspx).
* Ett självsignerat certifikat. Självsignerade certifikat rekommenderas inte för produktionsmiljöer.

### <a name="to-use-a-certificate-created-by-a-trusted-certificate-authority-ca"></a>Använda ett certifikat som skapats av en betrodd certifikatutfärdare (CA)
1. **Begära ett certifikat för webbplatsen från en certifikatutfärdare**. 
   
    Du kan använda guiden Servercertifikat för att generera en fil med certifikatbegäran (Certreq.txt) som du skickar till en certifikatutfärdare eller att generera en begäran om en online-certifikatutfärdare. Till exempel Microsoft Certificate Services i Windows Server 2012. Beroende på säkerhetsnivån identifiering av servercertifikatet, är det flera dagar att flera månader för certifikatutfärdare för att godkänna din begäran och skickar dig en certifikatfil. 
   
    Mer information om hur du begär ett servercertifikat finns i följande: 
   
   * Använd [Certreq](https://technet.microsoft.com/library/cc725793.aspx), [Certreq](https://technet.microsoft.com/library/cc725793.aspx).
   * Säkerhetsverktyg för att administrera Windows Server 2012.
     
     [Säkerhetsverktyg för att administrera Windows Server 2012](https://technet.microsoft.com/library/jj730960.aspx)
     
     > [!NOTE]
     > Den **utfärdat till** fält för betrodda SSL-certifikat ska vara samma som den **Molntjänstens DNS-namn** du använde för den nya virtuella datorn.

2. **Installera servercertifikatet på webbservern**. Webbservern i det här fallet är den virtuella datorn som är värd för rapportservern och webbplatsen skapas i senare steg när du konfigurerar Reporting Services. Mer information om hur du installerar servercertifikatet på webbservern med hjälp av snapin-modulen certifikat finns i [installera ett servercertifikat](https://technet.microsoft.com/library/cc740068).
   
    Om du vill använda skript som ingår i det här avsnittet för att konfigurera rapportservern, värdet för certifikaten **tumavtryck** måste anges som en parameter för skriptet. Se nästa avsnitt för information om hur du skaffar tumavtrycket för certifikatet.
3. Tilldela servercertifikatet till rapportservern. Tilldelningen har slutförts i nästa avsnitt när du konfigurerar rapportservern.

### <a name="to-use-the-virtual-machines-self-signed-certificate"></a>Om du vill använda de virtuella datorerna självsignerade certifikat
Ett självsignerat certifikat skapades på den virtuella datorn när den virtuella datorn har etablerats. Certifikatet har samma namn som VM-DNS-namn. För att undvika certifikatfel, det krävs att certifikatet är betrott på Virtuellt datorn och också av alla användare av platsen.

1. Om du vill lita på rot-CA: N för certifikatet på den lokala virtuella datorn, lägga till certifikatet till den **betrodda rotcertifikatutfärdare**. Här följer en sammanfattning av de steg som krävs. Detaljerade anvisningar om hur du litar på Certifikatutfärdaren i [installera ett servercertifikat](https://technet.microsoft.com/library/cc740068).
   
   1. Välj den virtuella datorn från Azure-portalen och klicka på Anslut. Beroende på din webbläsarkonfiguration kan du uppmanas att spara en .rdp-fil för att ansluta till den virtuella datorn.
      
       ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Använd användarnamn för virtuell dator, användarnamnet och lösenordet som du konfigurerade när du skapade den virtuella datorn. 
      
       Till exempel i följande bild, VM-namnet är **ssrsnativecloud** och användarnamnet är **testuser**.
      
       ![inloggningsnamn innehåller vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
   2. Kör mmc.exe. Mer information finns i [så här: Visa certifikat med MMC-snapin-modulen](https://msdn.microsoft.com/library/ms788967.aspx).
   3. I konsolprogrammet **filen** menyn Lägg till den **certifikat** snapin-modulen, väljer **datorkontot** när du uppmanas, och klicka sedan på **nästa**.
   4. Välj **lokala** att hantera och klicka sedan på **Slutför**.
   5. Klicka på **Ok** och expandera sedan den **certifikat - personliga** noder och klicka sedan på **certifikat**. Certifikatet är döpt efter DNS-namnet på den virtuella datorn och slutar med **cloudapp.net**. Högerklicka på certifikatets namn och klicka på **kopiera**.
   6. Expandera den **betrodda rotcertifikatutfärdare** noden och sedan högerklickar du **certifikat** och klicka sedan på **klistra in**.
   7. För att verifiera, dubbelklicka på certifikatets namn under **betrodda rotcertifikatutfärdare** och kontrollera att det finns några fel och du ser ditt certifikat. Om du vill använda HTTPS-skript som ingår i det här avsnittet för att konfigurera rapportservern, värdet för certifikaten **tumavtryck** måste anges som en parameter för skriptet. **Att hämta tumavtrycksvärde**, Slutför följande. Det finns också ett PowerShell-exempel för att hämta tumavtrycket i avsnittet [använda skript för att konfigurera report server- och HTTPS](#use-script-to-configure-the-report-server-and-HTTPS).
      
      1. Dubbelklicka på namnet på certifikatet, till exempel ssrsnativecloud.cloudapp.net.
      2. Klicka på den **information** fliken.
      3. Klicka på **tumavtryck**. Värdet för tumavtrycket visas i Informationsfältet, till exempel a6 08 3c df f9 0b f7 e3 7c 25 ed a4 ed 7e ac 91 9c 2c fb 2f.
      4. Kopiera tumavtrycket och spara värdet för senare eller redigera skriptet nu.
      5. (*) Ta bort blanksteg mellan par med värden innan du kör skriptet. Till exempel är nu tumavtrycket notering a6083cdff90bf7e37c25eda4ed7eac919c2cfb2f.
      6. Tilldela servercertifikatet till rapportservern. Tilldelningen har slutförts i nästa avsnitt när du konfigurerar rapportservern.

Om du använder ett självsignerat SSL-certifikat matchar namnet på certifikatet redan värdnamnet för den virtuella datorn. Därför DNS för datorn har redan registrerats globalt och kan nås från alla klienter.

## <a name="step-3-configure-the-report-server"></a>Steg 3: Konfigurera rapportservern
Det här avsnittet beskriver hur du konfigurerar den virtuella datorn som en rapportserver för enhetligt läge av Reporting Services. Du kan använda någon av följande metoder för att konfigurera rapportservern:

* Använd skript för att konfigurera rapportservern
* Använda Configuration Manager för att konfigurera rapportservern.

Mer detaljerade steg finns i avsnittet [ansluta till den virtuella datorn och starta konfigurationshanteraren för Reporting Services](virtual-machines-windows-classic-ps-sql-bi.md#connect-to-the-virtual-machine-and-start-the-reporting-services-configuration-manager).

**Autentisering Obs:** Windows-autentisering är den rekommendera autentiseringsmetoden och det är standard Reporting Services-autentisering. Endast användare som har konfigurerats på den virtuella datorn har åtkomst till Reporting Services och tilldelas till Reporting Services-roller.

### <a name="use-script-to-configure-the-report-server-and-http"></a>Använd skript för att konfigurera report server- och HTTP
Om du vill använda Windows PowerShell-skript för att konfigurera rapportservern, utför du följande steg. Konfigurationen innehåller HTTP, HTTPS inte:

1. Välj den virtuella datorn från Azure-portalen och klicka på Anslut. Beroende på din webbläsarkonfiguration kan du uppmanas att spara en .rdp-fil för att ansluta till den virtuella datorn.
   
    ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Använd användarnamn för virtuell dator, användarnamnet och lösenordet som du konfigurerade när du skapade den virtuella datorn. 
   
    Till exempel i följande bild, VM-namnet är **ssrsnativecloud** och användarnamnet är **testuser**.
   
    ![inloggningsnamn innehåller vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. På den virtuella datorn, öppna **Windows PowerShell ISE** med administrativa privilegier. PowerShell ISE installeras som standard på Windows server 2012. Det rekommenderas att du använder ISE i stället för en standard Windows PowerShell-fönstret så att du kan klistra in skriptet i ISE, ändra skriptet och sedan köra skriptet.
3. I Windows PowerShell ISE klickar du på den **visa** menyn och klicka sedan på **visa Skriptfönster**.
4. Kopiera följande skript och klistra in skriptet i skriptfönstret Windows PowerShell ISE.
   
        ## This script configures a Native mode report server without HTTPS
        $ErrorActionPreference = "Stop"
   
        $server = $env:COMPUTERNAME
        $HTTPport = 80 # change the value if you used a different port for the private HTTP endpoint when the VM was created.
   
        ## Set PowerShell execution policy to be able to run scripts
        Set-ExecutionPolicy RemoteSigned -Force
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Report Server Configuration Steps
   
        ## Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port $HTTPport (for local usage)
            write-host "Calling ReserveURL port $HTTPport"
            $r = $RSObject.ReserveURL('ReportServerWebService',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportServer port $HTTPport" 
   
        ## Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS              ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port $HTTPport
            write-host "Calling ReserveURL for ReportManager, port $HTTPport"
            $r = $RSObject.ReserveURL('ReportManager',"http://+:$HTTPport",1033)
            CheckResult $r "ReserveURL for ReportManager port $HTTPport"
   
        write-host -foregroundcolor green "Open Firewall port for $HTTPport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $HTTPport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $HTTPport)” -Direction Inbound –Protocol TCP –LocalPort $HTTPport
            write-host "Added rule Report Server (TCP on port $HTTPport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
5. Om du har skapat den virtuella datorn med en HTTP-port än 80, ändra parametern $HTTPport = 80.
6. Skriptet är konfigurerad för Reporting Services. Om du vill köra skriptet för Reporting Services kan du ändra version delen av sökvägen till namnområdet som ska ”v11” om uttrycket Get-WmiObject.
7. Kör skriptet.

**Verifiering**: Om du vill kontrollera att den grundläggande rapportfunktioner för server fungerar, se den [verifiera konfigurationen](#verify-the-configuration) senare i det här avsnittet.

### <a name="use-script-to-configure-the-report-server-and-https"></a>Använd skript för att konfigurera report server- och HTTPS
Om du vill använda Windows PowerShell för att konfigurera rapportservern, utför du följande steg. Konfigurationen innehåller HTTPS, inte HTTP.

1. Välj den virtuella datorn från Azure-portalen och klicka på Anslut. Beroende på din webbläsarkonfiguration kan du uppmanas att spara en .rdp-fil för att ansluta till den virtuella datorn.
   
    ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif) Använd användarnamn för virtuell dator, användarnamnet och lösenordet som du konfigurerade när du skapade den virtuella datorn. 
   
    Till exempel i följande bild, VM-namnet är **ssrsnativecloud** och användarnamnet är **testuser**.
   
    ![inloggningsnamn innehåller vm](./media/virtual-machines-windows-classic-ps-sql-report/IC764111.png)
2. På den virtuella datorn, öppna **Windows PowerShell ISE** med administrativa privilegier. PowerShell ISE installeras som standard på Windows server 2012. Det rekommenderas att du använder ISE i stället för en standard Windows PowerShell-fönstret så att du kan klistra in skriptet i ISE, ändra skriptet och sedan köra skriptet.
3. Om du vill aktivera skript som körs, kör du följande Windows PowerShell-kommando:
   
        Set-ExecutionPolicy RemoteSigned
   
    Du kan sedan köra följande för att verifiera principen:
   
        Get-ExecutionPolicy
4. I **Windows PowerShell ISE**, klickar du på den **visa** menyn och klicka sedan på **visa Skriptfönster**.
5. Kopiera följande skript och klistra in den i skriptfönstret Windows PowerShell ISE.
   
        ## This script configures the report server, including HTTPS
        $ErrorActionPreference = "Stop"
        $httpsport=443 # modify if you used a different port number when the HTTPS endpoint was created.
   
        # You can run the following command to get (.cloudapp.net certificates) so you can copy the thumbprint / certificate hash
        #dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
        #
        # The certifacte hash is a REQUIRED parameter
        $certificatehash="" 
        # the certificate hash should not contain spaces
   
        if ($certificatehash.Length -lt 1) 
        {
            write-error "certificatehash is a required parameter"
        } 
        # Certificates should be all lower case
        $certificatehash=$certificatehash.ToLower()
        $server = $env:COMPUTERNAME
        # If the certificate is not a wildcard certificate, comment out the following line, and enable the full $DNSNAme reference.
        $DNSName="+"
        #$DNSName="$server.cloudapp.net"
        $DNSNameAndPort = $DNSName + ":$httpsport"
   
        ## Utility method for verifying an operation's result
        function CheckResult
        {
            param($wmi_result, $actionname)
            if ($wmi_result.HRESULT -ne 0) {
                write-error "$actionname failed. Error from WMI: $($wmi_result.Error)"
            }
        }
   
        $starttime=Get-Date
        write-host -foregroundcolor DarkGray $starttime StartTime
   
        ## ReportServer Database name - this can be changed if needed
        $dbName='ReportServer'
   
        write-host "The script will use $DNSNameAndPort as the DNS name and port" 
   
        ## Register for MSReportServer_ConfigurationSetting
        ## Change the version portion of the path to "v11" to use the script for SQL Server 2012
        $RSObject = Get-WmiObject -class "MSReportServer_ConfigurationSetting" -namespace "root\Microsoft\SqlServer\ReportServer\RS_MSSQLSERVER\v12\Admin"
   
        ## Reporting Services Report Server Configuration Steps
   
        ## 1. Setting the web service URL ##
        write-host -foregroundcolor green "Setting the web service URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for ReportServer site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportServerWebService','ReportServer',1033)
            CheckResult $r "SetVirtualDirectory for ReportServer"
   
        ## ReserveURL for ReportServerWebService - port 80 (for local usage)
            write-host 'Calling ReserveURL port 80'
            $r = $RSObject.ReserveURL('ReportServerWebService','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportServer port 80" 
   
        ## ReserveURL for ReportServerWebService - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportServerWebService',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportServer port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportServerWebService port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport, with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportServerWebService',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportServer port $httpsport" 
   
        ## 2. Setting the Database ##
        write-host -foregroundcolor green "Setting the Database"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## GenerateDatabaseScript - for creating the database
            write-host "Calling GenerateDatabaseCreationScript for database $dbName"
            $r = $RSObject.GenerateDatabaseCreationScript($dbName,1033,$false)
            CheckResult $r "GenerateDatabaseCreationScript"
            $script = $r.Script
   
        ## Execute sql script to create the database
            write-host 'Executing Database Creation Script'
            $savedcvd = Get-Location
            Import-Module SQLPS                    ## this automatically changes to sqlserver provider
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## GenerateGrantRightsScript 
            $DBUser = "NT Service\ReportServer"
            write-host "Calling GenerateDatabaseRightsScript with user $DBUser"
            $r = $RSObject.GenerateDatabaseRightsScript($DBUser,$dbName,$false,$true)
            CheckResult $r "GenerateDatabaseRightsScript"
            $script = $r.Script
   
        ## Execute grant rights script
            write-host 'Executing Database Rights Script'
            $savedcvd = Get-Location
            cd sqlserver:\
            Invoke-SqlCmd -Query $script
            Set-Location $savedcvd
   
        ## SetDBConnection - uses Windows Service (type 2), username is ignored
            write-host "Calling SetDatabaseConnection server $server, DB $dbName"
            $r = $RSObject.SetDatabaseConnection($server,$dbName,2,'','')
            CheckResult $r "SetDatabaseConnection"  
   
        ## 3. Setting the Report Manager URL ##
   
        write-host -foregroundcolor green "Setting the Report Manager URL"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## SetVirtualDirectory for Reports (Report Manager) site
            write-host 'Calling SetVirtualDirectory'
            $r = $RSObject.SetVirtualDirectory('ReportManager','Reports',1033)
            CheckResult $r "SetVirtualDirectory"
   
        ## ReserveURL for ReportManager  - port 80
            write-host 'Calling ReserveURL for ReportManager, port 80'
            $r = $RSObject.ReserveURL('ReportManager','http://+:80',1033)
            CheckResult $r "ReserveURL for ReportManager port 80"
   
        ## ReserveURL for ReportManager - port $httpsport
            write-host "Calling ReserveURL port $httpsport, for URL: https://$DNSNameAndPort"
            $r = $RSObject.ReserveURL('ReportManager',"https://$DNSNameAndPort",1033)
            CheckResult $r "ReserveURL for ReportManager port $httpsport" 
   
        ## CreateSSLCertificateBinding for ReportManager port $httpsport
            write-host "Calling CreateSSLCertificateBinding port $httpsport with certificate hash: $certificatehash"
            $r = $RSObject.CreateSSLCertificateBinding('ReportManager',$certificatehash,'0.0.0.0',$httpsport,1033)
            CheckResult $r "CreateSSLCertificateBinding for ReportManager port $httpsport" 
   
        write-host -foregroundcolor green "Open Firewall port for $httpsport"
        write-host -foregroundcolor green ">>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>>"
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
   
        ## Open Firewall port for $httpsport
            New-NetFirewallRule -DisplayName “Report Server (TCP on port $httpsport)” -Direction Inbound –Protocol TCP –LocalPort $httpsport
            write-host "Added rule Report Server (TCP on port $httpsport) in Windows Firewall"
   
        write-host 'Operations completed, Report Server is ready'
        write-host -foregroundcolor DarkGray $starttime StartTime
        $time=Get-Date
        write-host -foregroundcolor DarkGray $time
6. Ändra den **$certificatehash** parameter i skriptet:
   
   * Det här är en **krävs** parametern. Om du inte sparade certifikatvärdet från föregående steg, använda en av följande metoder för att kopiera certifikatets hash-värde från certifikat-tumavtrycket.:
     
       Öppna Windows PowerShell ISE på den virtuella datorn och kör följande kommando:
     
           dir cert:\LocalMachine -rec | Select-Object * | where {$_.issuer -like "*cloudapp*" -and $_.pspath -like "*root*"} | select dnsnamelist, thumbprint, issuer
     
       Utdata ser ut ungefär så här. Om skriptet returnerar en tom rad, den virtuella datorn inte har ett certifikat som exempelvis konfigurerats, finns i avsnittet [att använda virtuella datorer självsignerat certifikat](#to-use-the-virtual-machines-self-signed-certificate).
     
     ELLER
   * Kör mmc.exe på den virtuella datorn och Lägg sedan till den **certifikat** snapin-modulen.
   * Under den **betrodda rotcertifikatutfärdare** noden dubbelklickar du på certifikatets namn. Om du använder det självsignerade certifikatet för den virtuella datorn, certifikatet är döpt efter DNS-namnet på den virtuella datorn och slutar med **cloudapp.net**.
   * Klicka på den **information** fliken.
   * Klicka på **tumavtryck**. Värdet för tumavtrycket visas i Informationsfältet, till exempel af 11 60 b6 4b 28 8 d 89 0a 82 12 ff 6b a9 c3 66 4f 31 90 48
   * **Innan du kör skriptet**, ta bort blanksteg mellan par med värden. Till exempel af1160b64b288d890a8212ff6ba9c3664f319048
7. Ändra den **$httpsport** parameter: 
   
   * Om du använde port 443 för HTTPS-slutpunkt, behöver du inte uppdatera den här parametern i skriptet. Annars använder du portvärdet som du valde när du har konfigurerat den privata HTTPS-slutpunkten på den virtuella datorn.
8. Ändra den **$DNSName** parameter: 
   
   * Skriptet har konfigurerats för ett certifikat med jokertecken $DNSName = ”+”. Om du gör inga vill konfigurera för en bindning för certifikat med jokertecken, kommentera ut $DNSName = ”+” och aktivera följande rad, fullständig $DNSNAme referens, ## $DNSName="$server.cloudapp.net”.
     
       Ändra $DNSName-värdet om du inte vill använda den virtuella datorns DNS-namn för Reporting Services. Om du använder parametern certifikatet måste också använda det här namnet och registrerar du namnet globalt på en DNS-server.
9. Skriptet är konfigurerad för Reporting Services. Om du vill köra skriptet för Reporting Services kan du ändra version delen av sökvägen till namnområdet som ska ”v11” om uttrycket Get-WmiObject.
10. Kör skriptet.

**Verifiering**: Om du vill kontrollera att den grundläggande rapportfunktioner för server fungerar, se den [verifiera konfigurationen](#verify-the-connection) senare i det här avsnittet. För att verifiera certifikatet bindningen öppna en kommandotolk med administrativ behörighet och kör sedan följande kommando:

    netsh http show sslcert

Resultatet innehåller följande:

    IP:port                      : 0.0.0.0:443

    Certificate Hash             : f98adf786994c1e4a153f53fe20f94210267d0e7

### <a name="use-configuration-manager-to-configure-the-report-server"></a>Använda Configuration Manager för att konfigurera rapportservern
Om du inte vill köra PowerShell-skript för att konfigurera rapportservern följer du stegen i det här avsnittet använder Konfigurationshanteraren för Reporting Services-enhetligt läge för att konfigurera rapportservern.

1. Välj den virtuella datorn från Azure-portalen och klicka på Anslut. Använd användarnamnet och lösenordet som du konfigurerade när du skapade den virtuella datorn.
   
    ![ansluta till azure-dator](./media/virtual-machines-windows-classic-ps-sql-report/IC650112.gif)
2. Kör Windows update och installera uppdateringar till den virtuella datorn. Om det krävs en omstart av den virtuella datorn måste starta om den virtuella datorn och återansluta till den virtuella datorn från Azure-portalen.
3. Start på den virtuella datorn och skriv **Reporting Services** och öppna **Konfigurationshanteraren för Reporting Services**.
4. Lämna standardvärdena för **servernamn** och **Rapportserverinstans**. Klicka på **Anslut**.
5. I den vänstra rutan klickar du på **Webbtjänstadress**.
6. Som standard har RS konfigurerats för HTTP-port 80 med IP-adress ”alla tilldelade”. Lägga till HTTPS:
   
   1. I **SSL-certifikat**: Välj det certifikat som du vill använda, till exempel [VM name]. cloudapp.net. Om inga certifikat visas, se avsnittet **steg 2: skapa ett servercertifikat** information om hur du installerar och certifikatet på den virtuella datorn.
   2. Under **SSL-porten**: Välj 443. Om du har konfigurerat den privata HTTPS-slutpunkten i den virtuella datorn med en annan privat port, använder du värdet här.
   3. Klicka på **tillämpa** och vänta tills åtgärden har slutförts.
7. I den vänstra rutan klickar du på **databasen**.
   
   1. Klicka på **ändra Databas**e.
   2. Klicka på **skapa en ny rapportserverdatabas** och klicka sedan på **nästa**.
   3. Låt standardvärdet **servernamn**: som den virtuella datorn namnge och låt standardvärdet **autentiseringstyp** som **aktuell användare** – **integrerad säkerhet**. Klicka på **Nästa**.
   4. Låt standardvärdet **databasnamn** som **ReportServer** och klicka på **nästa**.
   5. Låt standardvärdet **autentiseringstyp** som **autentiseringsuppgifter för tjänstens** och klicka på **nästa**.
   6. Klicka på **nästa** på den **sammanfattning** sidan.
   7. När konfigurationen är klar klickar du på **Slutför**.
8. I den vänstra rutan klickar du på **URL för Report Manager**. Låt standardvärdet **virtuell katalog** som **rapporter** och klicka på **tillämpa**.
9. Klicka på **avsluta** att Stäng Konfigurationshanteraren för Reporting Services.

## <a name="step-4-open-windows-firewall-port"></a>Steg 4: Öppna Windows-brandväggen porten
> [!NOTE]
> Om du använde ett av skript för att konfigurera rapportservern, kan du hoppa över det här avsnittet. Skriptet med ett steg för att öppna brandväggsporten. Standard var port 80 för HTTP och port 443 för HTTPS.
> 
> 

Om du vill fjärransluta till Report Manager eller rapportservern på den virtuella datorn krävs en TCP-slutpunkt på den virtuella datorn. Det krävs för att öppna samma port i brandväggen för den virtuella datorn. Slutpunkten skapas när den virtuella datorn har etablerats.

Det här avsnittet innehåller grundläggande information om hur du öppnar brandväggsporten. Mer information finns i [konfigurera en brandvägg för Reportserveråtkomst](https://technet.microsoft.com/library/bb934283.aspx)

> [!NOTE]
> Om du använder skriptet för att konfigurera rapportservern, kan du hoppa över det här avsnittet. Skriptet med ett steg för att öppna brandväggsporten.
> 
> 

Om du har konfigurerat en privat port för HTTPS än 443 kan du ändra följande skript på rätt sätt. Öppna port **443** på Windows-brandväggen, slutför du följande:

1. Öppna ett Windows PowerShell-fönster med administratörsbehörighet.
2. Om du använder en annan port än 443 när du har konfigurerat HTTPS-slutpunkt på den virtuella datorn, uppdatera port i följande kommando och sedan köra kommandot:
   
        New-NetFirewallRule -DisplayName “Report Server (TCP on port 443)” -Direction Inbound –Protocol TCP –LocalPort 443
3. När kommandot har slutförts **Ok** visas i Kommandotolken.

För att kontrollera att porten är öppen, öppna ett Windows PowerShell-fönster och kör följande kommando:

    get-netfirewallrule | where {$_.displayname -like "*report*"} | select displayname,enabled,action

## <a name="verify-the-configuration"></a>Kontrollera att konfigurationen
Öppna din webbläsare med administratörsbehörighet för att verifiera att den grundläggande rapportfunktioner för server fungerar nu, och bläddra till följande rapport server ad report manager URL: er:

* Bläddra till rapportserverns URL på den virtuella datorn:
  
        http://localhost/reportserver
* Gå till URL: en för report Manager på den virtuella datorn:
  
        http://localhost/Reports
* Från din lokala dator, bläddra till den **remote** rapportera Manager på den virtuella datorn. Uppdatera DNS-namn i följande exempel efter behov. När du tillfrågas om ett lösenord, använda administratörsautentiseringsuppgifter som du skapade när den virtuella datorn har etablerats. Användarnamnet är i [domän]\[användarnamn] format, där domänen är det Virtuella datornamnet, till exempel ssrsnativecloud\testuser. Om du inte använder HTTP**S**, ta bort den **s** i URL: en. Se nästa avsnitt för information om hur du skapar fler användare på den virtuella datorn.
  
        https://ssrsnativecloud.cloudapp.net/Reports
* Bläddra till fjärranslutna rapportserverns URL från din lokala dator. Uppdatera DNS-namn i följande exempel efter behov. Om du inte använder HTTPS kan du ta bort s i URL: en.
  
        https://ssrsnativecloud.cloudapp.net/ReportServer

## <a name="create-users-and-assign-roles"></a>Skapa användare och tilldela roller
När du konfigurerar och verifierar rapportservern, är en gemensam administrativa uppgift att skapa en eller flera användare och tilldela användare till Reporting Services-roller. Mer information finns i följande:

* [Skapa ett lokalt användarkonto](https://technet.microsoft.com/library/cc770642.aspx)
* [Bevilja användaren åtkomst till en rapportserver (Report Manager)](https://msdn.microsoft.com/library/ms156034.aspx))
* [Skapa och hantera rolltilldelningar](https://msdn.microsoft.com/library/ms155843.aspx)

## <a name="to-create-and-publish-reports-to-the-azure-virtual-machine"></a>Skapa och publicera rapporter till Azure-dator
I följande tabell sammanfattas några av de tillgängliga alternativ för att publicera befintliga rapporter från en lokal dator till rapportservern finns på Microsoft Azure-dator:

* **Skriptet med RS.exe**: Använd RS.exe-skript för att kopiera rapportobjekt från och befintlig rapportserver till din Microsoft Azure-dator. Mer information finns i avsnittet ”enhetligt läge till enhetligt läge – Microsoft Azure-dator” i [exempel Reporting Services rs.exe skript för att migrera innehåll mellan rapportservrar](https://msdn.microsoft.com/library/dn531017.aspx).
* **Report Builder**: den virtuella datorn innehåller klicka-en gång version av Microsoft SQL Server Report Builder. Starta Report builder först gången på den virtuella datorn:
  
  1. Starta din webbläsare med administrativa privilegier.
  2. Bläddra till report manager på den virtuella datorn och klicka på **Report Builder** i menyfliksområdet.
     
     Mer information finns i [installerar och avinstallerar stöd för Report Builder](https://technet.microsoft.com/library/dd207038.aspx).
* **SQL Server Data Tools: VM**: Om du har skapat den virtuella datorn med SQL Server 2012 sedan SQL Server Data Tools är installerat på den virtuella datorn och kan användas för att skapa **Report Server-projekt** och rapporter på den virtuella datorn. SQL Server Data Tools kan publicera rapporter till rapportservern på den virtuella datorn.
  
    Om du har skapat den virtuella datorn med SQLServer 2014 kan du installera SQL Server Data Tools - BI för visual Studio. Mer information finns i följande:
  
  * [Microsoft SQL Server Data Tools – Business Intelligence för Visual Studio 2013](https://www.microsoft.com/download/details.aspx?id=42313)
  * [Microsoft SQL Server Data Tools – Business Intelligence för Visual Studio 2012](https://www.microsoft.com/download/details.aspx?id=36843)
  * [SQL Server Data Tools och SQL Server Business Intelligence (SSDT BI)](https://docs.microsoft.com/sql/ssdt/previous-releases-of-sql-server-data-tools-ssdt-and-ssdt-bi)
* **SQL Server Data Tools: Remote**: skapa ett Reporting Services-projekt i SQL Server Data Tools som innehåller Reporting Services-rapporter på din lokala dator. Konfigurera projektet för att ansluta till webbtjänstens URL.
  
    ![SSDT Projektegenskaper för SSRS-projekt](./media/virtual-machines-windows-classic-ps-sql-report/IC650114.gif)
* **Använd skript för**: Använd skript för att kopiera rapportserverinnehåll. Mer information finns i [exempel Reporting Services rs.exe skript för att migrera innehåll mellan rapportservrar](https://msdn.microsoft.com/library/dn531017.aspx).

## <a name="minimize-cost-if-you-are-not-using-the-vm"></a>Minimera kostnaderna om du inte använder den virtuella datorn
> [!NOTE]
> För att minimera kostnader för ditt Azure Virtual Machines som, stänga av den virtuella datorn från Azure-portalen. Om du använder Windows Energialternativ inuti en virtuell dator för att stänga av den virtuella datorn, debiteras du ändå samma belopp för den virtuella datorn. För att minska kostnader, måste du stänga av den virtuella datorn i Azure-portalen. Om du inte längre behöver den virtuella datorn kan du komma ihåg att ta bort den virtuella datorn och de associerade VHD-filerna för att undvika lagringskostnader. Mer information finns i avsnittet vanliga frågor och svar på [prisinformation om virtuella datorer](https://azure.microsoft.com/pricing/details/virtual-machines/).

## <a name="more-information"></a>Mer information
### <a name="resources"></a>Resurser
* Liknande innehåll som relaterar till en enskild server-distribution av SQL Server Business Intelligence och SharePoint 2013, se [använda Windows PowerShell för att skapa en Azure virtuell dator med SQL Server BI och SharePoint 2013](https://blogs.technet.microsoft.com/ptsblog/2013/10/24/use-powershell-to-create-a-windows-azure-vm-with-sql-server-bi-and-sharepoint-2013/).
* Allmän information som rör distribution av SQL Server Business Intelligence på Azure virtuella datorer finns i [SQL Server Business Intelligence i Azure Virtual Machines](virtual-machines-windows-classic-ps-sql-bi.md).
* Mer information om kostnaden för Azure-beräkningsavgifter finns i fliken Virtual Machines i [Azures priskalkylator](https://azure.microsoft.com/pricing/calculator/?scenario=virtual-machines).

### <a name="community-content"></a>Community-innehåll
* Steg för steg instruktioner om hur du skapar en rapportserver för Reporting Services i enhetligt läge utan att använda skriptet finns i [som är värd för SQL Reporting Service på Azure-dator](http://adititechnologiesblog.blogspot.in/2012/07/hosting-sql-reporting-service-on-azure.html).

### <a name="links-to-other-resources-for-sql-server-in-azure-vms"></a>Länkar till andra resurser för SQL Server i virtuella Azure-datorer
[SQLServer på Azure virtuella datorer – översikt](../sql/virtual-machines-windows-sql-server-iaas-overview.md)

