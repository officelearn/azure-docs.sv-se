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
ms.date: 10/15/2018
ms.author: yijenj
ms.openlocfilehash: a0b3c220a1cd857bc8bea0eb5ab41625845fcc5d
ms.sourcegitcommit: 6361a3d20ac1b902d22119b640909c3a002185b3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2018
ms.locfileid: "49365636"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partner kundens användning attribution

Dina lösningar kräver Azure-komponenter som programvara partner för Azure, eller de behöver för att distribueras direkt på Azure-infrastrukturen. Kunder som distribuerar en partnerlösning och etablera sina egna Azure-resurser kan vara svårt att få insyn i statusen för distributionen och få optik i påverkan på Azure tillväxt. När du lägger till en högre nivå av synlighet överensstämmer med Microsofts säljteam och få kredit för Microsoft partner-program.   

Microsoft erbjuder nu en metod för att hjälpa partner att bättre spåra Azure-användning av kunders distributioner av sin programvara på Azure. Den nya metoden använder Azure Resource Manager för att dirigera distributionen av Azure-tjänster.

Som Microsoft-partner kan associera du Azure-användning med alla Azure-resurser som du etablerar för en kunds räkning. Du kan skapa kopplingen via Azure Marketplace, Snabbstart-databasen, privat GitHub-databaser och personlig Kundengagemang. Om du vill aktivera spårning finns två sätt:

- Azure Resource Manager-mallar: Resource Manager-mallar eller lösningsmallar att distribuera Azure-tjänster för att köra partnerns programvara. Partner kan skapa en Resource Manager-mall för att definiera infrastrukturen och konfigurationen av sina Azure-lösning. Resource Manager-mall kan du och dina kunder att distribuera din lösning under dess livscykel. Du kan vara säker på att resurserna distribueras i ett konsekvent tillstånd. 

- Azure Resource Manager API: er: Partner kan anropa Resource Manager API: er direkt för att distribuera en Resource Manager-mall eller att generera API-anrop till direkt etablera Azure-tjänster. 

## <a name="use-resource-manager-templates"></a>Använda Resource Manager-mallar

Många partnerlösningar distribueras på en kunds prenumeration med hjälp av Resource Manager-mallar. Om du har en Resource Manager-mall som är tillgänglig i Azure Marketplace, på GitHub eller som en Snabbstart ska processen för att ändra din mall för att aktivera den nya spårnings-metoden vara enkelt. Om du inte använder en Azure Resource Manager-mall, är här några länkar som hjälper dig att bättre förstå Resource Manager-mallar och hur du skapar en: 

*   [Skapa och distribuera din första Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Skapa en lösningsmall för Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="add-a-guid-to-your-template"></a>Lägga till ett GUID i mallen

Om du vill lägga till en globalt unik identifierare (GUID), kan du göra en enda ändring huvudsakliga mallfilen:

1. [Skapa ett GUID](#create-guids) (t.ex. eb7927c8-dd66-43e1-b0cf-c346a422063) och [registrera GUID](#register-guids-and-offers).

1. Öppna Resource Manager-mallen.

1. Lägg till en ny resurs i den huvudsakliga mallfilen. Resursen måste finnas i den **mainTemplate.json** eller **azuredeploy.json** filen endast, och inte i någon kapslad eller länkad mallar.

1. Ange GUID-värde efter den **pid -** prefix (t.ex. pid-eb7927c8-dd66-43e1-b0cf-c346a422063).

1. Kontrollera mallen innehåller fel.

1. Publicera om mallen i lämpliga databaser.

1. [Kontrollera GUID i malldistributionen](#verify-the-guid-deployment).

### <a name="sample-template-code"></a>Exempelkod för mallen

![Exempelkod för mallen](media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG)

## <a name="use-the-resource-manager-apis"></a>API: er i Resource Manager

I vissa fall kanske du föredrar att göra anrop direkt mot Resource Manager REST API: er för att distribuera Azure-tjänster. [Azure har stöd för flera SDK: er](https://docs.microsoft.com/azure/#pivot=sdkstools) att aktivera dessa anrop. Du kan använda någon av de SDK: er eller anropa REST-API: er direkt om du vill distribuera resurser.

Om du använder Resource Manager-mall ska du tagga din lösning genom att följa anvisningarna ovan. Om du inte använder en Resource Manager-mall och att direkta API-anrop, kan du fortfarande tagga distributionen om du vill associera användningen av Azure-resurser. 

### <a name="tag-a-deployment-with-the-resource-manager-apis"></a>Tagga en distribution med Resource Manager-API: er

Inkludera ett GUID i användaren agent huvudet i begäran för den här spårnings-metoden när du utformar din API-anrop. Lägg till GUID för varje erbjudande eller SKU. Formatera strängen med den **pid -** prefix och inkludera GUID som genereras av partner. Här är ett exempel på GUID-format för infogning i användaragenten: 

![Exempelformat för GUID](media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG)

> [!Note]
> Formatet för strängen är viktigt. Om den **pid -** prefix är inte inkluderade, det går inte att fråga efter data. Olika SDK: er spåra på olika sätt. Granska stöd och spåra metoden för din önskade Azure SDK för att implementera den här metoden. 

### <a name="example-the-python-sdk"></a>Exempel: Python SDK

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

## <a name="create-guids"></a>Skapa GUID

Ett GUID är ett unikt referensnummer med 32 hexadecimala siffror. För att skapa GUID för spårning, bör du använda en GUID-generator. Vi rekommenderar att du utnyttjar [Azure Storage GUID generator formuläret](https://aka.ms/StoragePartners). Men om du inte föredrar att använda Azure Storage GUID generator, det finns flera [online GUID generatorer](https://www.bing.com/search?q=guid%20generator) som du kan använda.

> [!Note]
> Det är mycket rekommenderar att du använder [Azure Storage GUID generator formuläret](https://aka.ms/StoragePartners) att skapa din GUID. Mer information finns i vår [vanliga frågor och svar](#faq).

Skapa ett unikt GUID för varje erbjudande och distribution kanal. Om du distribuerar två lösningar med hjälp av en mall och var och en är tillgänglig på Azure Marketplace och på GitHub, måste du skapa fyra GUID:

*   Erbjud en i Azure Marketplace 
*   Erbjud en på GitHub
*   Erbjudandet B i Azure Marketplace 
*   Erbjudandet B på GitHub

Rapportering sker genom att värdet för partner (Microsoft Partner-ID) och GUID. 

Du kan också spåra GUID på en mer detaljerad nivå som SKU: N, där SKU: er är varianter av ett erbjudande.

## <a name="register-guids-and-offers"></a>Registrera GUID och erbjudanden

GUID för att inkludera ett GUID i vår spårning, måste vara registrerad.  

Alla registreringar för mall-GUID görs via Azure Marketplace Cloud Partner Portal (CPP). 

När du lägger till GUID i mallen eller i användaragenten och registrera GUID i CPP, spåras alla distributioner. 

1. Gäller för [Azure Marketplace](http://aka.ms/listonazuremarketplace) och få tillgång till CPP.

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

## <a name="notify-your-customers"></a>Meddela dina kunder

Partner bör informera kunderna om distributioner som använder Resource Manager-GUID spårning. Microsoft rapporterar den Azure-användning som associeras med dessa distributioner till partnern. I följande exempel innehåller innehåll som du kan använda för att informera kunderna om dessa distributioner. I exemplen är ersätter \<PARTNER > med namnet på ditt företag. Partner bör kontrollera att meddelandet som överensstämmer med deras data sekretess och samling principer, inklusive alternativ för kunder som ska undantas från spårning. 

### <a name="notification-for-resource-manager-template-deployments"></a>Meddelande för Resource Manager malldistributioner

När du distribuerar den här mallen kan Microsoft kan identifiera installationen av \<PARTNER > programvara med Azure-resurser som har distribuerats. Microsoft kan kombinera Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Data samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter. 

### <a name="notification-for-sdk-or-api-deployments"></a>Meddelande för SDK eller API-distributioner

När du distribuerar \<PARTNER > programvara, Microsoft kan identifiera installationen av \<PARTNER > programvara med Azure-resurser som har distribuerats. Microsoft kan kombinera Azure-resurser som används för att stödja programvaran. Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Data samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter.

## <a name="get-support"></a>Få support

Följ dessa steg om du behöver hjälp.

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

## <a name="faq"></a>VANLIGA FRÅGOR OCH SVAR

**Vad är fördelen med att lägga till GUID för mallen?**

Microsoft ger partner med en vy av kunders distributioner av sina mallar och insikter på deras influenced användning. Både Microsoft och partner kan använda denna information för att öka närmare engagemanget mellan försäljningsteam för hjälp. Både Microsoft och partner kan använda data för att få en mer konsekvent överblick över en partner påverkan på Azure tillväxt. 

**Vem kan lägga till en GUID till en mall?**

Spårnings-resurs är avsedd att ansluta den partnerlösning till kundens Azure-användning. Användningsdata är knutna till identitet för en partner Microsoft Partner Network (MPN-ID). Reporting är tillgängligt för partner i CPP.

**När ett GUID har lagts till, kan det ändras?**
 
Ja, en kund eller implementering partner kan anpassa mallen och kan ändra eller ta bort GUID. Vi rekommenderar att partner proaktivt beskriver rollen för resursen och GUID för sina kunder och partner som förhindrar borttagning eller ändringar till spårning GUID. Om du ändrar GUID påverkas endast nya, inte befintliga distributioner och resurser.

**När rapportering blir tillgängligt?**

En beta-versionen av reporting ska vara tillgänglig snart. Rapportering kommer att integreras i CPP.

**Kan jag spåra mallar distribueras från en icke-Microsoft-lagringsplats som GitHub?**

Ja, så länge som GUID som är tillgängliga när mallen distribueras användning spåras. Partner måste ha en profil i CPP att registrera relaterade mallar som har publicerats utanför Azure Marketplace. 

**Finns det någon skillnad om mallen har distribuerats från Azure Marketplace jämfört med andra lagringsplatser som GitHub?**

Ja, kan partner publicerar erbjudanden i Azure Marketplace får mer detaljerade data på distributioner från Azure Marketplace. Partner dra nytta av exponera sitt erbjudande till kunder på Azure Marketplace-portalen och i Azure-portalen. Erbjudanden på Azure Marketplace kan du också generera leads för partnern.

**Vad händer om jag skapar en anpassad mall för en enskild Kundengagemang?**

Du är fortfarande Välkommen att lägga till GUID för mallen. Om du använder ett befintligt registrerade GUID, ingår det i reporting. Om du skapar ett nytt GUID som du behöver registrera nya GUID ha dem i spårning.

**Får kunden reporting samt?**

Kunder kan spåra deras användning av enskilda resurser eller kunddefinierad resursgrupper i Azure-portalen.   

**Påminner om den här metoden för spårning till den Digital Partner of Record (DPOR)?**

Den nya metoden för att ansluta distribution och användning till en partnerlösning är en mekanism för att länka en partnerlösning till Azure-användning. DPOR är avsedd att associera en konsult (systemintegratör) eller hanteringspartnern (Managed Service Provider) med en kunds Azure-prenumeration.   

**Vad är fördelen för Azure Storage GUID Generator formatet?**

Azure Storage GUID Generator formuläret garanteras att generera en GUID för formatet som krävs. Om du använder någon av Azure Storage-dataplanet spåra metoder, kan du dessutom använda samma GUID för Marketplace kontrollplanet spårning. På så sätt kan du utnyttja ett en enkel och enhetlig GUID för Partner attribution utan att behöva underhålla olika GUID.
