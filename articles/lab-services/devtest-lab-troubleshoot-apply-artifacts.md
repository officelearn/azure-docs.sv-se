---
title: Felsöka problem med artefakter i Azure DevTest Labs | Microsoft-dokument
description: Lär dig hur du felsöker problem som uppstår vid användning av artefakter på en virtuell Azure DevTest Labs-dator.
services: devtest-lab
documentationcenter: na
author: spelluru
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/03/2019
ms.author: spelluru
ms.openlocfilehash: fc5051667100a2ebaa01b7815f825fadd766b08f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75456984"
---
# <a name="troubleshoot-issues-when-applying-artifacts-in-an-azure-devtest-labs-virtual-machine"></a>Felsöka problem vid tillämpning av artefakter på en virtuell Azure DevTest Labs-dator
Att tillämpa artefakter på en virtuell dator kan misslyckas av olika skäl. Den här artikeln guidar dig genom några av metoderna för att identifiera möjliga orsaker.

Om du behöver mer hjälp när som helst i den här artikeln kan du kontakta DTL-experterna (Azure DevTest Labs) på [MSDN Azure- och Stack Overflow-forumen](https://azure.microsoft.com/support/forums/). Du kan också arkivera en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj Hämta support.   

> [!NOTE]
> Den här artikeln gäller både virtuella datorer i Windows och andra datorer än Windows. Även om det finns vissa skillnader, kommer de att kallas ut uttryckligen i den här artikeln.

## <a name="quick-troubleshooting-steps"></a>Snabba felsökningssteg
Kontrollera att den virtuella datorn körs. DevTest Labs kräver att den virtuella datorn körs och att [Microsoft Azure Virtual Machine Agent (VM Agent)](../virtual-machines/extensions/agent-windows.md) är installerad och klar.

> [!TIP]
> I **Azure-portalen**navigerar du till sidan **Hantera artefakter** för den virtuella datorn för att se om den virtuella datorn är klar för att tillämpa artefakter. Du ser ett meddelande högst upp på den sidan. 
> 
> Med **Azure PowerShell**bör du inspektera flaggan **canApplyArtifacts**, som returneras endast när du expanderar på en GET-åtgärd. Se följande exempelkommando:

```powershell
Select-AzSubscription -SubscriptionId $SubscriptionId | Out-Null
$vm = Get-AzResource `
        -Name "$LabName/$VmName" `
        -ResourceGroupName $LabRgName `
        -ResourceType 'microsoft.devtestlab/labs/virtualmachines' `
        -ApiVersion '2018-10-15-preview' `
        -ODataQuery '$expand=Properties($expand=ComputeVm)'
$vm.Properties.canApplyArtifacts
```

## <a name="ways-to-troubleshoot"></a>Felsöker olika sätt att felsöka 
Du kan felsöka virtuella datorer som skapats med DevTest Labs och Resurshanterarens distributionsmodell med någon av följande metoder:

- **Azure portal** - bra om du snabbt behöver få en visuell antydan om vad som kan orsaka problemet.
- **Azure PowerShell** – om du är bekväm med en PowerShell-prompt, fråga snabbt DevTest Labs-resurser med Azure PowerShell-cmdlets.

> [!TIP]
> Mer information om hur du granskar artefaktkörning i en virtuell dator finns [i Diagnostisera artefaktfel i labbet](devtest-lab-troubleshoot-artifact-failure.md).

## <a name="symptoms-causes-and-potential-resolutions"></a>Symptom, orsaker och potentiella upplösningar 

### <a name="artifact-appears-to-hang"></a>Artefakten verkar hänga   
En artefakt verkar hänga tills en fördefinierad timeout-period löper ut och artefakten markeras som **Misslyckad**.

När en artefakt verkar hänga, först bestämma var den har fastnat. En artefakt kan blockeras vid något av följande steg under körningen:

- **Under den första begäran**. DevTest Labs skapar en Azure Resource Manager-mall för att begära användning av CSE (Custom Script Extension). Bakom kulisserna utlöses därför en resursgruppsdistribution. När ett fel på den här nivån inträffar får du information i **aktivitetsloggarna** för resursgruppen för den virtuella datorn i fråga.  
    - Du kan komma åt aktivitetsloggen från navigeringsfältet för sidan för labbet. När du väljer den visas en post för **antingen att använda artefakter på den virtuella datorn** (om åtgärden tillämpa artefakter utlöstes direkt) eller Lägg till eller ändra virtuella **datorer** (om åtgärden tillämpa artefakter var en del av den virtuella datorns skapandeprocess).
    - Leta efter fel under dessa poster. Ibland kommer felet inte att taggas i enlighet med detta, och du måste undersöka varje post.
    - När du undersöker detaljerna för varje post, se till att granska innehållet i JSON nyttolasten. Du kan se ett fel längst ned i dokumentet.
- **När du försöker utföra artefakten**. Det kan bero på nätverk eller lagringsproblem. Mer information finns i respektive avsnitt senare i den här artikeln. Det kan också hända på grund av hur skriptet är författat. Ett exempel:
    - Ett PowerShell-skript har **obligatoriska parametrar**, men ett misslyckas med att skicka ett värde till det, antingen för att du tillåter användaren att lämna det tomt eller för att du inte har ett standardvärde för egenskapen i definitionsfilen artifactfile.json. Skriptet kommer att hängas eftersom det väntar på indata från användaren.
    - Ett PowerShell-skript **kräver indata från användaren** som en del av körningen. Skript måste skrivas för att fungera tyst utan att användaren behöver ingripa.
- **VM-agenten tar lång tid att vara redo**. När den virtuella datorn startas, eller när det anpassade skripttillägget först installeras för att hantera begäran om att tillämpa artefakter, kan den virtuella datorn kräva antingen uppgradering av VM-agenten eller vänta på att VM-agenten initieras. Det kan finnas tjänster som VM-agenten är beroende av som tar lång tid att initiera. I sådana fall läser du [Översikt över Azure Virtual Machine Agent](../virtual-machines/extensions/agent-windows.md) för ytterligare felsökning.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-script"></a>Så här kontrollerar du om artefakten verkar hängas på grund av skriptet

1. Logga in på den virtuella datorn i fråga.
2. Kopiera skriptet lokalt i den virtuella datorn eller `C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\<version>`leta upp det på den virtuella datorn under . Det är platsen där artefaktskripten hämtas.
3. Med hjälp av en upphöjd kommandotolk kör du skriptet lokalt, vilket ger samma parametervärden som används för att orsaka problemet.
4. Ta reda på om skriptet lider av något oönskat beteende. Om så är fallet, antingen begära en uppdatering av artefakten (om det är från den offentliga repo). eller göra korrigeringarna själv (om det är från din privata repo).

> [!TIP]
> Du kan korrigera problem med artefakter som finns i vår [offentliga repo](https://github.com/Azure/azure-devtestlab) och skicka in ändringarna för vår granskning och godkännande. Se avsnittet **Bidrag** i [README.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/README.md) dokumentet.
> 
> Information om hur du skriver egna artefakter finns [i AUTHORING.md](https://github.com/Azure/azure-devtestlab/blob/master/Artifacts/AUTHORING.md) dokument.

### <a name="to-verify-if-the-artifact-appears-to-hang-because-of-the-vm-agent"></a>Så här kontrollerar du om artefakten verkar hänga på grund av VM-agenten:
1. Logga in på den virtuella datorn i fråga.
2. Använda Utforskaren navigera till **C:\WindowsAzure\logs**.
3. Leta upp och öppna filen **WaAppAgent.log**.
4. Leta efter poster som visar när VM-agenten startar och när den slutför initieringen (det vill ha det första pulsslaget skickas). Gynna nyare poster eller specifikt de runt den tidsperiod som du upplever problemet.

    ```
    [00000006] [11/14/2019 05:52:13.44] [INFO]  WindowsAzureGuestAgent starting. Version 2.7.41491.949
    ...
    [00000006] [11/14/2019 05:52:31.77] [WARN]  Waiting for OOBE to Complete ...
    ...
    [00000006] [11/14/2019 06:02:30.43] [WARN]  Waiting for OOBE to Complete ...
    [00000006] [11/14/2019 06:02:33.43] [INFO]  StateExecutor initialization completed.
    [00000020] [11/14/2019 06:02:33.43] [HEART] WindowsAzureGuestAgent Heartbeat.
    ```
    I det här exemplet kan du se att vm-agentens starttid tog 10 minuter och 20 sekunder eftersom ett pulsslag skickades. Orsaken i det här fallet var att OOBE-tjänsten tog lång tid att starta.

> [!TIP]
> Allmän information om Azure-tillägg finns i [Azures tillägg och funktioner för virtuella datorer](../virtual-machines/extensions/overview.md).

## <a name="storage-errors"></a>Lagringsfel
DevTest Labs kräver åtkomst till labbets lagringskonto som skapas för att cachelagra artefakter. När DevTest Labs tillämpar en artefakt läser den artefaktkonfigurationen och dess filer från de konfigurerade databaserna. Som standard konfigurerar DevTest Labs åtkomst till den **offentliga artefaktrepon .**

Beroende på hur en virtuell dator är konfigurerad kanske den inte har direkt åtkomst till den här fördelningen. Därför cachelagrar DevTest Labs artefakterna i ett lagringskonto som skapas när labbet först initieras.

Om åtkomsten till det här lagringskontot blockeras på något sätt, som när trafiken blockeras från den virtuella datorn till Azure Storage-tjänsten, kan du se ett fel som liknar följande:

```shell
CSE Error: Failed to download all specified files. Exiting. Exception: Microsoft.WindowsAzure.Storage.StorageException: The remote server returned an error: (403) Forbidden. ---> System.Net.WebException: The remote server returned an error: (403) Forbidden.
```

Ovanstående fel visas i avsnittet **Distributionsmeddelande** på sidan **Artefaktresultat** under **Hantera artefakter**. Det visas också i **aktivitetsloggarna** under resursgruppen för den virtuella datorn i fråga.

### <a name="to-ensure-communication-to-the-azure-storage-service-isnt-being-blocked"></a>Så här säkerställer du att kommunikationen till Azure Storage-tjänsten inte blockeras:

- **Sök efter tillagda nätverkssäkerhetsgrupper (NSG)**. Det kan bero på att en prenumerationsprincip har lagts till där NSG:er konfigureras automatiskt i alla virtuella nätverk. Det skulle också påverka labbets standard virtuella nätverk, om det används, eller annat virtuellt nätverk som konfigurerats i labbet, som används för att skapa virtuella datorer.
- **Kontrollera standardlabbets lagringskonto** (det vill säga det första lagringskontot som skapades när labbet skapades, vars namn vanligtvis\<börjar\>med bokstaven "a" och slutar med ett flersiffrigt nummer som är ett labbnamn #).
    1. Navigera till labbets resursgrupp.
    2. Leta reda på resursen för **typlagringskonto**, vars namn matchar konventionen.
    3. Navigera till lagringskontosidan som kallas **Brandväggar och virtuella nätverk**.
    4. Se till att den är inställd på **Alla nätverk**. Om alternativet **Markerade nätverk** är markerat kontrollerar du att labbets virtuella nätverk som används för att skapa virtuella datorer läggs till i listan.

Mer detaljerad felsökning finns i [Konfigurera Azure Storage-brandväggar och virtuella nätverk](../storage/common/storage-network-security.md).

> [!TIP]
> **Verifiera regler för nätverkssäkerhetsgrupper**. Använd [IP-flödeskontroll](../network-watcher/diagnose-vm-network-traffic-filtering-problem.md#use-ip-flow-verify) för att bekräfta att en regel i en nätverkssäkerhetsgrupp blockerar trafik till eller från en virtuell dator. Du kan också granska effektiva säkerhetsgruppsregler för att säkerställa att det finns inkommande **tillåt** NSG-regel. Mer information finns i [Använda effektiva säkerhetsregler för att felsöka vm-trafikflöde](../virtual-network/diagnose-network-traffic-filter-problem.md).

## <a name="other-sources-of-error"></a>Andra felkällor
Det finns andra mindre frekventa möjliga felkällor. Se till att utvärdera var och en för att se om det gäller för ditt fall. Här är en av dem: 

- **Utgångna personlig åtkomsttoken för den privata repo.** När den har upphört att gälla visas inte artefakten och skript som refererar till artefakter från en databas med en förfallen privat åtkomsttoken misslyckas i enlighet med detta.

## <a name="next-steps"></a>Nästa steg
Om inget av dessa fel uppstod och du fortfarande inte kan använda artefakter kan du lämna in en Azure-supportincident. Gå till [Azure-supportwebbplatsen](https://azure.microsoft.com/support/options/) och välj **Hämta support**.

