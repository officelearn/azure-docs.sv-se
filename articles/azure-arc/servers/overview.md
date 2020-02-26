---
title: Översikt över Azure Arc for Servers (för hands version)
description: Lär dig hur du använder Azure Arc för servrar för att hantera datorer som ligger utanför Azure, som om det är en Azure-resurs.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: Azure Automation, DSC, PowerShell, önskad tillstånds konfiguration, uppdaterings hantering, ändrings spårning, inventering, Runbooks, python, grafisk, hybrid
ms.date: 02/24/2020
ms.topic: overview
ms.openlocfilehash: 57b44db9c1bb9a607ad8478b7208df40441020c2
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77586248"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Vad är Azure-båge för servrar (förhands granskning)

Med Azure Arc för servrar (för hands version) kan du hantera dina Windows-och Linux-datorer utanför Azure på företagets nätverk eller annan moln leverantör, på samma sätt som du hanterar virtuella datorer i Azure. När en hybrid dator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, hanteras som en del av en resurs grupp i en prenumeration och fördelar med Azures standard konstruktioner, till exempel Azure Policy och att använda taggar.

För att kunna leverera den här upplevelsen med dina hybrid datorer utanför Azure måste den Azure-anslutna dator agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inga andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics agent för Windows och Linux krävs om du vill övervaka operativ system och arbets belastningar som körs på datorn proaktivt, hantera den med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Den här för hands versionen är avsedd i utvärderings syfte och vi rekommenderar att du inte hanterar kritiska produktions datorer.
>

## <a name="supported-scenarios"></a>Scenarier som stöds

Azure-båge för servrar (för hands version) stöder följande scenarier med anslutna datorer:

- Tilldela [Azure policy gäst konfigurationer](../../governance/policy/concepts/guest-configuration.md) med samma erfarenhet som princip tilldelning för virtuella Azure-datorer.
- Loggdata som samlas in av Log Analytics-agenten och lagras på arbets ytan Log Analytics. datorn är registrerad med nu innehåller egenskaper som är speciella för datorn, t. ex. resurs-ID, som kan användas för att ge stöd åt [resurs kontexts](../../azure-monitor/platform/design-logs-deployment.md#access-mode) loggar.

## <a name="supported-regions"></a>Regioner som stöds

Med Azure Arc for Servers (för hands version) stöds endast vissa regioner:

- WestUS2
- Västeuropa
- WestAsia

I de flesta fall ska den plats som du väljer när du skapar installations skriptet vara den Azure-region som är geografiskt närmast din dators plats. Data i vila lagras i den region i Azure som innehåller den region som du anger, vilket även kan påverka ditt val av region om du har data placering krav. Om den Azure-region som datorn är ansluten till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanterings åtgärder som använder Azure kan inte slutföras. För återhämtning i händelse av ett regionalt avbrott, om du har flera platser som tillhandahåller en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

## <a name="prerequisites"></a>Förutsättningar

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande versioner av operativ systemet Windows och Linux stöds officiellt för den Azure-anslutna dator agenten: 

- Windows Server 2012 R2 och högre (inklusive Windows Server Core)
- Ubuntu 16,04 och 18,04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 7

>[!NOTE]
>Den här för hands versionen av den anslutna dator agenten för Windows stöder endast Windows Server som kon figurer ATS för att använda det engelska språket.
>

### <a name="required-permissions"></a>Nödvändiga behörigheter

- För att publicera datorer är du medlem i rollen **Azure Connected Machine onboarding** .

- Om du vill läsa, ändra, återställa och ta bort en dator är du medlem i rollen **resurs administratör för Azure Connected Machine** . 

### <a name="azure-subscription-and-service-limits"></a>Prenumerations-och tjänst begränsningar i Azure

Innan du konfigurerar dina datorer med Azure Arc for Servers (för hands version) bör du granska gränsen för Azure Resource Manager [prenumerations gränser](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) och [resurs gruppen](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) för att planera för antalet datorer som ska anslutas.

## <a name="tls-12-protocol"></a>TLS 1.2-protokollet

För att säkerställa säkerheten för data som överförs till Azure rekommenderar vi starkt att du konfigurerar datorn att använda Transport Layer Security (TLS) 1,2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har befunnits vara sårbara och även om de fortfarande arbetar för att tillåta bakåtkompatibilitet, rekommenderas de **inte**. 

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [openssl](https://www.openssl.org) för TLS 1,2-stöd. | Kontrol lera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows Server 2012 R2 och senare | Stöds och aktiverat som standard. | För att bekräfta att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Nätverks konfiguration

Den anslutna dator agenten för Linux och Windows kommunicerar på ett säkert sätt till Azure-bågen via TCP-port 443. Om datorn ansluter via en brand vägg eller proxyserver för att kommunicera via Internet kan du läsa igenom kraven nedan för att förstå kraven på nätverks konfigurationen.

Om den utgående anslutningen begränsas av din brand vägg eller proxyserver kontrollerar du att URL: erna i listan nedan inte är blockerade. Om du bara tillåter de IP-adressintervall eller domän namn som krävs för att agenten ska kunna kommunicera med tjänsten, måste du också tillåta åtkomst till följande service märken och URL: er.

Service märken:

- AzureActiveDirectory
- AzureTrafficManager

Er

| Agentresurs | Beskrivning |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gästkonfiguration|
|*-agentservice-prod-1.azure-automation.net|Gästkonfiguration|
|*. his.hybridcompute.azure-automation.net|Hybrid identitets tjänst|

En lista över IP-adresser för varje service tag/region finns i JSON-filen – [Azure IP-intervall och service märken – offentligt moln](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publicerar veckovis uppdateringar som innehåller varje Azure-tjänst och de IP-intervall som används. Mer information finns i [service tag](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

URL: erna i föregående tabell krävs utöver informationen om tjänst Tagns IP-adressintervall eftersom majoriteten av tjänsterna för närvarande inte har en registrering av service tag. Därför kan IP-adresserna ändras. Om det krävs IP-adressintervall för brand Väggs konfigurationen ska **AzureCloud** -tjänst tag gen användas för att ge åtkomst till alla Azure-tjänster. Inaktivera inte säkerhetsövervakning eller granskning av dessa URL: er, Tillåt dem som andra Internet trafik.

### <a name="register-azure-resource-providers"></a>Registrera Azure-resurs leverantörer

Azure båg for-servrar (för hands version) är beroende av följande Azure-resurs-providers i din prenumeration för att kunna använda den här tjänsten:

- **Microsoft. HybridCompute**
- **Microsoft. GuestConfiguration**

Om de inte är registrerade kan du registrera dem med hjälp av följande kommandon:

Azure PowerShell:

```azurepowershell-interactive
Login-AzAccount
Set-AzContext -SubscriptionId [subscription you want to onboard]
Register-AzResourceProvider -ProviderNamespace Microsoft.HybridCompute
Register-AzResourceProvider -ProviderNamespace Microsoft.GuestConfiguration
```

Azure CLI:

```azurecli-interactive
az account set --subscription "{Your Subscription Name}"
az provider register --namespace 'Microsoft.HybridCompute'
az provider register --namespace 'Microsoft.GuestConfiguration'
```

Du kan också registrera resurs leverantörerna i Azure Portal genom att följa stegen under [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Ansluten dator agent

Du kan ladda ned Azure Connected Machine agent-paketet för Windows och Linux från de platser som anges nedan.

- [Windows agent Windows Installer-paketet](https://aka.ms/AzureConnectedMachineAgent) från Microsoft Download Center.
- Linux Agent-paketet distribueras från Microsofts [paket lagrings plats](https://packages.microsoft.com/) med det önskade paket formatet för distributionen (. RPM eller. DEB).

>[!NOTE]
>Under för hands versionen har endast ett paket släppts, vilket är lämpligt för Ubuntu 16,04 eller 18,04.

Azure Connected Machine agent för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina behov. För Windows kan agent uppdateringen automatiskt utföras med hjälp av Windows Update och för Ubuntu med hjälp av kommando rads verktyget [apt](https://help.ubuntu.com/lts/serverguide/apt.html) .

### <a name="agent-status"></a>Agent status

Den anslutna dator agenten skickar ett vanligt pulsslags meddelande till tjänsten var 5: e minut. Om det inte har tagits emot i 15 minuter anses datorn vara offline och statusen kommer automatiskt att ändras till **frånkopplad** i portalen. När du tar emot ett efterföljande pulsslags meddelande från den anslutna dator agenten kommer dess status automatiskt att ändras till **ansluten**.

## <a name="install-and-configure-agent"></a>Installera och konfigurera agenten

Att ansluta datorer i din hybrid miljö direkt med Azure kan utföras med hjälp av olika metoder beroende på dina behov. I följande tabell beskrivs varje metod för att avgöra vilken som fungerar bäst för din organisation.

| Metod | Beskrivning |
|--------|-------------|
| Interaktivt | Installera agenten manuellt på ett enda eller litet antal datorer enligt stegen i [ansluta datorer från Azure Portal](onboard-portal.md).<br> Från Azure Portal kan du generera ett skript och köra det på datorn för att automatisera installations-och konfigurations stegen för agenten.|
| I skala | Installera och konfigurera agenten för flera datorer efter att [ansluta datorerna med ett huvud namn för tjänsten](onboard-service-principal.md).<br> Den här metoden skapar ett huvud namn för tjänsten för att ansluta datorer icke-interaktivt.|

## <a name="next-steps"></a>Nästa steg

- Om du vill börja utvärdera Azure-bågen för servrar (för hands version) följer du artikeln [Connect hybrid Machines to Azure från Azure Portal](onboard-portal.md). 