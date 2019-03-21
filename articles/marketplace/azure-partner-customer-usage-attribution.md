---
title: Azure attribution för användning av partners och kunder
description: Översikt över hur du spårar KUNDANVÄNDNING för Azure Marketplace-lösningar
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: yijenj
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/17/2018
ms.author: yijenj
ms.openlocfilehash: ae31aa19e5905ccafcd0161ea362cc68234f8295
ms.sourcegitcommit: ab6fa92977255c5ecbe8a53cac61c2cd2a11601f
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/20/2019
ms.locfileid: "58294764"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partner kundens användning attribution

Dina lösningar kräver Azure-komponenter som programvara partner för Azure, eller de behöver för att distribueras direkt på Azure-infrastrukturen. Kunder som distribuerar en partnerlösning och etablera sina egna Azure-resurser kan vara svårt att få insyn i statusen för distributionen och få optik i påverkan på Azure tillväxt. När du lägger till en högre nivå av synlighet överensstämmer med Microsofts säljteam och få kredit för Microsoft partner-program. 

Microsoft erbjuder nu en metod för att hjälpa partner att bättre spåra Azure-användning av kunders distributioner av sin programvara på Azure. Den nya metoden använder Azure Resource Manager för att dirigera distributionen av Azure-tjänster.

Som Microsoft-partner kan associera du Azure-användning med alla Azure-resurser som du etablerar för en kunds räkning. Du kan skapa kopplingen via Azure Marketplace, Snabbstart-databasen, privat GitHub-databaser och personlig Kundengagemang. Kundens användning attribution stöder tre alternativ:

- Azure Resource Manager-mallar: Partner kan använda Resource Manager-mallar för att distribuera Azure-tjänster för att köra partnerns programvara. Partner kan skapa en Resource Manager-mall för att definiera infrastrukturen och konfigurationen av sina Azure-lösning. Resource Manager-mall kan du och dina kunder att distribuera din lösning under dess livscykel. Du kan vara säker på att resurserna distribueras i ett konsekvent tillstånd. 
- Azure Resource Manager API: er: Partner kan anropa Resource Manager API: er direkt för att distribuera en Resource Manager-mall eller att generera API-anrop till direkt etablera Azure-tjänster. 
- Terraform: Partner kan använda molnet initierare som Terraform att distribuera en Resource Manager-mall eller distribuera direkt Azure-tjänster. 

Kundens användning attribution är för ny distribution och har inte stöd för taggning befintliga resurser som redan har distribuerats.

Kundens användning attribution krävs på [Azure-program](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): lösning mall erbjudandet publicerats på Azure Marketplace.

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar

Många partnerlösningar distribueras på en kunds prenumeration med hjälp av Resource Manager-mallar. Om du har en Resource Manager-mall som är tillgänglig i Azure Marketplace, på GitHub eller som en Snabbstart ska processen för att ändra din mall för att aktivera kundens användning attribution vara enkelt.

Läs mer om att skapa och publicera Lösningsmallar

* [Skapa och distribuera din första Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Azure Application-erbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [Att skapa mallar för lösningar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Lägga till ett GUID i mallen

Om du vill lägga till en globalt unik identifierare (GUID), kan du göra en enda ändring huvudsakliga mallfilen:

1. [Skapa ett GUID](#create-guids) med metoden föreslagna och [registrera GUID](#register-guids-and-offers).

1. Öppna Resource Manager-mallen.

1. Lägg till en ny resurs i den huvudsakliga mallfilen. Resursen måste finnas i den **mainTemplate.json** eller **azuredeploy.json** filen endast, och inte i någon kapslad eller länkad mallar.

1. Ange GUID-värde efter den **pid -** prefix (t.ex. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Kontrollera mallen innehåller fel.

1. Publicera om mallen i lämpliga databaser.

1. [Kontrollera GUID i malldistributionen](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exemplet Resource Manager-mallkod

Du måste lägga till följande ytterligare resurs under resursavsnittet för att aktivera spårning resurser för din mall. Glöm inte att ändra den nedan exempelkod med egna indata när du lägger till det huvudsakliga mallfilen.
Resursen måste läggas till i den **mainTemplate.json** eller **azuredeploy.json** filen endast, och inte i någon kapslad eller länkad mallar.
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

## <a name="use-the-resource-manager-apis"></a>API: er i Resource Manager

I vissa fall kanske du föredrar att göra anrop direkt mot Resource Manager REST API: er för att distribuera Azure-tjänster. [Azure har stöd för flera SDK: er](https://docs.microsoft.com/azure/#pivot=sdkstools) att aktivera dessa anrop. Du kan använda någon av de SDK: er eller anropa REST-API: er direkt om du vill distribuera resurser.

Om du använder Resource Manager-mall ska du tagga din lösning genom att följa anvisningarna ovan. Om du inte använder en Resource Manager-mall och att direkta API-anrop, kan du fortfarande tagga distributionen om du vill associera användningen av Azure-resurser. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tagga en distribution med Resource Manager-API: er

Om du vill aktivera KUNDANVÄNDNING attribution, när du utformar din API-anrop, inkludera ett GUID i användaren agent huvudet i begäran. Lägg till GUID för varje erbjudande eller SKU. Formatera strängen med den **pid -** prefix och inkludera GUID som genereras av partner. Här är ett exempel på GUID-format för infogning i användaragenten: 

![Exempelformat för GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Formatet för strängen är viktigt. Om den **pid -** prefix är inte inkluderade, det går inte att fråga efter data. Olika SDK: er spåra på olika sätt. Granska stöd och spåra metoden för din önskade Azure SDK för att implementera den här metoden. 

#### <a name="example-the-python-sdk"></a>Exempel: The Python SDK

För Python och använder den **config** attribut. Du kan bara lägga till attributet till en UserAgent. Här är ett exempel:

![Lägga till attributet i en användaragent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Lägg till attribut för varje klient. Det finns ingen global statisk konfiguration. Du kan tagga en klientfabrik för att se till att spåra alla klienter. Mer information finns i den här [klienten factory exemplet på GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Tagga en distribution med hjälp av Azure PowerShell

Om du har distribuerat resurser via Azure PowerShell kan du lägga till din GUID med hjälp av följande metod:

```
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Tagga en distribution med hjälp av Azure CLI

När du använder Azure CLI för att lägga till din GUID, ange den **AZURE_HTTP_USER_AGENT** miljövariabeln. Du kan ange den här variabeln inom omfånget för ett skript. Du kan också ange variabeln globalt för shell omfång:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Mer information finns i [Azure SDK för Go](https://docs.microsoft.com/go/azure/).

## <a name="use-terraform"></a>Använd Terraform

Stöd för Terraform är tillgänglig via Azure-providern 1.21.0 viktig: [ https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019 ](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Detta stöd gäller för alla partner som distribuerar lösningen via Terraform och alla resurser distribueras och förbrukade av Azure-providern (version 1.21.0 eller senare).

Azure Terraform-providern har lagts till ett nytt valfritt fält som kallas [ *partner_id* ](https://www.terraform.io/docs/providers/azurerm/#partner_id) där du kan ange spårnings GUID som du använder för din lösning. Värdet för det här fältet kan också hämtas från den *ARM_PARTNER_ID* miljövariabel.

```
provider "azurerm" { 
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx" 
          …… 
          # new stuff for ISV attribution
          partner_id = “xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partners som vill få deras distribution via Terraform spåras av kundens användning attribution måste du göra följande:

* Skapa en GUID (GUID ska läggas till för varje erbjudande eller SKU)
* Uppdatera sina Azure-providern för att ställa in värdet för *partner_id* till GUID (inte före korrigering GUID med ”pid-”, bara ange den till det faktiska GUID)

## <a name="create-guids"></a>Skapa GUID

Ett GUID är ett unikt referensnummer med 32 hexadecimala siffror. För att skapa GUID för spårning, bör du använda en GUID-generator. Azure Storage-teamet har skapat en [GUID generator formuläret](https://aka.ms/StoragePartners) att skicka en GUID för korrekt format och kan återanvändas i olika spårnings-system. 

> [!Note]
> Vi rekommenderar starkt att du använder [Azure Storage GUID generator formuläret](https://aka.ms/StoragePartners) att skapa din GUID. Mer information finns i vår [vanliga frågor och svar](#faq).

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distribution kanal för varje produkt. Du kan välja för att använda en enda GUID för produktens flera distributionskanaler om du inte vill att rapportering ska delas. 

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera 2 olika GUID:

*   Produkten A i Azure Marketplace 
*   Produkten A på GitHub

Rapportering sker genom att värdet för partner (Microsoft Partner-ID) och GUID. 

Du kan också spåra GUID på en mer detaljerad nivå som SKU: N, där SKU: er är varianter av ett erbjudande.

## <a name="register-guids-and-offers"></a>Registrera GUID och erbjudanden

GUID måste vara registrerad för att aktivera attribution för användning av kunden.

Alla registreringar för mall-GUID görs via Azure Marketplace Cloud Partner Portal (CPP). 

När du lägger till GUID i mallen eller i användaragenten och registrera GUID i CPP, spåras alla distributioner. 

1. Gäller för [Azure Marketplace](https://aka.ms/listonazuremarketplace) och få tillgång till CPP.

   * Partner som krävs för att [har en profil i CPP](https://docs.microsoft.com/azure/marketplace/become-publisher). Är du rekommenderas att erbjudandet i Azure Marketplace eller AppSource-lista.
   * Partner kan registrera flera GUID: er.
   * Partner kan registrera ett GUID för mallar för inte finns på Marketplace-lösningar och erbjudanden.
 
1. Logga in på den [Cloud Partner Portal](https://cloudpartner.azure.com/).

1. Välj din ikon i det övre högra hörnet och välj sedan **utgivarprofil**.

   ![Välj utgivarprofil](media/marketplace-publishers-guide/guid-image-for-lu.png)

1. På den **profilsida**väljer **lägga till spårnings-GUID.**

   ![Välj Lägg till spårning GUID](media/marketplace-publishers-guide/guid-how-to-add-tracking.png)

1. I den **spåra GUID** anger din spårning GUID. Ange bara GUID utan den **pid -** prefix. I den **anpassad beskrivning** anger du ditt erbjudandenamn eller beskrivning.

   ![Profilsida](media/marketplace-publishers-guide/guid-dev-center-login.png)
   
   ![Ange GUID och erbjuder beskrivning](media/marketplace-publishers-guide/guid-dev-center-example.png)

1. Om du vill registrera mer än en GUID, Välj **lägga till spårnings-GUID** igen. Ytterligare rutor visas på sidan.

   ![Välj Lägg till spårning GUID igen](media/marketplace-publishers-guide/guid-dev-center-example-add.png)
   
   ![Ange ett annat GUID och erbjuder beskrivning](media/marketplace-publishers-guide/guid-dev-center-example-description.png)

1. Välj **Spara**.

   ![Klicka på Spara](media/marketplace-publishers-guide/guid-dev-center-save.png)

När du lägger till GUID i mallen eller i användaragenten och registrera GUID i CPP, spåras alla distributioner. 

## <a name="verify-the-guid-deployment"></a>Kontrollera GUID-distributionen 

När du ändrar din mall och kör en test-distribution kan du använda följande PowerShell-skript för att hämta de resurser som du har distribuerat och taggade. 

Du kan använda skriptet för att kontrollera att GUID som har lagts till Resource Manager-mallen. Skriptet gäller inte för Resource Manager API-distribution.

Logga in i Azure. Välj prenumerationen med distributionen som du vill kontrollera innan du kör skriptet. Kör skriptet inom ramen för prenumerationen för distributionen.

Den **GUID** och **resourceGroup** namnet på distributionen är obligatoriska parametrar.

Du kan hämta [det ursprungliga skriptet](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1) på GitHub.

```
Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName
)

# Get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

# Find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

# Find all deploymentOperations in a deployment by name
# PowerShell doesn't surface outputResources on the deployment
# or correlationId on the deploymentOperation

foreach ($deployment in $deployments){

# Get deploymentOperations by deploymentName
# then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}
```

## <a name="report"></a>Rapport

Du hittar i rapporten för kundens användning attribution på Partner Center analysera instrumentpanelen. ([https://partner.microsoft.com/en-us/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)).

Välj spåras mall i den nedrullningsbara listan med Partner associationstypen vill visa rapporten.

![Rapport för kundens användning attribution](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Meddela dina kunder

Partner bör informera kunderna om distributioner som använder kundens användning attribution. Microsoft rapporterar den Azure-användning som associeras med dessa distributioner till partnern. I följande exempel innehåller innehåll som du kan använda för att informera kunderna om dessa distributioner. I exemplen är ersätter \<PARTNER > med namnet på ditt företag. Partner bör kontrollera att meddelandet som överensstämmer med deras data sekretess och samling principer, inklusive alternativ för kunder som ska undantas från spårning. 

### <a name="notification-for-resource-manager-template-deployments"></a>Meddelande för Resource Manager malldistributioner

När du distribuerar den här mallen kan Microsoft kan identifiera installationen av \<PARTNER > programvara med Azure-resurser som har distribuerats. Microsoft kan kombinera Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Data samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande för SDK eller API-distributioner

När du distribuerar \<PARTNER > programvara, Microsoft kan identifiera installationen av \<PARTNER > programvara med Azure-resurser som har distribuerats. Microsoft kan kombinera Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Data samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Få support

Följ dessa steg om du behöver hjälp för Marketplace Onboarding och/eller attribution för användning av kunden.

1. Gå till den [supportsidan](https://go.microsoft.com/fwlink/?linkid=844975). 

1. Under **problemtyp**väljer **Marketplace Onboarding**.

1. Välj den **kategori** för problemet:

   - Association användningsproblem, Välj **andra**.
   - Åtkomstproblem med Azure Marketplace CPP, Välj **problem med anslutningen**.
   
     ![Välj problem-kategori](media/marketplace-publishers-guide/lu-article-incident.png)

1. Välj **Startförfrågan**.

1. Ange värdena som krävs på nästa sida. Välj **Fortsätt**.

1. Ange värdena som krävs på nästa sida.

   > [!Important] 
   > I den **titeln Incident** anger **ISV användning spåra**. Beskrivning av problemet i detalj.
   
   ![Ange ISV användning spårning för incidentrubrik](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Fyll i formuläret och välj sedan **skicka**.

Du kan också få teknisk vägledning från Microsoft Partner teknisk konsult för tekniska före försäljning, distribution och app utvecklingsscenarier måste förstå och infoga attribution för användning av kunden.

### <a name="how-to-submit-a-technical-consultation-request"></a>Hur du skickar en begäran om teknisk samråd

1. Besök [ https://aka.ms/TechnicalJourney ](https://aka.ms/TechnicalJourney).
1. Välj moln-infrastruktur och hantering samt en ny sida öppnas om du vill visa den tekniska resan.
1. Klicka på Skicka en begäran-knappen under Deployment Services
1. Logga in med ditt MSA (MPN-konto) eller din AAD (konto på instrumentpanelen för Partner;) baserat på inloggningen autentiseringsuppgifter, öppnas en begäran om online-formuläret: 
    * Slutför/granska kontaktinformation.
    * Samråd information kan fyllas eller välja från listrutorna.
    * Ange en rubrik och beskrivning av problemet (ange så mycket information som möjligt).
1. Klicka på Skicka

Visa instruktioner med skärmdumpar på [ https://aka.ms/TechConsultInstructions ](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Vad händer nu

Du kommer att kontaktas av en Microsoft-Partner teknisk konsult för att ställa in ett anrop till omfång för dina behov.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad är fördelen med att lägga till GUID för mallen?**

Microsoft ger partner med en vy av kunders distributioner av sina lösningar och insikter på deras influenced användning. Både Microsoft och partner kan använda denna information för att öka närmare engagemanget mellan försäljningsteam för hjälp. Både Microsoft och partner kan använda data för att få en mer konsekvent överblick över en partner påverkan på Azure tillväxt. 

**När ett GUID har lagts till, kan det ändras?**
 
Ja, en kund eller implementering partner kan anpassa mallen och kan ändra eller ta bort GUID. Vi rekommenderar att partner proaktivt beskriver rollen för resursen och GUID för sina kunder och partner som förhindrar borttagning eller ändringar till GUID. Om du ändrar GUID påverkas endast nya, inte befintliga distributioner och resurser.

**Kan jag spåra mallar distribueras från en icke-Microsoft-lagringsplats som GitHub?**

Ja, så länge som GUID som är tillgängliga när mallen distribueras användning spåras. Partner måste ha en profil i CPP att registrera GUID som används för distribution utanför Azure Marketplace. 

**Får kunden reporting samt?**

Kunder kan spåra deras användning av enskilda resurser eller kunddefinierad resursgrupper i Azure-portalen.   

**Påminner om den här metoden att den Digital Partner of Record (DPOR)?**

Den nya metoden för att ansluta distribution och användning till en partnerlösning är en mekanism för att länka en partnerlösning till Azure-användning. DPOR är avsedd att associera en konsult (systemintegratör) eller hanteringspartnern (Managed Service Provider) med en kunds Azure-prenumeration.   

**Vad är fördelen för Azure Storage GUID Generator formatet?**

Azure Storage GUID Generator formuläret garanteras att generera en GUID för formatet som krävs. Om du använder någon av Azure Storage-dataplanet spåra metoder, kan du dessutom använda samma GUID för Marketplace kontrollplanet spårning. På så sätt kan du utnyttja ett en enkel och enhetlig GUID för Partner attribution utan att behöva underhålla olika GUID.

**Kan jag använda en privat, anpassade virtuell Hårddisk till en lösning för mallen i Azure Marketplace?**

Nej, du kan inte. Avbildningen av virtuella datorn måste komma från Azure Marketplace, se: [ https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines ](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines). 

Du kan skapa en VM-erbjudandet i marketplace med hjälp av din anpassade virtuella Hårddisk och markeras som privat så att ingen kan se den. Sedan referens till den här virtuella datorn i din mall.

**Det gick inte att uppdatera *mallegenskapen* egenskapen för den huvudsakliga mallen?**

Troligen en bugg i vissa fall när mallen distribueras med hjälp av en TemplateLink från en annan mall som räknar äldre mallegenskapen av någon anledning. Lösningen är att använda metadata-egenskap:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```