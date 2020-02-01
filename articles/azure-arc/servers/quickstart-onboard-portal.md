---
title: Ansluta hybrid datorer till Azure från Azure Portal
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter datorer till Azure med hjälp av Azure Arc for Servers (för hands version) från Azure Portal.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 01/29/2020
ms.custom: mvc
ms.topic: quickstart
ms.openlocfilehash: 165ed8d0aad7011f1ea71ff870ee4629f1f12613
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/31/2020
ms.locfileid: "76898596"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Ansluta hybrid datorer till Azure från Azure Portal

Du kan aktivera Azure Arc for Servers (för hands version) för ett eller flera Windows-eller Linux-datorer i din miljö genom att utföra en uppsättning steg manuellt eller använda en automatiserad metod genom att köra ett mall-skript som vi tillhandahåller. Det här skriptet automatiserar nedladdning och installation av båda agenterna.

Den här installations metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och Windows, är du medlem i den lokala gruppen Administratörer.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](overview.md#prerequisites) resurser uppfyller kraven.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="generate-install-script-from-the-azure-portal"></a>Generera installations skript från Azure Portal

Det finns ett skript för att automatisera hämtningen, installationen och upprättandet av anslutningen till Azure Arc från Azure Portal. Följande steg beskriver hur du slutför den här processen.

1. Starta [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal)i webbläsaren.

2. På sidan **datorer – Azure-båge** väljer du antingen **+ Lägg till** i det övre vänstra hörnet eller väljer alternativet **skapa dator-Azure-båge** längst ned i fönstret i mitten. 

3. På sidan **Välj en metod** väljer du från sidan **Lägg till datorer med interaktiv skript** panel **generera skript**.

4. På sidan **skapa skript** väljer du den prenumeration och resurs grupp där du vill att datorn ska hanteras i Azure. Välj en Azure-plats där datorns metadata ska lagras.

    >[!NOTE]
    >Azure-båge för servrar (för hands version) stöder endast följande regioner:
    >- WestUS2
    >- Västeuropa
    >- WestAsia
    >

5. På sidan **generera skript** , under List rutan **operativ system** , väljer du lämpligt operativ system som skriptet körs på.

6. Om datorn kommunicerar via en proxyserver för att ansluta till Internet väljer du alternativet **Nästa: proxyserver >** . På fliken **proxyserver** anger du IP-adressen eller namnet och port numret för proxyservern som datorn ska använda för att kommunicera med proxyservern. Ange värdet efter formatet `http://<proxyURL>:<proxyport>`. När du är klar väljer du **Granska + generera**.  Annars väljer du **Granska + generera** för att slutföra stegen.

7. På fliken **Granska och skapa** granskar du sammanfattnings informationen och väljer sedan **Hämta**. Annars kan du välja **föregående**om du behöver göra ändringar.

## <a name="install-and-validate-the-agent-on-windows"></a>Installera och verifiera agenten i Windows

### <a name="install-manually"></a>Installera manuellt

Du kan installera den anslutna dator agenten manuellt genom att köra installations paketet för Windows Installer `AzureConnectedMachineAgent.msi` när du har laddat ned det och kopiera det till en mapp på mål servern eller från en delad nätverksmapp. Om du kör installations paketet utan några alternativ startas en installations guide som du kan följa för att installera agenten interaktivt.

>[!NOTE]
>*Administratören* behörighet krävs för att installera eller avinstallera agenten.

Om datorn behöver kommunicera via en proxyserver till tjänsten efter det att du har installerat agenten måste du köra ett kommando som beskrivs i ett avsnitt nedan för att ställa in miljövariabeln för proxyserverns system miljö `https_proxy`.

Följande tabell visar de parametrar som stöds av installationsprogrammet för agenten från kommandoraden.

| Parameter | Beskrivning |
|:--|:--|
| /? | Returnerar en lista över kommandoradsalternativ. |
| / S | Utför en tyst installation utan interaktion från användaren. |

Om du till exempel vill köra installations programmet med parametern `/?` anger du `msiexec.exe /i AzureConnectedMachineAgent.msi /?`.

Filer för den anslutna dator agenten installeras i *C:\Program Files\AzureConnectedMachineAgent* som standard. Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. Logg katalogen är *%ProgramFiles%\AzureConnectedMachineAgentAgent\logs*.

### <a name="install-using-scripted-method"></a>Installera med skript metod

1. Logga in på servern.

2. Öppna en upphöjd PowerShell-kommandotolk.

3. Ändra till den mapp eller resurs som du kopierade skriptet till och kör det på servern genom att köra kommandot `./OnboardingScript.ps1`.

### <a name="configure-agent-proxy-setting"></a>Konfigurera agentens proxyinställningar

Kör följande kommando för att ange miljövariabeln för proxyservern.

```powershell
# If a proxy server is needed, execute these commands with proxy URL and port
[Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
$env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
# The agent service needs to be restarted after the proxy environment variable is set in order for the changes to take effect.
Restart-Service -Name himds
```

>[!NOTE]
>Agenten har inte stöd för att ställa in proxyautentisering i den här för hands versionen.
>

### <a name="configure-agent-communication"></a>Konfigurera agent kommunikation

När du har installerat agenten måste du konfigurera agenten att kommunicera med Azure Arc-tjänsten genom att köra följande kommando:

`%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="install-and-validate-the-agent-on-linux"></a>Installera och verifiera agenten på Linux

Den anslutna dator agenten för Linux finns i det önskade paket formatet för distributionen (. RPM eller. DEB) som finns på Microsofts [paket lagrings plats](https://packages.microsoft.com/). Ett paket för gränssnitts skript `Install_linux_azcmagent.sh` som finns på [https://aka.ms/azcmagent](https://aka.ms/azcmagent) utför följande åtgärder:

- Konfigurerar värd datorn för att ladda ned agent paketet från packages.microsoft.com.
- Installerar hybrid resurs leverantörs paketet.
- Du kan också konfigurera agenten med din proxyinformation genom att inkludera `--proxy "{proxy-url}:{proxy-port}"`-parametern.

Skriptet innehåller också logik för att identifiera stödda distributioner som stöds och som inte stöds, samt verifiera nödvändiga behörigheter för att utföra installationen. 

Exemplet nedan laddar ned agenten och installerar den, utan att utföra några av de villkorliga kontrollerna.

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh
```

Om du vill ladda ned och installera agenten, inklusive `--proxy` parameter för att konfigurera agenten att kommunicera via proxyservern, kör du följande kommandon:

```bash
# Download the installation package
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent. 
bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
```

### <a name="configure-agent-communication"></a>Konfigurera agent kommunikation

När du har installerat agenten måste du konfigurera agenten att kommunicera med Azure Arc-tjänsten genom att köra följande kommando:

`/opt/azcmagent/bin/azcmagent.exe" connect --resource-group "<resourceGroupName>" --tenant-id "<tenantID>" --location "<regionName>" --subscription-id "<subscriptionID>"`

## <a name="verify-connection-with-azure-arc"></a>Kontrol lera anslutningen till Azure-bågen

När du har utfört stegen för att installera agenten och konfigurerar den för att ansluta till Azure-bågen för servrar (för hands version) går du till Azure Portal för att kontrol lera att servern har anslutits. Du kan visa dina datorer i Azure Portal genom att besöka [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

![Lyckad onboarding](./media/quickstart-onboard/arc-for-servers-successful-onboard.png)

## <a name="clean-up"></a>Rensa

Om du vill koppla bort en dator från Azure-bågen för servrar (för hands version) måste du utföra följande steg.

1. Öppna Azure båg for Servers (för hands version) genom att besöka [https://aka.ms/hybridmachineportal](https://aka.ms/hybridmachineportal).

2. Välj datorn i listan, klicka på ellipsen (`...`) och välj **ta bort**.

3. Om du vill avinstallera Windows-agenten från datorn gör du följande:

    1. Logga in på datorn med ett konto som har administratörs behörighet.

    2. Välj **program och funktioner**på **kontroll panelen**.

    3. I **program och funktioner**väljer du **Azure Connected Machine agent**, väljer **Avinstallera**och väljer sedan **Ja**.

        >[!NOTE]
        >Installations guiden för agenten kan också köras genom att dubbelklicka på **AzureConnectedMachineAgent. msi** installations paket.

    Om du vill skripta avinstallationen kan du använda följande exempel, som hämtar produkt koden och avinstallerar agenten med hjälp av kommando raden msiexec. exe: `msiexec /x {Product Code}`. Öppna Registereditorn och titta under register nyckeln `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall` och hitta produkt kodens GUID. Sedan kan du avinstallera agenten med hjälp av Msiexec.

   Exemplet nedan visar hur du avinstallerar agenten.

   ```powershell
   Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
   Get-ItemProperty | `
   Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
   ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
   ```

4. Avinstallera Linux-agenten genom att köra följande kommando för att avinstallera agenten.

   ```bash
   sudo apt purge hybridagent
   ```

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Tilldela en princip till anslutna datorer](../../governance/policy/assign-policy-portal.md)
