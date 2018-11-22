---
title: Hantera Azure Log Analytics-agenten | Microsoft Docs
description: Den här artikeln beskrivs de olika administrativa uppgifter som du vanligtvis utför under livscykeln för den Microsoft Monitoring Agent (MMA) distribueras på en dator.
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
ms.component: ''
ms.openlocfilehash: 103c3a61c9899c79a2e4bdbf71fe45a13d8b31a8
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284939"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Hantering och underhåll av Log Analytics-agenten för Windows och Linux

Efter den första distributionen av Windows eller Linux-agenten för Log Analytics kan du behöva konfigurera om agenten eller ta bort den från datorn om du har nått tillbakadragande steg i livscykeln.  Du kan enkelt hantera dessa rutinunderhåll manuellt eller via automatisering, vilket minskar både operativt fel och utgifter.

## <a name="adding-or-removing-a-workspace"></a>Att lägga till eller ta bort en arbetsyta 

### <a name="windows-agent"></a>Windows-agenten

#### <a name="update-settings-from-control-panel"></a>Uppdatera inställningarna från Kontrollpanelen

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**.
3. Välj **Microsoft Monitoring Agent** och klicka sedan på den **Azure Log Analytics** fliken.
4. Om du tar bort en arbetsyta, markera den och klicka sedan på **ta bort**. Upprepa det här steget för en arbetsyta som du vill att agenten att sluta rapportera till.
5. Om du lägger till en arbetsyta, klickar du på **Lägg till** på den **lägga till en Log Analytics-arbetsyta** dialogrutan, klistra in arbetsyte-ID och Arbetsytenyckel (primärnyckel). Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du Azure US Government från listrutan Azure-molnet. 
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
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Lägg till en arbetsyta i Azure för amerikanska myndigheter med hjälp av PowerShell 

```PowerShell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Om du tidigare har använt den kommandorad eller ett skript att installera eller konfigurera agenten `EnableAzureOperationalInsights` ersattes med `AddCloudWorkspace` och `RemoveCloudWorkspace`.
>

### <a name="linux-agent"></a>Linux-agenten
Följande steg visar hur du konfigurerar om Linux-agenten om du vill registrera den med en annan arbetsyta eller vill du ta bort en arbetsyta från dess konfiguration.  

1.  Kör följande kommando för att verifiera den är registrerad till en arbetsyta.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Den ska returnera statusen liknar följande exempel- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Det är viktigt att även statusen agenten körs, annars följande steg för att konfigurera om agenten kommer inte att slutföra.  

2. Om den redan är registrerad med en arbetsyta kan du ta bort registrerade arbetsytan genom att köra följande kommando.  Annars om det inte är registrerad, fortsätter du till nästa steg.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`  
    
3. Om du vill registrera med en annan arbetsyta, kör du kommandot `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]` 
4. Om du vill kontrollera ändringarna tog påverkar, kör du kommandot.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l` 

    Den ska returnera statusen liknar följande exempel- 

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Agent-tjänsten behöver inte startas om för att ändringarna ska börja gälla.

## <a name="update-proxy-settings"></a>Inställningar för proxy 
Konfigurera agenten för kommunikation till tjänsten via en proxyserver eller [Log Analytics gateway](log-analytics-oms-gateway.md) efter distributionen kan du använda någon av följande metoder för att slutföra den här uppgiften.

### <a name="windows-agent"></a>Windows-agenten

#### <a name="update-settings-using-control-panel"></a>Uppdatera inställningar med hjälp av Kontrollpanelen

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**.
3. Välj **Microsoft Monitoring Agent** och klicka sedan på den **proxyinställningar** fliken.
4. Klicka på **använder en proxyserver** och anger URL och portnummer för proxyservern eller gateway. Om din proxyserver eller Log Analytics gateway kräver autentisering anger du användarnamn och lösenord för att autentisera och klicka sedan på **OK**. 

#### <a name="update-settings-using-powershell"></a>Uppdatera inställningar med hjälp av PowerShell 

Kopiera följande PowerShell exempelkod, uppdatera den information som är specifika för din miljö och spara det med filnamnstillägget PS1.  Kör skriptet på varje dator som ansluter direkt till Log Analytics-tjänsten.

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

### <a name="linux-agent"></a>Linux-agenten
Utför följande steg om Linux-datorerna måste kommunicera via en proxyserver eller Log Analytics-gateway.  Konfigurationsvärdet för proxyn har följande syntax `[protocol://][user:password@]proxyhost[:port]`.  Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern.

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

## <a name="uninstall-agent"></a>Avinstallera agent
Använd någon av följande procedurer för att avinstallera Windows eller Linux-agenten med hjälp av guiden kommandoraden eller konfiguration.

### <a name="windows-agent"></a>Windows-agenten

#### <a name="uninstall-from-control-panel"></a>Avinstallera från Kontrollpanelen
1. Logga in på datorn med ett konto som har administrativa rättigheter.  
2. I **Kontrollpanelen**, klickar du på **program och funktioner**.
3. I **program och funktioner**, klickar du på **Microsoft Monitoring Agent**, klickar du på **avinstallera**, och klicka sedan på **Ja**.

>[!NOTE]
>Installationsguiden för agenten kan även köra genom att dubbelklicka på **MMASetup -<platform>.exe**, som är tillgänglig för nedladdning från en arbetsyta i Azure-portalen.

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommandoraden
Den hämta filen för agenten är ett fristående installationsprogram som skapats med IExpress.  Installationsprogrammet för agenten och filerna som ingår i paketet och måste extraheras för att kunna återställas med hjälp av kommandoraden som visas i följande exempel. 

1. Logga in på datorn med ett konto som har administrativa rättigheter.  
2. Extrahera installationsfilerna för agent från en upphöjd kommandotolk kör `extract MMASetup-<platform>.exe` och du blir ombedd du sökvägen till filerna ska extraheras.  Du kan också ange sökvägen genom att skicka argument `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`.  Läs mer på kommandoraden switchar som stöds av IExpress [kommandoradsväxlar för IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) och uppdatera sedan det exemplet så att den passar dina behov.
3. I Kommandotolken, Skriv `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`.  

### <a name="linux-agent"></a>Linux-agenten
Kör följande kommando för att ta bort agenten på Linux-datorn.  Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurera agent rapporterar till en Operations Manager-hanteringsgrupp

### <a name="windows-agent"></a>Windows-agenten
Utför följande steg om du vill konfigurera Log Analytics-agenten för Windows att rapportera till en hanteringsgrupp för System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)] 

1. Logga in på datorn med ett konto som har administrativa rättigheter.
2. Öppna **Kontrollpanelen**. 
3. Klicka på **Microsoft Monitoring Agent** och klicka sedan på den **Operations Manager** fliken.
4. Om Operations Manager-servrar har integrering med Active Directory, klickar du på **automatiskt uppdatera hanteringsgrupptilldelningar från AD DS**.
5. Klicka på **Lägg till** att öppna den **lägga till en Hanteringsgrupp** dialogrutan.
6. I **hanteringsgruppnamn** skriver namnet på hanteringsgruppen.
7. I den **primära hanteringsserver** skriver namnet på den primära hanteringsservern.
8. I den **hanteringsserverport** skriver TCP-portnumret.
9. Under **Agentåtgärdskontot**, väljer du antingen kontot Lokalt System eller ett lokala domänkonto.
10. Klicka på **OK** att Stäng den **lägga till en Hanteringsgrupp** dialogrutan och klicka sedan på **OK** att Stäng den **egenskaper för Microsoft Monitoring Agent** dialogrutan.

### <a name="linux-agent"></a>Linux-agenten
Utför följande steg om du vill konfigurera Log Analytics-agenten för Linux för att rapportera till en hanteringsgrupp för System Center Operations Manager. 

[!INCLUDE [log-analytics-agent-note](../../includes/log-analytics-agent-note.md)]

1. Redigera filen `/etc/opt/omi/conf/omiserver.conf`
2. Kontrollera att den rad som början med `httpsport=` definierar port 1270. Exempel: `httpsport=1270`
3. Starta om OMI-servern: `sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Nästa steg

Granska [felsökning av Linux-agenten](log-analytics-agent-linux-support.md) om det uppstår problem när du installerar eller hantering av agenten.  
