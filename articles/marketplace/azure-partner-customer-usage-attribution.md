---
title: Attribution för kommersiell marketplace-partner och kundanvändning
description: Få en översikt över spårning av kundanvändning för Azure Marketplace-lösningar.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: dsindona
ms.openlocfilehash: e061baa8e7eb73bae0a78f4215f0d48610e85fcb
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686738"
---
# <a name="commercial-marketplace-partner-and-customer-usage-attribution"></a>Attribution för kommersiell marketplace-partner och kundanvändning

Attribution för kundanvändning är en metod för att associera Azure-resurser som körs i kundprenumerationer, som distribueras för att köra din lösning, med dig som partner. Genom att skapa dessa associationer i interna Microsoft-system får du större insyn i Azure-fotavtrycket som kör programvaran. När du använder den här spårningsfunktionen anpassar du dig till Microsofts säljteam och får kredit för Microsofts partnerprogram.

Du kan bilda associationen via Azure Marketplace, Snabbstartsdatabasen, privata GitHub-databaser och 1:1-kundeagemang som skapar hållbar IP (till exempel utveckling av en app).

Attribution för kundanvändning stöder tre distributionsalternativ:

- Azure Resource Manager-mallar: Partner kan använda Resource Manager-mallar för att distribuera Azure-tjänsterna för att köra partnerns programvara. Partner kan skapa en Resource Manager-mall för att definiera infrastrukturen och konfigurationen av sin Azure-lösning. Med en Resource Manager-mall kan du och dina kunder distribuera din lösning under hela livscykeln. Du kan vara säker på att dina resurser distribueras i ett konsekvent tillstånd.
- Azure Resource Manager API:er: Partner kan anropa Resource Manager-API:erna direkt för att distribuera en Resource Manager-mall eller generera API-anrop för att direkt etablera Azure-tjänster.
- Terraform: Partners kan använda Terraform för att distribuera en Resource Manager-mall eller distribuera Azure-tjänster direkt.

>[!IMPORTANT]
>- Attribution för kundanvändning är inte avsedd att spåra arbetet med systemintegratörer, hanterade tjänsteleverantörer eller verktyg som utformats för att distribuera och hantera programvara som körs på Azure.
>
>- Kundanvändningsattribution är för nya distributioner och stöder INTE taggning av befintliga resurser som redan har distribuerats.
>
>- Attribution för kundanvändning krävs för [Azure Application-erbjudanden](./partner-center-portal/create-new-azure-apps-offer.md) som publiceras på Azure Marketplace.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-guids"></a>Skapa GUID:er

En GUID är en unik referensidentifierare som har 32 hexadecimala siffror. Om du vill skapa GUID:er för spårning bör du använda en GUID-generator. Azure Storage-teamet har skapat ett [GUID-generatorformulär](https://aka.ms/StoragePartners) som skickar ett GUID-gränssnitt med rätt format och kan återanvändas över de olika spårningssystemen.

> [!NOTE]
> Vi rekommenderar starkt att du använder [Azure Storages GUID-generatorformulär](https://aka.ms/StoragePartners) för att skapa ditt GUID. För mer information, se vår [FAQ](#faq).

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributionskanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributionskanaler om du inte vill att rapporteringen ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub kan du skapa och registrera två olika GUIDS:

- Produkt A på Azure Marketplace
- Produkt A på GitHub

Rapporteringen görs av Microsoft Partner Network ID och GUID.

Du kan också spåra användning på en mer detaljerad nivå genom att registrera ytterligare GUID:er och ändra GUID:er mellan planer, där planer är varianter av ett erbjudande.

## <a name="register-guids"></a>Registrera GUID:er

GUID:erna måste vara registrerade i Partner Center för att kunna tillskrivas kundanvändning.

När du har lagt till ett GUID i mallen eller i användaragenten och registrerat GUID i Partner Center spåras framtida distributioner.

1. Logga in [på Partner Center](https://partner.microsoft.com/dashboard).

1. Registrera dig som en [kommersiell marknadsplats utgivare](https://aka.ms/JoinMarketplace).

   * Partner måste [ha en profil i Partner center](https://docs.microsoft.com/azure/marketplace/become-publisher). Du uppmanas att lista erbjudandet i Azure Marketplace eller AppSource.
   * Partner kan registrera flera GUID:er.
   * Partner kan registrera GUID för lösningsmallar och erbjudanden som inte är marknadsplats.

1. I det övre högra hörnet väljer du inställningsväxelikonen och väljer sedan **Utvecklarinställningar**.

1. På **sidan Kontoinställningar**väljer du **Lägg till GUID för spårning.**

1. Ange ditt guid-GUID i rutan **GUID.** Ange bara GUID utan **pid-prefixet.** Ange ditt erbjudandenamn eller beskrivning i rutan **Beskrivning.**

1. Om du vill registrera mer än ett GUID väljer du **Lägg till GUID för spårning** igen. Ytterligare rutor visas på sidan.

1. Välj **Spara**.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar
Många partnerlösningar distribueras med Azure Resource Manager-mallar. Om du har en Resource Manager-mall som är tillgänglig på Azure Marketplace, på GitHub eller som en snabbstart, är processen för att ändra mallen för att aktivera attribution för kundanvändning rakt fram.

> [!NOTE]
> Mer information om hur du skapar och publicerar lösningsmallar finns i
> * [Skapa och distribuera den första Resource Manager-mallen](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
>* [Azure-programerbjudande](./partner-center-portal/create-new-azure-apps-offer.md).
>* Video: [Skapa lösningsmallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


Om du vill lägga till en globalt unik identifierare (GUID) gör du en enda ändring av huvudmallfilen:

1. [Skapa ett GUID](#create-guids) med den föreslagna metoden och [registrera GUID](#register-guids).

1. Öppna mallen Resurshanteraren.

1. Lägg till en ny resurs i huvudmallfilen. Resursen måste bara finnas i **filen mainTemplate.json** eller **azuredeploy.json** och inte i några kapslade eller länkade mallar.

1. Ange GUID-värdet efter **pid-** prefixet (t.ex.

1. Kontrollera om det finns några fel i mallen.

1. Publicera om mallen i lämpliga databaser.

1. [Verifiera GUID-lyckad i malldistributionen](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exempel på resurshanterarens mallkod

Om du vill aktivera spårningsresurser för mallen måste du lägga till följande ytterligare resurs under avsnittet Resurser. Se till att ändra exempelkoden nedan med dina egna indata när du lägger till den i huvudmallfilen.
Resursen behöver bara läggas till i **filen mainTemplate.json** eller **azuredeploy.json** och inte i några kapslade eller länkade mallar.

```
// Make sure to modify this sample code with your own inputs where applicable

{ // add this resource to the resources section in the mainTemplate.json (do not add the entire file)
    "apiVersion": "2018-02-01",
    "name": "pid-XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX", // use your generated GUID here
    "type": "Microsoft.Resources/deployments",
    "properties": {
        "mode": "Incremental",
        "template": {
            "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
            "contentVersion": "1.0.0.0",
            "resources": []
        }
    }
} // remove all comments from the file when complete
```

## <a name="use-the-resource-manager-apis"></a>Använda API:erna för Resurshanteraren

I vissa fall kanske du föredrar att ringa samtal direkt mot RESOURCE Manager REST API:er för att distribuera Azure-tjänster. [Azure stöder flera SDK:er](https://docs.microsoft.com/azure/?pivot=sdkstools) för att aktivera dessa anrop. Du kan använda en av SDK:erna eller anropa REST-API:erna direkt för att distribuera resurser.

Om du använder en Resource Manager-mall bör du tagga lösningen genom att följa instruktionerna som beskrivs tidigare. Om du inte använder en Resource Manager-mall och gör direkta API-anrop kan du fortfarande tagga distributionen för att associera användningen av Azure-resurser.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tagga en distribution med Resource Manager-API:erna

Om du vill aktivera attribution för kundanvändning, när du utformar dina API-anrop, inkludera ett GUID i användaragenthuvudet i begäran. Lägg till GUID för varje erbjudande eller SKU. Formatera strängen med pid-prefixet och inkludera det partnergenererade GUID.Format the string with the **pid-** prefix and include the partner-generated GUID. Här är ett exempel på GUID-formatet för insättning i användaragenten:

![Exempel på GUID-format](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!NOTE]
> Strängens format är viktigt. Om **pid-prefixet** inte ingår är det inte möjligt att fråga efter data. Olika SDK-filer spårar olika. Om du vill implementera den här metoden läser du support- och spårningsmetoden för din önskade Azure SDK.

#### <a name="example-the-python-sdk"></a>Exempel: Python SDK

Använd **attributet config för** Python. Du kan bara lägga till attributet i en UserAgent. Här är ett exempel:

![Lägga till attributet i en användaragent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!NOTE]
> Lägg till attributet för varje klient. Det finns ingen global statisk konfiguration. Du kan tagga en klientfabrik för att vara säker på att alla klienter spårar. Mer information finns i det här [exemplet på klientfabriken på GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Tagga en distribution med hjälp av Azure PowerShell

Om du distribuerar resurser via Azure PowerShell lägger du till ditt GUID med hjälp av följande metod:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Tagga en distribution med hjälp av Azure CLI

När du använder Azure CLI för att lägga till ditt GUID anger du **AZURE_HTTP_USER_AGENT** miljövariabeln. Du kan ange den här variabeln inom ramen för ett skript. Du kan också ange variabeln globalt för skalomfattning:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Mer information finns i [Azure SDK for Go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Använd Terraform

Stödet för Terraform är tillgängligt via Azure Provider's 1.21.0-version: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Den här supporten gäller alla partner som distribuerar sin lösning via Terraform och alla resurser som distribueras och mäts av Azure Provider (version 1.21.0 eller senare).

Azure-provider för Terraform har lagt till ett nytt valfritt fält som heter [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) där du anger det guid för spårning som du använder för din lösning. Värdet för det här fältet kan också hämtas från *variabeln ARM_PARTNER_ID* miljö.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partner som vill få sin distribution via Terraform spåras av kundanvändning attribution måste göra följande:

* Skapa ett GUID (GUID ska läggas till för varje Erbjudande eller SKU)
* Uppdatera deras Azure-provider för att ställa in värdet *för partner_id* till GUID (INTE förkorrigering GUID med "pid-", bara ställa in den till den faktiska GUID)


## <a name="verify-the-guid-deployment"></a>Verifiera GUID-distributionen

När du har ändrat mallen och kört en testdistribution använder du följande PowerShell-skript för att hämta de resurser som du har distribuerat och taggat.

Du kan använda skriptet för att kontrollera att GUID har lagts till i resource manager-mallen. Skriptet gäller inte för Resource Manager API- eller Terraform-distributioner.

Logga in i Azure. Välj prenumerationen med den distribution som du vill verifiera innan du kör skriptet. Kör skriptet i prenumerationskontexten för distributionen.

**GUID-** och **resourceGroup-namnet** på distributionen är obligatoriska parametrar.

Du kan hämta [det ursprungliga skriptet](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) på GitHub.

```powershell
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzResourceGroupDeployment -ResourceGroupName
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Rapport

Du hittar rapporten för attribution för kundanvändning[https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)i instrumentpanelen i Partnercenter ( ). För att kunna se rapporten måste du använda dina partnercenteruppgifter för att logga in. Om du stöter på problem med rapporten eller logga in skapar du en supportbegäran enligt instruktionen i avsnittet Hämta support.

Välj Spårad mall i listrutan för partnerassociationstyp för att se rapporten.

![Rapport för attribution för kundanvändning](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Meddela dina kunder

Partner bör informera sina kunder om distributioner som använder attribution för kundanvändning. Microsoft rapporterar Azure-användningen som är associerad med dessa distributioner till partnern. Följande exempel är innehåll som du kan använda för att meddela dina kunder om dessa distributioner. I exemplen \<ersätter du PARTNER> med ditt företagsnamn. Partner bör se till att meddelandet överensstämmer med deras policyer för datasekretess och insamling, inklusive alternativ för kunder som ska uteslutas från spårning.

### <a name="notification-for-resource-manager-template-deployments"></a>Meddelanden för Resurshanterarens malldistributioner

När du distribuerar den här mallen \<kan Microsoft identifiera installationen av PARTNER> programvara med Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att ge de bästa upplevelserna med sina produkter och driva sin verksamhet. Uppgifterna samlas in och styrs av Microsofts sekretesspolicyer, https://www.microsoft.com/trustcentersom finns på .

### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande för SDK- eller API-distributioner

När du \<distribuerar PARTNER> programvara kan Microsoft identifiera \<installationen av PARTNER> programvara med Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att ge de bästa upplevelserna med sina produkter och driva sin verksamhet. Uppgifterna samlas in och styrs av Microsofts sekretesspolicyer, https://www.microsoft.com/trustcentersom finns på .

## <a name="get-support"></a>Få support

Det finns två supportkanaler beroende på vilka problem du står inför.

Om du stöter på problem i Partnercenter, till exempel att se attributionsrapporten för kundanvändning eller logga in, skapar du en supportbegäran med supportteamet för Partner center här:[https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Om du behöver hjälp med Marketplace Onboarding och/eller kundanvändningsattribution i allmänhet, till exempel hur du ställer in attributionen för kundanvändning, gör du följande steg:

1. Gå till [supportsidan](https://go.microsoft.com/fwlink/?linkid=844975).

1. Under **Problemtyp**väljer du **Marketplace Onboarding**.

1. Välj **kategori** för ditt problem:

   - För problem med användningsassociationen väljer du **Annat**.
   - Om du vill ha åtkomstproblem med Azure Marketplace väljer du **Åtkomstproblem**.

     ![Välj ärendekategori](media/marketplace-publishers-guide/lu-article-incident.png)

1. Välj **Starta begäran**.

1. På nästa sida anger du de värden som krävs. Välj **Fortsätt**.

1. På nästa sida anger du de värden som krävs.

   > [!IMPORTANT]
   > I rutan **Incidentrubrik** anger du **ISV Usage Tracking**. Beskriv problemet i detalj.

   ![Ange ISV-användningsspårning för incidenttiteln](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Fyll i formuläret och välj sedan **Skicka**.

Du kan också få teknisk vägledning från en teknisk konsult för Microsoft Partner för tekniska förköp, distribution och apputvecklingsscenarier för att förstå och införliva attribution för kundanvändning.

### <a name="how-to-submit-a-technical-consultation-request"></a>Så här skickar du in en begäran om tekniskt samråd

1. Besök [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Välj Molninfrastruktur och hantering så öppnas en ny sida där du kan visa den tekniska färden.
1. Klicka på knappen Skicka en begäran under Distributionstjänster
1. Logga in med ditt MSA-konto (MPN-konto) eller ditt AAD (Partner Dashboard-konto); baserat på dina inloggningsuppgifter öppnas ett onlineformulär för begäran:
    * Fyll i/granska kontaktinformationen.
    * Samrådsdetaljerna kan vara förifyllda eller välja bland list-downs.
    * Ange en rubrik och en beskrivning av problemet (ge så mycket information som möjligt).
1. Klicka på Skicka

Visa steg-för-steg-instruktioner med [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions)skärmdumpar på .

### <a name="whats-next"></a>Nästa steg

Du kommer att kontaktas av en teknisk konsult för Microsoft Partner för att konfigurera ett samtal för att begränsa dina behov.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad är fördelen med att lägga till GUID i mallen?**

Microsoft ger partner en vy över kunddistributioner av deras lösningar och insikter om deras påverkade användning. Både Microsoft och partnern kan använda den här informationen för att öka engagemanget mellan säljteamen. Både Microsoft och partnern kan använda data för att få en mer konsekvent bild av en enskild partners inverkan på Azure-tillväxten.

**När ett GUID har lagts till, kan det ändras?**

Ja, en kund eller implementeringspartner kan anpassa mallen och ändra eller ta bort GUID. Vi föreslår att partner proaktivt beskriver resursens och GUID:s roll för sina kunder och partner för att förhindra borttagning eller redigering av GUID. Om du ändrar GUID påverkas bara nya, inte befintliga, distributioner och resurser.

**Kan jag spåra mallar som distribuerats från en databas som inte kommer från Microsoft, till exempel GitHub?**

Ja, så länge GUID finns när mallen distribueras spåras användningen. Partner måste fortfarande registrera sina GUID.Partners must still register their GUIDs.

**Får kunden också rapportering?**

Kunder kan spåra sin användning av enskilda resurser eller kunddefinierade resursgrupper i Azure-portalen. Kunderna ser inte användning delas ut av GUID.

**Liknar denna metod den digitala partnern för Record (DPOR)?**

Den här nya metoden för att ansluta distributionen och användningen till en partners lösning ger en mekanism för att länka en partnerlösning till Azure-användning. DPOR är avsett att associera en konsult -(Systems Integrator) eller hanteringspartner (Managed Service Provider) med en kunds Azure-prenumeration.

**Vad är fördelen med att använda Azure Storages GUID Generator-formulär?**

Azure Storages GUID Generator-formulär garanteras att generera ett GUID-format för det format som krävs. Om du använder någon av Azure Storages metoder för spårning av dataplan kan du dessutom använda samma GUID för marketplace-kontrollplanspårning. På så sätt kan du utnyttja ett enhetligt guid för partnerattribution utan att behöva behålla separata GUIDS.

**Kan jag använda en privat, anpassad virtuell hårddisk för ett lösningsmallserbjudande på Azure Marketplace?**

Nej, det kan du inte. Avbildningen för den virtuella datorn måste [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines)komma från Azure Marketplace, se: .

Du kan skapa ett vm-erbjudande på marketplace med din anpassade virtuella hårddisk och markera det som privat så att ingen kan se det. Referens sedan till den här virtuella datorn i lösningsmallen.

**Det gick inte att uppdatera egenskapen *contentVersion* för huvudmallen?**

Sannolikt ett fel i vissa fall när mallen distribueras med hjälp av en TemplateLink från en annan mall som förväntar sig äldre contentVersion av någon anledning. Lösningen är att använda egenskapen metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
