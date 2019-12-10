---
title: Översikt över Azure båge för servrar
description: Lär dig hur du använder Azure Arc för servrar för att automatisera livs cykeln för infrastruktur och program.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: bobbytreed
ms.author: robreed
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid
ms.date: 11/04/2019
ms.custom: mvc
ms.topic: overview
ms.openlocfilehash: d091b89342570b73ccde5fe496a3432102617918
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951436"
---
# <a name="what-is-azure-arc-for-servers"></a>Vad är Azure-båge för servrar?

Med Azure-båge för servrar kan du hantera datorer som ligger utanför Azure.
När en dator som inte är en Azure-dator är ansluten till Azure blir den en **ansluten dator** och behandlas som en resurs i Azure. Varje **ansluten dator** har ett resurs-ID, hanteras som en del av en resurs grupp i en prenumeration och fördelar med Azures standard konstruktioner, till exempel Azure policy och taggning.

Ett agent paket måste installeras på varje dator för att du ska kunna ansluta det till Azure. Resten av det här dokumentet förklarar processen i detalj.

Datorerna har statusen **ansluten** eller **frånkopplad** baserat på hur nyligen agenten har checkat in. Varje incheckning kallas för ett pulsslag. Om en dator inte har incheckat inom de senaste 5 minuterna visas den som offline tills anslutningen har återställts.  <!-- For more information on troubleshooting agent connectivity, see [Troubleshooting Azure Arc for servers](troubleshoot/arc-for-servers.md). -->

![Anslutna servrar](./media/overview/arc-for-servers-onboarded-servers.png)

## <a name="clients"></a>Klienter

### <a name="supported-operating-systems"></a>Operativsystem som stöds

I offentlig för hands version stöder vi:

- Windows Server 2012 R2 och senare
- Ubuntu 16,04 och 18,04

Den offentliga för hands versionen är utformad i utvärderings syfte och bör inte användas för att hantera kritiska produktions resurser.

## <a name="azure-subscription-and-service-limits"></a>Prenumerations-och tjänst begränsningar i Azure

Se till att du läser Azure Resource Manager gränser och planera för antalet datorer som ska anslutas enligt den rikt linje som anges för [prenumerationen](../../azure-subscription-service-limits.md#subscription-limits---azure-resource-manager)och för [resurs grupperna](../../azure-subscription-service-limits.md#resource-group-limits). I synnerhet finns det en gräns på 800 servrar per resurs grupp som standard.

## <a name="networking-configuration"></a>Nätverks konfiguration

Vid installation och körning kräver agenten anslutning till **Azure Arc-tjänstens slut punkter**. Om utgående anslutning blockeras av brand väggar kontrollerar du att följande URL: er inte blockeras som standard. Alla anslutningar är utgående från agenten till Azure och skyddas med **SSL**. All trafik kan dirigeras via en **https** -proxy. Om du tillåter IP-adressintervall eller domän namn som servrarna tillåts ansluta till måste du tillåta port 443 åtkomst till följande service märken och DNS-namn.

Service märken:

* AzureActiveDirectory
* AzureTrafficManager

En lista över IP-adresser för varje service tag/region finns i JSON-filen – [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publicerar veckovis uppdateringar som innehåller varje Azure-tjänst och de IP-intervall som används. Mer information finns i [tjänst Taggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Dessa DNS-namn tillhandahålls utöver service tag-information om IP-intervall, eftersom majoriteten av tjänsterna för närvarande inte har en registrering av service tag och, vilket kan vara att IP-adresser kan ändras. Om IP-intervall krävs för brand Väggs konfigurationen ska **AzureCloud** -tjänst tag gen användas för att ge åtkomst till alla Azure-tjänster. Inaktivera inte säkerhetsövervakning eller inspektion av dessa URL: er, men tillåt dem som andra Internet trafik.

| Domänmiljö | Nödvändiga Azure-tjänsteslutpunkter |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gästkonfiguration|
|*-agentservice-prod-1.azure-automation.net|Gästkonfiguration|
|*. his.hybridcompute.azure-automation.net|Hybrid identitets tjänst|

### <a name="installation-network-requirements"></a>Installations nätverks krav

Ladda ned [Azure Connected Machine agent-paketet](https://aka.ms/AzureConnectedMachineAgent) från våra officiella distributions servrar. de här platserna måste vara tillgängliga från din miljö. Du kan välja att ladda ned paketet till en fil resurs och installera agenten därifrån. I det här fallet kan onboarding-skriptet som genereras från Azure Portal behöva ändras.

Windows:

* `aka.ms`
* `download.microsoft.com`

Linux:

* `aka.ms`
* `packages.microsoft.com`

Se avsnittet [Proxy Server-konfiguration](quickstart-onboard-powershell.md#proxy-server-configuration)för information om hur du konfigurerar agenten för att använda proxyservern.

## <a name="register-the-required-resource-providers"></a>Registrera nödvändiga resurs leverantörer

För att kunna använda Azure-bågen för servrar måste du registrera nödvändiga resurs leverantörer.

* **Microsoft.HybridCompute**
* **Microsoft. GuestConfiguration**

Du kan registrera resurs leverantörerna med följande kommandon:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Du kan också registrera resurs leverantörer med hjälp av portalen genom att följa stegen under [Azure Portal](../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

## <a name="machine-changes-after-installing-the-agent"></a>Dator ändringar efter installation av agenten

Om du har en lösning för ändrings spårning distribuerad i din miljö kan du använda listan nedan för att spåra, identifiera och tillåta de ändringar som gjorts av installations paketet för **Azure Connected Machine agent (AzCMAgent)** .

När du har installerat agenten ser du följande ändringar som gjorts på dina servrar.

### <a name="windows"></a>Windows

Tjänster installerade:

* `Himds` – tjänsten **Azure Connected Machine agent** .
* `Dscservice` eller `gcd` – **gäst konfigurations** tjänsten.

Filer som lagts till på servern:

* `%ProgramFiles%\AzureConnectedMachineAgent\*.*` plats för **Azure Connected Machine agent** -filer.
* `%ProgramData%\GuestConfig\*.*` - **gäst konfigurations** loggar.

Register nyckel platser:

* `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure Connected Machine Agent`-register nycklar för **Azure Connected Machine agent**.

### <a name="linux"></a>Linux

Tjänster installerade:

* `Himdsd` – tjänsten **Azure Connected Machine agent** .
* `dscd` eller `gcd` – **gäst konfigurations** tjänsten.

Filer som lagts till på servern:

* `/var/opt/azcmagent/**` plats för **Azure Connected Machine agent** -filer.
* `/var/lib/GuestConfig/**` - **gäst konfigurations** loggar.

## <a name="supported-scenarios"></a>Scenarier som stöds

När du har registrerat en nod kan du börja hantera dina noder med andra Azure-tjänster.

I offentlig för hands version stöds följande scenarier för **anslutna datorer**.

## <a name="guest-configuration"></a>Gästkonfiguration

När du har anslutit datorn till Azure kan du tilldela Azure-principer till **anslutna datorer** med samma erfarenhet som princip tilldelning till virtuella Azure-datorer.

Mer information finns i [förstå Azure policys gäst konfiguration](../../governance/policy/concepts/guest-configuration.md).

Gäst konfigurations agentens loggar för en **ansluten dator** finns på följande platser:

* Windows – `%ProgramFiles%\AzureConnectedMachineAgent\logs\dsc.log`
* Linux:-`/opt/logs/dsc.log`

## <a name="log-analytics"></a>Log Analytics

Loggdata som samlas in av [Microsoft Monitoring Agent (MMA)](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview) och lagras på Log Analytics-arbetsyta innehåller nu egenskaper som är speciella för datorn, till exempel **ResourceID**, som kan användas för resursens koncentriska logg åtkomst.

- Datorer som redan har MMA-agenten installerad kommer att ha **Azure Arc** -funktioner aktiverade via uppdaterade hanterings paket.
- [MMA-agentens version 10.20.18011 eller senare](https://docs.microsoft.com/azure/virtual-machines/extensions/oms-windows#agent-and-vm-extension-version) krävs för integrering av Azure Arc för servrar.
- När du frågar efter loggdata i [Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/log-query-overview)kommer det returnerade dataschemat att innehålla hybrid- **ResourceId** i formuläret `/subscriptions/<SubscriptionId/resourceGroups/<ResourceGroup>/providers/Microsoft.HybridCompute/machines/<MachineName>`.

Mer information finns i [Kom igång med Log Analytics i Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

<!-- MMA agent version 10.20.18011 and later -->

## <a name="next-steps"></a>Nästa steg

Det finns två metoder för att ansluta datorer med Azure-båge för servrar.

* **Interaktivt** – Följ snabb starten för [portalen](quickstart-onboard-portal.md) om du vill generera ett skript från portalen och köra det på datorn. Det här är det bästa alternativet om du ansluter en dator i taget.
* **I skala** – Följ snabb starten för [PowerShell](quickstart-onboard-powershell.md) för att skapa ett huvud namn för tjänsten för att ansluta datorer icke-interaktivt.
