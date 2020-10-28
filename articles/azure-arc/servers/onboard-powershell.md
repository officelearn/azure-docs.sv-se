---
title: Ansluta hybrid datorer till Azure med PowerShell
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter en dator till Azure med hjälp av Azure Arc-aktiverade servrar med PowerShell.
ms.date: 10/27/2020
ms.topic: conceptual
ms.openlocfilehash: bb114ec3e279a7ea696d834af8eb7240cb892dc1
ms.sourcegitcommit: 4064234b1b4be79c411ef677569f29ae73e78731
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/28/2020
ms.locfileid: "92891949"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Ansluta hybrid datorer till Azure med PowerShell

Du kan aktivera Azure Arc-aktiverade servrar för en eller flera Windows-eller Linux-datorer i din miljö genom att utföra en uppsättning manuella steg. Du kan också använda PowerShell-cmdleten [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine) för att ladda ned den anslutna dator agenten, installera agenten och registrera datorn med Azure-bågen. Cmdlet: en laddar ned Windows agent Windows Installer-paketet från Microsoft Download Center och Linux Agent-paketet från Microsofts paket lagrings plats.

Den här metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, är du medlem i den lokala gruppen Administratörer. Du kan slutföra den här processen interaktivt eller via fjärr anslutning på en Windows-Server med [PowerShell-fjärrkommunikation](/powershell/scripting/learn/ps101/08-powershell-remoting).

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](agent-overview.md#prerequisites) resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds](overview.md#supported-regions).

Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Förutsättningar

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Innan du använder Azure PowerShell för att hantera VM-tillägg på hybrid servern som hanteras av Arc-aktiverade servrar måste du installera `Az.ConnectedMachine` modulen. Kör följande kommando på din ARC-aktiverade Server:

```powershell
Install-Module -Name Az.ConnectedMachine
```

När installationen är klar returneras följande meddelande:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och Anslut till Azure

1. Öppna en PowerShell-konsol med utökade privilegier.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera den anslutna dator agenten använder `Connect-AzConnectedMachine` du med `-Name` `-ResourceGroupName` parametrarna,, och `-Location` . Använd `-SubscriptionId` parametern för att åsidosätta standard prenumerationen som ett resultat av Azure-kontexten som skapats efter inloggning. Kör något av följande kommandon:

    * Kör följande om du vill installera den anslutna dator agenten på mål datorn som kan kommunicera direkt med Azure:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    * Om du vill installera den anslutna dator agenten på mål datorn som kommunicerar via en proxyserver kör du:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. På Windows på *%programdata%\AzureConnectedMachineAgent\Log\himds.log* och på Linux på */var/opt/azcmagent/log/himds.log* .

## <a name="install-and-connect-using-powershell-remoting"></a>Installera och Anslut med PowerShell-fjärrkommunikation

Utför följande steg för att konfigurera mål Windows Server eller dator med Azure Arc-aktiverade servrar. PowerShell-fjärrkommunikation måste vara aktiverat på fjärrdatorn. Använd `Enable-PSRemoting` cmdleten för att aktivera PowerShell-fjärrkommunikation.

1. Öppna en PowerShell-konsol som administratör.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera den anslutna dator agenten använder `Connect-AzConnectedMachine` du med `-Name` `-ResourceGroupName` parametrarna,, och `-Location` . Använd `-SubscriptionId` parametern för att åsidosätta standard prenumerationen som ett resultat av Azure-kontexten som skapats efter inloggning.

Kör följande kommando för att installera den anslutna dator agenten på mål datorn som kan kommunicera direkt med Azure:

```azurepowershell
$session = Connect-PSSession -ComputerName myMachineName
Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -PSSession $session
```

Följande exempel är resultatet av kommandot:

```azurepowershell
time="2020-08-07T13:13:25-07:00" level=info msg="Onboarding Machine. It usually takes a few minutes to complete. Sometimes it may take longer depending on network and server load status."
time="2020-08-07T13:13:25-07:00" level=info msg="Check network connectivity to all endpoints..."
time="2020-08-07T13:13:29-07:00" level=info msg="All endpoints are available... continue onboarding"
time="2020-08-07T13:13:50-07:00" level=info msg="Successfully Onboarded Resource to Azure" VM Id=f65bffc7-4734-483e-b3ca-3164bfa42941

Name           Location OSName   Status     ProvisioningState
----           -------- ------   ------     -----------------
myMachineName  eastus   windows  Connected  Succeeded
```

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://portal.azure.com).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-policy.md)och mycket mer.

* Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill samla in operativ system och data för övervakning av arbets belastning, hantera dem med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).