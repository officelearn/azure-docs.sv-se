---
title: Snabb start – ansluta datorer till Azure med Azure Arc for servers – PowerShell
description: I den här snabb starten får du lära dig hur du ansluter datorer till Azure med hjälp av Azure Arc för servrar med hjälp av PowerShell
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid och internt
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 7fb24d53876ab8c06fca4fbfe929c06a889335f3
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/03/2019
ms.locfileid: "74786358"
---
# <a name="quickstart-connect-machines-to-azure-using-azure-arc-for-servers---powershell"></a>Snabb start: ansluta datorer till Azure med Azure Arc for servers – PowerShell

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

Granska de klienter som stöds och nödvändig nätverks konfiguration i [översikten över Azure-bågen för servrar](overview.md).

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Skapa ett huvud namn för tjänsten för onboarding i skala

Ett tjänst huvud namn är en särskild begränsad hanterings identitet som endast beviljas den lägsta behörighet som krävs för att ansluta datorer till Azure. Detta är säkrare än att använda ett mer kraftfullt konto som innehavaradministratör. Tjänstens huvud namn används bara vid onboarding. Du kan på ett säkert sätt ta bort tjänstens huvud namn när du har anslutit dina önskade servrar.

> [!NOTE]
> Det här steget rekommenderas, men krävs inte.

### <a name="steps-to-create-the-service-principal"></a>Steg för att skapa tjänstens huvud namn

I det här exemplet ska vi använda [Azure PowerShell](/powershell/azure/install-az-ps) för att skapa ett tjänst huvud namn (SPN). Du kan också följa stegen som visas under [skapa ett huvud namn för tjänsten med hjälp av Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) för den här aktiviteten.

`Azure Connected Machine Onboarding`-rollen innehåller bara de behörigheter som krävs för onboarding. Du kan definiera behörigheten för ett SPN-namn så att dess omfattning kan omfatta en resurs grupp eller en prenumeration.

Du måste lagra utdata från [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) -cmdleten eller så kan du inte hämta lösen ordet för användning i ett senare steg.

```azurepowershell-interactive
$sp = New-AzADServicePrincipal -DisplayName "Arc-for-servers" -Role "Azure Connected Machine Onboarding"
$sp
```

```output
Secret                : System.Security.SecureString
ServicePrincipalNames : {ad9bcd79-be9c-45ab-abd8-80ca1654a7d1, https://Arc-for-servers}
ApplicationId         : ad9bcd79-be9c-45ab-abd8-80ca1654a7d1
ObjectType            : ServicePrincipal
DisplayName           : Hybrid-RP
Id                    : 5be92c87-01c4-42f5-bade-c1c10af87758
Type                  :
```

Hämta nu lösen ordet med PowerShell.

```azurepowershell-interactive
$credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
$credential.GetNetworkCredential().password
```

Från utdata kopierar du **lösen ordet** och **ApplicationId** (från föregående steg) och lagrar dem senare på en säker plats, till exempel det hemliga arkivet för Server konfigurations verktyget. Om du glömmer bort eller förlorar ditt SPN-lösenord kan du återställa det med hjälp av [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) cmdlet.

I installera agent onboarding-skriptet:

* Egenskapen **ApplicationId** används för den `--service-principal-id`-parameter som används i installations agenten
* **Lösen ords** egenskapen används för parametern `--service-principal-secret` i installations agenten.

## <a name="manually-install-the-agent-and-connect-to-azure"></a>Installera agenten manuellt och Anslut till Azure

I följande guide kan du ansluta en dator till Azure genom att logga in på datorn och utföra stegen. Du kan också ansluta datorer till Azure [från portalen](quickstart-onboard-portal.md).

### <a name="download-and-install-the-agent"></a>Hämta och installera agenten

Installation av agent paketet kräver åtkomst till rot-eller lokal administratör på mål servern, men ingen Azure-åtkomst.

#### <a name="linux"></a>Linux

För **Linux** -servrar distribueras agenten via [Microsofts paket lagrings plats](https://packages.microsoft.com) med det önskade paket formatet för distributionen (. RPM eller. DEB).

> [!NOTE]
> Under den offentliga för hands versionen har endast ett paket släppts, vilket är lämpligt för Ubuntu 16,04 eller 18,04.

<!-- What about this aks? -->
Det enklaste alternativet är att registrera paketets lagrings plats och sedan installera paketet med distributionens paket hanterare.
Bash-skriptet som finns på [https://aka.ms/azcmagent](https://aka.ms/azcmagent) utför följande åtgärder:

1. Konfigurerar värddatorn för hämtning från `packages.microsoft.com`.
2. Installerar hybrid resurs leverantörs paketet.
3. Du kan också konfigurera agenten för proxy-åtgärd om du anger `--proxy`.

Skriptet innehåller också kontroller för stödda distributioner som stöds och som inte stöds, samt för att identifiera nödvändiga behörigheter för installation.

Exemplet nedan laddar ned agenten och installerar den, utan någon av de villkorliga kontrollerna.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. Omit the '--proxy "{proxy-url}"' parameters if proxy is not needed
bash ~/Install_linux_azcmagent.sh--proxy "{proxy-url}"
```

> [!NOTE]
> Om du inte vill referera till Microsofts paket lagrings plats kan du kopiera paket filen därifrån till din interna lagrings plats.

#### <a name="windows"></a>Windows

För **Windows**paketeras agenten i en Windows Installer (`.MSI`)-fil och kan laddas ned från [https://aka.ms/AzureConnectedMachineAgent](https://aka.ms/AzureConnectedMachineAgent), som finns på [https://download.microsoft.com](https://download.microsoft.com).

```powershell
# Download the package
Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String
```

> [!NOTE]
> Om du kör installations skriptet på Linux igen uppgraderas det automatiskt till den senaste versionen. I Windows måste du avinstallera "Azure Connected Machine agent" innan du kör installations programmet igen för att uppgradera.

### <a name="connecting-to-azure"></a>Ansluta till Azure

När du har installerat kan du hantera och konfigurera agenten med hjälp av ett kommando rads verktyg som kallas `azcmagent.exe`. Agenten finns under `/opt/azcmagent/bin` på Linux och `$env:programfiles\AzureConnectedMachineAgent` i Windows.

Öppna PowerShell som administratör på en målnod i Windows och kör:

```powershell
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{your-spn-appid}" `
  --service-principal-secret "{your-spn-password}" `
  --resource-group "{your-resource-group-name}" `
  --tenant-id "{your-tenant-id}" `
  --location "{location-of-your-resource-group}" `
  --subscription-id "{your-subscription-id}"
```

Öppna ett gränssnitt i Linux och kör

<!-- Same command for linux?-->
```bash
azcmagent connect \
  --service-principal-id "{your-spn-appid}" \
  --service-principal-secret "{your-spn-password}" \
  --resource-group "{your-resource-group-name}" \
  --tenant-id "{your-tenant-id}" \
  --location "{location-of-your-resource-group}" \
  --subscription-id "{your-subscription-id}"
```

Parametrar:

* `tenant-id`: klient-GUID. Du kan hitta det i Azure Portal genom att välja **Egenskaper** för **Azure Active Directory** ->  -> **katalog-ID**.
* `subscription-id`: GUID för prenumerationen i Azure där du vill ansluta din dator.
* `resource-group`: den resurs grupp där du vill att datorn ska vara ansluten.
* `location`: se [Azure-regioner och-platser](https://azure.microsoft.com/global-infrastructure/regions/). Den här platsen kan vara samma eller olika som resurs gruppens plats. För offentlig för hands version stöds tjänsten i **WestUS2** och **Europa, västra**.
* `resource-name`: (*valfritt*) som används för Azures resurs åter givning av din lokala dator. Om du inte anger det här värdet används datorns värdnamn.

Du hittar mer information om verktyget ' azcmagent ' i Azcmagent- [referensen](azcmagent-reference.md).
<!-- Isn't this still needed to view machines? -->

När åtgärden har slutförts är datorn ansluten till Azure. Du kan visa datorn i Azure Portal genom att besöka [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Lyckad onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

### <a name="proxy-server-configuration"></a>Konfiguration av proxyserver

#### <a name="linux"></a>Linux

<!-- New proxy name? -->

Om servern kräver en proxyserver för **Linux**kan du antingen:

* Kör `install_linux_hybrid_agent.sh`-skriptet från avsnittet [Installera agent](#download-and-install-the-agent) ovan, med `--proxy`.
* Om du redan har installerat agenten kör du kommandot `/opt/azcmagent/bin/hybridrp_proxy add http://{proxy-url}:{proxy-port}`, som konfigurerar proxyservern och startar om agenten.

#### <a name="windows"></a>Windows

För **Windows**, om servern kräver proxyserver för åtkomst till Internet resurser, bör du köra kommandot nedan för att ställa in proxy-serverns miljö variabel. Detta gör att agenten kan använda proxyservern för Internet åtkomst.

```powershell
# If a proxy server is needed, execute these commands with actual proxy URL
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

> [!NOTE]
> Autentiserade proxyservrar stöds inte för offentlig för hands version.

## <a name="clean-up"></a>Rensa

Om du vill koppla bort en dator från Azure-bågen för servrar måste du utföra två steg.

1. Välj datorn i [portalen](https://aka.ms/hybridmachineportal), klicka på ellipsen (`...`) och välj **ta bort**.
1. Avinstallera agenten från datorn.

   I Windows kan du använda "apparna & funktioner" på kontroll panelen för att avinstallera agenten.
  
  ![Appar & funktioner](./media/quickstart-onboard/apps-and-features.png)

   Om du vill skripta avinstallationen kan du använda följande exempel som hämtar **PackageId** och avinstallerar agenten med hjälp av `msiexec /X`.

   Titta under register nyckeln `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` och hitta **PackageId**. Sedan kan du avinstallera agenten med hjälp av `msiexec`.

   Exemplet nedan visar hur du avinstallerar agenten.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /Quiet /X "$($_.PsChildName)"}
   ```

   Kör följande kommando i Linux för att avinstallera agenten.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Tilldela en princip till anslutna datorer](../../governance/policy/assign-policy-portal.md)
