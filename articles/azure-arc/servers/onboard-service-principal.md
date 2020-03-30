---
title: Anslut hybriddatorer till Azure i stor skala
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med Azure Arc för servrar (förhandsversion) med hjälp av ett tjänsthuvudnamn.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77192277"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Anslut hybriddatorer till Azure i stor skala

Du kan aktivera Azure Arc för servrar (förhandsversion) för flera Windows- eller Linux-datorer i din miljö med flera flexibla alternativ beroende på dina behov. Med hjälp av mallskriptet vi tillhandahåller kan du automatisera varje steg i installationen, inklusive upprättande av anslutningen till Azure Arc. Du måste dock interaktivt köra skriptet med ett konto som har förhöjda behörigheter på måldatorn och i Azure. Om du vill ansluta datorerna till Azure Arc för servrar kan du använda [huvudnamnen](../../active-directory/develop/app-objects-and-service-principals.md) för Tjänsten Azure i stället för att använda din privilegierade identitet för att [interaktivt ansluta datorn](onboard-portal.md). Ett tjänsthuvudnamn är en särskild begränsad hanteringsidentitet som endast beviljas `azcmagent` den minsta behörighet som krävs för att ansluta datorer till Azure med kommandot. Detta är säkrare än att använda ett högre privilegierat konto som en klientadministratör och följer våra bästa praxis för åtkomstkontrollsäkerhet. Tjänstens huvudnamn används endast under introduktion, det används inte för något annat ändamål.  

Installationsmetoderna för att installera och konfigurera agenten ansluten dator kräver att den automatiserade metod du använder har administratörsbehörighet på datorerna. På Linux, genom att använda rotkontot och i Windows, som medlem i gruppen Lokala administratörer.

Innan du börjar bör du se till att granska [förutsättningarna](overview.md#prerequisites) och kontrollera att din prenumeration och dina resurser uppfyller kraven.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

I slutet av den här processen har du anslutit dina hybriddatorer till Azure Arc för servrar.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Skapa en tjänsthuvudnamn för introduktion i stor skala

Du kan använda [Azure PowerShell](/powershell/azure/install-az-ps) för att skapa ett tjänsthuvudnamn med cmdleten [New-AzADServicePrincipal.](/powershell/module/Az.Resources/New-AzADServicePrincipal) Du kan också följa stegen under [Skapa ett tjänsthuvudnamn med hjälp av Azure-portalen](../../active-directory/develop/howto-create-service-principal-portal.md) för att slutföra den här uppgiften.

> [!NOTE]
> När du skapar ett tjänsthuvudnamn måste ditt konto vara en ägare eller administratör för användaråtkomst i den prenumeration som du vill använda för introduktion. Om du inte har tillräcklig behörighet för att skapa rolltilldelningar kan tjänstens huvudnamn skapas, men det kan inte vara inbyggda datorer.
>

Om du vill skapa tjänstens huvudnamn med PowerShell utför du följande.

1. Kör följande kommando. Du måste lagra cmdletens [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) utdata i en variabel, annars kan du inte hämta lösenordet som behövs i ett senare steg.

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

2. Om du vill hämta `$sp` lösenordet som lagras i variabeln kör du följande kommando:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. Leta reda på lösenordsvärdet under **fältets lösenord** i utdata och kopiera det. Också hitta värdet under fältet **ApplicationId** och kopiera det också. Spara dem till senare på en säker plats. Om du glömmer eller förlorar tjänstens huvudlösenord kan du återställa det med hjälp av cmdleten. [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential)

Värdena från följande egenskaper används med `azcmagent`parametrar som skickas till :

* Värdet från egenskapen **ApplicationId** används `--service-principal-id` för parametervärdet
* Värdet från **password** lösenordsegenskapen `--service-principal-secret` används för parametern som används för att ansluta agenten.

> [!NOTE]
> Se till att använda egenskapen **ApplicationId** för tjänsten, inte **egenskapen Id.**
>

**Azure Connected Machine Onboarding-rollen** innehåller endast de behörigheter som krävs för att registrera en dator. Du kan tilldela tjänstens huvudbehörighet så att dess omfattning kan inkludera en resursgrupp eller en prenumeration. Information om hur du lägger till rolltilldelning finns i [Lägga till eller ta bort rolltilldelningar med Azure RBAC och Azure-portalen](../../role-based-access-control/role-assignments-portal.md) eller Lägg till eller ta bort [rolltilldelningar med Azure RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och ansluta till Azure

Följande steg installerar och konfigurerar agenten ansluten dator på hybriddatorerna med hjälp av skriptmallen, som utför liknande steg som beskrivs i [Connect-hybriddatorerna till Azure från Azure-portalartikeln.](onboard-portal.md) Skillnaden är i det sista steget där du upprättar anslutningen till Azure Arc med `azcmagent` kommandot med hjälp av tjänstens huvudnamn. 

Följande är de inställningar som `azcmagent` du konfigurerar kommandot som ska användas för tjänstens huvudnamn.

* `tenant-id`: Den unika identifierare (GUID) som representerar din dedikerade instans av Azure AD.
* `subscription-id`: Prenumerations-ID (GUID) för din Azure-prenumeration som du vill ha datorerna i.
* `resource-group`: Resursgruppsnamnet där du vill att dina anslutna datorer ska tillhöra.
* `location`: Se [Azure-regioner som stöds](overview.md#supported-regions). Den här platsen kan vara samma eller annorlunda, som resursgruppens plats.
* `resource-name`: (*Valfritt*) Används för Azure-resursrepresentationen av din lokala dator. Om du inte anger det här värdet används datorvärden.

Du kan läsa `azcmagent` mer om kommandoradsverktyget genom att granska [Azcmagent Reference](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Installationsskript för Windows

Följande är ett exempel på installationsskriptet för ansluten dator för Windows som har ändrats för att använda tjänstens huvudnamn för att stödja en helt automatiserad, icke-interaktiv installation av agenten.

```
 # Download the package
function download() {$ProgressPreference="SilentlyContinue"; Invoke-WebRequest -Uri https://aka.ms/AzureConnectedMachineAgent -OutFile AzureConnectedMachineAgent.msi}
download

# Install the package
msiexec /i AzureConnectedMachineAgent.msi /l*v installationlog.txt /qn | Out-String

# Run connect command
& "$env:ProgramFiles\AzureConnectedMachineAgent\azcmagent.exe" connect `
  --service-principal-id "{serviceprincipalAppID}" `
  --service-principal-secret "{serviceprincipalPassword}" `
  --resource-group "{ResourceGroupName}" `
  --tenant-id "{tenantID}" `
  --location "{resourceLocation}" `
  --subscription-id "{subscriptionID}"
```

### <a name="linux-installation-script"></a>Installationsskript för Linux

Följande är ett exempel på Connected Machine-agenten för Linux-installationsskriptet som har ändrats för att använda tjänstens huvudnamn för att stödja en helt automatiserad, icke-interaktiv installation av agenten.

```
# Download the installation package
wget https://aka.ms/azcmagent -O ~/install_linux_azcmagent.sh

# Install the hybrid agent
bash ~/install_linux_azcmagent.sh

# Run connect command
azcmagent connect \
  --service-principal-id "{serviceprincipalAppID}" \
  --service-principal-secret "{serviceprincipalPassword}" \
  --resource-group "{ResourceGroupName}" \
  --tenant-id "{tenantID}" \
  --location "{resourceLocation}" \
  --subscription-id "{subscriptionID}"
```

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc för servrar (förhandsversion) går du till Azure-portalen för att kontrollera att servern har anslutits. Visa dina datorer i [Azure-portalen](https://aka.ms/hybridmachineportal).

![En lyckad serveranslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med [Azure Policy](../../governance/policy/overview.md), för till exempel [VM-gästkonfiguration,](../../governance/policy/concepts/guest-configuration.md)verifierar att datorn rapporterar till den förväntade Log Analytics-arbetsytan, aktiverar övervakning med [Azure Monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)och mycket mer.

- Läs mer om [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du proaktivt vill övervaka operativsystemet och arbetsbelastningar som körs på datorn, hantera det med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).
