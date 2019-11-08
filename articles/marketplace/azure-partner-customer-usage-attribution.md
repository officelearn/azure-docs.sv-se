---
title: Azure-partner och kund användnings behörighet | Azure Marketplace
description: Översikt över hur du spårar kund användning för Azure Marketplace-lösningar
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
author: yijenj
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 9/23/2019
ms.author: pabutler
ms.openlocfilehash: c84f5538d2f553a713b52aa795a10acddac9aff8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819893"
---
# <a name="azure-partner-customer-usage-attribution"></a>Spårning av kundanvändning för Azure-partner

Som en program varu partner för Azure kräver dina lösningar Azure-komponenter eller så måste de distribueras direkt i Azure-infrastrukturen. Kunder som distribuerar en partner lösning och etablerar sina egna Azure-resurser kan vara svåra att få insyn i distributionens status och få optik till påverkan på Azure-tillväxten. När du lägger till en högre Synlighets nivå kan du justera mot Microsofts försäljnings team och få kredit för Microsoft partner program.

Nu erbjuder Microsoft en metod för att hjälpa våra partner att bättre spåra Azures användning av kund distributioner av program varan på Azure. Den nya metoden använder Azure Resource Manager för att dirigera distributionen av Azure-tjänster.

Som Microsoft-partner kan du associera Azure-användning med alla Azure-resurser som du etablerar för en kunds räkning. Du kan skapa kopplingen via Azure Marketplace, snabb starts databasen, privata GitHub-lagringsplatser och en-till-ett-kund engagemang. Kund användnings behörighet stöder tre distributions alternativ:

- Azure Resource Manager mallar: partner kan använda Resource Manager-mallar för att distribuera Azure-tjänster för att köra partnerns program vara. Partner kan skapa en Resource Manager-mall för att definiera infrastrukturen och konfigurationen för deras Azure-lösning. Med en Resource Manager-mall kan du och dina kunder distribuera din lösning under hela livs cykeln. Du kan vara säker på att dina resurser distribueras i ett konsekvent tillstånd.
- Azure Resource Manager API: er kan anropa Resource Manager-API: er direkt för att distribuera en Resource Manager-mall eller generera API-anrop för att direkt etablera Azure-tjänster.
- Terraform: partner kan använda Cloud Orchestrator, till exempel terraform, för att distribuera en Resource Manager-mall eller Distribuera Azure-tjänster direkt.

Kund användnings behörighet är för ny distribution och stöder inte taggning av befintliga resurser som redan har distribuerats.

Kund användnings behörighet krävs på [Azure Application](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer): Solution Template-erbjudandet har publicerats på Azure Marketplace.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="use-resource-manager-templates"></a>Använd mallar för resurshanteraren
Många partner lösningar distribueras på en kunds prenumeration med hjälp av Resource Manager-mallar. Om du har en Resource Manager-mall som är tillgänglig på Azure Marketplace, på GitHub eller som en snabb start, ska processen för att ändra din mall för att aktivera kund användnings behörighet vara rak framåt.

Mer information om hur du skapar och publicerar Solution-mallar finns i

* [Skapa och distribuera din första Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal).
* [Azure Application erbjudande](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/azure-applications/cpp-azure-app-offer).
* Video: [skapa Solution-mallar och hanterade program för Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603).


## <a name="add-a-guid-to-your-template"></a>Lägg till ett GUID i mallen

Om du vill lägga till en globalt unik identifierare (GUID) gör du en enskild ändring av filen med huvud mal len:

1. [Skapa ett GUID](#create-guids) med den föreslagna metoden och [Registrera GUID](#register-guids-and-offers).

1. Öppna Resource Manager-mallen.

1. Lägg till en ny resurs i filen main Template. Resursen måste vara i **mainTemplate. JSON** -eller **azuredeploy. JSON** -filen och inte i några kapslade eller länkade mallar.

1. Ange GUID-värdet efter **PID-** prefixet (t. ex. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Kontrol lera om det finns några fel i mallen.

1. Publicera om mallen i lämpliga databaser.

1. [Verifiera att GUID lyckades i mal Lav distributionen](#verify-the-guid-deployment).

### <a name="sample-resource-manager-template-code"></a>Exempel på Resource Manager-mallkod

Om du vill aktivera spårning av resurser för mallen måste du lägga till följande ytterligare resurs under avsnittet resurser. Se till att ändra exempel koden nedan med dina egna indata när du lägger till den i filen för huvud mal len.
Resursen måste läggas till i **mainTemplate. JSON** -eller **azuredeploy. JSON** -filen och inte i någon kapslad eller länkad mall.

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

## <a name="use-the-resource-manager-apis"></a>Använda Resource Manager-API: er

I vissa fall kanske du föredrar att ringa direkt till Resource Manager REST-API: er för att distribuera Azure-tjänster. [Azure stöder flera SDK](https://docs.microsoft.com/azure/#pivot=sdkstools) : er för att aktivera dessa anrop. Du kan använda en av SDK: erna eller anropa REST-API: er direkt för att distribuera resurser.

Om du använder en Resource Manager-mall bör du tagga lösningen genom att följa anvisningarna ovan. Om du inte använder en Resource Manager-mall och gör direkta API-anrop kan du fortfarande tagga distributionen för att associera användningen av Azure-resurser.

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tagga en distribution med Resource Manager-API: er

Om du vill aktivera kund användnings behörighet ska du inkludera ett GUID i användar agent huvudet i begäran när du utformar dina API-anrop. Lägg till GUID för varje erbjudande eller SKU. Formatera strängen med **PID-** prefixet och inkludera GUID för den partner som genereras. Här är ett exempel på GUID-formatet för infogning i användar agenten:

![Exempel-GUID-format](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Strängens format är viktigt. Om **PID-** prefixet inte ingår är det inte möjligt att fråga efter data. Olika SDK: er spårar på olika sätt. Om du vill implementera den här metoden granskar du support-och spårnings metoden för din önskade Azure SDK.

#### <a name="example-the-python-sdk"></a>Exempel: python SDK

För python använder du **config** -attributet. Du kan bara lägga till attributet i en UserAgent. Här är ett exempel:

![Lägg till attributet i en användar agent](media/marketplace-publishers-guide/python-for-lu.PNG)

> [!Note]
> Lägg till attributet för varje klient. Det finns ingen global statisk konfiguration. Du kan tagga en klient fabrik för att se till att alla klienter spårar. Mer information finns i detta [klient fabriks exempel på GitHub](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79).

#### <a name="tag-a-deployment-by-using-the-azure-powershell"></a>Tagga en distribution med hjälp av Azure PowerShell

Om du distribuerar resurser via Azure PowerShell lägger du till ditt GUID med hjälp av följande metod:

```powershell
[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")
```

#### <a name="tag-a-deployment-by-using-the-azure-cli"></a>Tagga en distribution med hjälp av Azure CLI

När du använder Azure CLI för att lägga till ditt GUID, anger du **AZURE_HTTP_USER_AGENT** -miljövariabeln. Du kan ange den här variabeln inom omfånget för ett skript. Du kan också ställa in variabeln globalt för Shell-omfång:

```
export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'
```
Mer information finns i [Azure SDK för go](https://docs.microsoft.com/azure/go/).

## <a name="use-terraform"></a>Använd terraform

Support för terraform är tillgängligt via Azure providers 1.21.0-utgåva: [https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019](https://github.com/terraform-providers/terraform-provider-azurerm/blob/master/CHANGELOG.md#1210-january-11-2019).  Detta stöd gäller alla partner som distribuerar sin lösning via terraform, och alla resurser som distribueras och mäts av Azure-providern (version 1.21.0 eller senare).

Azure-providern för terraform har lagt till ett nytt valfritt fält med namnet [*partner_id*](https://www.terraform.io/docs/providers/azurerm/#partner_id) vilket är där du anger det spårnings-GUID som du använder för din lösning. Värdet för det här fältet kan också hämtas från *ARM_PARTNER_ID* -miljövariabeln.

```
provider "azurerm" {
          subscription_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          client_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
          ……
          # new stuff for ISV attribution
          partner_id = "xxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"}
```
Partner som vill få sin distribution via terraform som spåras av kund användnings behörighet måste göra följande:

* Skapa ett GUID (GUID ska läggas till för varje erbjudande eller SKU)
* Uppdatera Azure-providern för att ange värdet för *partner_id* till GUID (korrigera inte GUID med "PID-", och Ställ in det på det faktiska GUID)

## <a name="create-guids"></a>Skapa GUID

Ett GUID är ett unikt referens nummer som innehåller 32 hexadecimala siffror. Om du vill skapa GUID för spårning bör du använda en GUID-Generator. Azure Storages teamet har skapat ett [GUID Generator-formulär](https://aka.ms/StoragePartners) som kommer att skicka ett GUID till rätt format och kan återanvändas över olika spårnings system.

> [!Note]
> Vi rekommenderar starkt att du använder [Azure Storage s GUID Generator form](https://aka.ms/StoragePartners) för att skapa ditt GUID. Mer information finns i [vanliga frågor och svar](#faq).

Vi rekommenderar att du skapar ett unikt GUID för varje erbjudande och distributions kanal för varje produkt. Du kan välja att använda ett enda GUID för produktens flera distributions kanaler om du inte vill att rapportering ska delas.

Om du distribuerar en produkt med hjälp av en mall och den är tillgänglig på både Azure Marketplace och på GitHub, kan du skapa och registrera två distinkta GUID:

*   Produkt A på Azure Marketplace
*   Produkt A på GitHub

Rapportering görs av partner värdet (Microsoft partner-ID) och GUID.

Du kan också spåra GUID på en mer detaljerad nivå, till exempel SKU, där SKU: er är varianter av ett erbjudande.

## <a name="register-guids-and-offers"></a>Registrera GUID och erbjudanden

GUID måste registreras för att aktivera kund användnings behörighet.

Alla registreringar för mall-GUID görs i Partner Center.

När du har lagt till GUID i mallen eller i användar agenten och registrerat GUID i Partner Center, spåras alla distributioner.

1. Registrera dig som en [extern Marketplace-utgivare](https://aka.ms/JoinMarketplace).

   * Partner måste [ha en profil i Partner Center](https://docs.microsoft.com/azure/marketplace/become-publisher). Du uppmanas att ange erbjudandet i Azure Marketplace eller AppSource.
   * Partner kan registrera flera GUID.
   * Partner kan registrera ett GUID för mallarna och erbjudandena för icke-Marketplace-lösningar.

1. Logga in på [partner Center](https://partner.microsoft.com/dashboard).

1. I det övre högra hörnet väljer du kugg hjuls ikonen Inställningar och väljer sedan **Inställningar för utvecklare**.

1. På **sidan konto inställningar**väljer du **Lägg till spårnings-GUID.**

1. I rutan **GUID** anger du ditt spårnings-GUID. Ange bara GUID utan **PID-** prefix. I rutan **Beskrivning** anger du namnet eller beskrivningen för erbjudandet.

1. Om du vill registrera fler än ett GUID väljer du **Lägg till spårnings-GUID** igen. Fler rutor visas på sidan.

1. Välj **Spara**.


## <a name="verify-the-guid-deployment"></a>Verifiera GUID-distributionen

När du har ändrat mallen och kört en test distribution använder du följande PowerShell-skript för att hämta de resurser som du har distribuerat och taggat.

Du kan använda skriptet för att kontrol lera att GUID har lagts till i Resource Manager-mallen. Skriptet gäller inte för Resource Manager API eller terraform-distributioner.

Logga in i Azure. Välj den prenumeration med distributionen som du vill verifiera innan du kör skriptet. Kör skriptet i prenumerations kontexten för distributionen.

**GUID** och **resourceGroup** namn för distributionen är obligatoriska parametrar.

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

Du hittar rapporten för kund användnings behörighet i din partner Center analys instrument panel. ([https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure](https://partner.microsoft.com/dashboard/mpn/analytics/CPP/MicrosoftAzure)). För att kunna se rapporten måste du använda dina partner Center-autentiseringsuppgifter för att logga in. Om du stöter på problem med rapporten eller inloggningen skapar du en supportbegäran enligt anvisningarna i avsnittet få support.

Välj spårad mall i list rutan över partner associerings typ för att visa rapporten.

![Rapport för kund användnings behörighet](media/marketplace-publishers-guide/customer-usage-attribution-report.png)

## <a name="notify-your-customers"></a>Meddela kunderna

Partner bör informera sina kunder om distributioner som använder sig av kund användnings behörighet. Microsoft rapporterar Azure-användningen som är kopplad till dessa distributioner till partnern. Följande exempel innehåller innehåll som du kan använda för att meddela dina kunder om dessa distributioner. I exemplen ersätter du \<PARTNER > med företagets namn. Partner bör se till att meddelandet överensstämmer med sina principer för data sekretess och insamling, inklusive alternativ för kunder som ska undantas från spårning.

### <a name="notification-for-resource-manager-template-deployments"></a>Meddelande för distributioner av Resource Manager-mallar

När du distribuerar den här mallen kan Microsoft identifiera installationen av \<PARTNER > program vara med de Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som du hittar på https://www.microsoft.com/trustcenter.

### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande om SDK-eller API-distributioner

När du distribuerar \<PARTNER > program vara kan Microsoft identifiera installationen av \<PARTNER > program vara med de Azure-resurser som distribueras. Microsoft kan korrelera de Azure-resurser som används för att stödja program varan. Microsoft samlar in den här informationen för att ge bästa möjliga upplevelse av sina produkter och för att driva verksamheten. Data samlas in och regleras av Microsofts sekretess principer, som du hittar på https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Få support

Det finns två Support kanaler beroende på de problem som du är riktad mot.

Om du stöter på problem i partner centret, t. ex. genom att se rapport om kund användning eller logga in, kan du skapa en support förfrågan med support teamet för partner Center här: [https://partner.microsoft.com/support](https://partner.microsoft.com/support)

![](./media/marketplace-publishers-guide/partner-center-log-in-support.png)

Om du behöver hjälp med att registrera dig för Marketplace och/eller kund användning i allmänhet, till exempel hur du ställer in kund användnings behörighet, följer du stegen nedan:

1. Gå till [support Sidan](https://go.microsoft.com/fwlink/?linkid=844975).

1. Välj **Marketplace-onboarding**under **problem typ**.

1. Välj **kategori** för ditt problem:

   - För användnings Kopplings problem väljer du **annat**.
   - För åtkomst problem med Azure Marketplace CPP väljer du **åtkomst problem**.

     ![Välj ärende kategori](media/marketplace-publishers-guide/lu-article-incident.png)

1. Välj **Start förfrågan**.

1. Ange de värden som krävs på nästa sida. Välj **Fortsätt**.

1. Ange de värden som krävs på nästa sida.

   > [!Important]
   > I rutan **incident rubrik** anger du **användnings spårning för ISV**. Beskriv problemet i detalj.

   ![Ange användnings spårning i ISV för incident rubriken](media/marketplace-publishers-guide/guid-dev-center-help-hd%201.png)

1. Fyll i formuläret och välj sedan **Skicka**.

Du kan också få teknisk vägledning från en Microsoft-partner teknisk konsult för tekniska försäljnings-, distributions-och app Development-scenarier för att förstå och införliva kund användnings behörighet.

### <a name="how-to-submit-a-technical-consultation-request"></a>Så här skickar du en begäran om tekniskt samråd

1. Besök [https://aka.ms/TechnicalJourney](https://aka.ms/TechnicalJourney).
1. Välj moln infrastruktur och hantering, så öppnas en ny sida där du kan se den tekniska resan.
1. Under distributions tjänster klickar du på knappen Skicka en förfrågan
1. Logga in med ditt MSA (MPN-konto) eller ditt AAD-konto (partner instrument panel). baserat på dina inloggnings uppgifter öppnas ett formulär för online-begäran:
    * Slutför/granska kontakt uppgifterna.
    * Information om konsultationen kan vara ifylld eller väljas i list rutan.
    * Ange en rubrik och en beskrivning av problemet (ange så mycket information som möjligt).
1. Klicka på Skicka

Visa steg-för-steg-instruktioner med skärm dum par på [https://aka.ms/TechConsultInstructions](https://aka.ms/TechConsultInstructions).

### <a name="whats-next"></a>Nästa steg

Du kommer att kontaktas av en teknisk konsult från Microsoft-partner för att skapa ett samtal för att tillgodose dina behov.

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad är fördelen med att lägga till GUID i mallen?**

Microsoft tillhandahåller partner med en överblick över kund distributioner av lösningar och insikter om deras påverkan på användningen. Både Microsoft och partnern kan använda den här informationen för att driva närmare engagemang mellan sälj team. Både Microsoft och partnern kan använda dessa data för att få en mer enhetlig vy av en enskild partners påverkan på Azure-tillväxten.

**Kan den ändras när ett GUID har lagts till?**

Ja, en kund-eller implementerings partner kan anpassa mallen och kan ändra eller ta bort GUID. Vi rekommenderar att partners proaktivt beskriver rollen för resursen och GUID för sina kunder och partners för att förhindra borttagning eller redigering av GUID. Att ändra GUID påverkar endast nya, inte befintliga, distributioner och resurser.

**Kan jag spåra mallar som har distribuerats från en annan databas än Microsoft, t. ex. GitHub?**

Ja, så länge som GUID finns när mallen distribueras, spåras användningen. Partner måste ha en profil i CPP för att registrera GUID som används för distributionen utanför Azure Marketplace.

**Kan kunden även ta emot rapportering?**

Kunder kan spåra användningen av enskilda resurser eller kunddefinierade resurs grupper inom Azure Portal.

**Liknar den här metoden en digital partner av Record (DPOR)?**

Den här nya metoden för att ansluta distribution och användning till en partners lösning är en mekanism för att länka en partner lösning till Azure-användning. DPOR är avsett att koppla en konsult (Systems Integrator) eller hanterings partner (hanterad tjänst leverantör) till en kunds Azure-prenumeration.

**Vad är fördelarna med att använda Azure Storages GUID Generator form?**

Azure Storages formulär för GUID-Generator garanterar att ett GUID för det begärda formatet genereras. Om du använder någon av Azure Storages spårnings metoder för data planet kan du dessutom använda samma GUID för spårning av plats kontroll planet. På så sätt kan du använda en enda enhetlig GUID för partner tilldelning utan att behöva ha separata GUID.

**Kan jag använda en privat, anpassad VHD för en lösnings mal len-erbjudande på Azure Marketplace?**

Nej, du kan inte. Avbildningen av den virtuella datorn måste komma från Azure Marketplace, se: [https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines](https://docs.microsoft.com/azure/marketplace/marketplace-virtual-machines).

Du kan skapa ett virtuellt dator erbjudande i Marketplace med din anpassade virtuella hård disk och markera det som privat så att ingen kan se den. Referera till den här virtuella datorn i din lösnings mall.

**Det gick inte att uppdatera *contentVersion* -egenskapen för huvud mal len?**

Troligen en bugg i vissa fall när mallen distribueras med hjälp av en TemplateLink från en annan mall som förväntar sig äldre contentVersion av någon anledning. Lösningen är att använda egenskapen metadata:

```
"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "metadata": {
        "contentVersion": "1.0.1.0"
    },
    "parameters": {
```
