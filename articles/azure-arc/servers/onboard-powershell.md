---
title: Ansluta hybrid datorer till Azure med PowerShell
description: I den här artikeln får du lära dig hur du installerar agenten och ansluter en dator till Azure med hjälp av Azure Arc-aktiverade servrar med PowerShell.
ms.date: 10/21/2020
ms.topic: conceptual
ms.openlocfilehash: d36fd174606b49b28b1d8343bff6ccc1f62e5194
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/22/2020
ms.locfileid: "92376299"
---
# <a name="connect-hybrid-machines-to-azure-using-powershell"></a>Ansluta hybrid datorer till Azure med PowerShell

Du kan aktivera Azure Arc-aktiverade servrar för ett eller ett litet antal Windows-eller Linux-datorer i din miljö genom att utföra en uppsättning steg manuellt. Du kan också använda PowerShell-cmdleten [Connect-AzConnectedMachine](/powershell/module/az.connectedmachine/remove-azconnectedmachine). Denna cmdlet utför följande åtgärder:

- Konfigurerar värddatorn för att ladda ned Windows-agenten från Microsoft Download Center och Linux Agent-paketet från packages.microsoft.com.
- Installerar den anslutna dator agenten.
- Registrerar datorn med Azure Arc

Den här metoden kräver att du har administratörs behörighet på datorn för att installera och konfigurera agenten. I Linux, med hjälp av rot kontot och i Windows, är du medlem i den lokala gruppen Administratörer.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](agent-overview.md#prerequisites) resurser uppfyller kraven. Information om regioner som stöds och andra relaterade överväganden finns i [Azure-regioner som stöds](overview.md#supported-regions).

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="prerequisites"></a>Krav

- En dator med Azure PowerShell. Anvisningar finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/).

Innan du använder Azure PowerShell för att hantera VM-tillägg på hybrid servern som hanteras av Arc-aktiverade servrar måste du installera `Az.ConnectedMachine` modulen. Kör följande kommando på din ARC-aktiverade Server:

```powershell
Install-Module -Name Az.ConnectedMachine
```

När installationen är klar returneras följande meddelande:

`The installed extension ``Az.ConnectedMachine`` is experimental and not covered by customer support. Please use with discretion.`

## <a name="install-and-validate-the-agent-on-windows"></a>Installera och verifiera agenten i Windows

1. Öppna en PowerShell-konsol som administratör.

2. Logga in på Azure genom att köra kommandot `Connect-AzAccount` .

3. Om du vill installera den anslutna dator agenten använder `Connect-AzConnectedMachine` du med `-Name` `-ResourceGroupName` parametrarna,, och `-Location` . Använd `-SubscriptionId` parametern för att åsidosätta standard prenumerationen som ett resultat av Azure-kontexten som skapats efter inloggning.

    Kör följande kommando för att installera den anslutna dator agenten på mål datorn, som kan kommunicera direkt med Azure:

    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e
    ```
    
    Om mål datorn kommunicerar via en proxyserver kör du följande kommando:
    
    ```azurepowershell
    Connect-AzConnectedMachine -ResourceGroupName myResourceGroup -Name myMachineName -Location <region> -SubscriptionId 978ab182-6cf0-4de3-a58b-53c8d0a3235e -proxy http://<proxyURL>:<proxyport>
    ```

    Om agenten inte startar efter att installationen har slutförts, kontrollerar du i loggarna om det finns detaljerad fel information. På Windows på *%programdata%\AzureConnectedMachineAgent\Log\himds.log*och på Linux på */var/opt/azcmagent/log/himds.log*.

## <a name="verify-the-connection-with-azure-arc"></a>Kontrollera anslutningen med Azure Arc

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc-aktiverade servrar går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://portal.azure.com).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

* Felsöknings information finns i [fel söknings guide för anslutna datorer](troubleshoot-agent-onboard.md).

* Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-policy.md)och mycket mer.

* Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill samla in operativ system och data för övervakning av arbets belastning, hantera dem med hjälp av Automation-runbooks eller funktioner som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-introduction.md).