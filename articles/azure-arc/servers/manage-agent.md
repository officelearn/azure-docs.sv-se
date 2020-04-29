---
title: Hantera Azure Arc for servers-agenten (för hands version)
description: I den här artikeln beskrivs de olika hanterings aktiviteter som du normalt kommer att utföra under livs cykeln för Azure-bågen för servrar som är anslutna till dator agenten.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 04/14/2020
ms.topic: conceptual
ms.openlocfilehash: 5ad2127b4cb9da3ca83aa04bd1885908a88dba62
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "81308960"
---
# <a name="managing-and-maintaining-the-connected-machine-agent"></a>Hantera och underhålla den anslutna dator agenten

Efter den första distributionen av Azure Arc for Servers (Preview) Connected Machine agent för Windows eller Linux, kan du behöva konfigurera om agenten, uppgradera den eller ta bort den från datorn om den har nått indragnings fasen i livs cykeln. Du kan enkelt hantera dessa rutin underhålls aktiviteter manuellt eller via Automation, vilket minskar både drift fel och kostnader.

## <a name="upgrading-agent"></a>Uppgraderar agent

Azure Connected Machine agent för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina behov. I följande tabell beskrivs de metoder som stöds för att utföra agent uppgraderingen.

| Operativsystem | Uppgraderingsmetod |
|------------------|----------------|
| Windows | Manuellt<br> Windows Update |
| Ubuntu | [Apt](https://help.ubuntu.com/lts/serverguide/apt.html) |
| SUSE Linux Enterprise Server | [zypper](https://en.opensuse.org/SDB:Zypper_usage_11.3) |
| RedHat Enterprise, Amazon, CentOS Linux | [yum](https://wiki.centos.org/PackageManagement/Yum) | 

### <a name="windows-agent"></a>Windows-agent

Om du vill uppdatera agenten på en Windows-dator till den senaste versionen, är agenten tillgänglig från Microsoft Update och kan distribueras med den befintliga processen för hantering av program uppdateringar. Den kan också köras manuellt från kommando tolken, från ett skript eller någon annan Automation-lösning eller från användar gränssnitts guiden genom att `AzureConnectedMachine.msi`köra. 

> [!NOTE]
> * Du måste ha *Administratörs* behörighet för att kunna uppgradera agenten.
> * Om du vill uppgradera manuellt måste du först hämta och kopiera installations paketet till en mapp på mål servern eller från en delad nätverksmapp. 

Om du inte känner till kommando rads alternativen för Windows Installer-paket kan du läsa mer om kommando rads alternativ i [msiexec](https://docs.microsoft.com/windows/win32/msi/standard-installer-command-line-options) och [kommando rads alternativ i msiexec](https://docs.microsoft.com/windows/win32/msi/command-line-options).

#### <a name="to-upgrade-using-the-setup-wizard"></a>Uppgradera med installations guiden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör **AzureConnectedMachineAgent. msi** för att starta installations guiden.

Installations guiden identifierar om en tidigare version finns och utför sedan automatiskt en uppgradering av agenten. När uppgraderingen är klar stängs installations guiden automatiskt.

#### <a name="to-upgrade-from-the-command-line"></a>Uppgradera från kommando raden

1. Logga in på datorn med ett konto som har administratörs behörighet.

2. Kör följande kommando för att uppgradera agenten tyst och skapa en installations logg `C:\Support\Logs` fil i mappen.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentupgradesetup.log"
    ```

### <a name="linux-agent"></a>Linux-Agent

Om du vill uppdatera agenten på en Linux-dator till den senaste versionen, omfattar den två kommandon. Ett kommando för att uppdatera det lokala paket indexet med listan över senaste tillgängliga paket från databaserna och ett kommando för att uppgradera det lokala paketet. 

> [!NOTE]
> Om du vill uppgradera agenten måste du ha behörighet som *rot* åtkomst eller med ett konto som har utökade rättigheter med sudo.

#### <a name="upgrade-ubuntu"></a>Uppgradera Ubuntu

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    apt update
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    apt upgrade
    ```

Åtgärder för kommandot [apt](https://help.ubuntu.com/lts/serverguide/apt.html) , till exempel installation och borttagning av paket, loggas i `/var/log/dpkg.log` logg filen.

#### <a name="upgrade-red-hatcentosamazon-linux"></a>Uppgradera Red Hat/CentOS/Amazon Linux

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    yum check-update
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    yum update
    ```

Åtgärder för kommandot [yum](https://access.redhat.com/articles/yum-cheat-sheet) , till exempel installation och borttagning av paket, loggas i `/var/log/yum.log` logg filen. 

#### <a name="upgrade-suse-linux-enterprise"></a>Uppgradera SUSE Linux Enterprise

1. Kör följande kommando för att uppdatera det lokala paket indexet med de senaste ändringarna som gjorts i databaserna:

    ```bash
    zypper refresh
    ```

2. Kör följande kommando för att uppgradera systemet:

    ```bash
    zypper update
    ```

Åtgärder för kommandot [zypper](https://en.opensuse.org/Portal:Zypper) , till exempel installation och borttagning av paket, loggas i `/var/log/zypper.log` logg filen. 

## <a name="about-the-azcmagent-tool"></a>Om verktyget Azcmagent

Verktyget Azcmagent (Azcmagent. exe) används för att konfigurera Azure-bågen för servrar (förhands granskning) anslutna dator agenter under installationen eller ändra den inledande konfigurationen av agenten efter installationen. Azcmagent. exe tillhandahåller kommando rads parametrar för att anpassa agenten och visa dess status:

* **Anslut** – för att ansluta datorn till Azure-bågen

* **Koppla** från-koppla bort datorn från Azure-bågen

* **Återanslut** – för att återansluta en frånkopplad dator till Azure-bågen

* **Visa** agent status och dess konfigurations egenskaper (resurs gruppens namn, PRENUMERATIONS-ID, version osv.), som kan hjälpa dig att felsöka ett problem med agenten.

* **-h eller--help** – visar tillgängliga kommando rads parametrar

    Om du till exempel vill visa detaljerad hjälp för parametern **reconnect** skriver `azcmagent reconnect -h`du. 

* **-v eller--utförlig** -Aktivera utförlig loggning

Du kan utföra en **anslutning**, koppla **från**och **återansluta** manuellt när du är inloggad interaktivt eller automatisera med samma tjänst huvud namn som du använde för att publicera flera agenter eller med en [åtkomsttoken för Microsoft Identity Platform.](../../active-directory/develop/access-tokens.md) Om du inte använde ett huvud namn för tjänsten för att registrera datorn med Azure Arc for Servers (för hands version) kan du läsa följande [artikel](onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) för att skapa ett huvud namn för tjänsten.

### <a name="connect"></a>Anslut

Den här parametern anger en resurs i Azure Resource Manager som representerar datorn som skapas i Azure. Resursen är i den angivna prenumerations-och resurs gruppen och data om datorn lagras i den Azure-region som anges av `--location` inställningen. Standard resurs namnet är värd namnet för den här datorn om inget värde anges.

Ett certifikat som motsvarar systemets tilldelade identitet för datorn laddas ned och lagras lokalt. När det här steget har slutförts börjar den Azure Connected Machine-Metadata Service och gäst konfigurations agenten att synkronisera med Azure Arc for Servers (för hands version).

Om du vill ansluta med ett huvud namn för tjänsten kör du följande kommando:

`azcmagent connect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Kör följande kommando för att ansluta med hjälp av en åtkomsttoken:

`azcmagent connect --access-token <> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

Kör följande kommando för att ansluta till dina förhöjda inloggade autentiseringsuppgifter (interaktiva):

`azcmagent connect --tenant-id <TenantID> --subscription-id <subscriptionID> --resource-group <ResourceGroupName> --location <resourceLocation>`

### <a name="disconnect"></a>Koppla från

Den här parametern anger en resurs i Azure Resource Manager som representerar datorn som tas bort i Azure. Agenten tas inte bort från datorn. Detta måste göras som ett separat steg. När datorn har kopplats från, om du vill omregistrera den med Azure Arc for Servers (för hands version), använder `azcmagent connect` du så att en ny resurs skapas för den i Azure.

Kör följande kommando för att koppla från med ett huvud namn för tjänsten:

`azcmagent disconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Kör följande kommando för att koppla från med hjälp av en åtkomsttoken:

`azcmagent disconnect --access-token <accessToken>`

Kör följande kommando för att koppla bort med dina förhöjda inloggade autentiseringsuppgifter (interaktiva):

`azcmagent disconnect --tenant-id <tenantID>`

### <a name="reconnect"></a>Återansluta

Den här parametern återansluter den redan registrerade eller anslutna datorn med Azure Arc for Servers (för hands version). Detta kan vara nödvändigt om datorn har inaktiverats, minst 45 dagar, för att certifikatet ska upphöra att gälla. Den här parametern använder de autentiseringsalternativ som finns för att hämta nya autentiseringsuppgifter som motsvarar den Azure Resource Manager resursen som representerar den här datorn.

Det här kommandot kräver högre privilegier än den [Azure Connected Machine onboarding](overview.md#required-permissions) -rollen.

Kör följande kommando för att återansluta med ett huvud namn för tjänsten:

`azcmagent reconnect --service-principal-id <serviceprincipalAppID> --service-principal-secret <serviceprincipalPassword> --tenant-id <tenantID>`

Kör följande kommando för att återansluta med hjälp av en åtkomsttoken:

`azcmagent reconnect --access-token <accessToken>`

Kör följande kommando för att återansluta med dina förhöjda inloggade autentiseringsuppgifter (interaktiva):

`azcmagent reconnect --tenant-id <tenantID>`

## <a name="remove-the-agent"></a>Ta bort agenten

Utför någon av följande metoder för att avinstallera den Windows-eller Linux-anslutna dator agenten från datorn. Att ta bort agenten avregistrerar inte datorn med ARC for Servers (för hands version), det här är en separat process som du utför när du inte längre behöver hantera datorn i Azure.

### <a name="windows-agent"></a>Windows-agent

Följande metoder tar bort agenten, men tar inte bort mappen *C:\Program Files\AzureConnectedMachineAgent* på datorn.

#### <a name="uninstall-from-control-panel"></a>Avinstallera från kontroll panelen

1. Om du vill avinstallera Windows-agenten från datorn gör du följande:

    a. Logga in på datorn med ett konto som har administratörs behörighet.  
    b. Välj **program och funktioner**på **kontroll panelen**.  
    c. I **program och funktioner**väljer du **Azure Connected Machine agent**, väljer **Avinstallera**och väljer sedan **Ja**.  

    >[!NOTE]
    > Du kan också köra installations guiden för agenten genom att dubbelklicka på installations paketet **AzureConnectedMachineAgent. msi** .

#### <a name="uninstall-from-the-command-line"></a>Avinstallera från kommando raden

Om du vill avinstallera agenten manuellt från kommando tolken eller använda en automatiserad metod, till exempel ett skript, kan du använda följande exempel. Först måste du hämta produkt koden, som är en GUID som är huvud-ID för programpaketet, från operativ systemet. Avinstallationen utförs med hjälp av kommando raden msiexec. exe – `msiexec /x {Product Code}`.
    
1. Öppna Registereditorn.

2. Leta upp och `HKEY_LOCAL_MACHINE\Software\Microsoft\Windows\CurrentVersion\Uninstall`kopiera produkt kod-GUID under register nyckel.

3. Du kan sedan avinstallera agenten med hjälp av Msiexec med hjälp av följande exempel:

   * Från kommando rads typ:

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

### <a name="linux-agent"></a>Linux-Agent

> [!NOTE]
> Om du vill avinstallera agenten måste du ha behörighet som *rot* åtkomst eller med ett konto som har utökade rättigheter med sudo.

Om du vill avinstallera Linux-agenten är kommandot som ska användas beroende av operativ systemet Linux.

- Kör följande kommando för Ubuntu:

    ```bash
    sudo apt purge azcmagent
    ```

- Kör följande kommando för RHEL, CentOS och Amazon Linux:

    ```bash
    sudo yum remove azcmagent
    ```

- Kör följande kommando för SLES:

    ```bash
    sudo zypper remove azcmagent
    ```

## <a name="unregister-machine"></a>Avregistrera datorn

Om du planerar att stoppa hanteringen av datorn med stöd tjänster i Azure utför du följande steg för att avregistrera datorn med ARC for Servers (för hands version). Du kan utföra de här stegen antingen innan eller efter att du har tagit bort den anslutna dator agenten från datorn.

1. Öppna Azure båg for Servers (för hands version) genom att gå till [Azure Portal](https://aka.ms/hybridmachineportal).

2. Välj datorn i listan, Välj ellipsen (..**.**) och välj sedan **ta bort**.
