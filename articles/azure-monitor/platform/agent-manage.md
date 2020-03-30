---
title: Hantera Azure Log Analytics-agenten
description: I den här artikeln beskrivs de olika hanteringsuppgifter som du vanligtvis utför under livscykeln för Log Analytics Windows eller Linux-agenten som distribueras på en dator.
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/14/2019
ms.openlocfilehash: 4d0ceacd37748e9761903d02fd7e052d70b10e15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79275105"
---
# <a name="managing-and-maintaining-the-log-analytics-agent-for-windows-and-linux"></a>Hantera och underhålla Log Analytics-agenten för Windows och Linux

Efter den första distributionen av Log Analytics Windows eller Linux-agenten i Azure Monitor kan du behöva konfigurera om agenten, uppgradera den eller ta bort den från datorn om den har nått pensioneringsfasen i dess livscykel. Du kan enkelt hantera dessa rutinmässiga underhållsuppgifter manuellt eller genom automatisering, vilket minskar både driftfel och kostnader.

## <a name="upgrading-agent"></a>Uppgraderingsagent

Log Analytics-agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på distributionsscenariot och miljön som den virtuella datorn körs i. Följande metoder kan användas för att uppgradera agenten.

| Miljö | Installationsmetod | Uppgraderingsmetod |
|--------|----------|-------------|
| Azure VM | Feltillägg för Logganalysagent vm för Windows/Linux | Agenten uppgraderas automatiskt som standard om du inte har konfigurerat mallen Azure Resource Manager för att välja bort det genom att ange egenskapen *autoUpgradeMinorVersion* till **false**. |
| Anpassade Azure VM-avbildningar | Manuell installation av Log Analytics-agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommandoraden som kör Windows-installationspaketet eller Linux självextraherande och installerbara skalskriptpaket.|
| Virtuella datorer som inte är azure | Manuell installation av Log Analytics-agent för Windows/Linux | Uppdatering av virtuella datorer till den senaste versionen av agenten måste utföras från kommandoraden som kör Windows-installationspaketet eller Linux självextraherande och installerbara skalskriptpaket. |

### <a name="upgrade-windows-agent"></a>Uppgradera Windows-agent 

Om du vill uppdatera agenten på en Virtuell Windows-dator till den senaste versionen som inte är installerad med hjälp av\<tillägget\>Log Analytics VM, kör du antingen från Kommandotolken, skriptet eller någon annan automatiseringslösning eller med installationsguiden för MMASetup-plattformen .msi.  

Du kan hämta den senaste versionen av Windows-agenten från logganalysarbetsytan genom att utföra följande steg.

1. Logga in på [Azure-portalen](https://portal.azure.com).

2. Klicka på **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.

3. Välj arbetsytan i listan över Logganalysarbetsytor.

4. I logganalysarbetsytan väljer du **Avancerade inställningar**och väljer sedan **Anslutna källor**och slutligen **Windows-servrar**.

5. På sidan **Windows-servrar** väljer du lämplig **Version hämta Windows Agent** som ska hämtas beroende på processorarkitekturen i Operativsystemet Windows.

>[!NOTE]
>Under uppgraderingen av Log Analytics-agenten för Windows stöder den inte konfiguration eller omkonfigurering av en arbetsyta att rapportera till. Om du vill konfigurera agenten måste du följa någon av de metoder som stöds under [Lägga till eller ta bort en arbetsyta](#adding-or-removing-a-workspace).
>

#### <a name="to-upgrade-using-the-setup-wizard"></a>Så här uppgraderar du med installationsguiden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Kör **\<MMASetup-platform\>.exe** för att starta installationsguiden.

3. Klicka på **Nästa**på den första sidan i installationsguiden .

4. Klicka på **Jag godkänner** licensavtalet i dialogrutan **Installationsprogram för Microsoft Monitoring Agent.**

5. I dialogrutan **Installationsprogram för Microsoft Monitoring Agent** klickar du på **Uppgradera**. På statussidan visas uppgraderingsförloppet.

6. När **Microsoft Monitoring Agent-konfigurationen har slutförts.** visas klickar du på **Slutför**.

#### <a name="to-upgrade-from-the-command-line"></a>Så här uppgraderar du från kommandoraden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Om du vill extrahera agentinstallationsfilerna `MMASetup-<platform>.exe /c` från en upphöjd kommandotolkskörning och det kommer att fråga dig om sökvägen att extrahera filer till. Du kan också ange sökvägen genom `MMASetup-<platform>.exe /c /t:<Full Path>`att skicka argumenten .

3. Kör följande kommando, där D:\ är platsen för uppgraderingsloggfilen.

    ```dos
    setup.exe /qn /l*v D:\logs\AgentUpgrade.log AcceptEndUserLicenseAgreement=1
    ```

### <a name="upgrade-linux-agent"></a>Uppgradera Linux-agent 

Uppgradering från tidigare versioner (>1.0.0-47) stöds. Om du utför `--upgrade` installationen med kommandot uppgraderas alla komponenter i agenten till den senaste versionen.

Kör följande kommando för att uppgradera agenten.

`sudo sh ./omsagent-*.universal.x64.sh --upgrade`

## <a name="adding-or-removing-a-workspace"></a>Lägga till eller ta bort en arbetsyta

### <a name="windows-agent"></a>Windows-agent
Stegen i det här avsnittet är nödvändiga när du inte bara vill konfigurera om Windows-agenten för att rapportera till en annan arbetsyta eller ta bort en arbetsyta från konfigurationen, utan även när du vill konfigurera agenten så att den rapporterar till mer än en arbetsyta (vanligtvis kallas multi-homing). Konfigurera Windows-agenten så att den rapporterar till flera arbetsytor kan endast utföras efter den första installationen av agenten och med hjälp av de metoder som beskrivs nedan.    

#### <a name="update-settings-from-control-panel"></a>Uppdatera inställningar från Kontrollpanelen

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Öppna **Kontrollpanelen**.

3. Välj **Microsoft Monitoring Agent** och klicka sedan på fliken Azure Log **Analytics.**

4. Om du tar bort en arbetsyta markerar du den och klickar sedan på **Ta bort**. Upprepa det här steget för alla andra arbetsytor som du vill att agenten ska sluta rapportera till.

5. Om du lägger till en arbetsyta klickar du på **Lägg till** och klistrar du in arbetsyte-ID:t och arbetsytatangenten (primärnyckel) i dialogrutan Lägg till **en logganalysarbetsyta.** Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du Azure US Government i listrutan Azure Cloud.

6. Spara ändringarna genom att klicka på **OK**.

#### <a name="remove-a-workspace-using-powershell"></a>Ta bort en arbetsyta med PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.RemoveCloudWorkspace($workspaceId)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-commercial-using-powershell"></a>Lägga till en arbetsyta i Azure commercial med PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey)
$mma.ReloadConfiguration()
```

#### <a name="add-a-workspace-in-azure-for-us-government-using-powershell"></a>Lägga till en arbetsyta i Azure för amerikanska myndigheter med PowerShell

```powershell
$workspaceId = "<Your workspace Id>"
$workspaceKey = "<Your workspace Key>"
$mma = New-Object -ComObject 'AgentConfigManager.MgmtSvcCfg'
$mma.AddCloudWorkspace($workspaceId, $workspaceKey, 1)
$mma.ReloadConfiguration()
```

>[!NOTE]
>Om du har använt kommandoraden eller skriptet tidigare `EnableAzureOperationalInsights` för `AddCloudWorkspace` att `RemoveCloudWorkspace`installera eller konfigurera agenten ersattes agenten och .
>

### <a name="linux-agent"></a>Linux-agent
Följande steg visar hur du konfigurerar om Linux-agenten om du bestämmer dig för att registrera den med en annan arbetsyta eller ta bort en arbetsyta från dess konfiguration.

1. Om du vill kontrollera att den är registrerad på en arbetsyta kör du följande kommando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Den bör returnera en status som liknar följande exempel:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

    Det är viktigt att statusen också visar att agenten körs, annars kommer följande steg för att konfigurera om agenten inte att slutföras.

2. Om den redan är registrerad med en arbetsyta tar du bort den registrerade arbetsytan genom att köra följande kommando. Annars om den inte är registrerad går du vidare till nästa steg.

    `/opt/microsoft/omsagent/bin/omsadmin.sh -X`

3. Om du vill registrera dig med en annan arbetsyta kör du följande kommando:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -w <workspace id> -s <shared key> [-d <top level domain>]`
    
4. Kör följande kommando för att kontrollera att ändringarna trädde i kraft:

    `/opt/microsoft/omsagent/bin/omsadmin.sh -l`

    Den bör returnera en status som liknar följande exempel:

    `Primary Workspace: <workspaceId>   Status: Onboarded(OMSAgent Running)`

Agenttjänsten behöver inte startas om för att ändringarna ska börja gälla.

## <a name="update-proxy-settings"></a>Uppdatera proxyinställningar
Om du vill konfigurera agenten så att den kommunicerar med tjänsten via en proxyserver eller [Log Analytics-gateway](gateway.md) efter distributionen använder du någon av följande metoder för att slutföra den här uppgiften.

### <a name="windows-agent"></a>Windows-agent

#### <a name="update-settings-using-control-panel"></a>Uppdatera inställningar med Kontrollpanelen

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Öppna **Kontrollpanelen**.

3. Välj **Microsoft Monitoring Agent** och klicka sedan på fliken **Proxyinställningar.**

4. Klicka på **Använd en proxyserver** och ange URL och portnummer för proxyservern eller gatewayen. Om proxyservern eller Log Analytics-gatewayen kräver autentisering anger du användarnamn och lösenord för att autentisera och klickar sedan på **OK**.

#### <a name="update-settings-using-powershell"></a>Uppdatera inställningar med PowerShell

Kopiera följande exempel på PowerShell-kod, uppdatera den med information som är specifik för din miljö och spara den med ett PS1-filnamnstillägg. Kör skriptet på varje dator som ansluter direkt till Log Analytics-arbetsytan i Azure Monitor.

```powershell
param($ProxyDomainName="https://proxy.contoso.com:30443", $cred=(Get-Credential))

# First we get the Health Service configuration object. We need to determine if we
#have the right update rollup with the API we need. If not, no need to run the rest of the script.
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
Utför följande steg om dina Linux-datorer behöver kommunicera via en proxyserver eller Log Analytics-gateway. Konfigurationsvärdet för proxyn har följande syntax `[protocol://][user:password@]proxyhost[:port]`. Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern.

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
Använd någon av följande procedurer för att avinstallera Windows- eller Linux-agenten med hjälp av kommandoraden eller installationsguiden.

### <a name="windows-agent"></a>Windows-agent

#### <a name="uninstall-from-control-panel"></a>Avinstallera från Kontrollpanelen
1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Klicka på **Program och funktioner**på **Kontrollpanelen**.

3. Klicka på Microsoft **Monitoring Agent**i **Program och funktioner,** klicka på **Avinstallera**och klicka sedan på **Ja**.

>[!NOTE]
>Guiden Agentinstallationsguiden kan också köras genom att dubbelklicka på **\<\>MMASetup-plattformen .exe**, som är tillgänglig för hämtning från en arbetsyta i Azure-portalen.

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommandoraden
Den nedladdade filen för agenten är ett fristående installationspaket som skapats med IExpress. Installationsprogrammet för agenten och stödfiler finns i paketet och måste extraheras för att korrekt avinstallera med hjälp av kommandoraden som visas i följande exempel.

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Om du vill extrahera agentinstallationsfilerna `extract MMASetup-<platform>.exe` från en upphöjd kommandotolkskörning och det kommer att fråga dig om sökvägen att extrahera filer till. Du kan också ange sökvägen genom `extract MMASetup-<platform>.exe /c:<Path> /t:<Path>`att skicka argumenten . Mer information om kommandoradsväxlar som stöds av IExpress finns i [Kommandoradsväxlar för IExpress](https://support.microsoft.com/help/197147/command-line-switches-for-iexpress-software-update-packages) och sedan uppdatera exemplet så att det passar dina behov.

3. Skriv i `%WinDir%\System32\msiexec.exe /x <Path>:\MOMAgent.msi /qb`prompten .

### <a name="linux-agent"></a>Linux-agent
Kör följande kommando för att ta bort agenten på Linux-datorn. Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

## <a name="configure-agent-to-report-to-an-operations-manager-management-group"></a>Konfigurera agenten att rapportera till en Hanteringsgrupp för Operations Manager

### <a name="windows-agent"></a>Windows-agent
Utför följande steg för att konfigurera Log Analytics-agenten för Windows för att rapportera till en hanteringsgrupp för System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Öppna **Kontrollpanelen**.

3. Klicka på **Microsoft Monitoring Agent** och sedan på fliken Operations **Manager.**

4. Om Operations Manager-servrarna har integrering med Active Directory klickar du på **Uppdatera hantera grupptilldelningar automatiskt från AD DS**.

5. Klicka på **Lägg till** för att öppna dialogrutan Lägg till **en hanteringsgrupp.**

6. Skriv namnet på hanteringsgruppen i **fältet Hanteringsgrupp.**

7. Skriv datornamnet för den primära hanteringsservern i fältet **Primär hanteringsserver.**

8. Skriv TCP-portnumret i fältet **Hanteringsserverport.**

9. Under **Agent Åtgärdskonto**väljer du antingen kontot Lokalt system eller ett lokalt domänkonto.

10. Klicka på **OK** för att stänga dialogrutan **Lägg till en hanteringsgrupp** och sedan på **OK** för att stänga dialogrutan Egenskaper för Microsoft **Monitoring Agent.**

### <a name="linux-agent"></a>Linux-agent
Utför följande steg för att konfigurera Log Analytics-agenten för Linux för att rapportera till en hanteringsgrupp för System Center Operations Manager.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]

1. Redigera filen`/etc/opt/omi/conf/omiserver.conf`

2. Se till att `httpsport=` raden som börjar med definierar porten 1270. Som:`httpsport=1270`

3. Starta om OMI-servern:`sudo /opt/omi/bin/service_control restart`

## <a name="next-steps"></a>Nästa steg

- Granska [Felsökning av Linux-agenten](agent-linux-troubleshoot.md) om du stöter på problem när du installerar eller hanterar Linux-agenten.

- Granska [Felsökning av Windows-agenten](agent-windows-troubleshoot.md) om du stöter på problem när du installerar eller hanterar Windows-agenten.
