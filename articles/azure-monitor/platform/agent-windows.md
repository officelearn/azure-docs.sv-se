---
title: Ansluta Windows-datorer till Azure Monitor | Microsoft-dokument
description: I den här artikeln beskrivs hur du ansluter Windows-datorer som finns i andra moln eller lokalt till Azure Monitor med Log Analytics-agenten för Windows.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/07/2019
ms.openlocfilehash: 70fa66a96291e0c2a638bf69bdce7da531d32bb7
ms.sourcegitcommit: 0450ed87a7e01bbe38b3a3aea2a21881f34f34dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80637474"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Ansluta Windows-datorer till Azure Monitor

För att övervaka och hantera virtuella datorer eller fysiska datorer i ditt lokala datacenter eller annan molnmiljö med Azure Monitor måste du distribuera Log Analytics-agenten (kallas även Microsoft Monitoring Agent (MMA)) och konfigurera den så att den rapporterar till en eller flera Log Analytics-arbetsytor. Agenten stöder också hybridkörningsarbetsrollen för Azure Automation.  

På en övervakad Windows-dator visas agenten som Microsoft Monitoring Agent-tjänsten. Tjänsten Microsoft Monitoring Agent samlar in händelser från loggfiler och Windows-händelselogg, prestandadata och annan telemetri. Även när agenten inte kan kommunicera med Azure Monitor som den rapporterar till, fortsätter agenten att köra och köar insamlade data på disken på den övervakade datorn. När anslutningen återställs skickar Microsoft Monitoring Agent-tjänsten insamlade data till tjänsten.

Agenten kan installeras med någon av följande metoder. De flesta installationer använder en kombination av dessa metoder för att installera olika uppsättningar datorer efter behov.  Information om hur du använder varje metod finns senare i artikeln.

* Manuell installation. Installationsprogrammet körs manuellt på datorn med hjälp av installationsguiden, från kommandoraden eller distribueras med hjälp av ett befintligt distributionsverktyg för programvara.
* Azure Automation önskad tillståndskonfiguration (DSC). Använda DSC i Azure Automation med ett skript för Windows-datorer som redan distribuerats i din miljö.  
* PowerShell-skript.
* Resource Manager-mall för virtuella datorer som kör Windows lokalt i Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) är beroende av Microsoft Monitoring Agent (kallas även Log Analytics Windows-agenten) och installerar och konfigurerar den för att rapportera till en Log Analytics-arbetsyta som en del av distributionen. ASC innehåller ett automatiskt etableringsalternativ som möjliggör automatisk installation av Log Analytics Windows-agenten på alla virtuella datorer i din prenumeration och konfigurerar det så att den rapporterar till en viss arbetsyta. Mer information om det här alternativet finns i [Aktivera automatisk etablering av Log Analytics-agent](../../security-center/security-center-enable-data-collection.md#auto-provision-mma).
>

Om du behöver konfigurera agenten så att den rapporterar till mer än en arbetsyta kan detta inte utföras under den första installationen, först efteråt genom att uppdatera inställningarna från Kontrollpanelen eller PowerShell enligt beskrivningen i [Lägga till eller ta bort en arbetsyta](agent-manage.md#adding-or-removing-a-workspace).  

Om du vill förstå konfigurationen som stöds, så granska [de Windows-operativsystem som stöds](log-analytics-agent.md#supported-windows-operating-systems) och [nätverkets brandväggskonfiguration](log-analytics-agent.md#network-requirements).

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans id och nyckel
Innan du installerar Log Analytics-agenten för Windows behöver du arbetsytans ID och nyckeln för din Log Analytics-arbetsyta.  Den här informationen krävs under installationen från varje installationsmetod för att korrekt konfigurera agenten och se till att den kan kommunicera med Azure Monitor i Azure kommersiella och amerikanska myndigheters moln. 

1. Sök efter och välj **Log Analytics-arbetsytor**i Azure-portalen .
2. I listan över Log Analytics-arbetsytor väljer du den arbetsyta som du tänker konfigurera agenten att rapportera till.
3. Välj **Avancerade inställningar**.<br><br> ![Avancerade inställningar i Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **Anslutna källor** och välj sedan **Windows-servrar**.   
5. Kopiera och klistra in i din favoritredigerare, **arbetsyte-ID** och **Primärnyckel**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurera agent för att använda TLS 1.2
Om du vill konfigurera användningen av [TLS 1.2-protokollet](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) för kommunikation mellan Windows-agenten och Log Analytics-tjänsten kan du följa stegen nedan för att aktivera innan agenten installeras på den virtuella datorn eller efteråt.

>[!NOTE]
>Om du konfigurerar en virtuell dator med Windows Server 2008 SP2 x64 för att använda TLS 1.2 måste du först installera följande supportuppdatering för [SHA-2-kodsignering](https://support.microsoft.com/help/4474419/sha-2-code-signing-support-update) innan du utför stegen nedan. 
>

1. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protokoll**
2. Skapa en undernyckel under **Protokoll** för TLS 1.2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2**
3. Skapa en **klientundernyckel** under underviktsundernyckeln för TLS 1.2-protokollversionen som du skapade tidigare. **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**.
4. Skapa följande DWORD-värden under **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client**:

    * **Aktiverat** [Värde = 1]
    * **DisabledByDefault** [Värde = 0]  

Konfigurera .NET Framework 4.6 eller senare för att stödja säker kryptografi, eftersom den som standard är inaktiverad. Den [starka kryptografin](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) använder säkrare nätverksprotokoll som TLS 1.2 och blockerar protokoll som inte är säkra. 

1. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\. NETFramework\v4.0.30319**.  
2. Skapa DWORD-värdet **SchUseStrongCrypto** under den här undernyckeln med värdet **1**.  
3. Leta upp följande registerundernyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\. NETFramework\v4.0.30319**.  
4. Skapa DWORD-värdet **SchUseStrongCrypto** under den här undernyckeln med värdet **1**. 
5. Starta om systemet så att inställningarna ska börja gälla. 

## <a name="install-the-agent-using-setup-wizard"></a>Installera agenten med hjälp av installationsguiden
Följande steg installerar och konfigurerar Log Analytics-agenten i Azure- och Azure Government-molnet med hjälp av installationsguiden för agenten på datorn. Om du vill lära dig hur du konfigurerar agenten så att den även rapporterar till en hanteringsgrupp för System Center Operations Manager läser [du distribuera Operations Manager-agenten med guiden Agentinställningar](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard).

1. På arbetsytan Log Analytics väljer du den **hämtningsversion av Windows Agent** som du vill hämta beroende på processorarkitekturen i Windows-operativsystemet på sidan **Windows-servrar** som du navigerade till tidigare.   
2. Kör installationsprogrammet för att installera agenten på datorn.
2. På sidan **Välkommen** klickar du på **Nästa**.
3. På sidan **Licensvillkor** läser du licensen och klickar sedan på **Jag accepterar**.
4. På sidan **Målmapp** ändrar du eller behåller standardinstallationsmappen och klickar sedan på **Nästa**.
5. På sidan **Installationsalternativ för agent** väljer du att ansluta agenten till Azure Log Analytics och klickar sedan på **Nästa**.   
6. På sidan **Azure Log Analytics** gör du följande:
   1. Klistra in **Id för arbetsyta** och **Arbetsytenyckel (primär nyckel)** som du kopierade tidigare.  Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government-molnet väljer du **Azure US Government** i listrutan **Azure Cloud**.  
   2. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten klickar du på **Avancerat** och anger URL och portnummer för proxyservern.  Om proxyservern kräver autentisering anger du användarnamn och lösenord för att autentisera hos proxyservern och klickar sedan på **Nästa**.  
7. Klicka på **Nästa** när du har gjort de konfigurationsinställningar som krävs.<br><br> ![klistra in ID för arbetsyta och primär nyckel](media/agent-windows/log-analytics-mma-setup-laworkspace.png)<br><br>
8. På sidan **Klar att installera** kontrollerar du valen och klickar sedan på **Installera**.
9. På sidan **Konfigurationen har slutförts** klickar du på **Slutför**.

När du är klar visas **Microsoft Monitoring Agent** i **Kontrollpanelen**. Om du vill bekräfta att den rapporterar till Log Analytics granskar du [Verifiera agentanslutning till Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Installera agenten med kommandoraden
Den nedladdade filen för agenten är ett fristående installationspaket.  Installationsprogrammet för agent- och stödfilerna finns i paketet och måste extraheras för att kunna installeras korrekt med hjälp av kommandoraden som visas i följande exempel.    

>[!NOTE]
>Om du vill uppgradera en agent måste du använda Logganalys-skript-API:et. Mer information finns i avsnittet [Hantera och underhålla Log Analytics-agenten för Windows och Linux.](agent-manage.md)

I följande tabell beskrivs de specifika parametrar som stöds av konfigurationen för agenten, inklusive när de distribueras med Hjälp av Automation DSC.

|MMA-specifika alternativ                   |Anteckningar         |
|---------------------------------------|--------------|
| NOAPM=1                               | Valfri parameter. Installerar agenten utan .NET-programprestandaövervakning.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurera agenten så att den rapporterar till en arbetsyta                |
|OPINSIGHTS_WORKSPACE_ID                | Arbetsyte-ID (guid) för arbetsytan att lägga till                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbetsytan nyckel som används för att initialt autentisera med arbetsytan |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Ange den molnmiljö där arbetsytan finns <br> 0 = Azure kommersiella moln (standard) <br> 1 = Azure-regeringen |
|OPINSIGHTS_PROXY_URL               | URI för proxy att använda |
|OPINSIGHTS_PROXY_USERNAME               | Användarnamn för att komma åt en autentiserat proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Lösenord för att komma åt en autentiserat proxy |

1. Om du vill extrahera agentinstallationsfilerna `MMASetup-<platform>.exe /c` från en upphöjd kommandotolkskörning och det kommer att fråga dig om sökvägen att extrahera filer till.  Du kan också ange sökvägen genom `MMASetup-<platform>.exe /c /t:<Full Path>`att skicka argumenten .  
2. Om du vill installera agenten tyst och konfigurera den för att rapportera till en arbetsyta i Azures kommersiella moln, från mappen du extraherade installationsfilerna för att skriva: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   eller för att konfigurera agenten att rapportera till Azure US Government moln, typ: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Strängvärdena för parametrarna *OPINSIGHTS_WORKSPACE_ID* och *OPINSIGHTS_WORKSPACE_KEY* måste kapslas in i dubbla citattecken för att instruera Windows Installer att interprit som giltiga alternativ för paketet. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installera agenten med DSC i Azure Automation

Du kan använda följande skriptexempel för att installera agenten med Azure Automation DSC.   Om du inte har ett Automation-konto läser [du Komma igång med Azure Automation](/azure/automation/) för att förstå krav och steg för att skapa ett Automation-konto som krävs innan du använder Automation DSC.  Om du inte är bekant med Automation DSC kan du granska [Komma igång med Automation DSC](../../automation/automation-dsc-getting-started.md).

I följande exempel installeras 64-bitarsagenten, `URI` som identifieras av värdet. Du kan också använda 32-bitarsversionen genom att ersätta URI-värdet. Uri:erna för båda versionerna är:

- Windows 64-bitars agent -https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bitars agent -https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Det här procedur- och skriptexemplet stöder inte uppgradering av agenten som redan har distribuerats till en Windows-dator.

32-bitars- och 64-bitarsversionerna av agentpaketet har olika produktkoder och nya versioner som släpps har också ett unikt värde.  Produktkoden är ett GUID som är huvudidentifieringen av ett program eller en produkt och representeras av egenskapen Windows Installer **ProductCode.**  Värdet `ProductId` i **MMAgent.ps1-skriptet** måste matcha produktkoden från installationspaketet för 32- eller 64-bitarsagenter.

Om du vill hämta produktkoden direkt från agentinstallationspaketet kan du använda Orca.exe från [Windows SDK-komponenter för Windows Installer Developers](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) som är en komponent i Windows Software Development Kit eller använda PowerShell efter ett [exempelskript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) skrivet av en Microsoft Valuable Professional (MVP).  För båda tillvägagångssätten måste du först extrahera **FILEN MOMagent.msi** från MMASetup-installationspaketet.  Detta visas tidigare i det första steget under avsnittet [Installera agenten med kommandoraden](#install-the-agent-using-the-command-line).  

1. Importera dsc-modulen xPSDesiredStateConfiguration från [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) till Azure Automation.  
2.    Skapa Azure Automation-variabeltillgångar för *OPSINSIGHTS_WS_ID* och *OPSINSIGHTS_WS_KEY*. Ange *OPSINSIGHTS_WS_ID* till ditt Log Analytics-arbetsyte-ID och ange *OPSINSIGHTS_WS_KEY* till den primära nyckeln på arbetsytan.
3.    Kopiera skriptet och spara det som MMAgent.ps1.

```powershell
Configuration MMAgent
{
    $OIPackageLocalPath = "C:\Deploy\MMASetup-AMD64.exe"
    $OPSINSIGHTS_WS_ID = Get-AutomationVariable -Name "OPSINSIGHTS_WS_ID"
    $OPSINSIGHTS_WS_KEY = Get-AutomationVariable -Name "OPSINSIGHTS_WS_KEY"

    Import-DscResource -ModuleName xPSDesiredStateConfiguration
    Import-DscResource -ModuleName PSDesiredStateConfiguration

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
            Arguments = '/C:"setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_ID=' + $OPSINSIGHTS_WS_ID + ' OPINSIGHTS_WORKSPACE_KEY=' + $OPSINSIGHTS_WS_KEY + ' AcceptEndUserLicenseAgreement=1"'
            DependsOn = "[xRemoteFile]OIPackage"
        }
    }
}

```

4. Uppdatera `ProductId` värdet i skriptet med produktkoden som extraherats från den senaste versionen av agentinstallationspaketet med de metoder som rekommenderas tidigare. 
5. [Importera konfigurationsskriptet MMAgent.ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) till ditt Automation-konto. 
5. [Tilldela en Windows-dator eller nod](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) till konfigurationen. Inom 15 minuter kontrollerar noden dess konfiguration och agenten skjuts till noden.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verifiera agentanslutning till Log Analytics

När installationen av agenten är klar kan verifiera att den är ansluten och rapporteringen ske på två sätt.  

På datorn går du till **Kontrollpanelen** och letar upp objektet **Microsoft Monitoring Agent**.  Välj det och på fliken **Azure Log Analytics** ska agenten visa ett meddelande som anger: Microsoft Monitoring Agent har anslutit till Tjänsten Microsoft Operations Management **Suite.**<br><br> ![MMA-anslutningsstatus till Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Du kan också utföra en enkel loggfråga i Azure-portalen.  

1. Sök efter i Azure-portalen och välj **Övervaka**.
1. Välj **Loggar** på menyn.
1. I **frågefältstypen** i fönstret Loggar:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

I sökresultaten som returneras bör du se pulsslagsposter för datorn som anger att den är ansluten och rapporterar till tjänsten.   

## <a name="next-steps"></a>Nästa steg

- Granska [Hantera och underhålla Log Analytics-agenten för Windows och Linux om](agent-manage.md) du vill veta mer om hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [Felsökning av Windows-agenten](agent-windows-troubleshoot.md) om du stöter på problem när du installerar eller hanterar agenten.
