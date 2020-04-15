---
title: Översikt över Azure Arc för servrar (förhandsversion)
description: Lär dig hur du använder Azure Arc för servrar för att hantera datorer som finns utanför Azure som om det är en Azure-resurs.
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-servers
author: mgoedtel
ms.author: magoedte
keywords: azure automation, DSC, powershell, önskad tillståndskonfiguration, uppdateringshantering, ändringsspårning, lager, runbooks, python, grafisk, hybrid
ms.date: 03/24/2020
ms.topic: overview
ms.openlocfilehash: 5fa39028f1041a063bab295adabf8145a8b46ae4
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81308775"
---
# <a name="what-is-azure-arc-for-servers-preview"></a>Vad är Azure Arc för servrar (förhandsversion)

Med Azure Arc för servrar (förhandsversion) kan du hantera dina Windows- och Linux-datorer som finns utanför Azure i ditt företagsnätverk eller annan molnleverantör, på samma sätt som du hanterar inbyggda virtuella Azure-datorer. När en hybriddator är ansluten till Azure blir den en ansluten dator och behandlas som en resurs i Azure. Varje ansluten dator har ett resurs-ID, hanteras som en del av en resursgrupp i en prenumeration och drar nytta av vanliga Azure-konstruktioner som Azure-princip och tillämpa taggar.

För att kunna leverera den här upplevelsen med dina hybriddatorer som finns utanför Azure måste Azure Connected Machine-agenten installeras på varje dator som du planerar att ansluta till Azure. Den här agenten levererar inga andra funktioner och ersätter inte Azure [Log Analytics-agenten](../../azure-monitor/platform/log-analytics-agent.md). Log Analytics-agenten för Windows och Linux krävs när du proaktivt vill övervaka operativsystemet och arbetsbelastningar som körs på datorn, hantera det med hjälp av Automation-runbooks eller lösningar som Uppdateringshantering eller använda andra Azure-tjänster som [Azure Security Center](../../security-center/security-center-intro.md).

>[!NOTE]
>Den här förhandsversionen är avsedd för utvärderingsändamål och vi rekommenderar att du inte hanterar kritiska produktionsdatorer.
>

## <a name="supported-scenarios"></a>Scenarier som stöds

Azure Arc för servrar (förhandsversion) stöder följande scenarier med anslutna datorer:

- Tilldela [Azure Policy-gästkonfigurationer](../../governance/policy/concepts/guest-configuration.md) med samma upplevelse som principtilldelning för virtuella Azure-datorer.
- Loggdata som samlas in av Log Analytics-agenten och lagras på log Analytics-arbetsytan som datorn är registrerad med innehåller nu egenskaper som är specifika för datorn, till exempel Resurs-ID, som kan användas för att stödja [resurskontextloggåtkomst.](../../azure-monitor/platform/design-logs-deployment.md#access-mode)

## <a name="supported-regions"></a>Regioner som stöds

Med Azure Arc för servrar (förhandsversion) stöds bara vissa regioner:

- WestUS2 (På andra sätt)
- Västeuropa
- WestAsia (på ett år)

I de flesta fall bör den plats du väljer när du skapar installationsskriptet vara den Azure-region som ligger geografiskt närmast datorns plats. Data i vila lagras inom Azure-geografin som innehåller den region du anger, vilket också kan påverka ditt val av region om du har krav på dataplacering. Om Azure-regionen som datorn är ansluten till påverkas av ett avbrott påverkas inte den anslutna datorn, men hanteringsåtgärder som använder Azure kanske inte kan slutföras. För återhämtning i händelse av ett regionalt avbrott, om du har flera platser som tillhandahåller en geografiskt redundant tjänst, är det bäst att ansluta datorerna på varje plats till en annan Azure-region.

## <a name="prerequisites"></a>Krav

### <a name="supported-operating-systems"></a>Operativsystem som stöds

Följande versioner av Operativsystemet Windows och Linux stöds officiellt för Azure Connected Machine-agenten: 

- Windows Server 2012 R2 och senare (inklusive Windows Server Core)
- Ubuntu 16,04 och 18,04
- CentOS Linux 7
- SUSE Linux Enterprise Server (SLES) 15
- Red Hat Enterprise Linux (RHEL) 7
- Amazon Linux 2

>[!NOTE]
>Den här förhandsversionen av Agenten ansluten dator för Windows stöder endast Windows Server som konfigurerats för att använda det engelska språket.
>

### <a name="required-permissions"></a>Nödvändiga behörigheter

- Till inbyggda datorer är du medlem i azure **connected machine onboarding-rollen.**

- Om du vill läsa, ändra, gå in på den igen och ta bort en dator är du medlem i azure **connected machine resource administrator-rollen.** 

### <a name="azure-subscription-and-service-limits"></a>Azure-prenumerations- och tjänstgränser

Innan du konfigurerar dina datorer med Azure Arc för servrar (förhandsversion) bör du granska [prenumerationsgränserna](../../azure-resource-manager/management/azure-subscription-service-limits.md#subscription-limits) för Azure Resource Manager och [resursgruppsgränser](../../azure-resource-manager/management/azure-subscription-service-limits.md#resource-group-limits) för att planera för antalet datorer som ska anslutas.

## <a name="tls-12-protocol"></a>TLS 1.2-protokoll

För att säkerställa säkerheten för data under överföring till Azure rekommenderar vi starkt att du konfigurerar datorn för att använda Transport Layer Security (TLS) 1.2. Äldre versioner av TLS/Secure Sockets Layer (SSL) har visat sig vara sårbara och medan de fortfarande arbetar för att tillåta bakåtkompatibilitet rekommenderas de **inte**. 

|Plattform/språk | Support | Mer information |
| --- | --- | --- |
|Linux | Linux-distributioner tenderar att förlita sig på [OpenSSL](https://www.openssl.org) för TLS 1.2-stöd. | Kontrollera [openssl-ändringsloggen](https://www.openssl.org/news/changelog.html) för att bekräfta att din version av OpenSSL stöds.|
| Windows Server 2012 R2 och senare | Stöds och aktiveras som standard. | Så här bekräftar du att du fortfarande använder [standardinställningarna](https://docs.microsoft.com/windows-server/security/tls/tls-registry-settings).|

### <a name="networking-configuration"></a>Konfiguration av nätverk

Connected Machine-agenten för Linux och Windows kommunicerar utgående säkert till Azure Arc via TCP-port 443. Om datorn ansluter via en brandvägg eller proxyserver för att kommunicera via Internet granskar du kraven nedan för att förstå kraven i nätverkskonfigurationen.

Om utgående anslutning begränsas av brandväggen eller proxyservern kontrollerar du att webbadresserna som anges nedan inte är blockerade. Om du bara tillåter de IP-intervall eller domännamn som krävs för att agenten ska kunna kommunicera med tjänsten, måste du också tillåta åtkomst till följande tjänsttaggar och webbadresser.

Tjänsttaggar:

- AzureActiveDirectory
- AzureTrafficManager

Webbadresser:

| Agentresurs | Beskrivning |
|---------|---------|
|management.azure.com|Azure Resource Manager|
|login.windows.net|Azure Active Directory|
|dc.services.visualstudio.com|Application Insights|
|agentserviceapi.azure-automation.net|Gästkonfiguration|
|*-agentservice-prod-1.azure-automation.net|Gästkonfiguration|
|*.his.hybridcompute.azure-automation.net|Hybrid identitetstjänst|

En lista över IP-adresser för varje tjänsttag/region finns i JSON-filen – [Azure IP Ranges and Service Tags – Public Cloud](https://www.microsoft.com/download/details.aspx?id=56519). Microsoft publicerar veckouppdateringar som innehåller varje Azure-tjänst och de IP-intervall som används. Mer information finns i [Service-taggar](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Webbadresserna i den föregående tabellen krävs utöver ip-adressintervallinformationen för Service Tag, eftersom de flesta tjänster för närvarande inte har någon Service Tag-registrering. Ip-adresserna kan därför komma att ändras. Om IP-adressintervall krävs för brandväggskonfigurationen bör **AzureCloud-servicetag** användas för att tillåta åtkomst till alla Azure-tjänster. Inaktivera inte säkerhetsövervakning eller inspektion av dessa webbadresser, tillåt dem på samma sätt som annan Internettrafik.

### <a name="register-azure-resource-providers"></a>Registrera Azure-resursleverantörer

Azure Arc för servrar (förhandsversion) beror på följande Azure-resursleverantörer i din prenumeration för att kunna använda den här tjänsten:

- **Microsoft.HybridCompute**
- **Microsoft.GuestConfiguration**

Om de inte är registrerade kan du registrera dem med följande kommandon:

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

Du kan också registrera resursleverantörerna i Azure-portalen genom att följa stegen under [Azure Portal](../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

## <a name="connected-machine-agent"></a>Ansluten maskinagent

Du kan hämta Agentpaketet för Azure Connected Machine för Windows och Linux från de platser som anges nedan.

- [Windows agent Windows Installer-paketet](https://aka.ms/AzureConnectedMachineAgent) från Microsoft Download Center.
- Linux-agentpaketet distribueras från Microsofts [paketarkiv](https://packages.microsoft.com/) med det önskade paketformatet för distributionen (. RPM eller . deb).

>[!NOTE]
>Under den här förhandsversionen har endast ett paket släppts, som är lämpligt för Ubuntu 16.04 eller 18.04.

Azure Connected Machine-agenten för Windows och Linux kan uppgraderas till den senaste versionen manuellt eller automatiskt beroende på dina behov. Mer information finns [här](manage-agent.md).

### <a name="agent-status"></a>Agentstatus

Den anslutna datorn agent skickar ett regelbundet pulsslag meddelande till tjänsten var 5 minuter. Om tjänsten slutar ta emot dessa pulsslagsmeddelanden från en dator betraktas den datorn som offline och statusen ändras automatiskt till **Frånkopplad** i portalen inom 15 till 30 minuter. När du tar emot ett efterföljande pulsslagsmeddelande från agenten ansluten dator ändras dess status automatiskt till **Ansluten**.

## <a name="install-and-configure-agent"></a>Installera och konfigurera agent

Ansluta datorer i din hybridmiljö direkt med Azure kan utföras med olika metoder beroende på dina krav. I följande tabell beskrivs varje metod för att avgöra vilken som fungerar bäst för din organisation.

| Metod | Beskrivning |
|--------|-------------|
| Interaktivt | Installera agenten manuellt på ett eller flera datorer i [anslutningsstegen i Connect-datorer från Azure-portalen](onboard-portal.md).<br> Från Azure-portalen kan du generera ett skript och köra det på datorn för att automatisera installations- och konfigurationsstegen för agenten.|
| I stor skala | Installera och konfigurera agenten för flera datorer som följer [Connect-datorerna med hjälp av ett huvudnamn för tjänsten](onboard-service-principal.md).<br> Den här metoden skapar ett huvudnamn för tjänsten för att ansluta datorer som inte är interaktivt.|
| I stor skala | Installera och konfigurera agenten för flera datorer enligt metoden [Använda Windows PowerShell DSC](onboard-dsc.md).<br> Den här metoden använder ett tjänsthuvudnamn för att ansluta datorer som inte interaktivt ansluter till PowerShell DSC. |

## <a name="next-steps"></a>Nästa steg

- Om du vill börja utvärdera Azure Arc för servrar (förhandsversion) följer du artikeln [Anslut hybriddatorer till Azure från Azure-portalen](onboard-portal.md). 
