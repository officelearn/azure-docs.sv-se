---
title: Hantera Azure Log Analytics agenten | Microsoft Docs
description: Den här artikeln beskriver de olika administrativa uppgifter som du vanligtvis utför under livscykeln för Microsofts övervakning Agent (MMA) har distribuerats på en dator.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: magoedte
ms.component: na
ms.openlocfilehash: 908418dffaffc25be320bd0008edf03493aa4e55
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37128800"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Hantera och underhålla logganalys-agenten för Windows och Linux

Du kan behöva konfigurera om agenten eller ta bort den från datorn om du har nått pensionering stadium i livscykeln efter första distributionen av Windows eller Linux-agenten för Log Analytics.  Du kan enkelt hantera dessa rutinunderhåll manuellt eller via automation, vilket minskar både åtgärdsfel och kostnader.

## <a name="adding-or-removing-a-workspace"></a>Lägga till eller ta bort en arbetsyta 

### <a name="windows-agent"></a>Windows-agenten

#### <a name="update-settings-from-control-panel"></a>Uppdatera inställningar från Kontrollpanelen

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**.
3. Välj **Microsoft Monitoring Agent** och klicka sedan på den **Azure logganalys (OMS)** fliken.
4. Om du tar bort en arbetsyta, markerar du den och klicka sedan på **ta bort**. Upprepa det här steget för alla andra arbetsytan som du vill att agenten att sluta rapportera till.
5. Om du lägger till en arbetsyta, klickar du på **Lägg till** och på den **lägga till en Log Analytics-arbetsyta** dialogrutan, klistra in arbetsyte-ID och Arbetsytenyckel (primärnyckel). Om datorn ska rapportera till logganalys-arbetsytan i Azure Government molnet, väljer du Azure som tillhör amerikanska myndigheter från listrutan Azure-molnet. 
6. Spara ändringarna genom att klicka på **OK**.

#### <a name="remove-a-workspace-using-powershell"></a>Ta bort en arbetsyta med hjälp av PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Lägg till en arbetsyta i Azure kommersiella med hjälp av PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Lägga till en arbetsyta i Azure för som tillhör amerikanska myndigheter med hjälp av PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>”
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Om du tidigare har använt kommandorad eller skript att installera eller konfigurera agenten `EnableAzureOperationalInsights` ersattes med `AddCloudWorkspace` och `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Linux-agent
Följande steg visar hur du konfigurerar om Linux-agent om du vill registrera den med en annan arbetsyta eller vill du ta bort en arbetsyta från dess konfiguration.  

1.  Kör följande kommando för att verifiera den är registrerad på en arbetsyta.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Det ska returnera statusen liknar följande exempel- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Det är viktigt att statusen visar även agenten körs, annars följande steg för att konfigurera om agenten kommer inte att slutföra.  

2. Om den redan är registrerad med en arbetsyta kan du ta bort registrerade arbetsytan genom att köra följande kommando.  Annars om den inte är registrerad, fortsätter du till nästa steg.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Om du vill registrera med en annan arbetsyta, kör du kommandot `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. Kör kommandot för att verifiera ändringarna tog påverkar.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Det ska returnera statusen liknar följande exempel- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Agent-tjänsten behöver inte startas om för att ändringarna ska börja gälla.

## <a name="update-proxy-settings"></a>Uppdatera proxyinställningarna 
Konfigurera agenten för kommunikation till tjänsten via en proxyserver eller [OMS Gateway](log-analytics-oms-gateway.md) efter distributionen, använda något av följande metoder för att slutföra åtgärden.

### <a name="windows-agent"></a>Windows-agenten

#### <a name="update-settings-using-control-panel"></a>Uppdatera inställningar med hjälp av Kontrollpanelen

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**.
3. Välj **Microsoft Monitoring Agent** och klicka sedan på den **proxyinställningar** fliken.
4. Klicka på **använder en proxyserver** och ange en URL och portnummer för proxyservern eller gateway. Om din proxyserver eller OMS Gateway kräver autentisering, skriver du användarnamn och lösenord för att autentisera och klicka sedan på **OK**. 

#### <a name="update-settings-using-powershell"></a>Uppdatera inställningar med hjälp av PowerShell 

Kopiera följande PowerShell exempelkod, uppdatera det med information som är specifika för din miljö och spara om filen med filnamnstillägget PS1.  Kör skriptet på varje dator som ansluter direkt till Log Analytics-tjänsten.

```PowerShell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

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
```  

### <a name="linux-agent"></a>Linux-agent
Utför följande steg om Linux-datorer som behöver kommunicera via en proxyserver eller OMS Gateway till logganalys.  Konfigurationsvärdet för proxyn har följande syntax `[protocol://][user:password@]proxyhost[:port]`.  Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern.

1. Redigera filen `/etc/opt/microsoft/omsagent/proxy.conf` genom att köra följande kommandon och ändra värdena enligt dina specifika inställningar.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="uninstall-agent"></a>Avinstallera agenten
Använd någon av följande procedurer för att avinstallera Windows eller Linux-agenten med hjälp av guiden kommandorads- eller installationen.

### <a name="windows-agent"></a>Windows-agenten

#### <a name="uninstall-from-control-panel"></a>Avinstallera från Kontrollpanelen
1. Logga in på datorn med ett konto som har administrativa rättigheter.  
2. I **Kontrollpanelen**, klickar du på **program och funktioner**.
3. I **program och funktioner**, klickar du på **Microsoft Monitoring Agent**, klickar du på **avinstallera**, och klicka sedan på **Ja**.

>[!NOTE]
>Kan också köra installationsguiden för agenten genom att dubbelklicka på **MMASetup -<platform>.exe**, som är tillgänglig för nedladdning från en arbetsyta i Azure-portalen.

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommandoraden
Den hämta filen för agenten är ett fristående installationsprogram som skapats med IExpress.  Installationsprogrammet för agenten och stödfilerna finns i paketet och behöver extraheras för att kunna återställas med hjälp av kommandoraden som visas i följande exempel. 

1. Logga in på datorn med ett konto som har administrativa rättigheter.  
2. Extrahera installationsfilerna för agent från en upphöjd kommandotolk kör `extract MMASetup-<platform>.exe` och blir du ombedd för sökvägen till filerna ska extraheras.  Du kan också ange sökvägen genom att skicka argumenten `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Mer information om kommandoradsverktyget switchar som stöds av IExpress finns [kommandoradsväxlar för IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) och uppdatera sedan exempel så att de passar dina behov.
3. I Kommandotolken skriver du `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Linux-agent
Kör följande kommando för att ta bort agenten på Linux-datorn.  Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurera agent ska rapportera till en Operations Manager-hanteringsgrupp

### <a name="windows-agent"></a>Windows-agenten
Utför följande steg om du vill konfigurera OMS-Agent för Windows att rapportera till en System Center Operations Manager-hanteringsgrupp. 

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**. 
3. Klicka på **Microsoft Monitoring Agent** och klicka sedan på den **Operations Manager** fliken.
4. Om Operations Manager-servrar har integrering med Active Directory, klickar du på **automatiskt uppdatera hanteringsgrupptilldelningar från AD DS**.
5. Klicka på **Lägg till** att öppna den **lägga till en Hanteringsgrupp** dialogrutan.
6. I **hanteringsgruppnamn** skriver du namnet på hanteringsgruppen.
7. I den **primära hanteringsserver** skriver du namnet på den primära hanteringsservern.
8. I den **hanteringsserverporten** anger TCP-portnummer.
9. Under **Agentåtgärdskontot**, välja kontot Lokalt System eller en lokal domänkonto.
10. Klicka på **OK** att stänga den **lägga till en Hanteringsgrupp** dialogrutan och klicka sedan på **OK** att stänga den **egenskaper för Microsoft Monitoring Agent** i dialogrutan.

### <a name="linux-agent"></a>Linux-agent
Utför följande steg för att konfigurera OMS-Agent för Linux rapportera till en System Center Operations Manager-hanteringsgrupp. 

1. Redigera filen `/etc/opt/omi/conf/omiserver.conf`
2. Se till att den rad som början med `httpsport=` definierar port 1270. Exempelvis: `httpsport=1270`
3. Starta om OMI-servern: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Nästa steg

Granska [felsökning av Linux-agenten](log-analytics-agent-linux-support.md) om du får problem när du installerar eller hantera agenten.  