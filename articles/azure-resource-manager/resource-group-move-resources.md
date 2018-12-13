---
title: Flytta Azure-resurser till ny prenumeration eller resursgrupp grupp | Microsoft Docs
description: Använd Azure Resource Manager för att flytta resurser till en ny resursgrupp eller prenumeration.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
ms.assetid: ab7d42bd-8434-4026-a892-df4a97b60a9b
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/12/2018
ms.author: tomfitz
ms.openlocfilehash: 36cdb8a84c7ec7e05917be1144ae008f90cad03a
ms.sourcegitcommit: e37fa6e4eb6dbf8d60178c877d135a63ac449076
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2018
ms.locfileid: "53321143"
---
# <a name="move-resources-to-new-resource-group-or-subscription"></a>Flytta resurser till ny resursgrupp eller prenumeration

Den här artikeln visar hur du flyttar resurser till en ny prenumeration eller en ny resursgrupp i samma prenumeration. Du kan använda portalen, PowerShell, Azure CLI eller REST API för att flytta resursen. Flyttåtgärder i den här artikeln är tillgängliga för dig utan någon hjälp från Azure-supporten.

När du flyttar resurser, är både källgruppen och målgruppen låsta under åtgärden. Skriva och ta bort blockeras på resursgrupper tills flyttningen är klar. Låset innebär att du kan inte lägga till, uppdatera eller ta bort resurser i resursgrupper, men det innebär inte att resurserna som är låsta. Om du flyttar en SQL Server och dess databas till en ny resursgrupp, inträffar ett program som använder databasen utan avbrott. Det kan fortfarande läsa och skriva till databasen.

Du kan inte ändra platsen för resursen. En resurs flyttas bara flyttar det till en ny resursgrupp. Den nya resursgruppen kan ha en annan plats, men som ändra inte platsen för resursen.

> [!NOTE]
> Den här artikeln beskrivs hur du flyttar resurser i ett befintligt Azure-konto erbjudande. Om du vill ändra ditt Azure-konto erbjudande (t.ex uppgraderar från kostnadsfritt till betala per användning) måste konvertera din prenumeration.
> * Om du vill uppgradera en kostnadsfri utvärderingsversion, [uppgradera din kostnadsfria utvärderingsversion eller en Microsoft Imagine Azure-prenumeration till betala per användning](..//billing/billing-upgrade-azure-subscription.md).
> * Om du vill ändra en betala per användning-konto, se [ändra din betala per användning för Azure-prenumeration till ett annat erbjudande](../billing/billing-how-to-switch-azure-offer.md).
> * Om du inte kan konvertera prenumerationen, [skapa en Azure-supportbegäran](../azure-supportability/how-to-create-azure-support-request.md). Välj **prenumerationshantering** för typ av ärende.

## <a name="checklist-before-moving-resources"></a>Checklistan innan du flyttar resurser

Det finns några viktiga steg att göra innan du flyttar en resurs. Du kan undvika fel genom att verifiera dessa villkor.

1. Käll- och målprenumerationer måste finnas inom samma [Azure Active Directory-klient](../active-directory/develop/quickstart-create-new-tenant.md). Om du vill kontrollera att båda prenumerationerna har samma klient-ID, använder du Azure PowerShell eller Azure CLI.

  Använd för Azure PowerShell:

  ```azurepowershell-interactive
  (Get-AzureRmSubscription -SubscriptionName <your-source-subscription>).TenantId
  (Get-AzureRmSubscription -SubscriptionName <your-destination-subscription>).TenantId
  ```

  Om du använder Azure CLI använder du:

  ```azurecli-interactive
  az account show --subscription <your-source-subscription> --query tenantId
  az account show --subscription <your-destination-subscription> --query tenantId
  ```

  Om klient-ID: N för käll- och målprenumerationer inte är samma, kan du använda följande metoder för att stämma av klient-ID: N:

  * [Överföra ägarskap för en Azure-prenumeration till ett annat konto](../billing/billing-subscription-transfer.md)
  * [Så här associerar du eller lägger till en prenumeration i din Azure Active Directory](../active-directory/fundamentals/active-directory-how-subscriptions-associated-directory.md)

1. Målprenumerationen måste vara registrerad för resursprovidern för den resurs som flyttas. Om inte, du får ett felmeddelande om att den **prenumerationen har inte registrerats för en resurstyp**. Du kan se det här felet när du flyttar en resurs till en ny prenumeration, men att prenumerationen aldrig har använts med den resurstypen.

  Använd följande kommandon för att hämta registreringsstatus PowerShell:

  ```azurepowershell-interactive
  Set-AzureRmContext -Subscription <destination-subscription-name-or-id>
  Get-AzureRmResourceProvider -ListAvailable | Select-Object ProviderNamespace, RegistrationState
  ```

  Om du vill registrera en resursleverantör, använder du:

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Batch
  ```

  För Azure CLI, använder du följande kommandon för att hämta registreringsstatus:

  ```azurecli-interactive
  az account set -s <destination-subscription-name-or-id>
  az provider list --query "[].{Provider:namespace, Status:registrationState}" --out table
  ```

  Om du vill registrera en resursleverantör, använder du:

  ```azurecli-interactive
  az provider register --namespace Microsoft.Batch
  ```

1. Det konto som flyttar resurser måste ha minst följande behörigheter:

   * **Microsoft.Resources/subscriptions/resourceGroups/moveResources/action** på resursgrupp för källa.
   * **Microsoft.Resources/subscriptions/resourceGroups/write** på målresursgruppen.

1. Kontrollera prenumerationskvoter för den prenumeration som du flyttar resurser till innan du flyttar resurser. Om du flytta resurserna innebär prenumerationen kommer att överskrida gränsen, måste du granska om du kan begära en ökning av kvoten. En lista över begränsningar och hur du begär en ökning, se [Azure-prenumeration och tjänstbegränsningar, kvoter och begränsningar](../azure-subscription-service-limits.md).

1. När det är möjligt, flyttar break stora till separata flyttåtgärder. Resource Manager returnerar omedelbart ett fel när det finns fler än 800 resurser i en enda åtgärd. Men kan flyttar resurser som är mindre än 800 också misslyckas av tiden går ut.

1. Tjänsten måste göra det möjligt att flytta resurser. Att fastställa om flytten kommer att lyckas [verifiera din begäran om att flytta](#validate-move). I avsnitten nedan i den här artikeln som [tjänster kan flytta resurser](#services-that-can-be-moved) och vilka [tjänster inte kan flytta resurser](#services-that-cannot-be-moved).

## <a name="when-to-call-support"></a>När du ska kontakta supporten

Du kan flytta de flesta resurser via självbetjäning åtgärder som visas i den här artikeln. Använd självbetjäningsåtgärder till:

* Flytta resurshanterarens resurser.
* Flytta klassiska resurser enligt den [begränsningar för klassisk distribution](#classic-deployment-limitations).

Kontakta [stöder](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview) när du behöver:

* Flytta dina resurser till en ny Azure-konto (och Azure Active Directory-klient) och du behöver hjälp med anvisningarna i föregående avsnitt.
* Flytta klassiska resurser, men har problem med begränsningar.

## <a name="validate-move"></a>Verifiera flytt

Den [verifiera flyttåtgärden](/rest/api/resources/resources/validatemoveresources) kan du testa ditt move-scenario utan att faktiskt flytta resurserna. Använd den här åtgärden för att avgöra om flytten lyckas. Om du vill köra den här åtgärden, måste den:

* namn på resursgrupp för källa
* resurs-ID för målresursgruppen
* resurs-ID för varje resurs att flytta
* den [åtkomsttoken](/rest/api/azure/#acquire-an-access-token) för ditt konto

Skicka följande begäran:

```
POST https://management.azure.com/subscriptions/<subscription-id>/resourceGroups/<source-group>/validateMoveResources?api-version=2018-02-01
Authorization: Bearer <access-token>
Content-type: application/json
```

Med en brödtext i begäran:

```json
{
 "resources": ["<resource-id-1>", "<resource-id-2>"],
 "targetResourceGroup": "/subscriptions/<subscription-id>/resourceGroups/<target-group>"
}
```

Om begäran har formaterats korrekt, returnerar åtgärden:

```
Response Code: 202
cache-control: no-cache
pragma: no-cache
expires: -1
location: https://management.azure.com/subscriptions/<subscription-id>/operationresults/<operation-id>?api-version=2018-02-01
retry-after: 15
...
```

202 statuskoden anger verifieringsförfrågan togs emot, men den inte har fastställt om flyttåtgärden lyckas. Den `location` värdet innehåller en URL som används för att kontrollera statusen för långvarig åtgärd.  

Om du vill kontrollera statusen genom att skicka följande begäran:

```
GET <location-url>
Authorization: Bearer <access-token>
```

Medan åtgärden körs, fortsätta att ta emot 202 statuskoden. Vänta antalet sekunder som anges i den `retry-after` värdet innan du försöker igen. Om åtgärden för att flytta verifieras felmeddelandet 204 statuskoden. Om flytten valideringen misslyckas visas ett felmeddelande som:

```json
{"error":{"code":"ResourceMoveProviderValidationFailed","message":"<message>"...}}
```

## <a name="services-that-can-be-moved"></a>Tjänster som kan flyttas

Följande lista innehåller en allmän översikt över Azure-tjänster som kan flyttas till en ny resursgrupp och prenumeration. Läs mer detaljerat, [flytta åtgärden stöd för resurser](move-support-resources.md).

* Analysis Services
* API Management
* App Service-appar (webbappar) – Se [begränsningar för App Service](#app-service-limitations)
* App Service-certifikat – Se [begränsningar för App Service Certificate](#app-service-certificate-limitations)
* Application Insights
* Automation
* Azure Active Directory B2C
* Azure Cosmos DB
* Azure Database for MySQL
* Azure Database for PostgreSQL
* Azure DevOps - köper Azure DevOps-organisationer med icke-Microsoft-tillägg måste [Avbryt sina inköp](https://go.microsoft.com/fwlink/?linkid=871160) innan de kan flytta kontot mellan prenumerationer.
* Azure Maps
* Azure Relay
* Azure Stack - registreringar
* Batch
* BizTalk Services
* Robottjänst
* CDN
* Molntjänster – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* Cognitive Services
* Container Registry – ett behållarregister kan inte flyttas när geo-replikering är aktiverat.
* Content Moderator
* Cost Management
* Customer Insights
* Data Catalog
* Data Factory
* Data Lake Analytics
* Data Lake Store
* DNS
* Event Grid
* Event Hubs
* Ytterdörren
* HDInsight-kluster – Se [HDInsight begränsningar](#hdinsight-limitations)
* IoT Central
* IoT-hubbar
* Key Vault
* Belastningsutjämnare – Se [belastningsutjämnaren begränsningar](#lb-limitations)
* Log Analytics
* Logic Apps
* Maskininlärning – Machine Learning Studio-webbtjänster kan flyttas till en resursgrupp i samma prenumeration, men inte en annan prenumeration. Andra Machine Learning-resurser kan flyttas mellan prenumerationer.
* Managed Disks – Se [begränsningar för virtuella datorer för begränsningar](#virtual-machines-limitations)
* Hanterad identitet - användartilldelade
* Media Services
* Notification Hubs
* Operational Insights
* Operations Management
* Portalen instrumentpaneler
* Powerbi – både Power BI Embedded och Power BI-Arbetsytesamling
* Offentliga IP - Se [offentliga IP-begränsningar](#pip-limitations)
* Recovery Services-valv – du måste vara registrerade i en privat förhandsgranskning. Se [begränsningar för Recovery Services](#recovery-services-limitations).
* Azure Cache för Redis - om Azure Cache för Redis-instans som är konfigurerad med ett virtuellt nätverk, instansen kan inte flyttas till en annan prenumeration. Se [virtuella nätverk begränsningar](#virtual-networks-limitations).
* Scheduler
* Search
* Service Bus
* Service Fabric
* Service Fabric Mesh
* SignalR Service
* Storage - konton i olika regioner kan inte flyttas på samma gång. Använd i stället separata åtgärder för varje region.
* Storage (klassisk) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* Stream Analytics - tillstånd för Stream Analytics-jobb inte kan flyttas när du kör i.
* SQL Database-server - databas och server måste vara i samma resursgrupp. Om du flyttar en SQLServer, flyttas även alla dess databaser. Det här beteendet gäller för Azure SQL Database och Azure SQL Data Warehouse-databaser.
* Time Series Insights
* Traffic Manager
* Virtual Machines – för virtuella datorer med hanterade diskar, se [begränsningar för virtuella datorer](#virtual-machines-limitations)
* Virtuella datorer (klassiska) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)
* VM Scale Sets – Se [begränsningar för virtuella datorer](#virtual-machines-limitations)
* Virtuella nätverk - finns i [begränsningar för virtuella nätverk](#virtual-networks-limitations)
* VPN Gateway

## <a name="services-that-cannot-be-moved"></a>Tjänster som inte kan flyttas

Följande lista innehåller en allmän översikt över Azure-tjänster som inte kan flyttas till en ny resursgrupp och prenumeration. Läs mer detaljerat, [flytta åtgärden stöd för resurser](move-support-resources.md).

* AD DS
* Hybrid AD-tjänsten för hälsotillstånd
* Application Gateway
* Azure Database Migration
* Azure Databricks
* Azure Migrate
* Batch AI
* Certifikat - App Service-certifikat kan flyttas, men uppladdade certifikat har [begränsningar](#app-service-limitations).
* Container Instances
* Container Service
* Data Box
* Dev blanksteg
* Dynamics LCS
* Express Route
* Kubernetes Service
* Lab Services – flytta till ny resursgrupp i samma prenumeration har aktiverats, men flytta över prenumerationer har inte aktiverats.
* Belastningsutjämnare – Se [belastningsutjämnaren begränsningar](#lb-limitations)
* Managed Applications
* Microsoft Genomics
* NetApp
* Offentliga IP - Se [offentliga IP-begränsningar](#pip-limitations)
* SAP HANA på Azure
* Säkerhet
* Site Recovery
* StorSimple Device Manager
* Virtuella nätverk (klassisk) – Se [begränsningar för klassisk distribution](#classic-deployment-limitations)

## <a name="virtual-machines-limitations"></a>Begränsningar för virtuella datorer

Hanterade diskar stöds för flytt från och med den 24 September 2018. 

1. Registrera den här funktionen i käll-prenumeration.

  ```azurepowershell-interactive
  Register-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature register --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. Förfrågan om funktionsregistrering returnerar först statusen `Registering`. Du kan kontrollera den aktuella statusen med:

  ```azurepowershell-interactive
  Get-AzureRmProviderFeature -FeatureName ManagedResourcesMove -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az feature show --namespace Microsoft.Compute --name ManagedResourcesMove
  ```

1. Vänta några minuter för statusen att ändra till `Registered`.

1. När funktionen har registrerats kan du registrera den `Microsoft.Compute` resursprovidern. Utför det här steget, även om resursprovidern tidigare har registrerats.

  ```azurepowershell-interactive
  Register-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute
  ```

  ```azurecli-interactive
  az provider register --namespace Microsoft.Compute
  ```

Det här stödet innebär att du kan också flytta:

* Virtuella datorer med hanterade diskar
* Hanterade avbildningar
* Hanterade ögonblicksbilder
* Tillgänglighetsuppsättningar med virtuella datorer med hanterade diskar

Här följer begränsningarna som ännu inte stöds:

* Virtuella datorer med certifikat som lagras i Key Vault kan flyttas till en ny resursgrupp i samma prenumeration, men inte mellan prenumerationer.
* Om den virtuella datorn har konfigurerats för säkerhetskopiering, se [begränsningar för Recovery Services](#recovery-services-limitations).
* Virtual Machine Scale Sets med Standard-SKU-belastningsutjämnare eller SKU offentlig IP kan inte flyttas
* Virtuella datorer som skapats från Marketplace-resurser med anslutna-planer kan inte flyttas mellan resursgrupper eller prenumerationer. Avetablera den virtuella datorn i den aktuella prenumerationen och distribuera igen i den nya prenumerationen.

## <a name="virtual-networks-limitations"></a>Begränsningar för virtuellt nätverk

När du flyttar ett virtuellt nätverk, måste du även flytta beroende resurser. För VPN-gatewayer måste du flytta IP-adresser, virtuella nätverksgatewayer och alla associerade anslutningsresurser. Lokala nätverksgatewayer kan finnas i en annan resursgrupp.

Om du vill flytta en peer-kopplade virtuella nätverket måste du först inaktivera virtuell nätverkspeering. Du kan flytta det virtuella nätverket när inaktiverat. Återaktivera virtuell nätverkspeering efter överflyttningen.

Du kan inte flytta ett virtuellt nätverk till en annan prenumeration om det virtuella nätverket innehåller ett undernät med resursnavigeringslänkar. Om Azure Cache för Redis-resursen har distribuerats i ett undernät har till exempel en resursnavigeringslänken i det undernätet.

## <a name="app-service-limitations"></a>Begränsningar för App Service

Begränsningar för att flytta App Service-resurser variera beroende på om du flyttar resurser inom en prenumeration eller till en ny prenumeration. Om din webbapp använder ett App Service Certificate kan se [begränsningar för App Service Certificate](#app-service-certificate-limitations)

### <a name="moving-within-the-same-subscription"></a>Flytta inom samma prenumeration

När du flyttar en Webbapp _inom samma prenumeration_, du kan inte flytta från tredje part SSL-certifikat. Men du kan flytta en Webbapp till den nya resursgruppen utan att flytta dess certifikat från tredje part och appens SSL fortfarande fungerar.

Om du vill flytta SSL-certifikat med Webbappen gör du följande:

1. Ta bort certifikatet från tredje part från Web App, men behålla en kopia av certifikatet
2. Flytta Webbappen.
3. Ladda upp certifikatet från tredje part till flyttade Webbappen.

### <a name="moving-across-subscriptions"></a>Flytta mellan prenumerationer

När du flyttar en Webbapp _mellan prenumerationer_, gäller följande begränsningar:

- Målresursgruppen får inte ha några befintliga App Service-resurser. App Service-resurser är:
    - Web Apps
    - App Service-planer
    - Överförda eller importerade SSL-certifikat
    - Apptjänstmiljöer
- Alla App Service-resurser i resursgruppen måste flyttas tillsammans.
- App Service-resurser kan bara flyttas från resursgruppen där de skapades. Om en App Service-resursen är inte längre i dess ursprungliga resursgruppen, den måste flyttas tillbaka till den ursprungliga resursgruppen först och sedan de kan flyttas mellan prenumerationer.

## <a name="app-service-certificate-limitations"></a>Begränsningar för App Service Certificate

Du kan flytta din App Service-certifikat till en ny resursgrupp eller prenumeration. Om din App Service-certifikat är bundet till en webbapp, måste du vidta vissa åtgärder innan du flyttar resurser till en ny prenumeration. Ta bort SSL-bindning och privata certifikat från webbapp innan du flyttar resurser. App Service Certificate behöver inte tas bort, bara privata certifikat i webbapp.

## <a name="classic-deployment-limitations"></a>Begränsningar för klassisk distribution

Alternativ för att flytta resurser som har distribuerats via den klassiska modellen variera beroende på om du flyttar resurser inom en prenumeration eller till en ny prenumeration.

### <a name="same-subscription"></a>Samma prenumeration

När du flyttar resurser från en resursgrupp till en annan resursgrupp i samma prenumeration, gäller följande begränsningar:

* Virtuella nätverk (klassisk) kan inte flyttas.
* Virtuella datorer (klassiska) måste flyttas med Molntjänsten.
* Molntjänsten kan bara flyttas när flytten omfattar alla virtuella datorer.
* Endast en molntjänst kan flyttas åt gången.
* Endast en storage-konto (klassisk) kan flyttas åt gången.
* Storage-konto (klassisk) kan inte flyttas på samma gång med en virtuell dator eller en tjänst i molnet.

Flytta klassiska resurser till en ny resursgrupp i samma prenumeration genom att använda standard flyttåtgärder via den [portal](#use-portal), [Azure PowerShell](#use-powershell), [Azure CLI](#use-azure-cli), eller [REST-API](#use-rest-api). Du kan använda samma åtgärder som du använder för att flytta Resurshanterarens resurser.

### <a name="new-subscription"></a>Ny prenumeration

När du flyttar resurser till en ny prenumeration, gäller följande begränsningar:

* Alla klassiska resurser i prenumerationen måste flyttas på samma gång.
* Målprenumerationen får inte ha andra klassiska resurser.
* Flytten kan bara begäras via en separat REST-API för klassiska flyttar. Standardkommandon för Resource Manager-flytta fungerar inte när du flyttar klassiska resurser till en ny prenumeration.

Flytta klassiska resurser till en ny prenumeration genom att använda REST-åtgärder som är specifika för klassiska resurser. Om du vill använda REST, utför du följande steg:

1. Kontrollera om käll-prenumeration kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

  ```HTTP
  POST https://management.azure.com/subscriptions/{sourceSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     I begärandetexten, inkluderar du:

  ```json
  {
    "role": "source"
  }
  ```

     Svaret för valideringen har följande format:

  ```json
  {
    "status": "{status}",
    "reasons": [
      "reason1",
      "reason2"
    ]
  }
  ```

2. Kontrollera om målprenumerationen kan delta i en flytt mellan prenumerationer. Använd följande åtgärd:

  ```HTTP
  POST https://management.azure.com/subscriptions/{destinationSubscriptionId}/providers/Microsoft.ClassicCompute/validateSubscriptionMoveAvailability?api-version=2016-04-01
  ```

     I begärandetexten, inkluderar du:

  ```json
  {
    "role": "target"
  }
  ```

     Svaret är i samma format som datakälla prenumerationen verifieringen.
3. Om båda prenumerationerna passerar valideringen flytta alla klassiska resurser från en prenumeration till en annan prenumeration med följande åtgärd:

  ```HTTP
  POST https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.ClassicCompute/moveSubscriptionResources?api-version=2016-04-01
  ```

    I begärandetexten, inkluderar du:

  ```json
  {
    "target": "/subscriptions/{target-subscription-id}"
  }
  ```

Åtgärden kan ta flera minuter.

## <a name="recovery-services-limitations"></a>Recovery Services-begränsningar

Om du vill flytta ett Recovery Services-valv, måste du registrera i privat förhandsgranskning. Om du vill testa den genom att skriva till AskAzureBackupTeam@microsoft.com.

För närvarande kan du flytta ett Recovery Services-valv per region i taget. Du kan inte flytta valv som säkerhetskopiera Azure Files eller Azure File Sync SQL i IaaS-datorer. 

Om en virtuell dator inte flyttas med valvet, förblir aktuella återställningspunkterna för virtuell dator i valvet tills de upphör att gälla. Om den virtuella datorn flyttas med valvet eller inte, kan du återställa den virtuella datorn från historik för säkerhetskopiering i valvet.

Recovery Services-valv stöder inte över prenumerationer säkerhetskopieringar. Om du flyttar ett valv med säkerhetskopierade data för virtuell dator mellan prenumerationer måste du flytta dina virtuella datorer till samma prenumeration och Använd samma målresursgruppen för att fortsätta säkerhetskopieringar.

Principer för säkerhetskopiering som definierats för valvet hålls när valvet har flyttats. Rapportering och övervakning måste ställas in igen för valvet efter flytten.

Flytta en virtuell dator till en ny prenumeration utan att flytta Recovery Services-valvet:

 1. Tillfälligt stoppa säkerhetskopiering
 2. Flytta de virtuella datorerna till den nya prenumerationen
 3. Skydda den igen under ett nytt valv i prenumerationen

Flytta är inte aktiverad för lagring, nätverk och beräkning av de resurser som används för att konfigurera haveriberedskap med Azure Site Recovery. Anta exempelvis att du har konfigurerat replikeringen av dina lokala datorer till ett lagringskonto (Storage1) och vill att den skyddade datorn för att få fram efter en redundansväxling till Azure som en virtuell dator (VM1) kopplade till ett virtuellt nätverk (Network1). Du kan inte flytta någon av dessa Azure-resurser – Storage1 VM1 och Network1 - mellan resursgrupper i samma prenumeration eller mellan prenumerationer.

## <a name="hdinsight-limitations"></a>HDInsight-begränsningar

Du kan flytta HDInsight-kluster till en ny prenumeration eller resursgrupp. Men kan inte du flytta mellan prenumerationer som nätverksresurser som är länkad till HDInsight-klustret (till exempel virtuella nätverk, nätverkskort eller belastningsutjämnare). Dessutom kan flytta du inte till en ny resursgrupp ett nätverkskort som är kopplad till en virtuell dator för klustret.

När du flyttar ett HDInsight-kluster till en ny prenumeration först flytta andra resurser (t.ex. storage-konto). Flytta sedan HDInsight-klustret ensamt.

## <a name="search-limitations"></a>Sök begränsningar

Du kan inte flytta flera Sök efter resurser i olika regioner på samma gång.
I sådana fall behöver du flytta dem separat.

## <a name="lb-limitations"></a> Load Balancer begränsningar

Grundläggande SKU-belastningsutjämnare kan flyttas.
Standard-SKU-belastningsutjämnare kan inte flyttas.

## <a name="pip-limitations"></a> Offentliga IP-begränsningar

Grundläggande SKU offentlig IP-adress kan flyttas.
Standard-SKU offentlig IP-adress kan inte flyttas.

## <a name="use-portal"></a>Använda portalen

Välj resursgruppen med dessa resurser för att flytta resurser, och välj sedan den **flytta** knappen.

![Flytta resurser](./media/resource-group-move-resources/select-move.png)

Välj om du flyttar resurser till en ny resursgrupp eller en ny prenumeration.

Välj resurser att flytta och målresursgruppen. Bekräfta att du behöver uppdatera skript för dessa resurser och välj **OK**. Om du har valt prenumerationen redigeringsikonen i föregående steg, måste du också välja målprenumerationen.

![Välj mål](./media/resource-group-move-resources/select-destination.png)

I **meddelanden**, du ser att flyttåtgärden körs.

![Visa flytta status](./media/resource-group-move-resources/show-status.png)

När den har slutförts meddelas du om resultatet.

![Visa flytta resultat](./media/resource-group-move-resources/show-result.png)

## <a name="use-powershell"></a>Använd PowerShell

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [Move-AzureRmResource](/powershell/module/azurerm.resources/move-azurermresource) kommando. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp.

```azurepowershell-interactive
$webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
$plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $webapp.ResourceId, $plan.ResourceId
```

Om du vill flytta till en ny prenumeration kan innehålla ett värde för den `DestinationSubscriptionId` parametern.

## <a name="use-azure-cli"></a>Använda Azure CLI

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att använda den [az resursflytt](/cli/azure/resource?view=azure-cli-latest#az-resource-move) kommando. Ange resurs-ID resurser att flytta. I följande exempel visas hur du flyttar flera resurser till en ny resursgrupp. I den `--ids` parameter, ange en blankstegsavgränsad lista med resurs-ID för att flytta.

```azurecli
webapp=$(az resource show -g OldRG -n ExampleSite --resource-type "Microsoft.Web/sites" --query id --output tsv)
plan=$(az resource show -g OldRG -n ExamplePlan --resource-type "Microsoft.Web/serverfarms" --query id --output tsv)
az resource move --destination-group newgroup --ids $webapp $plan
```

Om du vill flytta till en ny prenumeration, ange den `--destination-subscription-id` parametern.

## <a name="use-rest-api"></a>Använda REST-API

Flytta befintliga resurser till en annan resursgrupp eller prenumeration genom att köra:

```HTTP
POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version}
```

I begärandetexten anger du målresursgruppen och resurser för att flytta. Mer information om åtgärden för att flytta REST finns i [flytta resurser](/rest/api/resources/Resources/MoveResources).

## <a name="next-steps"></a>Nästa steg

* Läs om PowerShell-cmdletar för att hantera din prenumeration i [med hjälp av Azure PowerShell med Resource Manager](powershell-azure-resource-manager.md).
* Läs mer om Azure CLI-kommandon för att hantera din prenumeration, i [med hjälp av Azure CLI med resurshanteraren](xplat-cli-azure-resource-manager.md).
* Läs om portalen funktioner för att hantera din prenumeration i [hantera resurser med hjälp av Azure portal](resource-group-portal.md).
* Läs om hur du tillämpar en logisk struktur till resurser i [med taggar för att organisera dina resurser](resource-group-using-tags.md).
