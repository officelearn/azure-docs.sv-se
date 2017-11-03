---
title: Ansluta Windows-datorer till Azure Log Analytics | Microsoft Docs
description: "Den här artikeln visar stegen för att ansluta Windows-datorer i din lokala infrastruktur till Log Analytics-tjänsten med hjälp av en anpassad version av Microsoft Monitoring Agent (MMA)."
services: log-analytics
documentationcenter: 
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 932f7b8c-485c-40c1-98e3-7d4c560876d2
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/03/2017
ms.author: magoedte
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e5f04f3b9135167c0f339c58323ebd931b260109
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="connect-windows-computers-to-the-log-analytics-service-in-azure"></a>Ansluta Windows-datorer till Log Analytics-tjänsten i Azure

Den här artikeln visar stegen för att ansluta Windows-datorer i din lokala infrastruktur till OMS-arbetsytor med hjälp av en anpassad version av Microsoft Monitoring Agent (MMA). Du måste installera och ansluta agenter för alla datorer som du vill ska publiceras för dem att skicka data till logganalys-tjänsten och för att visa och arbeta med data. Varje agent kan rapportera till flera arbetsytor.

Du kan installera agenter med installationsprogrammet, kommandorad, eller med önskad tillstånd Configuration (DSC) i Azure Automation.  

>[!NOTE]
För virtuella datorer som körs i Azure, kan du förenkla installationen med hjälp av den [tillägg för virtuell dator](log-analytics-azure-vm-extension.md).

På datorer med Internet-anslutning använder agenten anslutning till Internet för att skicka data till OMS. För datorer som inte har Internetanslutning kan du använda en proxyserver eller [OMS Gateway](log-analytics-oms-gateway.md).

Ansluta din Windows-datorer till OMS är enkelt med tre enkla steg:

1. Hämta installationsfilen för agenten från OMS-portalen
2. Installera agenten med hjälp av vilken metod du väljer
3. Konfigurera agenten eller Lägg till ytterligare arbetsytor, om det behövs

Följande diagram visar relationen mellan Windows-datorer och OMS när du har installerat och konfigurerat agenter.

![OMS-direct-agent-diagram](./media/log-analytics-windows-agents/oms-direct-agent-diagram.png)

Om din IT-säkerhetsprinciper inte tillåter datorer i nätverket för att ansluta till Internet, kan du konfigurera datorer för att ansluta till OMS-Gateway. Mer information och anvisningar om hur du konfigurerar servrarna kan kommunicera via en OMS-Gateway till OMS-tjänsten finns [ansluta datorer till OMS med OMS-gatewayen](log-analytics-oms-gateway.md).

## <a name="system-requirements-and-required-configuration"></a>Systemkrav och nödvändiga konfigurationsinställningar
Granska följande information för att säkerställa att du uppfyller kraven innan du installerar eller distribuera agenter.

- Du kan bara installera OMS MMA på datorer som kör Windows Server 2008 SP 1 eller senare eller Windows 7 SP1 eller senare.
- Du behöver en Azure-prenumeration.  Mer information finns i [Kom igång med logganalys](log-analytics-get-started.md).
- Alla Windows-datorer måste kunna ansluta till Internet med hjälp av HTTPS eller OMS-Gateway. Den här anslutningen kan vara direkt via en proxy eller via OMS-Gateway.
- Du kan installera OMS MMA på fristående datorer, servrar och virtuella datorer. Om du vill ansluta Azure som värd för virtuella datorer till OMS finns [ansluta Azure virtuella datorer till logganalys](log-analytics-azure-vm-extension.md).
- Agenten måste använda TCP-port 443 för olika resurser.

### <a name="network"></a>Nätverk

De måste ha åtkomst till nätverksresurser, inklusive portnummer och URL: er för domän för Windows-agenter att ansluta till och registrera med OMS-tjänsten.

- För proxyservrar måste du se till att lämpliga proxy serverresurser konfigureras i agentinställningarna.
- För brandväggar som begränsar åtkomsten till Internet, måste du eller ditt nätverk engineers konfigurera brandväggen att tillåta åtkomst till OMS. Ingen åtgärd krävs i agentinställningarna.

I följande tabell visas resurser som krävs för kommunikation.

>[!NOTE]
>Några av följande resurser nämnt åtgärdsinformation, som hette tidigare för Log Analytics.

| Agentresurs | Portar | Kringgå HTTPS-kontroll |
|---|---|---|
| *.ods.opinsights.azure.com | 443 | Ja |
| *.oms.opinsights.azure.com | 443 | Ja |
| *.blob.core.windows.net | 443 | Ja |
| *.azure-automation.net | 443 | Ja |



## <a name="download-the-agent-setup-file-from-oms"></a>Hämta installationsfilen för agenten från OMS
1. I den [OMS-portalen](https://www.mms.microsoft.com)på den **översikt** klickar du på den **inställningar** panelen.  Klicka på den **anslutna källor** högst upp.  
    ![Fliken för anslutna datakällor](./media/log-analytics-windows-agents/oms-direct-agent-connected-sources.png)
2. Klicka på **Windows-servrar** och klicka sedan på **ladda ned Windows Agent** gäller för din typ av processor att ladda ned installationsfilen.
3. Till höger om **arbetsyte-ID**, klickar du på ikonen kopiera och klistra in det ID: T i anteckningar.
4. Till höger om **primärnyckel**, klicka på ikonen kopiera och klistra in nyckeln i anteckningar.     

## <a name="install-the-agent-using-setup"></a>Installera agenten med hjälp av installationsprogrammet
1. Kör installationsprogrammet för att installera agenten på en dator som du vill hantera.
2. På sidan Välkommen **nästa**.
3. På sidan Licensvillkor Läs licensvillkoren och klicka sedan på **jag accepterar**.
4. På sidan målmappen ändra eller behålla standardinstallationsmappen och klicka sedan på **nästa**.
5. På sidan installationsalternativ för Agent kan du ansluta agenten till Azure logganalys (OMS), Operations Manager, eller lämna alternativen tomt om du vill konfigurera agenten senare. Klicka på **Nästa**.   
    - Om du vill ansluta till Azure logganalys (OMS), klistra in den **arbetsyte-ID** och **Arbetsytenyckel (primärnyckel)** som du kopierade i anteckningar i föregående procedur och klicka sedan på **nästa**.  
        ![Klistra in arbetsyte-ID och primärnyckel](./media/log-analytics-windows-agents/connect-workspace.png)
    - Om du vill ansluta till Operations Manager, skriver den **Hanteringsgruppnamn**, **hanteringsservern** namn, och **Hanteringsserverporten**, och klicka sedan på **nästa**. På sidan Agentåtgärdskontot välja kontot Lokalt System eller ett domänkonto som är lokala och klicka sedan på **nästa**.  
        ![konfiguration av hanteringsgrupp](./media/log-analytics-windows-agents/oms-mma-om-setup01.png)![agentåtgärdskontot](./media/log-analytics-windows-agents/oms-mma-om-setup02.png)

6. Klar att installera sida, granska dina val och klicka sedan på **installera**.
7. Om konfigurationen har slutförts, klickar du på **Slutför**.
8. När du är färdig den **Microsoft Monitoring Agent** visas i **Kontrollpanelen**. Du kan granska konfigurationen av det och kontrollera att agenten är ansluten till Operational Insights (OMS). När du är ansluten till OMS agenten visas ett meddelande om: **i Microsoft Monitoring Agent har lyckats ansluta till tjänsten Microsoft Operations Management Suite.**

## <a name="configure-proxy-settings"></a>Konfigurera proxyinställningar

Du kan använda följande procedur för att konfigurera proxyinställningar för Microsoft Monitoring Agent med Kontrollpanelen. Du måste använda den här proceduren för varje server. Om du har många servrar att konfigurera är det kanske enklare att använda ett skript för att automatisera processen. I så fall kan du läsa mer i nästa procedur [Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript](#to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script).

### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-control-panel"></a>Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av Kontrollpanelen
1. Öppna **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent**.
3. Klicka på fliken **Proxyinställningar**.  
    ![fliken proxyinställningar](./media/log-analytics-windows-agents/proxy-direct-agent-proxy.png)
4. Välj **Använd en proxyserver** och ange URL och portnummer, om det behövs, som i exemplet nedan. Om proxyservern kräver autentisering anger du användarnamn och lösenord för att få åtkomst till proxyservern.


### <a name="verify-agent-connectivity-to-oms"></a>Verifiera agenten anslutning till OMS

Du kan enkelt kontrollera om dina agenter kommunicerar med OMS på följande sätt:

1.  Öppna Kontrollpanelen på datorn med Windows-agenten.
2.  Öppna Microsoft Monitoring Agent.
3.  Klicka på fliken Azure logganalys (OMS).
4.  Du bör se att agenten har ansluten till Operations Management Suite-tjänsten i kolumnen Status.

![Agent som är ansluten](./media/log-analytics-windows-agents/mma-connected.png)


### <a name="to-configure-proxy-settings-for-the-microsoft-monitoring-agent-using-a-script"></a>Konfigurera proxyinställningar för Microsoft Monitoring Agent med hjälp av ett skript
Kopiera följande exempel, uppdatera den information som är specifik för din miljö, spara filen med filnamnstillägget PS1 och kör sedan skriptet på varje dator som ansluter direkt till OMS-tjänsten.

    param($ProxyDomainName="http://proxy.contoso.com:80", $cred=(Get-Credential))

    # First we get the Health Service configuration object.  We need to determine if we
    #have the right update rollup with the API we need.  If not, no need to run the rest of the script.
    $healthServiceSettings = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'

    $proxyMethod = $healthServiceSettings | Get-Member -Name 'SetProxyInfo'

    if (!$proxyMethod)
    {
         Write-Output 'Health Service proxy API not present, will not update settings.'
         return
    }

    Write-Output "Clearing proxy settings."
    $healthServiceSettings.SetProxyInfo('', '', '')

    $ProxyUserName = $cred.username

    Write-Output "Setting proxy to $ProxyDomainName with proxy username $ProxyUserName."
    $healthServiceSettings.SetProxyInfo($ProxyDomainName, $ProxyUserName, $cred.GetNetworkCredential().password)



## <a name="install-the-agent-using-the-command-line"></a>Installera agenten med hjälp av kommandoraden
- Ändra och Använd följande exempel för att installera agenten med hjälp av kommandoraden. Exemplet utför en fullständigt tyst installation.

    >[!NOTE]
    Om du vill uppgradera en agent måste du använda logganalys scripting-API. Se avsnittet nästa för att uppgradera en agent.

    ```dos
    MMASetup-AMD64.exe /Q:A /R:N /C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1"
    ```

Agenten använder IExpress som dess Self-Extractor med hjälp av den `/c` kommando. Du kan se kommandoradsväxlar vid [kommandoradsväxlar för IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) och uppdatera sedan exempel så att de passar dina behov.

|MMA-specifika alternativ                   |Anteckningar         |
|---------------------------------------|--------------|
|ADD_OPINSIGHTS_WORKSPACE               | 1 = konfigurera agenten ska rapportera till en arbetsyta                |
|OPINSIGHTS_WORKSPACE_ID                | Arbetsyte-Id (guid) för arbetsytan att lägga till                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbetsytenyckel som används för att först autentisera med arbetsytan |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Ange molnmiljön där arbetsytan finns <br> 0 = kommersiella azuremolnet (standard) <br> 1 = azure Government |
|OPINSIGHTS_PROXY_URL               | URI för proxy som ska användas |
|OPINSIGHTS_PROXY_USERNAME               | Användarnamn för att komma åt en autentiserad proxyserver |
|OPINSIGHTS_PROXY_PASSWORD               | Lösenord för åtkomst till en autentiserad proxyserver |

>[!NOTE]
Installera agenten med Ingen arbetsyta som konfigurerats för att undvika träffa kommandorad maxlängden för IExpress, och använda ett skript för att ange konfigurationen för arbetsytan.

>[!NOTE]
Om du får en `Command line option syntax error.` när du använder den `OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE` parameter, kan du använda följande lösning:
```dos
MMASetup-AMD64.exe /C /T:.\MMAExtract
cd .\MMAExtract
setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID=<your workspace id> OPINSIGHTS_WORKSPACE_KEY=<your workspace key> AcceptEndUserLicenseAgreement=1
```

## <a name="add-a-workspace-using-a-script"></a>Lägg till en arbetsyta med hjälp av ett skript
Lägg till en arbetsyta med följande exempel logganalys agent scripting-API:

```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

Lägg till en arbetsyta i Azure för US Government använda följande skriptexempel.
```PowerShell
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
Om du tidigare har använt kommandorad eller skript att installera eller konfigurera agenten `EnableAzureOperationalInsights` ersattes med `AddCloudWorkspace`.

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installera agenten i Azure Automation DSC

Du kan använda följande exempel på skript för att installera agenten i Azure Automation DSC. Exemplet installerar du 64-bitars-agenten som identifieras av den `URI` värde. Du kan också använda 32-bitars version genom att ersätta URI-värdet. URI: er för båda versionerna är:

- Windows 64-bitars agent - https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bitars agent - https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
Den här proceduren och skript exempel uppgraderar inte en befintlig agent.

1. Importera xPSDesiredStateConfiguration DSC-modul från [http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](http://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) i Azure Automation.  
2.  Skapa en variabel Azure Automation-tillgångar för *OPSINSIGHTS_WS_ID* och *OPSINSIGHTS_WS_KEY*. Ange *OPSINSIGHTS_WS_ID* till OMS logganalys arbetsyte-ID och ange *OPSINSIGHTS_WS_KEY* till den primära nyckeln för arbetsytan.
3.  Använd följande skript och spara den som MMAgent.ps1
4.  Ändra och Använd följande exempel för att installera agenten i Azure Automation DSC. Importera MMAgent.ps1 till Azure Automation med hjälp av Azure Automation-gränssnittet eller cmdlet.
5.  Tilldela en nod i konfigurationen. Noden kontrollerar konfigurationen inom 15 minuter och MMA skickas till noden.

```PowerShell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"


    Import-DscResource -ModuleName xPSDesiredStateConfiguration

    Node OMSnode {
        Service OIService
        {
            Name = "HealthService"
            State = "Running"
            DependsOn = "[Package]OI"
        }

        xRemoteFile OIPackage {
            Uri = "https://go.microsoft.com/fwlink/?LinkId=828603"
            DestinationPath = $OIPackageLocalPath
        }

        Package OI {
            Ensure = "Present"
            Path  = $OIPackageLocalPath
            Name = "Microsoft Monitoring Agent"
            ProductId = "8A7F2C51-4C7D-4BFD-9014-91D11F24AAE2"
            Arguments = '/C:"setup.exe /qn ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}


```

### <a name="get-the-latest-productid-value"></a>Hämta det senaste värdet ProductId

Den `ProductId value` i MMAgent.ps1 skript som är unik för varje agentversion. När en uppdaterad version av varje agent publiceras ändras ProductId värdet. Så när ProductId ändras i framtiden, kan du hitta agentversionen med hjälp av ett enkelt skript. Du kan använda följande skript för att hämta värdet för installerade ProductId när du har agent-version som är installerad på en testserver. Du kan uppdatera värdet i skriptet MMAgent.ps1 med det senaste ProductId-värdet.

```PowerShell
$InstalledApplications  = Get-ChildItem hklm:\SOFTWARE\Microsoft\Windows\CurrentVersion\Uninstall


foreach ($Application in $InstalledApplications)

{

     $Key = Get-ItemProperty $Application.PSPath

     if ($Key.DisplayName -eq "Microsoft Monitoring Agent")

     {

        $Key.DisplayName

        Write-Output ("Product ID is: " + $Key.PSChildName.Substring(1,$Key.PSChildName.Length -2))

     }

}  
```

## <a name="configure-an-agent-manually-or-add-additional-workspaces"></a>Konfigurera en agent manuellt eller lägga till ytterligare arbetsytor
Om du har installerat agenterna men inte konfigurerar dem eller om du vill att agenten ska rapportera till flera arbetsytor, kan du använda följande information för att aktivera en agent eller konfigurera om den. När du har konfigurerat agenten kan registreras med agenttjänsten och får nödvändiga konfigurationsinformation och hanteringspaket som innehåller information om lösning.

1. När du har installerat Microsoft Monitoring Agent kan öppna **Kontrollpanelen**.
2. Öppna **Microsoft Monitoring Agent** och klicka sedan på den **Azure logganalys (OMS)** fliken.   
3. Klicka på **Lägg till** att öppna den **lägga till en Log Analytics-arbetsyta** rutan.
4. Klistra in den **arbetsyte-ID** och **Arbetsytenyckel (primärnyckel)** som du kopierade i anteckningar i föregående procedur för den arbetsyta som du vill lägga till och klicka sedan på **OK**.  
    ![Konfigurera åtgärdsinformation](./media/log-analytics-windows-agents/add-workspace.png)

När data har samlats in från datorer som övervakas av agenten, antalet datorer som övervakas av OMS visas i OMS-portalen på den **anslutna källor** fliken i **inställningar** som **servrar anslutna**.


## <a name="to-disable-an-agent"></a>Så här inaktiverar du en agent
1. När du har installerat agenten öppna **Kontrollpanelen**.
2. Öppna Microsoft Monitoring Agent och klicka sedan på den **Azure logganalys (OMS)** fliken.
3. Välj en arbetsyta och klicka sedan på **ta bort**. Upprepa det här steget för alla andra arbetsytor.


## <a name="optionally-configure-agents-to-report-to-an-operations-manager-management-group"></a>Alternativt kan du konfigurera att agenterna ska rapportera till en Operations Manager-hanteringsgrupp

Du kan också använda MMA agenten som en Operations Manager-agent om du använder Operations Manager i din IT-infrastruktur.

### <a name="to-configure-mma-agents-to-report-to-an-operations-manager-management-group"></a>Så här konfigurerar du MMA agenterna ska rapportera till en Operations Manager-hanteringsgrupp
1.  Öppna på den dator där agenten är installerad, **Kontrollpanelen**.  
2.  Öppna **Microsoft Monitoring Agent** och klicka sedan på den **Operations Manager** fliken.  
    ![Microsoft Monitoring Agent Operations Manager-fliken](./media/log-analytics-windows-agents/om-mg01.png)
3.  Om Operations Manager-servrar har integrering med Active Directory, klickar du på **automatiskt uppdatera hanteringsgrupptilldelningar från AD DS**.
4.  Klicka på **Lägg till** att öppna den **lägga till en Hanteringsgrupp** dialogrutan.  
    ![Microsoft Monitoring Agent lägga till en Hanteringsgrupp](./media/log-analytics-windows-agents/oms-mma-om02.png)
5.  I **hanteringsgruppnamn** skriver du namnet på hanteringsgruppen.
6.  I den **primära hanteringsserver** skriver du namnet på den primära hanteringsservern.
7.  I den **hanteringsserverporten** skriver TCP-portnummer.
8.  Under **Agentåtgärdskontot**, välja kontot Lokalt System eller en lokal domänkonto.
9.  Klicka på **OK** att stänga den **lägga till en Hanteringsgrupp** dialogrutan och klicka sedan på **OK** att stänga den **egenskaper för Microsoft Monitoring Agent** dialogrutan.


## <a name="next-steps"></a>Nästa steg

- [Lägg till Log Analytics-lösningar från lösningsgalleriet](log-analytics-add-solutions.md) för att lägga till funktioner och samla in data.
