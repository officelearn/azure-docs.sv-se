---
title: Anslut Windows-datorer till Azure Monitor | Microsoft Docs
description: Den här artikeln beskriver hur du ansluter Windows-datorer som finns i andra moln eller lokalt för att Azure Monitor med Log Analytics agent för Windows.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 06/14/2019
ms.author: magoedte
ms.openlocfilehash: 5e1fe6252f396a4585b5d7d7190728b79229d5c7
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70073981"
---
# <a name="connect-windows-computers-to-azure-monitor"></a>Anslut Windows-datorer till Azure Monitor

För att kunna övervaka och hantera virtuella datorer eller fysiska datorer i ditt lokala data Center eller annan moln miljö med Azure Monitor måste du distribuera Log Analytics-agenten (kallas även Microsoft Monitoring Agent (MMA)) och konfigurera den till rapportera till en eller flera Log Analytics arbets ytor. Agenten stöder också Hybrid Runbook Worker-rollen för Azure Automation.  

På en övervakad Windows-dator visas agenten som Microsoft Monitoring Agent-tjänst. Tjänsten Microsoft Monitoring Agent samlar in händelser från loggfiler och Windows händelse logg, prestanda data och annan telemetri. Även om agenten inte kan kommunicera med Azure Monitor den rapporterar till, fortsätter agenten att köra och köar insamlade data på den övervakade datorns disk. När anslutningen har återställts skickar tjänsten Microsoft Monitoring Agent insamlade data till tjänsten.

Agenten kan installeras med hjälp av någon av följande metoder. De flesta installationer använder en kombination av dessa metoder för att installera olika uppsättningar datorer efter behov.  Information om hur du använder varje metod finns längre fram i artikeln.

* Manuell installation. Installations programmet körs manuellt på datorn med hjälp av installations guiden, från kommando raden eller distribueras med hjälp av ett befintligt program distributions verktyg.
* Azure Automation önskad tillstånds konfiguration (DSC). Att använda DSC i Azure Automation med ett skript för Windows-datorer som redan har distribuerats i din miljö.  
* PowerShell-skript.
* Resource Manager-mall för virtuella datorer som kör Windows lokalt i Azure Stack. 

>[!NOTE]
>Azure Security Center (ASC) är beroende av Microsoft Monitoring Agent (kallas även för Log Analytics Windows-agent) och kommer att installera och konfigurera den så att den rapporterar till en Log Analytics arbets yta som en del av dess distribution. ASC innehåller ett automatiskt etablerings alternativ som aktiverar automatisk installation av Log Analytics Windows-agenten på alla virtuella datorer i din prenumeration och konfigurerar den att rapportera till en angiven arbets yta. Mer information om det här alternativet finns i [Aktivera automatisk etablering av Log Analytics agent](../../security-center/security-center-enable-data-collection.md#enable-automatic-provisioning-of-the-log-analytics-agent-).
>

Om du behöver konfigurera agenten för att rapportera till fler än en arbets yta, kan detta inte utföras under den första installationen, bara efteråt genom att uppdatera inställningarna från kontroll panelen eller PowerShell enligt beskrivningen i [lägga till eller ta bort en arbets yta](agent-manage.md#adding-or-removing-a-workspace).  

Om du vill förstå konfigurationen som stöds, så granska [de Windows-operativsystem som stöds](log-analytics-agent.md#supported-windows-operating-systems) och [nätverkets brandväggskonfiguration](log-analytics-agent.md#network-firewall-requirements).

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans ID och nyckel
Innan du installerar Log Analytics agent för Windows behöver du arbets ytans ID och nyckel för din Log Analytics-arbetsyta.  Den här informationen krävs under installationen från varje installations metod för att konfigurera agenten korrekt och se till att den kan kommunicera med Azure Monitor i Azures kommersiella och amerikanska myndighets moln. 

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. I listan med Log Analytics arbets ytor väljer du den arbets yta som du vill konfigurera agenten att rapportera till.
3. Välj **Avancerade inställningar**.<br><br> ![Avancerade inställningar i Log Analytics](media/agent-windows/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **Anslutna källor** och välj sedan **Windows-servrar**.   
5. Kopiera och klistra in i din favorit redigerare, **arbetsyte-ID** och **primär nyckel**.    
   
## <a name="configure-agent-to-use-tls-12"></a>Konfigurera agenten att använda TLS 1,2
Om du vill konfigurera användningen av [TLS 1,2](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings#tls-12) -protokollet för kommunikation mellan Windows-agenten och tjänsten Log Analytics, kan du följa stegen nedan för att aktivera innan agenten installeras på den virtuella datorn eller senare.   

1. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols**
2. Skapa en under nyckel under **protokoll** för TLS 1,2 **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1,2**
3. Skapa en **klient** under nyckel under under nyckeln TLS 1,2 Protocol version som du skapade tidigare. Till exempel **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**.
4. Skapa följande DWORD-värden under **HKLM\System\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2 \ client**:

    * **Aktive rad** [Värde = 1]
    * **DisabledByDefault** [Värde = 0]  

Konfigurera .NET Framework 4,6 eller senare för att stödja säker kryptografi, som standard är inaktiverat. Den [starka kryptografi](https://docs.microsoft.com/dotnet/framework/network-programming/tls#schusestrongcrypto) användningen använder säkrare nätverks protokoll som TLS 1,2 och blockerar protokoll som inte är säkra. 

1. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\\.NETFramework\v4.0.30319**.  
2. Skapa DWORD-värdet **SchUseStrongCrypto** under den här under nyckeln med värdet **1**.  
3. Leta upp följande register under nyckel: **HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\\.NETFramework\v4.0.30319**.  
4. Skapa DWORD-värdet **SchUseStrongCrypto** under den här under nyckeln med värdet **1**. 
5. Starta om systemet för att inställningarna ska börja gälla. 

## <a name="install-the-agent-using-setup-wizard"></a>Installera agenten med hjälp av installations guiden
Följande steg installerar och konfigurerar Log Analytics agenten i Azure och Azure Government molnet med hjälp av installations guiden för agenten på datorn. Om du vill lära dig hur du konfigurerar agenten så att den rapporterar till en System Center Operations Manager hanterings grupp läser du [distribuera Operations Manager agent med installations guiden](https://docs.microsoft.com/system-center/scom/manage-deploy-windows-agent-manually#to-deploy-the-operations-manager-agent-with-the-agent-setup-wizard)för agenten.

1. På arbets ytan Log Analytics, på sidan **Windows-servrar** som du har navigerat till tidigare, väljer du den version av Windows **agent** som du vill ladda ned beroende på processor arkitekturen i Windows-operativsystemet.   
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

När du är klar visas **Microsoft Monitoring Agent** i **Kontrollpanelen**. Om du vill bekräfta att den rapporterar till Log Analytics granskar du [Verifiera agent anslutningen till Log Analytics](#verify-agent-connectivity-to-log-analytics). 

## <a name="install-the-agent-using-the-command-line"></a>Installera agenten med hjälp av kommando raden
Den hämtade filen för agenten är ett fristående installations paket.  Installations programmet för agenten och de filer som stöds finns i paketet och måste extraheras för att kunna installeras på rätt sätt med hjälp av kommando raden som visas i följande exempel.    

>[!NOTE]
>Om du vill uppgradera en agent måste du använda skript-API: et för Log Analytics. Mer information finns i avsnittet [Hantera och underhålla Log Analytics agent för Windows och Linux](agent-manage.md) .

I följande tabell visas de olika parametrarna som stöds av installations programmet för agenten, inklusive när du distribuerar med hjälp av Automation DSC.

|MMA alternativ                   |Anteckningar         |
|---------------------------------------|--------------|
| NOAPM = 1                               | Valfri parameter. Installerar agenten utan prestanda övervakning av .NET-program.|   
|ADD_OPINSIGHTS_WORKSPACE               | 1 = Konfigurera agenten att rapportera till en arbets yta                |
|OPINSIGHTS_WORKSPACE_ID                | Arbetsyte-ID (GUID) för arbets ytan som ska läggas till                    |
|OPINSIGHTS_WORKSPACE_KEY               | Arbets ytans nyckel som används för att börja autentisera med arbets ytan |
|OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE  | Ange den moln miljö där arbets ytan finns <br> 0 = Azures kommersiella moln (standard) <br> 1 = Azure Government |
|OPINSIGHTS_PROXY_URL               | URI som proxyservern ska använda |
|OPINSIGHTS_PROXY_USERNAME               | Användar namn för åtkomst till en autentiserad proxy |
|OPINSIGHTS_PROXY_PASSWORD               | Lösen ord för åtkomst till en autentiserad proxy |

1. Om du vill extrahera installationsfilerna för agenten, kan du köra `MMASetup-<platform>.exe /c` från en upphöjd kommando tolk och uppmana dig att ange sökvägen för att extrahera filer till.  Alternativt kan du ange sökvägen genom att skicka argumenten `MMASetup-<platform>.exe /c /t:<Full Path>`.  
2. Om du vill installera agenten tyst och konfigurera den för att rapportera till en arbets yta i Azures kommersiella moln, från den mapp som du extraherade installationsfilerna till skriver du: 
   
     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=0 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```

   eller om du vill konfigurera agenten att rapportera till Azure-molnet för amerikanska myndigheter skriver du: 

     ```dos
    setup.exe /qn NOAPM=1 ADD_OPINSIGHTS_WORKSPACE=1 OPINSIGHTS_WORKSPACE_AZURE_CLOUD_TYPE=1 OPINSIGHTS_WORKSPACE_ID="<your workspace ID>" OPINSIGHTS_WORKSPACE_KEY="<your workspace key>" AcceptEndUserLicenseAgreement=1
    ```
    >[!NOTE]
    >Sträng värden för parametrarna *OPINSIGHTS_WORKSPACE_ID* och *OPINSIGHTS_WORKSPACE_KEY* måste kapslas in i dubbla citat tecken för att instruera Windows Installer till interprit som giltiga alternativ för paketet. 

## <a name="install-the-agent-using-dsc-in-azure-automation"></a>Installera agenten med DSC i Azure Automation

Du kan använda följande skript exempel för att installera agenten med hjälp av Azure Automation DSC.   Om du inte har ett Automation-konto kan du läsa [komma igång med Azure Automation](/azure/automation/) för att förstå krav och anvisningar för att skapa ett Automation-konto som krävs innan du använder Automation DSC.  Om du inte är bekant med Automation DSC kan du läsa [komma igång med Automation DSC](../../automation/automation-dsc-getting-started.md).

I följande exempel installeras 64-bitars agenten som identifieras av `URI` värdet. Du kan också använda 32-bitars versionen genom att ersätta URI-värdet. URI: erna för båda versionerna är:

- Windows 64-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828603
- Windows 32-bitars agent- https://go.microsoft.com/fwlink/?LinkId=828604


>[!NOTE]
>Den här proceduren och skript exemplet stöder inte uppgradering av agenten som redan har distribuerats till en Windows-dator.

32-bitars-och 64-bitars versionerna av agent paketet har olika produkt koder och nya versioner som släpps har också ett unikt värde.  Produkt koden är en GUID som är huvud-ID för ett program eller en produkt och representeras av egenskapen Windows Installer **ProductCode** .  Värdet i skriptet **MMAgent. ps1** måste matcha produkt koden från installations paketet 32-bitars eller 64-bitars agent. `ProductId`

Om du vill hämta produkt koden från agent installations paketet direkt kan du använda Orca. exe från Windows SDK- [komponenter för Windows Installer utvecklare](https://msdn.microsoft.com/library/windows/desktop/aa370834%28v=vs.85%29.aspx) som är en del av Windows Software Development Kit eller använda PowerShell efter en [ exempel skript](https://www.scconfigmgr.com/2014/08/22/how-to-get-msi-file-information-with-powershell/) som skrivits av en Microsoft Valuable Professional (MVP).  För båda metoderna måste du först extrahera filen **MOMAgent. msi** från MMASetup-installations paketet.  Detta visas tidigare i det första steget under avsnittet [Installera agenten med hjälp av kommando raden](#install-the-agent-using-the-command-line).  

1. Importera xPSDesiredStateConfiguration DSC-modulen från [https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration](https://www.powershellgallery.com/packages/xPSDesiredStateConfiguration) till Azure Automation.  
2.  Skapa Azure Automation variabel till gångar för *OPSINSIGHTS_WS_ID* och *OPSINSIGHTS_WS_KEY*. Ange *OPSINSIGHTS_WS_ID* till din Log Analytics arbetsyte-ID och ange *OPSINSIGHTS_WS_KEY* till den primära nyckeln för din arbets yta.
3.  Kopiera skriptet och spara det som MMAgent. ps1.

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

4. `ProductId` Uppdatera värdet i skriptet med produkt koden som extraherats från den senaste versionen av agent installations paketet med hjälp av de metoder som rekommenderas tidigare. 
5. [Importera konfigurations skriptet MMAgent. ps1](../../automation/automation-dsc-getting-started.md#importing-a-configuration-into-azure-automation) till ditt Automation-konto. 
5. [Tilldela en Windows-dator eller-nod](../../automation/automation-dsc-getting-started.md#onboarding-an-azure-vm-for-management-with-azure-automation-state-configuration) till konfigurationen. Inom 15 minuter kontrollerar noden konfigurationen och agenten flyttas till noden.

## <a name="verify-agent-connectivity-to-log-analytics"></a>Verifiera agent anslutningen till Log Analytics

När installationen av agenten är klar kan du kontrol lera att den har anslutits och rapportering kan utföras på två sätt.  

På datorn går du till **Kontrollpanelen** och letar upp objektet **Microsoft Monitoring Agent**.  Välj den. På fliken **Azure Log Analytics** bör agenten visa ett meddelande där det står: **Microsoft Monitoring Agent har anslutit till Microsoft Operations Management Suite tjänsten.**<br><br> ![MMA-anslutningsstatus till Log Analytics](media/agent-windows/log-analytics-mma-laworkspace-status.png)

Du kan också utföra en enkel logg fråga i Azure Portal.  

1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Azure Monitor**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Azure Monitor**.  
2. Välj **loggar** på menyn. 
2. I fönstret loggar i fältet frågefält skriver du:  

    ```
    Heartbeat 
    | where Category == "Direct Agent" 
    | where TimeGenerated > ago(30m)  
    ```

I Sök resultaten som returneras bör du se pulsslags poster för datorn som indikerar att den är ansluten och rapporterar till tjänsten.   

## <a name="next-steps"></a>Nästa steg

- Granska [hantering och underhåll av Log Analytics agent för Windows och Linux](agent-manage.md) för att lära dig hur du konfigurerar om, uppgraderar eller tar bort agenten från den virtuella datorn.

- Granska [fel sökning av Windows](agent-windows-troubleshoot.md) -agenten om det uppstår problem när du installerar eller hanterar agenten.
