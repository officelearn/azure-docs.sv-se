---
title: Hantera Azure Arc för servrar (förhandsversion) agent
description: I den här artikeln beskrivs de olika hanteringsuppgifter som du vanligtvis utför under livscykeln för Azure Arc för servrar Connected Machine agent.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308960"
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

## <a name="about-the-azcmagent-tool"></a>Om Azcmagent-verktyget

Azcmagent-verktyget (Azcmagent.exe) används för att konfigurera Azure Arc för servrar (förhandsversion) Connected Machine-agenten under installationen eller ändra agentens första konfiguration efter installationen. Azcmagent.exe tillhandahåller kommandoradsparametrar för att anpassa agenten och visa dess status:

* **Anslut** - För att ansluta datorn till Azure Arc

* **Koppla från** - För att koppla från datorn från Azure Arc

* **Återanslut** - Om du vill återansluta en frånkopplad dator till Azure Arc

* **Visa** - Visa agentstatus och dess konfigurationsegenskaper (resursgruppsnamn, prenumerations-ID, version osv.), vilket kan vara till hjälp vid felsökning av ett problem med agenten.

* **-h eller --help** - Visar tillgängliga kommandoradsparametrar

    Om du till exempel vill se detaljerad `azcmagent reconnect -h`hjälp för parametern **Återanslutning** skriver du . 

* **-v eller --verbose** - Aktivera utförlig loggning

Du kan utföra en **Connect,** **Disconnect**och **Återanslut** manuellt när du är inloggad interaktivt, eller automatisera med samma tjänsthuvudnamn som du använde för att registrera flera agenter eller med en Microsoft-åtkomsttoken för identitetsplattform . [access token](../../active-directory/develop/access-tokens.md) Om du inte använde ett tjänsthuvudnamn för att registrera datorn med Azure Arc för servrar (förhandsversion) läser du följande [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) för att skapa ett huvudnamn för tjänsten.

### <a name="connect"></a>Anslut

Den här parametern anger en resurs i Azure Resource Manager som representerar datorn skapas i Azure. Resursen finns i den angivna prenumerations- och resursgruppen och data om datorn `--location` lagras i Azure-regionen som anges av inställningen. Standardresursnamnet är den här datorns värdnamn om det inte anges.

Ett certifikat som motsvarar datorns systemtilldelade identitet hämtas och lagras sedan lokalt. När det här steget är klart börjar Azure Connected Machine Metadata Service och Guest Configuration Agent synkronisera med Azure Arc för servrar (förhandsversion).

Om du vill ansluta med hjälp av ett huvudnamn för tjänsten kör du följande kommando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Om du vill ansluta med hjälp av en åtkomsttoken kör du följande kommando:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Om du vill ansluta till dina förhöjda inloggningsuppgifter (interaktiv) kör du följande kommando:

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Koppla från

Den här parametern anger en resurs i Azure Resource Manager som representerar att datorn tas bort i Azure. Det tar inte bort agenten från maskinen, detta måste göras som ett separat steg. När datorn har kopplats från, om du vill registrera den igen `azcmagent connect` med Azure Arc för servrar (förhandsversion), använd så att en ny resurs skapas för den i Azure.

Om du vill koppla från med ett tjänsthuvudnamn kör du följande kommando:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Om du vill koppla från med hjälp av en åtkomsttoken kör du följande kommando:

`azcmagent disconnect --access-token <accessToken>`

Om du vill koppla från med dina förhöjda inloggningsuppgifter (interaktiv) kör du följande kommando:

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Återansluta

Den här parametern återansluter den redan registrerade eller anslutna datorn med Azure Arc för servrar (förhandsversion). Detta kan vara nödvändigt om maskinen har stängts av, minst 45 dagar, för att certifikatet ska upphöra att gälla. Den här parametern använder de autentiseringsalternativ som anges för att hämta nya autentiseringsuppgifter som motsvarar Azure Resource Manager-resursen som representerar den här datorn.

Det här kommandot kräver högre behörighet än azure [connected machine onboarding-rollen.](overview.md#required-permissions)

Om du vill återansluta med ett huvudnamn för tjänsten kör du följande kommando:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Om du vill återansluta med hjälp av en åtkomsttoken kör du följande kommando:

`azcmagent reconnect --access-token <accessToken>`

Om du vill återansluta till dina förhöjda inloggningsuppgifter (interaktiv) kör du följande kommando:

`azcmagent reconnect --tenant-id <tenantID>`

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
