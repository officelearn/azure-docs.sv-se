---
title: Viktig information för Azure Security Center
description: En beskrivning av vad som är nytt och ändrat i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/22/2020
ms.author: memildin
ms.openlocfilehash: 565282b2da5383c1d3471337f64588f97157a79c
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92328682"
---
# <a name="whats-new-in-azure-security-center"></a>Vad är nytt i Azure Security Center?

Security Center är i aktiv utveckling och tar emot förbättringar kontinuerligt. För att hålla dig uppdaterad med den senaste utvecklingen ger den här sidan information om nya funktioner, fel korrigeringar och föråldrade funktioner.

Den här sidan uppdateras ofta, så du kan gå tillbaka den ofta. 

> [!TIP]
> Om du söker efter objekt som är äldre än sex månader hittar du dem i [arkivet för vad som är nytt i Azure Security Center](release-notes-archive.md).


## <a name="october-2020"></a>Oktober 2020

- [Sårbarhets bedömning för datorer på plats och flera moln (för hands version)](#vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview)
- [Azure Firewall-rekommendation har lagts till (för hands version)](#azure-firewall-recommendation-added-preview)
- [Microsoft. Security/securityStatuses-tabellen har tagits bort från Azure Resource Graph (ARG)](#microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg)

### <a name="vulnerability-assessment-for-on-premise-and-multi-cloud-machines-preview"></a>Sårbarhets bedömning för datorer på plats och flera moln (för hands version)

[Azure Defender for servers](defender-for-servers-introduction.md)"Integrated sårbarhet Assessment Scanner (drivs av Qualys) genomsöker nu Azure Arc-aktiverade servrar.

När du har aktiverat Azure Arc på datorer som inte är Azure-datorer erbjuder Security Center att distribuera den integrerade sårbarhets skannern på dem – manuellt och i skala.

Med den här uppdateringen kan du utnyttja kraften hos **Azure Defender för servrar** för att konsolidera ditt sårbarhets hanterings program över alla dina Azure-och icke-Azure-tillgångar.

Huvud funktioner:

- Övervaka etablerings status för VA-skannern (sårbarhets bedömning) på Azure Arc-datorer
- Etablering av den integrerade VA-agenten för att ta bort oskyddade Windows-och Linux Azure Arc-datorer (manuellt och i skala)
- Ta emot och analysera identifierade sårbarheter från distribuerade agenter (manuellt och i skala)
- Enhetlig upplevelse för virtuella Azure-datorer och Azure Arc-datorer

[Lär dig mer om att distribuera den integrerade sårbarhets-skannern till dina hybrid datorer](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines).

[Läs mer om Azure Arc-aktiverade servrar](https://docs.microsoft.com/azure/azure-arc/servers/).


### <a name="azure-firewall-recommendation-added-preview"></a>Azure Firewall-rekommendation har lagts till (för hands version)

En ny rekommendation har lagts till för att skydda alla dina virtuella nätverk med Azure-brandväggen.

Rekommendationen, **virtuella nätverk bör skyddas av Azure Firewall** och du får hjälp att begränsa åtkomsten till dina virtuella nätverk och förhindra potentiella hot genom att använda Azure-brandväggen.

Läs mer om [Azure-brandväggen](https://azure.microsoft.com/services/azure-firewall/).


### <a name="microsoftsecuritysecuritystatuses-table-removed-from-azure-resource-graph-arg"></a>Microsoft. Security/securityStatuses-tabellen har tagits bort från Azure Resource Graph (ARG)

Azure Resource Graph är en tjänst i Azure som är utformad för att tillhandahålla effektiv resurs utforskning med möjlighet att fråga i skala över en specifik uppsättning prenumerationer så att du effektivt kan styra din miljö. 

För Azure Security Center kan du använda ARG och [KQL (Kusto Query Language)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) för att fråga efter en mängd säkerhets position data. Exempel:

- Till gångs lager använder (ARG)
- Vi har dokumenterat en exempel-ARG-fråga för att [identifiera konton utan Multi-Factor Authentication (MFA) aktiverat](security-center-identity-access.md#identify-accounts-without-multi-factor-authentication-mfa-enabled)

I ARG finns det data tabeller som du kan använda i dina frågor.

:::image type="content" source="./media/release-notes/azure-resource-graph-tables.png" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna&quot;:::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: &quot;/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet&quot;,
name: &quot;mico-rg-vnet&quot;,
type: &quot;Microsoft.Security/securityStatuses&quot;,
properties:  {
    policyAssessments: [
        {assessmentKey: &quot;e3deicce-f4dd-3b34-e496-8b5381bazd7e&quot;, category: &quot;Networking&quot;, policyName: &quot;Azure DDOS Protection Standard should be enabled&quot;,...},
        {assessmentKey: &quot;sefac66a-1ec5-b063-a824-eb28671dc527&quot;, category: &quot;Compute&quot;, policyName: &quot;&quot;,...}
    ],
    securitystateByCategory: [{category: &quot;Networking&quot;, securityState: &quot;None&quot; }, {category: &quot;Compute&quot;,...],
    name: &quot;GenericResourceHealthProperties&quot;,
    type: &quot;VirtualNetwork&quot;,
    securitystate: &quot;High"
}
```
Microsoft. Security/bedömningar innehåller en post för varje sådan princip utvärdering enligt följande:

```
{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft. Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/e3delcce-f4dd-3b34-e496-8b5381ba2d70",
name: "e3deicce-f4dd-3b34-e496-8b5381ba2d70",
properties:  {
    resourceDetails: {Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet"...},
    displayName: "Azure DDOS Protection Standard should be enabled",
    status: (code: "NotApplicable", cause: "VnetHasNOAppGateways", description: "There are no Application Gateway resources attached to this Virtual Network"...}
}

{
type: "Microsoft.Security/assessments",
id:  "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet/providers/Microsoft.Security/assessments/80fac66a-1ec5-be63-a824-eb28671dc527",
name: "8efac66a-1ec5-be63-a824-eb28671dc527",
properties: {
    resourceDetails: (Source: "Azure", Id: "/subscriptions/449bc1dd-3470-4804-ab56-2752595f01ab/resourcegroups/mico-rg/providers/microsoft.network/virtualnetworks/mico-rg-vnet"...),
    displayName: "Audit diagnostic setting",
    status:  {code: "Unhealthy"}
}
```

**Exempel på konvertering av en befintlig ARG-fråga med hjälp av securityStatuses för att nu använda bedömnings tabellen:**

Fråga som refererar till SecurityStatuses:

```kusto
SecurityResources 
| where type == 'microsoft.security/securitystatuses' and properties.type == 'virtualMachine'
| where name in ({vmnames}) 
| project name, resourceGroup, policyAssesments = properties.policyAssessments, resourceRegion = location, id, resourceDetails = properties.resourceDetails
```

Ersättnings fråga för bedömnings tabellen:

```kusto
securityresources
| where type == "microsoft.security/assessments" and id contains "virtualMachine"
| extend resourceName = extract(@"(?i)/([^/]*)/providers/Microsoft.Security/assessments", 1, id)
| extend source = tostring(properties.resourceDetails.Source)
| extend resourceId = trim(" ", tolower(tostring(case(source =~ "azure", properties.resourceDetails.Id,
source =~ "aws", properties.additionalData.AzureResourceId,
source =~ "gcp", properties.additionalData.AzureResourceId,
extract("^(.+)/providers/Microsoft.Security/assessments/.+$",1,id)))))
| extend resourceGroup = tolower(tostring(split(resourceId, "/")[4]))
| where resourceName in ({vmnames}) 
| project resourceName, resourceGroup, resourceRegion = location, id, resourceDetails = properties.additionalData
```

Läs mer på följande länkar:
- [Så här skapar du frågor med Azure Resource Graph Explorer](../governance/resource-graph/first-query-portal.md)
- [Kusto Query Language (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/)


## <a name="september-2020"></a>September 2020

Uppdateringar i september omfattar:
- [Security Center får ett nytt utseende!](#security-center-gets-a-new-look)
- [Azure Defender lanserad](#azure-defender-released)
- [Azure Defender för Key Vault är allmänt tillgänglig](#azure-defender-for-key-vault-is-generally-available)
- [Azure Defender för lagrings skydd för filer och ADLS Gen2 är allmänt tillgängligt](#azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available)
- [Till gångs inventerings verktyg är nu allmänt tillgängliga](#asset-inventory-tools-are-now-generally-available)
- [Inaktivera en särskilt sårbarhet som söker efter genomsökningar av behållar register och virtuella datorer](#disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines)
- [Undanta en resurs från en rekommendation](#exempt-a-resource-from-a-recommendation)
- [AWS-och GCP-anslutningar i Security Center ta en upplevelse i flera moln](#aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience)
- [Paket för Kubernetes-skydds rekommendation](#kubernetes-workload-protection-recommendation-bundle)
- [Rön om sårbarhets bedömning är nu tillgängliga i löpande export](#vulnerability-assessment-findings-are-now-available-in-continuous-export)
- [Förhindra felaktig säkerhet genom att tvinga rekommendationer när du skapar nya resurser](#prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources)
- [Bättre rekommendationer för nätverks säkerhets grupp](#network-security-group-recommendations-improved)
- [Föråldrad för hands version AKS-rekommendation "Pod Security Policies definieras i Kubernetes Services"](#deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services)
- [E-postaviseringar från Azure Security Center bättre](#email-notifications-from-azure-security-center-improved)
- [Säkra Poäng omfattar inte för hands versions rekommendationer](#secure-score-doesnt-include-preview-recommendations)
- [Rekommendationerna innehåller nu en allvarlighets grad och aktualitets intervallet](#recommendations-now-include-a-severity-indicator-and-the-freshness-interval)


### <a name="security-center-gets-a-new-look"></a>Security Center får ett nytt utseende!

Vi har publicerat ett uppdaterat användar gränssnitt för Security Center Portal sidor. De nya sidorna innehåller en ny översikts sida och instrument paneler för säkra poäng, till gångs lager och Azure Defender.

På sidan för den omdesignade översikten finns nu en panel för att komma åt säkra poäng-, inventarie-och Azure Defender-instrumentpaneler. Den har också en panel som länkar till instrument panelen för kontroll av efterlevnad.

Läs mer om [översikts sidan](overview-page.md).


### <a name="azure-defender-released"></a>Azure Defender lanserad

**Azure Defender** är CWPP (Cloud Protection Platform) som är integrerad i Security Center för avancerad, intelligent, skydd av dina Azure-och hybrid arbets belastningar. Den ersätter Security Center standard alternativ för pris nivå. 

När du aktiverar Azure Defender från avsnittet **priser och inställningar** i Azure Security Center, aktive ras följande Defender-planer samtidigt och ger omfattande försvars områden i din miljö:

- [Azure Defender för servrar](defender-for-servers-introduction.md)
- [Azure Defender för App Service](defender-for-app-service-introduction.md)
- [Azure Defender för Storage](defender-for-storage-introduction.md)
- [Azure Defender för SQL](defender-for-sql-introduction.md)
- [Azure Defender för Key Vault](defender-for-key-vault-introduction.md)
- [Azure Defender för Kubernetes](defender-for-kubernetes-introduction.md)
- [Azure Defender för containerregister](defender-for-container-registries-introduction.md)

Var och en av dessa planer beskrivs separat i Security Center-dokumentationen.

Med dess dedikerade instrument panel tillhandahåller Azure Defender säkerhets aviseringar och Avancerat skydd för virtuella datorer, SQL-databaser, behållare, webb program, nätverk och mycket annat.

[Läs mer om Azure Defender](azure-defender.md)

### <a name="azure-defender-for-key-vault-is-generally-available"></a>Azure Defender för Key Vault är allmänt tillgänglig

Azure Key Vault är en moln tjänst som skyddar krypterings nycklar och hemligheter som certifikat, anslutnings strängar och lösen ord. 

**Azure Defender för Key Vault** ger Azure-inbyggt Avancerat skydd för Azure Key Vault, vilket ger ytterligare ett lager av säkerhets information. Som tillägg skyddar Azure Defender för Key Vault därför många av resurserna beroende på dina Key Vault-konton.

Den valfria planen är nu GA. Funktionen var i för hands version som "Avancerat skydd för Azure Key Vault".

Key Vault sidorna i Azure Portal innehåller nu också en särskild **säkerhets** sida för **Security Center** rekommendationer och aviseringar.

Läs mer i [Azure Defender för Key Vault](defender-for-key-vault-introduction.md).


### <a name="azure-defender-for-storage-protection-for-files-and-adls-gen2-is-generally-available"></a>Azure Defender för lagrings skydd för filer och ADLS Gen2 är allmänt tillgängligt 

**Azure Defender för lagring** identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar.

Support för [Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) och [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction) är nu allmänt tillgänglig.

Från 1 oktober 2020 börjar vi debitera för att skydda resurser på dessa tjänster.

Läs mer i [Azure Defender för lagring](defender-for-storage-introduction.md).


### <a name="asset-inventory-tools-are-now-generally-available"></a>Till gångs inventerings verktyg är nu allmänt tillgängliga

Sidan till gångs inventeringen för Azure Security Center ger en enda sida för att Visa säkerhets position för de resurser som du har anslutit till Security Center.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen.

När en resurs har utestående rekommendationer visas de i inventeringen.

Läs mer i [utforska och hantera dina resurser med till gångs inventering och hanterings verktyg](asset-inventory.md).



### <a name="disable-a-specific-vulnerability-finding-for-scans-of-container-registries-and-virtual-machines"></a>Inaktivera en särskilt sårbarhet som söker efter genomsökningar av behållar register och virtuella datorer

Azure Defender innehåller sårbarhets skannrar för att skanna bilder i dina Azure Container Registry och dina virtuella datorer.

Om du har ett organisations behov av att ignorera en sökning i, i stället för att åtgärda det, kan du inaktivera det. Inaktiverade resultat påverkar inte din säkra poäng eller genererar oönskad brus.

När en sökning matchar de kriterier som du har definierat i inaktiverade regler visas de inte i listan över resultat.

Det här alternativet är tillgängligt från sidan rekommendationer information för:

- **Säkerhets risker i Azure Container Registry avbildningar bör åtgärdas**
- **Säkerhets risker på dina virtuella datorer bör åtgärdas**

Läs mer i [inaktivera speciella resultat för dina behållar avbildningar](defender-for-container-registries-usage.md#disable-specific-findings-preview) och [inaktivera de olika resultaten för dina virtuella datorer](remediate-vulnerability-findings-vm.md#disable-specific-findings-preview).


### <a name="exempt-a-resource-from-a-recommendation"></a>Undanta en resurs från en rekommendation

Ibland visas en resurs som inte är felfri för en speciell rekommendation (och därmed sänka dina säkra poäng) även om du känner till att den inte borde vara det. Den kan ha åtgärd ATS av en process som inte spåras av Security Center. Eller så kanske din organisation har valt att godkänna risken för den aktuella resursen. 

I sådana fall kan du skapa en undantags regel och se till att resursen inte listas bland de felaktiga resurserna i framtiden. Dessa regler kan omfatta dokumenterade motiveringar som beskrivs nedan.

Läs mer i [undanta en resurs från rekommendationer och säkra Poäng](exempt-resource.md).


### <a name="aws-and-gcp-connectors-in-security-center-bring-a-multi-cloud-experience"></a>AWS-och GCP-anslutningar i Security Center ta en upplevelse i flera moln

Med moln arbets belastningar som ofta spänner över flera moln plattformar måste moln säkerhets tjänster göra samma sak.

Azure Security Center skyddar nu arbets belastningar i Azure, Amazon Web Services (AWS) och Google Cloud Platform (GCP).

När du registrerar dina AWS-och GCP-konton i Security Center integreras AWS-säkerhetshubben, GCP säkerhets kommando och Azure Security Center. 

Läs mer i [Anslut dina AWS-konton till Azure Security Center](quickstart-onboard-aws.md) och [Anslut dina GCP-konton till Azure Security Center](quickstart-onboard-gcp.md).


### <a name="kubernetes-workload-protection-recommendation-bundle"></a>Paket för Kubernetes-skydds rekommendation

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard, lägger Security Center till Kubernetes nivå skärp rekommendationer, inklusive verk ställnings alternativ med Kubernetes-åtkomstkontroll.

När du har installerat Azure Policy-tillägget för Kubernetes i ditt AKS-kluster, kommer varje begäran till Kubernetes API-servern att övervakas mot den fördefinierade uppsättningen med bästa metoder innan den sparas i klustret. Du kan sedan konfigurera för att tillämpa bästa praxis och bestämma dem för framtida arbets belastningar.

Du kan till exempel bestämma att privilegierade behållare inte ska skapas och eventuella framtida förfrågningar om detta kommer att blockeras.

Läs mer i [metod tips för arbets belastnings skydd med Kubernetes-åtkomstkontroll](container-security.md#workload-protection-best-practices-using-kubernetes-admission-control).


### <a name="vulnerability-assessment-findings-are-now-available-in-continuous-export"></a>Rön om sårbarhets bedömning är nu tillgängliga i löpande export

Använd kontinuerlig export för att strömma aviseringar och rekommendationer i real tid till Azure Event Hubs, Log Analytics arbets ytor eller Azure Monitor. Därifrån kan du integrera dessa data med Siem (till exempel Azure Sentinel, Power BI, Azure Datautforskaren med mera.

Security Center s verktyg för integrerad sårbarhets bedömning returnerar resultat om dina resurser som åtgärds bara rekommendationer i en överordnad rekommendation, till exempel säkerhets risker i dina virtuella datorer, bör åtgärdas. 

Säkerhets resultaten är nu tillgängliga för export genom löpande export när du väljer rekommendationer och aktiverar alternativet **Inkludera säkerhets resultat** .

:::image type="content" source="./media/continuous-export/include-security-findings-toggle.png" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna&quot;:::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: &quot;/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet&quot;,
name: &quot;mico-rg-vnet&quot;,
type: &quot;Microsoft.Security/securityStatuses&quot;,
properties:  {
    policyAssessments: [
        {assessmentKey: &quot;e3deicce-f4dd-3b34-e496-8b5381bazd7e&quot;, category: &quot;Networking&quot;, policyName: &quot;Azure DDOS Protection Standard should be enabled&quot;,...},
        {assessmentKey: &quot;sefac66a-1ec5-b063-a824-eb28671dc527&quot;, category: &quot;Compute&quot;, policyName: &quot;&quot;,...}
    ],
    securitystateByCategory: [{category: &quot;Networking&quot;, securityState: &quot;None&quot; }, {category: &quot;Compute&quot;,...],
    name: &quot;GenericResourceHealthProperties&quot;,
    type: &quot;VirtualNetwork&quot;,
    securitystate: &quot;High" :::

Relaterade sidor:

- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Virtual Machines](deploy-vulnerability-assessment-vm.md)
- [Security Center är en integrerad lösning för sårbarhets bedömning för Azure Container Registry avbildningar](defender-for-container-registries-usage.md)
- [Löpande export](continuous-export.md)

### <a name="prevent-security-misconfigurations-by-enforcing-recommendations-when-creating-new-resources"></a>Förhindra felaktig säkerhet genom att tvinga rekommendationer när du skapar nya resurser

Fel konfiguration av säkerhet är en stor orsak till säkerhets incidenter. Security Center kan nu hjälpa till *att förhindra* fel konfiguration av nya resurser med avseende på särskilda rekommendationer. 

Den här funktionen kan hjälpa till att hålla arbets belastningarna säkra och stabilisera dina säkra poäng.

Att framtvinga en säker konfiguration, som baseras på en viss rekommendation, erbjuds i två lägen:

- Genom att använda Azure Policy **neka** kan du förhindra att resurser som inte är felfria skapas

- Med alternativet **tillämpa** kan du dra nytta av Azure policys **DeployIfNotExist** -påverkan och automatiskt reparera icke-kompatibla resurser när de skapas
 
Detta är tillgängligt för de valda säkerhets rekommendationerna och finns överst på sidan med resurs information.

Läs mer i [förhindra felaktig konfiguration med tvinga/neka-rekommendationer](prevent-misconfigurations.md).

###  <a name="network-security-group-recommendations-improved"></a>Bättre rekommendationer för nätverks säkerhets grupp

Följande säkerhets rekommendationer relaterade till nätverks säkerhets grupper har förbättrats för att minska vissa instanser av falska positiva identifieringar.

- Alla nätverks portar bör vara begränsade på NSG som är kopplade till den virtuella datorn
- Hanterings portar bör stängas på dina virtuella datorer
- Virtuella datorer som är riktade mot Internet bör skyddas med nätverks säkerhets grupper
- Undernät ska associeras med en nätverks säkerhets grupp


### <a name="deprecated-preview-aks-recommendation-pod-security-policies-should-be-defined-on-kubernetes-services"></a>Föråldrad för hands version AKS-rekommendation "Pod Security Policies definieras i Kubernetes Services"

Förhands gransknings rekommendationen "Pod Security Policies" måste definieras i Kubernetes Services ", vilket beskrivs i dokumentationen för [Azure Kubernetes-tjänsten](https://docs.microsoft.com/azure/aks/use-pod-security-policies) .

Funktionen Pod Security Policy (för hands version) är inställd för utfasning och kommer inte längre att vara tillgänglig efter den 15 oktober 2020 i Azure Policy för AKS.

När Pod säkerhets princip (förhands granskning) är föråldrad måste du inaktivera funktionen på alla befintliga kluster med hjälp av den föråldrade funktionen för att utföra framtida kluster uppgraderingar och hålla koll på Azure-supporten.


### <a name="email-notifications-from-azure-security-center-improved"></a>E-postaviseringar från Azure Security Center bättre

Följande delar av e-postmeddelandena om säkerhets aviseringar har förbättrats: 

- Har lagt till möjligheten att skicka e-postmeddelanden om aviseringar för alla allvarlighets nivåer
- Har lagt till möjligheten att meddela användare med olika RBAC-roller i prenumerationen
- Vi meddelar proaktivt prenumerations ägare som standard på aviseringar med hög allvarlighets grad (som har hög sannolikhet för att vara äkta)
- Vi har tagit bort fältet telefonnummer från konfigurations sidan för e-postaviseringar

Läs mer i [Konfigurera e-postaviseringar för säkerhets aviseringar](security-center-provide-security-contact-details.md).


### <a name="secure-score-doesnt-include-preview-recommendations"></a>Säkra Poäng omfattar inte för hands versions rekommendationer 

Security Center utvärderar kontinuerligt dina resurser, prenumerationer och din organisation efter säkerhets problem. Den sammanställer sedan alla resultat i en enda poäng så att du snabbt kan tala om din aktuella säkerhets situation: ju högre poäng, desto lägre är den identifierade risk nivån.

När nya hot upptäcks görs nya säkerhets rådgivning i Security Center via nya rekommendationer. För att undvika oväntade ändringar av dina säkra poäng och för att tillhandahålla en respitperiod där du kan utforska nya rekommendationer innan de påverkar dina resultat, ingår rekommendationer som har flaggats som för **hands version** inte längre i beräkningarna av dina säkra poäng. De bör fortfarande åtgärdas när så är möjligt, så att när förhands gransknings perioden är slut bidrar de till dina poäng.

För **hands versions** rekommendationer återges inte heller en resurs "ohälsosam".

Ett exempel på en förhands gransknings rekommendation:

:::image type="content" source="./media/secure-score-security-controls/example-of-preview-recommendation.png" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna&quot;:::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: &quot;/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet&quot;,
name: &quot;mico-rg-vnet&quot;,
type: &quot;Microsoft.Security/securityStatuses&quot;,
properties:  {
    policyAssessments: [
        {assessmentKey: &quot;e3deicce-f4dd-3b34-e496-8b5381bazd7e&quot;, category: &quot;Networking&quot;, policyName: &quot;Azure DDOS Protection Standard should be enabled&quot;,...},
        {assessmentKey: &quot;sefac66a-1ec5-b063-a824-eb28671dc527&quot;, category: &quot;Compute&quot;, policyName: &quot;&quot;,...}
    ],
    securitystateByCategory: [{category: &quot;Networking&quot;, securityState: &quot;None&quot; }, {category: &quot;Compute&quot;,...],
    name: &quot;GenericResourceHealthProperties&quot;,
    type: &quot;VirtualNetwork&quot;,
    securitystate: &quot;High":::

[Läs mer om säkra Poäng](secure-score-security-controls.md).


### <a name="recommendations-now-include-a-severity-indicator-and-the-freshness-interval"></a>Rekommendationerna innehåller nu en allvarlighets grad och aktualitets intervallet

På informations sidan för rekommendationer finns nu en indikator för aktualitets intervall (när det är relevant) och en tydlig visning av rekommendationens allvarlighets grad.

:::image type="content" source="./media/release-notes/recommendations-severity-freshness-indicators.png" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna&quot;:::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: &quot;/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet&quot;,
name: &quot;mico-rg-vnet&quot;,
type: &quot;Microsoft.Security/securityStatuses&quot;,
properties:  {
    policyAssessments: [
        {assessmentKey: &quot;e3deicce-f4dd-3b34-e496-8b5381bazd7e&quot;, category: &quot;Networking&quot;, policyName: &quot;Azure DDOS Protection Standard should be enabled&quot;,...},
        {assessmentKey: &quot;sefac66a-1ec5-b063-a824-eb28671dc527&quot;, category: &quot;Compute&quot;, policyName: &quot;&quot;,...}
    ],
    securitystateByCategory: [{category: &quot;Networking&quot;, securityState: &quot;None&quot; }, {category: &quot;Compute&quot;,...],
    name: &quot;GenericResourceHealthProperties&quot;,
    type: &quot;VirtualNetwork&quot;,
    securitystate: &quot;High":::



## <a name="august-2020"></a>Augusti 2020

Uppdateringarna i augusti inkluderar:

- [Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet](#asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets)
- [Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)](#added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication)
- [Rekommendation för tjänstens huvud namn lades till](#service-principals-recommendation-added)
- [Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer](#vulnerability-assessment-on-vms---recommendations-and-policies-consolidated)
- [Nya AKS säkerhets principer har lagts till i ASC_default initiativ – för användning av privata för hands kunder](#new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only)


### <a name="asset-inventory---powerful-new-view-of-the-security-posture-of-your-assets"></a>Till gångs lager – kraftfull ny vy av position till gångarnas säkerhet

Security Center till gångs lagret (för närvarande i för hands version) ger dig ett sätt att Visa säkerhets position för de resurser som du har anslutit till Security Center.

Security Center analyserar regelbundet säkerhets status för dina Azure-resurser för att identifiera potentiella säkerhets risker. Därefter får du rekommendationer om hur du åtgärdar problemen. När en resurs har utestående rekommendationer visas de i inventeringen.

Du kan använda vyn och dess filter för att utforska dina säkerhets position data och vidta ytterligare åtgärder baserat på dina resultat.

Läs mer om [till gångs inventering](asset-inventory.md).


### <a name="added-support-for-azure-active-directory-security-defaults-for-multi-factor-authentication"></a>Stöd har lagts till för Azure Active Directory säkerhets inställningar (för Multi-Factor Authentication)

Security Center har lagt till fullständigt stöd för [säkerhets standarder](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults), Microsofts kostnads fria identitets skydds skydd.

Säkerhets standarder tillhandahåller förkonfigurerade säkerhets inställningar för identitet för att skydda din organisation från vanliga problem med identitets angrepp. Säkerhets standarder skyddar redan över 5 000 000 klienter totalt. 50 000-klienter skyddas också av Security Center.

Security Center har nu en säkerhets rekommendation när den identifierar en Azure-prenumeration utan säkerhets inställningar som är aktiverade. Fram till nu rekommenderar Security Center att du aktiverar Multi-Factor Authentication med villkorlig åtkomst, som är en del av Azure Active Directory (AD) Premium-licensen. För kunder som använder Azure AD kostnads fri rekommenderar vi nu att du aktiverar säkerhets inställningar. 

Vårt mål är att uppmuntra fler kunder att skydda sina moln miljöer med MFA och minimera en av de högsta riskerna som också är mest påverkan av dina [säkra Poäng](secure-score-security-controls.md).

Läs mer om [säkerhets inställningar](https://docs.microsoft.com/azure/active-directory/fundamentals/concept-fundamentals-security-defaults).


### <a name="service-principals-recommendation-added"></a>Rekommendation för tjänstens huvud namn lades till

En ny rekommendation har lagts till för att rekommendera att Security Center kunder som använder hanterings certifikat för att hantera sina prenumerationer växlar till tjänstens huvud namn.

Rekommendationen är att **tjänstens huvud namn ska användas för att skydda dina prenumerationer i stället för hanterings certifikat** som rekommenderar att du använder tjänstens huvud namn eller Azure Resource Manager för att hantera dina prenumerationer på ett säkert sätt. 

Läs mer om [program-och tjänst huvud objekt i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals#service-principal-object).


### <a name="vulnerability-assessment-on-vms---recommendations-and-policies-consolidated"></a>Sårbarhets bedömning på virtuella datorer – rekommendationer och konsoliderade principer

Security Center granskar de virtuella datorerna för att identifiera om de använder en lösning för sårbarhets bedömning. Om ingen lösning för sårbarhets bedömning hittas ger Security Center en rekommendation för att förenkla distributionen.

När det finns sårbarheter ger Security Center en rekommendation som sammanfattar resultaten för att undersöka och åtgärda vid behov.

För att säkerställa en konsekvent upplevelse för alla användare, oavsett vilken skanner typ de använder, har vi förenat fyra rekommendationer i följande två:

|Enhetlig rekommendation|Ändra beskrivning|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**|Ersätter följande två rekommendationer:<br> **•** Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys (nu föråldrad) (ingår i standard-nivån)<br> **•** Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer (nu föråldrade) (standard-och kostnads fria nivåer)|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**|Ersätter följande två rekommendationer:<br>**•** Åtgärda säkerhets problem som finns på dina virtuella datorer (drivs av Qualys) (nu föråldrade)<br>**•** Sårbarheter bör åtgärdas av en lösning för sårbarhets bedömning (nu föråldrad)|
|||

Nu ska du använda samma rekommendation för att distribuera Security Centers tillägg för sårbarhets bedömning eller en privat licensierad lösning ("BYOL") från en partner, till exempel Qualys eller Rapid7.

När säkerhets risker upptäcks och rapporteras till Security Center får du dessutom en enda rekommendation som meddelar dig om avgöranden, oavsett vilken lösning för sårbarhets bedömning som identifierade dem.

#### <a name="updating-dependencies"></a>Uppdaterar beroenden

Om du har skript, frågor eller automatiseringar som refererar till föregående rekommendationer eller princip nycklar/namn, använder du tabellerna nedan för att uppdatera referenserna:

##### <a name="before-august-2020"></a>Före augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys)**<br>Nyckel: 550e890b-e652-4D22-8274-60b3bdb24c63|Inbyggd|
|**Åtgärda sårbarheter som finns på dina virtuella datorer (drivs av Qualys)**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggd|
|**Lösningen för sårbarhets bedömning bör installeras på dina virtuella datorer**<br>Nyckel: 01b1ed4c-b733-4fee-b145-f23236e70cf3|BYOL|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Nyckel: 71992a2a-D168-42e0-b10e-6b45fa2ecddb|BYOL|
||||


|Princip|Omfång|
|----|:----|
|**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9|Inbyggd|
|**Säkerhets risker bör åtgärdas av en lösning för sårbarhets bedömning**<br>Princip-ID: 760a85ff-6162-42b3-8d70-698e268f648c|BYOL|
||||


##### <a name="from-august-2020"></a>Från augusti 2020

|Rekommendation|Omfång|
|----|:----|
|**En lösning för sårbarhets bedömning ska vara aktive rad på dina virtuella datorer**<br>Nyckel: ffff0522-1e88-47fc-8382-2a80ba848f5d|Inbyggda + BYOL|
|**Säkerhets risker på dina virtuella datorer bör åtgärdas**<br>Nyckel: 1195afff-c881-495e-9bc5-1486211ae03f|Inbyggda + BYOL|
||||

|Princip|Omfång|
|----|:----|
|[**Sårbarhets bedömning ska vara aktiverat på virtuella datorer**](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f501541f7-f7e7-4cd6-868c-4190fdad3ac9)<br>Princip-ID: 501541f7-f7e7-4cd6-868c-4190fdad3ac9 |Inbyggda + BYOL|
||||


### <a name="new-aks-security-policies-added-to-asc_default-initiative--for-use-by-private-preview-customers-only"></a>Nya AKS säkerhets principer har lagts till i ASC_default initiativ – för användning av privata för hands kunder

För att säkerställa att Kubernetes-arbetsbelastningar är säkra som standard, lägger Security Center till principer för Kubernetes-nivå och skärper rekommendationer, inklusive verk ställnings alternativ med Kubernetes-åtkomstkontroll.

Den tidiga fasen i det här projektet innehåller en privat för hands version och tillägg av nya (inaktiverade som standard) principer för ASC_default initiativ.

Du kan ignorera dessa principer utan att påverka din miljö. Om du vill aktivera dem kan du registrera dig för för hands versionen på https://aka.ms/SecurityPrP och välja bland följande alternativ:

1. **Enkel förhands granskning** – om du bara vill ansluta till denna privata för hands version Nämna uttryckligen "ASC-kontinuerlig skanning" som förhands granskningen som du vill delta i.
1. **Kontinuerligt program** – som ska läggas till i den här och framtida privata för hands versionerna. Du måste slutföra ett profil-och sekretess avtal.


## <a name="july-2020"></a>Juli 2020

I juli ingår följande uppdateringar:
- [Sårbarhets bedömning för virtuella datorer är nu tillgängligt för avbildningar som inte är Marketplace](#vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images)
- [Skydd mot Azure Storage utökas till att omfatta Azure Files och Azure Data Lake Storage Gen2 (förhands granskning)](#threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview)
- [Åtta nya rekommendationer för att aktivera hot skydds funktioner](#eight-new-recommendations-to-enable-threat-protection-features)
- [Förbättringar av behållar säkerhet – snabbare register genomsökning och uppdaterad dokumentation](#container-security-improvements---faster-registry-scanning-and-refreshed-documentation)
- [Anpassningsbara program kontroller uppdaterade med en ny rekommendation och stöd för jokertecken i Sök vägs regler](#adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules)
- [Sex principer för avancerad data säkerhet i SQL är inaktuell](#six-policies-for-sql-advanced-data-security-deprecated)




### <a name="vulnerability-assessment-for-virtual-machines-is-now-available-for-non-marketplace-images"></a>Sårbarhets bedömning för virtuella datorer är nu tillgängligt för avbildningar som inte är Marketplace

När du distribuerar en lösning för sårbarhets bedömning Security Center tidigare genomförde en verifierings kontroll innan du distribuerar. Kontrollen var att bekräfta en Marketplace-SKU för den virtuella mål datorn. 

Från den här uppdateringen har kontrollen tagits bort och nu kan du distribuera verktyg för sårbarhets bedömning till anpassade Windows-och Linux-datorer. Anpassade avbildningar är de som du har ändrat från Marketplace-standardvärden.

Även om du nu kan distribuera det integrerade tillägget för sårbarhets bedömning (drivs av Qualys) på många fler datorer, är support endast tillgängligt om du använder ett operativ system som anges i [distribuera den integrerade säkerhets luckan till virtuella datorer med standard nivå](deploy-vulnerability-assessment-vm.md#deploy-the-integrated-scanner-to-your-azure-and-hybrid-machines) .

Läs mer om den [integrerade säkerhets genomsökningen för virtuella datorer (kräver Azure Defender)](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).

Lär dig mer om att använda en egen privat licensierad lösning för sårbarhets bedömning från Qualys eller Rapid7 i [distribuera en lösning för genomsökning av partner problem](deploy-vulnerability-assessment-vm.md).


### <a name="threat-protection-for-azure-storage-expanded-to-include-azure-files-and-azure-data-lake-storage-gen2-preview"></a>Skydd mot Azure Storage utökas till att omfatta Azure Files och Azure Data Lake Storage Gen2 (förhands granskning)

Skydd mot hot för Azure Storage identifierar potentiellt skadlig aktivitet på dina Azure Storage-konton. Security Center visar aviseringar när du upptäcker försök att komma åt eller utnyttja dina lagrings konton. 

Dina data kan skyddas oavsett om de lagras som BLOB-behållare, fil resurser eller data sjöar.




### <a name="eight-new-recommendations-to-enable-threat-protection-features"></a>Åtta nya rekommendationer för att aktivera hot skydds funktioner

Åtta nya rekommendationer har lagts till för att ge ett enkelt sätt att aktivera Azure Security Centers hot skydds funktioner för följande resurs typer: virtuella datorer, App Service-planer, Azure SQL Database-servrar, SQL-servrar på datorer, Azure Storage konton, Azure Kubernetes service-kluster, Azure Container Registry register och Azure Key Vault-valv.

De nya rekommendationerna är:

- **Avancerad data säkerhet ska vara aktiverat på Azure SQL Database servrar**
- **Avancerad data säkerhet ska vara aktiverat på SQL-servrar på datorer**
- **Avancerat skydd bör vara aktiverat på Azure App Service planer**
- **Avancerat skydd bör vara aktiverat på Azure Container Registry register**
- **Avancerat skydd bör vara aktiverat på Azure Key Vault valv**
- **Avancerat skydd bör vara aktiverat på Azure Kubernetes service-kluster**
- **Avancerat skydd bör vara aktiverat på Azure Storage konton**
- **Avancerat skydd bör vara aktiverat på virtuella datorer**

De här nya rekommendationerna hör till säkerhets kontrollen **Aktivera avancerat skydd** .

Rekommendationerna omfattar även snabb korrigerings funktionen. 

> [!IMPORTANT]
> Att reparera någon av dessa rekommendationer ger kostnader för att skydda relevanta resurser. Dessa avgifter börjar omedelbart om du har relaterade resurser i den aktuella prenumerationen. Eller i framtiden, om du lägger till dem senare.
> 
> Om du till exempel inte har några Azure Kubernetes service-kluster i din prenumeration och du aktiverar hot skyddet debiteras inga avgifter. Om du i framtiden lägger till ett kluster i samma prenumeration, kommer det automatiskt att skyddas och kostnader kommer att börja vid den tidpunkten.

Läs mer om var och en av dessa på [referens sidan för säkerhets rekommendationer](recommendations-reference.md).

Läs mer om [skydd av hot i Azure Security Center](azure-defender.md).




### <a name="container-security-improvements---faster-registry-scanning-and-refreshed-documentation"></a>Förbättringar av behållar säkerhet – snabbare register genomsökning och uppdaterad dokumentation

Som en del av de kontinuerliga investeringarna i behållar säkerhets domänen är vi glada att dela en betydande prestanda förbättring i Security Center dynamiska genomsökningar av behållar avbildningar som lagras i Azure Container Registry. Genomsökningar är nu vanligt vis slutförda på ungefär två minuter. I vissa fall kan det ta upp till 15 minuter.

För att förbättra skärpan och vägledningen avseende Azure Security Center behållar säkerhetsfunktioner har vi också uppdaterat dokumentations sidorna för behållar säkerhet. 

Läs mer om Security Center behållar säkerhet i följande artiklar:

- [Översikt över Security Center behållar säkerhetsfunktioner](container-security.md)
- [Information om integreringen med Azure Container Registry](defender-for-container-registries-introduction.md)
- [Information om integrering med Azure Kubernetes-tjänsten](defender-for-kubernetes-introduction.md)
- [Anvisningar – söka igenom dina register och torka Docker-värdarna](container-security.md)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-kluster](alerts-reference.md#alerts-akscluster)
- [Säkerhets aviseringar från hot Protection-funktionerna i Azure Kubernetes service-värdar](alerts-reference.md#alerts-containerhost)
- [Säkerhets rekommendationer för behållare](recommendations-reference.md#recs-containers)



### <a name="adaptive-application-controls-updated-with-a-new-recommendation-and-support-for-wildcards-in-path-rules"></a>Anpassningsbara program kontroller uppdaterade med en ny rekommendation och stöd för jokertecken i Sök vägs regler

Funktionen för anpassningsbara program kontroller har tagit emot två viktiga uppdateringar:

* En ny rekommendation identifierar potentiellt legitimt beteende som inte tidigare har tillåtits. Den nya rekommendationen, **tillåten-regler i din anpassningsbar program kontroll princip ska uppdateras**, och du uppmanas att lägga till nya regler i den befintliga principen för att minska antalet falska positiva identifieringar i adaptiva program kontroller fel aviseringar.

* Sök vägs regler stöder nu jokertecken. Från den här uppdateringen kan du konfigurera tillåtna Sök vägs regler med hjälp av jokertecken. Det finns två scenarier som stöds:

    * Använda jokertecken i slutet av en sökväg för att tillåta alla körbara filer i den här mappen och undermappar

    * Använd ett jokertecken i mitten av en sökväg för att aktivera ett känt körbart namn med ett ändrat mappnamn (t. ex. personliga användarmappar med en känd körbar fil, automatiskt genererade mappnamn osv.).


[Läs mer om anpassningsbara program kontroller](security-center-adaptive-application.md).



### <a name="six-policies-for-sql-advanced-data-security-deprecated"></a>Sex principer för avancerad data säkerhet i SQL är inaktuell

Sex principer relaterade till avancerad data säkerhet för SQL-datorer är föråldrade:

- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL-hanterad instans
- Avancerade skydds typer måste anges till alla i avancerade data säkerhets inställningar för SQL Server
- Avancerade data säkerhets inställningar för SQL-hanterad instans ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- Avancerade data säkerhets inställningar för SQL Server ska innehålla en e-postadress för att ta emot säkerhets aviseringar
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar för SQL-hanterad instans
- E-postaviseringar till administratörer och prenumerations ägare måste vara aktiverade i avancerade data säkerhets inställningar i SQL Server

Läs mer om [inbyggda principer](security-center-policy-definitions.md).





## <a name="june-2020"></a>Juni 2020

Uppdateringarna i juni inkluderar:
- [Secure score-API (för hands version)](#secure-score-api-preview)
- [Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokal) (för hands version)](#advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview)
- [Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)](#two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview)
- [Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala](#new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale)
- [Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade](#new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines)
- [Nya principer för att aktivera hot skydd och avancerad data säkerhet](#new-policies-for-enabling-threat-protection-and-advanced-data-security)



### <a name="secure-score-api-preview"></a>Secure score-API (för hands version)

Nu kan du komma åt dina poäng via [Secure score-API: t](https://docs.microsoft.com/rest/api/securitycenter/securescores/) (för närvarande i för hands version). API-metoderna ger flexibiliteten att fråga data och skapa en egen rapporterings mekanism för dina säkra poäng över tid. Du kan till exempel använda **Secure Scores** -API: et för att hämta poängen för en speciell prenumeration. Dessutom kan du använda API: et för **säker Poäng** för att visa en lista över säkerhets kontrollerna och de aktuella poängen för dina prenumerationer.

Exempel på externa verktyg som möjliggörs med Secure score-API: et finns i avsnittet [Secure Scores i vår GitHub-community](https://github.com/Azure/Azure-Security-Center/tree/master/Secure%20Score).

Läs mer om [säkra poäng-och säkerhets kontroller i Azure Security Center](secure-score-security-controls.md).



### <a name="advanced-data-security-for-sql-machines-azure-other-clouds-and-on-prem-preview"></a>Avancerad data säkerhet för SQL-datorer (Azure, andra moln och lokal) (för hands version)

Azure Security Center avancerade data säkerhet för SQL-datorer skyddar nu SQL-servrar som finns i Azure, i andra moln miljöer och till och med lokala datorer. Detta utökar skydds inställningarna för dina Azure-inhemska SQL-servrar för att fullt stödja hybrid miljöer.

Avancerad data säkerhet ger sårbarhets bedömning och Avancerat skydd för dina SQL-datorer oavsett var de befinner sig.

Konfigurations åtgärder omfattar två steg:

1. Distribuera Log Analytics agenten till SQL Servers värddator för att tillhandahålla anslutningen till Azure-kontot.

1. Aktivera det valfria paketet på Security Center pris-och inställnings sida.

Lär dig mer om [Avancerad data säkerhet för SQL-datorer](defender-for-sql-usage.md).



### <a name="two-new-recommendations-to-deploy-the-log-analytics-agent-to-azure-arc-machines-preview"></a>Två nya rekommendationer för att distribuera Log Analytics agent till Azure Arc-datorer (för hands version)

Två nya rekommendationer har lagts till för att hjälpa till att distribuera [Log Analytics agenten](https://docs.microsoft.com/azure/azure-monitor/platform/log-analytics-agent) till dina Azure Arc-datorer och se till att de skyddas av Azure Security Center:

- **Log Analytics agenten ska installeras på dina Windows-baserade Azure Arc-datorer (för hands version)**
- **Log Analytics agent ska installeras på Linux-baserade Azure Arc-datorer (för hands version)**

De här nya rekommendationerna visas i samma fyra säkerhets kontroller som den befintliga (relaterade) rekommendationen, **övervaknings agenten ska installeras på datorerna**: åtgärda säkerhetskonfigurationer, tillämpa anpassningsbar program kontroll, tillämpa system uppdateringar och Aktivera Endpoint Protection.

Rekommendationerna innehåller också snabb korrigerings funktionen som hjälper dig att påskynda distributions processen. 

Läs mer om de här två nya rekommendationerna i tabellen med [rekommendationer för beräkning och appar](recommendations-reference.md#recs-computeapp) .

Läs mer om hur Azure Security Center använder agenten i [Vad är Log Analytics agenten?](faq-data-collection-agents.md#what-is-the-log-analytics-agent).

Läs mer om [tillägg för Azure Arc-datorer](../azure-arc/servers/manage-vm-extensions.md#enable-extensions-from-the-portal).


### <a name="new-policies-to-create-continuous-export-and-workflow-automation-configurations-at-scale"></a>Nya principer för att skapa kontinuerliga konfigurationer för export och automatisering av arbets flöden i skala

Att automatisera organisationens övervaknings-och incident svars processer kan avsevärt förbättra tiden det tar att undersöka och minimera säkerhets incidenter.

Om du vill distribuera dina automatiserings konfigurationer i organisationen använder du de här inbyggda "DeployIfdNotExist" Azure-principerna för att skapa och konfigurera [kontinuerliga](continuous-export.md) metoder för export och [automatisering av arbets flöde](workflow-automation.md) :

Du hittar principerna i Azure policy:


|Mål  |Princip  |Princip-ID  |
|---------|---------|---------|
|Kontinuerlig export till händelsehubben|[Distribuera export till händelsehubben för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fcdfcce10-4578-4ecd-9703-530938e4abcb)|cdfcce10-4578-4ecd-9703-530938e4abcb|
|Löpande export till Log Analytics arbets yta|[Distribuera export till Log Analytics-arbetsytan för aviseringar och rekommendationer i Azure Security Center](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fffb6f416-7bd2-4488-8828-56585fef2be9)|ffb6f416-7bd2-4488-8828-56585fef2be9|
|Arbets flödes automatisering för säkerhets aviseringar|[Distribuera arbetsflödesautomation för Azure Security Center-aviseringar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2ff1525828-9a90-4fcf-be48-268cdd02361e)|f1525828-9a90-4fcf-be48-268cdd02361e|
|Arbets flödes automatisering för säkerhets rekommendationer|[Distribuera arbetsflödesautomation för Azure Security Center-rekommendationer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f73d6ab6c-2475-4850-afd6-43795f3492ef)|73d6ab6c-2475-4850-afd6-43795f3492ef|
||||

Kom igång med [automatiserings mallar för arbets flöden](https://github.com/Azure/Azure-Security-Center/tree/master/Workflow%20automation).

Lär dig mer om att använda de två export principerna för att [kontinuerligt exportera Azure Security Center aviseringar och rekommendationer via policy](https://techcommunity.microsoft.com/t5/azure-security-center/continuously-export-azure-security-center-alerts-and/ba-p/1440745).


### <a name="new-recommendation-for-using-nsgs-to-protect-non-internet-facing-virtual-machines"></a>Ny rekommendation för att använda NSG: er för att skydda virtuella datorer som inte är Internet-riktade

Säkerhets kontrollen "implementera rekommenderade säkerhets metoder" innehåller nu följande nya rekommendation:

- **Virtuella datorer som inte är baserade på Internet bör skyddas med nätverks säkerhets grupper**

En befintlig rekommendation, **virtuella datorer som riktas mot Internet bör skyddas med nätverks säkerhets grupper**, skiljer sig inte mellan Internet-riktade och icke-Internetbaserade virtuella datorer. För båda, genererade en rekommendation med hög allvarlighets grad om en virtuell dator inte var tilldelad till en nätverks säkerhets grupp. Den nya rekommendationen separerar datorer som inte är på Internet för att minska antalet falska positiva identifieringar och undvika onödiga aviseringar med hög allvarlighets grad.

Läs mer i tabellen med [nätverks rekommendationer](recommendations-reference.md#recs-network) .




### <a name="new-policies-for-enabling-threat-protection-and-advanced-data-security"></a>Nya principer för att aktivera hot skydd och avancerad data säkerhet

De nya principerna nedan lades till i ASC-standardinitiativet och är utformade för att hjälpa till med att aktivera skydd av hot eller avancerad data säkerhet för relevanta resurs typer.

Du hittar principerna i Azure policy:


| Princip                                                                                                                                                                                                                                                                | Princip-ID                            |
|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|--------------------------------------|
| [Avancerad data säkerhet ska vara aktiverat på Azure SQL Database servrar](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f7fe3b40f-802b-4cdd-8bd4-fd799c948cc2)     | 7fe3b40f-802b-4cdd-8bd4-fd799c948cc2 |
| [Avancerad data säkerhet ska vara aktiverat på SQL-servrar på datorer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f6581d072-105e-4418-827f-bd446d56421b) | 6581d072-105e-4418-827f-bd446d56421b |
| [Avancerat skydd bör vara aktiverat på Azure Storage konton](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f308fbb08-4ab8-4e67-9b29-592e93fb94fa)           | 308fbb08-4ab8-4e67-9b29-592e93fb94fa |
| [Avancerat skydd bör vara aktiverat på Azure Key Vault valv](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f0e6763cc-5078-4e64-889d-ff4d9a839047)           | 0e6763cc-5078-4e64-889d-ff4d9a839047 |
| [Avancerat skydd bör vara aktiverat på Azure App Service planer](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f2913021d-f2fd-4f3d-b958-22354e2bdbcb)                | 2913021d-f2fd-4f3d-b958-22354e2bdbcb |
| [Avancerat skydd bör vara aktiverat på Azure Container Registry register](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2fc25d9a16-bc35-4e15-a7e5-9db606bf9ed4)   | c25d9a16-bc35-4e15-a7e5-9db606bf9ed4 |
| [Avancerat skydd bör vara aktiverat på Azure Kubernetes service-kluster](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f523b5cd1-3e23-492f-a539-13118b6d1e3a)   | 523b5cd1-3e23-492f-a539-13118b6d1e3a |
| [Avancerat skydd bör vara aktiverat på Virtual Machines](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2fproviders%2fMicrosoft.Authorization%2fpolicyDefinitions%2f4da35fc9-c9e7-4960-aec9-797fe7d9051d)           | 4da35fc9-c9e7-4960-aec9-797fe7d9051d |
|                                                                                                                                                                                                                                                                       |                                      |

Läs mer om [skydd av hot i Azure Security Center](azure-defender.md).





## <a name="may-2020"></a>Maj 2020

Uppdateringar i kan vara:
- [Undervisnings regler för aviseringar (för hands version)](#alert-suppression-rules-preview)
- [Utvärdering av säkerhets risker för virtuella datorer är nu allmänt tillgänglig](#virtual-machine-vulnerability-assessment-is-now-generally-available)
- [Ändringar av åtkomst till virtuell dator (VM) för just-in-Time (JIT)](#changes-to-just-in-time-jit-virtual-machine-vm-access)
- [Anpassade rekommendationer har flyttats till en separat säkerhets kontroll](#custom-recommendations-have-been-moved-to-a-separate-security-control)
- [Växla tillagd för att Visa rekommendationer i kontroller eller som en platt lista](#toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list)
- [Utökad säkerhets kontroll "implementera säkerhets metod tips"](#expanded-security-control-implement-security-best-practices)
- [Anpassade principer med anpassade metadata är nu allmänt tillgängliga](#custom-policies-with-custom-metadata-are-now-generally-available)
- [Analys funktioner för krasch dumpning migrera till fil lös attack identifiering](#crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection)


### <a name="alert-suppression-rules-preview"></a>Undervisnings regler för aviseringar (för hands version)

Den här nya funktionen (för närvarande i för hands version) hjälper till att minska aviserings utmattningen Använd regler för att automatiskt dölja aviseringar som är kända för att vara innocuous eller relaterade till normala aktiviteter i din organisation. På så sätt kan du fokusera på de mest relevanta hoten. 

Aviseringar som matchar dina aktiverade undertrycks regler kommer fortfarande att genereras, men deras tillstånd ställs in på stängs. Du kan se statusen i Azure Portal eller så får du åtkomst till dina Security Center säkerhets aviseringar.

Under trycknings regler definierar kriterierna för vilka aviseringar automatiskt ska avvisas. Normalt använder du en undertrycks regel för att:

- Ignorera aviseringar som du har identifierat som falska positiva identifieringar

- Ignorera aviseringar som utlöses för ofta för att vara användbara

Lär dig mer om att [Ignorera aviseringar från Azure Security Center skydd mot hot](alerts-suppression-rules.md).


### <a name="virtual-machine-vulnerability-assessment-is-now-generally-available"></a>Utvärdering av säkerhets risker för virtuella datorer är nu allmänt tillgänglig

Security Center standard nivån innehåller nu en integrerad sårbarhets bedömning för virtuella datorer utan extra avgift. Det här tillägget drivs av Qualys men rapporterar sina resultat direkt tillbaka till Security Center. Du behöver inte en Qualys-licens eller ens ett Qualys-konto – allt hanteras sömlöst i Security Center.

Den nya lösningen kan kontinuerligt genomsöka dina virtuella datorer för att hitta sårbarheter och visa resultaten i Security Center. 

Använd den nya säkerhets rekommendationen för att distribuera lösningen:

"Aktivera den inbyggda lösningen för sårbarhets bedömning på virtuella datorer (drivs av Qualys)"

Lär dig mer om [Security Center s integrerade sårbarhets bedömning för virtuella datorer](deploy-vulnerability-assessment-vm.md#overview-of-the-integrated-vulnerability-scanner).



### <a name="changes-to-just-in-time-jit-virtual-machine-vm-access"></a>Ändringar av åtkomst till virtuell dator (VM) för just-in-Time (JIT)

Security Center innehåller en valfri funktion för att skydda de virtuella datorernas hanterings portar. Detta ger ett försvar mot den vanligaste formen av brute force-attacker.

Den här uppdateringen ger följande ändringar i den här funktionen:

- Rekommendationen som aviserar dig om att aktivera JIT på en virtuell dator har bytt namn. Tidigare bör "just-in-Time-Network Access Control" tillämpas på virtuella datorer "det är nu:" hanterings portar för virtuella datorer bör skyddas med just-in-Time Network Access Control ".

- Rekommendationen utlöses endast om det finns öppna hanterings portar.

Lär dig mer om [funktionen för JIT-åtkomst](security-center-just-in-time.md).


### <a name="custom-recommendations-have-been-moved-to-a-separate-security-control"></a>Anpassade rekommendationer har flyttats till en separat säkerhets kontroll

En säkerhets kontroll som introducerades med den förbättrade säkra poängen var "implementera säkerhets metod tips". Alla anpassade rekommendationer som skapats för dina prenumerationer placerades automatiskt i denna kontroll. 

För att göra det lättare att hitta dina anpassade rekommendationer har vi flyttat dem till en särskild säkerhets kontroll, "anpassade rekommendationer". Den här kontrollen påverkar inte dina säkra poäng.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).


### <a name="toggle-added-to-view-recommendations-in-controls-or-as-a-flat-list"></a>Växla tillagd för att Visa rekommendationer i kontroller eller som en platt lista

Säkerhets kontroller är logiska grupper av relaterade säkerhets rekommendationer. De återspeglar dina sårbara angrepps ytor. En kontroll är en uppsättning säkerhets rekommendationer med anvisningar som hjälper dig att implementera dessa rekommendationer.

Om du vill se hur väl din organisation skyddar varje enskild attack yta granskar du poängen för varje säkerhets kontroll.

Som standard visas dina rekommendationer i säkerhets kontrollerna. Från den här uppdateringen kan du också visa dem som en lista. Om du vill visa dem som en enkel lista som sorteras efter hälso status för de berörda resurserna använder du den nya växla-gruppen efter-kontroller. Växlingen är ovanför listan i portalen.

Säkerhets kontrollerna – och den här växlingen är en del av den nya säkra poängen. Kom ihåg att skicka oss din feedback från portalen.

Läs mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version) i Azure Security Center](secure-score-security-controls.md).

:::image type="content" source="./media/secure-score-security-controls/recommendations-group-by-toggle.gif" alt-text="Azure Resource Graph Explorer och de tillgängliga tabellerna&quot;:::

> [!TIP]
> I ARG-dokumentationen visas en lista över alla tillgängliga tabeller i [Azures resurs diagram tabell och referens för resurs typ](../governance/resource-graph/reference/supported-tables-resources.md).

Från den här uppdateringen har **Microsoft. Security/securityStatuses-** tabellen tagits bort. SecurityStatuses-API: et är fortfarande tillgängligt.

Data ersättning kan användas av Microsoft. Security/assessments-tabellen.

Den största skillnaden mellan Microsoft. Security/securityStatuses och Microsoft. Security/Assessment är att när den första visar agg regering av utvärderingar, innehåller sekunderna en enda post för varje.

Till exempel, Microsoft. Security/securityStatuses skulle returnera ett resultat med en matris med två policyAssessments:

```
{
id: &quot;/subscriptions/449bcidd-3470-4804-ab56-2752595 felab/resourceGroups/mico-rg/providers/Microsoft.Network/virtualNetworks/mico-rg-vnet/providers/Microsoft.Security/securityStatuses/mico-rg-vnet&quot;,
name: &quot;mico-rg-vnet&quot;,
type: &quot;Microsoft.Security/securityStatuses&quot;,
properties:  {
    policyAssessments: [
        {assessmentKey: &quot;e3deicce-f4dd-3b34-e496-8b5381bazd7e&quot;, category: &quot;Networking&quot;, policyName: &quot;Azure DDOS Protection Standard should be enabled&quot;,...},
        {assessmentKey: &quot;sefac66a-1ec5-b063-a824-eb28671dc527&quot;, category: &quot;Compute&quot;, policyName: &quot;&quot;,...}
    ],
    securitystateByCategory: [{category: &quot;Networking&quot;, securityState: &quot;None&quot; }, {category: &quot;Compute&quot;,...],
    name: &quot;GenericResourceHealthProperties&quot;,
    type: &quot;VirtualNetwork&quot;,
    securitystate: &quot;High":::

### <a name="expanded-security-control-implement-security-best-practices"></a>Utökad säkerhets kontroll "implementera säkerhets metod tips" 

En säkerhets kontroll som introduceras med förbättrade säkra poäng är "implementera säkerhets metod tips". När en rekommendation är i den här kontrollen påverkar den inte de säkra poängen. 

Med den här uppdateringen har tre rekommendationer flyttats ut från de kontroller som de ursprungligen placerades i och i den här kontrollen med bästa praxis. Vi har vidtagit det här steget eftersom vi har fastställt att risken för dessa tre rekommendationer är lägre än vad som ursprungligen troddes.

Dessutom har två nya rekommendationer införts och lagts till i den här kontrollen.

De tre rekommendationer som flyttats är:

- **MFA ska vara aktiverat på konton med Läs behörighet för din prenumeration** (ursprungligen i "Aktivera MFA"-kontrollen)
- **Externa konton med Läs behörighet bör tas bort från din prenumeration** (från början i kontrollen hantera åtkomst och behörigheter)
- **Högst 3 ägare bör anges för din prenumeration** (ursprungligen i "hantera åtkomst och behörigheter"-kontrollen)

De två nya rekommendationerna som läggs till i kontrollen är:

- **Gäst konfigurations tillägget bör installeras på virtuella Windows-datorer (för hands version)** – med [Azure policy gäst konfiguration](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) får du insyn i virtuella datorer för Server-och program inställningar (endast Windows).

- **Windows Defender sårbarhet Guard måste vara aktiverat på dina datorer (för hands version)** – Windows Defender sårbarhet guard utnyttjar Azure policy konfigurations agenten för gäst. I sårbarhets Guard finns fyra komponenter som är utformade för att låsa enheter mot en mängd olika angrepps vektorer och blockera beteenden som ofta används i angrepp mot skadlig kod och som gör det möjligt för företag att balansera sina säkerhets risker och produktivitets krav (endast Windows).

Lär dig mer om Windows Defender sårbarhet Guard i [skapa och distribuera en princip för sårbarhets skydd](https://docs.microsoft.com/mem/configmgr/protect/deploy-use/create-deploy-exploit-guard-policy).

Lär dig mer om säkerhets kontroller i [förbättrade säkra poäng (för hands version)](secure-score-security-controls.md).



### <a name="custom-policies-with-custom-metadata-are-now-generally-available"></a>Anpassade principer med anpassade metadata är nu allmänt tillgängliga

Anpassade principer är nu en del av Security Center rekommendationer, säkra poäng och kontroll panelen för regler för efterlevnad. Den här funktionen är nu allmänt tillgänglig och gör att du kan utöka din organisations täckning för säkerhets utvärdering i Security Center. 

Skapa ett anpassat initiativ i Azure policy, Lägg till principer till den och publicera det i Azure Security Center och visualisera det som rekommendationer.

Nu har vi även lagt till alternativet för att redigera metadata för anpassade rekommendationer. Alternativen för metadata omfattar allvarlighets grad, reparations steg, Hot information och mycket annat.  

Lär dig mer om [att förbättra dina anpassade rekommendationer med detaljerad information](custom-security-policies.md#enhance-your-custom-recommendations-with-detailed-information).



### <a name="crash-dump-analysis-capabilities-migrating-to-fileless-attack-detection"></a>Analys funktioner för krasch dumpning migrera till fil lös attack identifiering 

Vi integrerar Windows Crash dump Analysis-identifieringen (CDA) i en [fil med filbaserad attack identifiering](defender-for-servers-introduction.md#what-are-the-benefits-of-azure-defender-for-servers). Med filbaserad attack identifierings analys får du förbättrade versioner av följande säkerhets aviseringar för Windows-datorer: kod inmatning identifierad, maskerad Windows-modul identifierad, shellcode identifierad och misstänkt kod segment identifierat.

Några av fördelarna med den här över gången:

- **Proaktivt identifiering** av skadlig kod – den CDA-metod som väntar på att krascher ska inträffa och sedan kör analys för att hitta skadliga artefakter. Genom att använda filbaserad attack identifiering får du proaktiv identifiering av minnes minnes hot medan de körs. 

- **Omfattande aviseringar** – säkerhets aviseringar från fil igenkännings identifiering inkluderar berikade objekt som inte är tillgängliga från CDA, till exempel information om aktiva nätverks anslutningar. 

- **Varnings agg regering** – när CDA har identifierat flera angrepps mönster i en enda kraschdump utlöses det flera säkerhets aviseringar. Vid fil lös angrepp kombineras alla identifierade angrepps mönster från samma process till en enda avisering, vilket tar bort behovet av att korrelera flera aviseringar.

- **Lägre krav på din Log Analytics-arbetsyta** – krasch dum par som innehåller potentiellt känsliga data kommer inte längre att överföras till Log Analytics-arbetsytan.