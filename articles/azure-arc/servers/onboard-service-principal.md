---
title: Ansluta hybrid datorer till Azure i stor skala
description: I den här artikeln får du lära dig hur du ansluter datorer till Azure med hjälp av Azure Arc for Servers (för hands version) med ett huvud namn för tjänsten.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
ms.date: 02/04/2020
ms.topic: conceptual
ms.openlocfilehash: 3a19dc019d2566ddddb2c0ba7988b342d30a45d4
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192277"
---
# <a name="connect-hybrid-machines-to-azure-at-scale"></a>Ansluta hybrid datorer till Azure i stor skala

Du kan aktivera Azure Arc for Servers (för hands version) för flera Windows-eller Linux-datorer i din miljö med flera flexibla alternativ beroende på dina behov. Med hjälp av det mall-skript som vi tillhandahåller kan du automatisera alla steg i installationen, inklusive att upprätta anslutningen till Azure-bågen. Du måste dock köra det här skriptet interaktivt med ett konto som har förhöjd behörighet på mål datorn och i Azure. Om du vill ansluta datorerna till Azure-bågen för servrar kan du använda ett Azure Active Directory [tjänstens huvud namn](../../active-directory/develop/app-objects-and-service-principals.md) i stället för att använda din privilegierade identitet för att [ansluta datorn interaktivt](onboard-portal.md). Ett tjänst huvud namn är en särskild begränsad hanterings identitet som endast beviljas den lägsta behörighet som krävs för att ansluta datorer till Azure med hjälp av kommandot `azcmagent`. Detta är säkrare än att använda ett högre privilegierat konto, till exempel en innehavaradministratör, och följer våra bästa metoder för åtkomst kontroll. Tjänstens huvud namn används bara vid onboarding, det används inte i något annat syfte.  

Installations metoderna för att installera och konfigurera den anslutna dator agenten kräver att den automatiserade metoden du använder har administratörs behörighet på datorerna. I Linux, med hjälp av rot kontot och Windows, som medlem i den lokala administratörs gruppen.

Innan du börjar bör du läsa igenom kraven och kontrol lera att din [prenumeration och dina](overview.md#prerequisites) resurser uppfyller kraven.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

I slutet av den här processen kommer du att ha anslutit dina hybrid datorer till Azure-bågen för servrar.

## <a name="create-a-service-principal-for-onboarding-at-scale"></a>Skapa ett huvud namn för tjänsten för onboarding i skala

Du kan använda [Azure PowerShell](/powershell/azure/install-az-ps) för att skapa ett huvud namn för tjänsten med cmdleten [New-AzADServicePrincipal](/powershell/module/Az.Resources/New-AzADServicePrincipal) . Du kan också följa stegen som visas under [skapa ett huvud namn för tjänsten med hjälp av Azure Portal](../../active-directory/develop/howto-create-service-principal-portal.md) för att slutföra den här aktiviteten.

> [!NOTE]
> När du skapar ett huvud namn för tjänsten måste ditt konto vara en ägare eller administratör för användar åtkomst i den prenumeration som du vill använda för onboarding. Om du inte har tillräcklig behörighet för att skapa roll tilldelningar kan tjänstens huvud namn skapas, men det går inte att publicera datorer.
>

Utför följande för att skapa tjänstens huvud namn med PowerShell.

1. Kör följande kommando. Du måste lagra utdata från [`New-AzADServicePrincipal`](/powershell/module/az.resources/new-azadserviceprincipal) cmdlet i en variabel, eller så kan du inte hämta lösen ordet som krävs i ett senare steg.

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

2. Kör följande kommando för att hämta det lösen ord som lagras i variabeln `$sp`:

    ```azurepowershell-interactive
    $credential = New-Object pscredential -ArgumentList "temp", $sp.Secret
    $credential.GetNetworkCredential().password
    ```

3. I utdata söker du efter lösen ordets värde under fältet **lösen ord** och kopierar det. Hitta även värdet under fältet **ApplicationId** och kopiera det också. Spara dem för senare på ett säkert ställe. Om du glömmer bort eller förlorar lösen ordet för tjänstens huvud namn kan du återställa det med hjälp av [`New-AzADSpCredential`](/powershell/module/azurerm.resources/new-azurermadspcredential) -cmdleten.

Värdena från följande egenskaper används med parametrar som skickas till `azcmagent`:

* Värdet från egenskapen **ApplicationId** används för värdet `--service-principal-id` parameter
* Värdet från **lösen ords** egenskapen används för den `--service-principal-secret`-parameter som används för att ansluta agenten.

> [!NOTE]
> Se till att använda egenskapen **ApplicationId** för tjänstens huvud namn, inte egenskapen **ID** .
>

Den **Azure-anslutna dator onboarding** -rollen innehåller bara de behörigheter som krävs för att publicera en dator. Du kan tilldela tjänstens huvud namn behörighet att tillåta att dess omfång inkluderar en resurs grupp eller en prenumeration. Information om hur du lägger till roll tilldelning finns i [lägga till eller ta bort roll tilldelningar med hjälp av Azure RBAC och Azure Portal](../../role-based-access-control/role-assignments-portal.md) eller [lägga till eller ta bort roll tilldelningar med Azure RBAC och Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="install-the-agent-and-connect-to-azure"></a>Installera agenten och Anslut till Azure

Följande steg installerar och konfigurerar den anslutna dator agenten på dina hybrid datorer med hjälp av skript mal len, som utför liknande steg som beskrivs i [ansluta hybrid datorer till Azure från Azure Portal](onboard-portal.md) artikeln. Skillnaden är i det sista steget där du upprättar anslutningen till Azure-bågen med hjälp av `azcmagent` kommandot med hjälp av tjänstens huvud namn. 

Följande är de inställningar som du konfigurerar `azcmagent` kommandot som ska användas för tjänstens huvud namn.

* `tenant-id`: den unika identifieraren (GUID) som representerar din dedikerade instans av Azure AD.
* `subscription-id`: det prenumerations-ID (GUID) för din Azure-prenumeration som du vill ha datorerna i.
* `resource-group`: namnet på den resurs grupp där du vill att dina anslutna datorer ska tillhöra.
* `location`: se [Azure-regioner som stöds](overview.md#supported-regions). Den här platsen kan vara samma eller olika, som resurs gruppens plats.
* `resource-name`: (*valfritt*) som används för Azures resurs åter givning av din lokala dator. Om du inte anger det här värdet används datorns värdnamn.

Du kan lära dig mer om kommando rads verktyget `azcmagent` genom att granska [Azcmagent-referensen](azcmagent-reference.md).

### <a name="windows-installation-script"></a>Installations skript för Windows

Följande är ett exempel på installations skriptet för den anslutna dator agenten för Windows som har ändrats för att använda tjänstens huvud namn för att stödja en helt automatiserad, icke-interaktiv installation av agenten.

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

### <a name="linux-installation-script"></a>Installations skript för Linux

Följande är ett exempel på installations skriptet för den anslutna dator agenten för Linux som har ändrats för att använda tjänstens huvud namn för att stödja en helt automatiserad, icke-interaktiv installation av agenten.

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

När du har installerat agenten och konfigurerat den för att ansluta till Azure Arc for Servers (för hands version) går du till Azure Portal för att kontrol lera att servern har anslutits. Visa dina datorer i [Azure Portal](https://aka.ms/hybridmachineportal).

![En lyckad Server anslutning](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du hanterar din dator med hjälp av [Azure policy](../../governance/policy/overview.md), till exempel för [gäst konfiguration](../../governance/policy/concepts/guest-configuration.md)av virtuella datorer, verifiera att datorn rapporterar till den förväntade Log Analytics arbets ytan, aktivera övervakning med [Azure monitor med virtuella datorer](../../azure-monitor/insights/vminsights-enable-at-scale-policy.md)och mycket mer.

- Läs mer om den [Log Analytics agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).
