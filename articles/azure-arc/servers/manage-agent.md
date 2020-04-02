---
title: Hantera Azure Arc för servrar (förhandsversion) agent
description: I den här artikeln beskrivs de olika hanteringsuppgifter som du vanligtvis utför under livscykeln för Azure Arc för servrar Connected Machine agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/01/2020
ms.topic: conceptual
ms.openlocfilehash: 8bcf59ee863bb2fd2a3213480372ad215c2fc00d
ms.sourcegitcommit: c5661c5cab5f6f13b19ce5203ac2159883b30c0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80528595"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Hantera och underhålla den anslutna maskinagenten

Efter den första distributionen av Azure Arc för servrar (förhandsversion) Connected Machine agent för Windows eller Linux, kan du behöva konfigurera om agenten, uppgradera den eller ta bort den från datorn om den har nått pensioneringsstadiet i sin livscykel. Du kan enkelt hantera dessa rutinmässiga underhållsuppgifter manuellt eller genom automatisering, vilket minskar både driftfel och kostnader.

## <a name="upgrading-agent"></a>Uppgraderingsagent

Azure Connected Machine-agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina behov. I följande tabell beskrivs de metoder som stöds för att utföra agentuppgraderingen.

| Operativsystem | Uppgraderingsmetod |
|------------------|----------------|
| Windows | Manuellt<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper (zypper)](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [Yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows-agent

Om du vill uppdatera agenten på en Windows-dator till den senaste versionen är agenten tillgänglig från Microsoft Update och kan distribueras med hjälp av din befintliga programuppdateringshanteringsprocess. Den kan också köras manuellt från Kommandotolken, från ett skript eller en annan `AzureConnectedMachine.msi`automatiseringslösning, eller från användargränssnittsguiden genom att köra . 

> [!NOTE]
> * Om du vill uppgradera agenten måste du ha *administratörsbehörighet.*
> * Om du vill uppgradera manuellt måste du först hämta och kopiera installationspaketet till en mapp på målservern eller från en delad nätverksmapp. 

Om du inte känner till kommandoradsalternativen för Windows Installer-paket läser du [Msiexecs standardkommandoradsalternativ](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) och [msiexec-kommandoradsalternativ](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Så här uppgraderar du med installationsguiden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Kör **AzureConnectedMachineAgent.msi** för att starta installationsguiden.

Installationsguiden identifierar om det finns en tidigare version och sedan automatiskt en uppgradering av agenten. När uppgraderingen är klar stängs installationsguiden automatiskt.

#### <a name="to-upgrade-from-the-command-line"></a>Så här uppgraderar du från kommandoraden

1. Logga in på datorn med ett konto som har administratörsrättigheter.

2. Om du vill uppgradera agenten tyst och `C:\Support\Logs` skapa en installationsloggfil i mappen kör du följande kommando.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-agent

För att uppdatera agenten på en Linux-dator till den senaste versionen, innebär det två kommandon. Ett kommando för att uppdatera det lokala paketindexet med listan över de senaste tillgängliga paketen från databaserna och ett kommando för att uppgradera det lokala paketet. 

> [!NOTE]
> Om du vill uppgradera *root* agenten måste du ha root-åtkomstbehörigheter eller med ett konto som har förhöjda rättigheter med Sudo.

#### <a name="upgrade-ubuntu"></a>Uppgradera Ubuntu

1. Om du vill uppdatera det lokala paketindexet med de senaste ändringarna i databaserna kör du följande kommando:

    ```bash
    apt update
    ```

2. Om du vill uppgradera systemet kör du följande kommando:

    ```bash
    apt upgrade
    ```

Åtgärder för [apt-kommandot,](https://help.ubuntu.com/lts/serverguide/apt.html) till exempel installation och borttagning `/var/log/dpkg.log` av paket, loggas i loggfilen.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uppgradera Red Hat/CentOS/Amazon Linux

1. Om du vill uppdatera det lokala paketindexet med de senaste ändringarna i databaserna kör du följande kommando:

    ```bash
    yum check-update
    ```

2. Om du vill uppgradera systemet kör du följande kommando:

    ```bash
    yum update
    ```

Åtgärder för [yum-kommandot,](https://access.redhat.com/articles/yum-cheat-sheet) till exempel installation och borttagning `/var/log/yum.log` av paket, loggas i loggfilen. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uppgradera SUSE Linux Enterprise

1. Om du vill uppdatera det lokala paketindexet med de senaste ändringarna i databaserna kör du följande kommando:

    ```bash
    zypper refresh
    ```

2. Om du vill uppgradera systemet kör du följande kommando:

    ```bash
    zypper update
    ```

Åtgärder av [zypper](https://en.opensuse.org/Portal:Zypper) kommandot, såsom installation och borttagning av `/var/log/zypper.log` paket, loggas i loggfilen. 

## <a name="remove-the-agent"></a>Ta bort agenten

Utför någon av följande metoder för att avinstallera Windows- eller Linux Connected Machine-agenten från datorn. Om du tar bort agenten avregistrerar du inte datorn med Arc för servrar (förhandsversion), det här är en separat process som du utför när du inte längre behöver hantera datorn i Azure.

### <a name="windows-agent"></a>Windows-agent

Båda följande metoder tar bort agenten, men de tar inte bort mappen *C:\Program\AzureConnectedMachineAgent* på datorn.

#### <a name="uninstall-from-control-panel"></a>Avinstallera från Kontrollpanelen

1. Så här avinstallerar du Windows-agenten från datorn:

    a. Logga in på datorn med ett konto som har administratörsbehörighet.  
    b. Välj **Program och funktioner**på **Kontrollpanelen**.  
    c. I **Program och funktioner**väljer du Azure Connected Machine **Agent**, väljer **Avinstallera**och väljer sedan **Ja**.  

    >[!NOTE]
    > Du kan också köra guiden agentinstallation genom att dubbelklicka på **installationspaketet för AzureConnectedMachineAgent.msi.**

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommandoraden

Om du vill avinstallera agenten manuellt från kommandotolken eller använda en automatiserad metod, till exempel ett skript, kan du använda följande exempel. Först måste du hämta produktkoden, som är ett GUID som är huvudidentifieraren för programpaketet, från operativsystemet. Avinstallationen utförs med hjälp av kommandoraden Msiexec.exe - `msiexec /x {Product Code}`.
    
1. Öppna Registereditorn.

2. Leta efter `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`och kopiera produktkoden GUID under registernyckeln.

3. Du kan sedan avinstallera agenten med hjälp av Msiexec med hjälp av följande exempel:

   * Från kommandoradstypen:

       ```dos
       msiexec.exe /x {product code GUID} /qn
       ```

   * Du kan utföra samma steg med PowerShell:

       ```powershell
       Get-ChildItem -Path HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall | `
       Get-ItemProperty | `
       Where-Object {$_.DisplayName -eq "Azure Connected Machine Agent"} | `
       ForEach-Object {MsiExec.exe /x "$($_.PsChildName)" /qn}
       ```

### <a name="linux-agent"></a>Linux-agent

> [!NOTE]
> Om du vill avinstallera *root* agenten måste du ha root-åtkomstbehörigheter eller med ett konto som har förhöjda rättigheter med Sudo.

För att avinstallera Linux-agenten beror kommandot som ska användas på Operativsystemet Linux.

- För Ubuntu kör du följande kommando:

    ```bash
    sudo apt purge azcmagent
    ```

- För RHEL, CentOS och Amazon Linux kör du följande kommando:

    ```bash
    sudo yum remove azcmagent
    ```

- För SLES kör du följande kommando:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Avregistrera dator

Om du planerar att sluta hantera datorn med stödtjänster i Azure utför du följande steg för att avregistrera datorn med Arc för servrar (förhandsversion). Du kan utföra dessa steg antingen före eller efter att du har tagit bort agenten ansluten dator från maskinen.

1. Öppna Azure Arc för servrar (förhandsversion) genom att gå till [Azure-portalen](https://aka.ms/hybridmachineportal).

2. Välj maskinen i listan, välj ellipsen (**...**) och välj sedan **Ta bort**.
