---
title: Azure attribution för användning av partners och kunder
description: Översikt över hur du spårar KUNDANVÄNDNING för Azure Marketplace-lösningar
services: Azure, Marketplace, Compute, Storage, Networking, Blockchain, Security
documentationcenter: ''
author: ellacroi
manager: nunoc
editor: ''
ms.assetid: e8d228c8-f9e8-4a80-9319-7b94d41c43a6
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 07/26/2018
ms.author: ellacroi
ms.openlocfilehash: 95ad327380707dcfe14aa5aa3d91b8da2309eb05
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/08/2018
ms.locfileid: "39630899"
---
# <a name="azure-partner-customer-usage-attribution"></a>Azure-partner kundens användning attribution

Som partner med programvara för Azure kräver dina lösningar antingen Azure-komponenter eller distribueras direkt på Azure-infrastrukturen.  Idag, när en kund distribuerar en partnerlösning och etablerar sina egna Azure-resurser, är det svårt för partner att få insyn till statusen för dessa distributioner och svårt att få optik i påverkan på Azure tillväxt. Att lägga till en högre nivå av synlighet hjälper partner överensstämmer med Microsofts säljteam och få kredit för Microsoft partner-program.   

Microsoft skapar en ny metod för att hjälpa partner att bättre spåra användningen av Azure som är ett resultat av en kund distribuerar ditt program på Azure. Den nya metoden baseras på med Azure Resource Manager för att dirigera distribution av Azure-tjänster.

Som Microsoft-partner kan associera du Azure-användning med Azure-resurser som du etablerar för en kunds räkning.  Den här associationen kan göras via Azure Marketplace, Snabbstart-lagringsplatsen, privat github-lagringsplatser och även 1 på 1 för kundengagemang.  Om du vill aktivera spårning, finns det två sätt:

- Azure Resource Manager-mallar: Azure Resource Manager-mallar eller lösningsmallar att distribuera Azure-tjänster för att köra partnerns programvara. Partner kan skapa Azure Resource Manager-mallen som definierar infrastrukturen och konfigurationen av din Azure-lösning. Skapa en Azure Resource Manager-mall kan du och dina kunder att distribuera din lösning under dess livscykel och vara säker på att resurserna distribueras i ett konsekvent tillstånd. 

- Azure Resource Manager API: er: partner kan anropa Azure Resource Manager API: er direkt för att antingen distribuera en Azure Resource Manager-mall eller för att generera API-anrop till direkt etablera Azure-tjänster. 

## <a name="method-1-azure-resource-manager-templates"></a>Metod 1: Azure Resource Manager-mallar 

Idag är många partnerlösningar distribueras på en kunds prenumeration med hjälp av Azure Resource Manager-mallar.  Om du redan har en Azure Resource Manager-mall som är tillgängliga i Azure Marketplace, på GitHub eller som en Snabbstart, ska hur du ändrar din mall för att aktivera nya metoden vara enkelt.  Om du inte använder en Azure Resource Manager-mall i dag, är här några länkar som hjälper dig att bättre förstå Azure Resource Manager-mallar och hur du skapar ett: 

*   [Skapa och distribuera din första Azure Resource Manager-mall](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template)
*   [Guiden för att skapa en lösningsmall för Azure Marketplace](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-solution-template-creation)

## <a name="instructions-add-a-guid-to-your-existing-azure-resource-manager-template"></a>Anvisningar: lägga till en GUID till din befintliga Azure Resource Manager-mall

Att lägga till GUID som är en enkel ändring av huvudsakliga mallfilen:
 1. Skapa ett GUID, vi tänker oss att det genererade värdet eb7927c8-dd66-43e1-b0cf-c346a422063
 2. Öppna Azure Resource Manager-mall
 3. Lägg till en ny resurs i den huvudsakliga mallfilen. Resursen måste endast finnas i mainTemplate.json eller azuredeploy.json, inte i någon kapslad eller länkad mallar.
 4. Ange GUID efter den ”pid-” enligt ovan.

   Det bör se ut ungefär som i följande exempel: `pid-eb7927c8-dd66-43e1-b0cf-c346a422063`

 5. Kontrollera mallen innehåller fel
 6. Publicera om mallen i lämpliga databaser

## <a name="sample-template-code"></a>Exempelkod för mallen

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-code-for-lu-1.PNG?token=Ak8ZDB0JzsBdUGlKEIeHNJRS7b0BWn4Gks5bbMwwwA%3D%3D)


## <a name="method-2-azure-resource-manager-apis"></a>Metod 2: Azure Resource Manager API: er

I vissa fall kanske du föredrar att göra anrop direkt mot Azure Resource Manager REST API: er för att distribuera Azure-tjänster. [Azure har stöd för flera SDK: er](https://docs.microsoft.com/azure/#pivot=sdkstools) för sådan.  Du kan använda någon av de SDK: er eller anropa REST-API: er direkt om du vill distribuera resurser.

Om du använder en Azure Resource Manager-mall ska du tagga din lösning med hjälp av anvisningarna ovan.  Du kan tagga distributionen om du vill associera användningen av Azure-resurser även om du inte använder en Azure Resource Manager-mall och direkta API-anrop. 

**Tagga en distribution med Azure Resource Manager API: er:** för den här metoden när du utformar din API-anrop som ska ingå ett GUID i användaren agent huvudet i begäran. GUID ska läggas till för varje erbjudande eller SKU.  Strängen måste vara formaterad med prefixet pid - och inkludera den partner som har genererats GUID.   

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/tracking-sample-guid-for-lu-2.PNG?token=Ak8ZDDiokRcj4PJj0aMkZmfF8BdOuOTzks5bbM35wA%3D%3D)

>[!Note] 
>GUID-format för infogning i användaragenten: pid-eb7927c8-dd66-43e1-b0cf-c346a422063 / / ange din GUID efter den ”pid-”

Formatet för strängen är viktigt. Om det inte finns någon prefixet ”pid-”, är det inte går att fråga efter data. Olika SDK: er gör detta på olika sätt.  För att implementera den här metoden, behöver du granska stöd och metoden för din önskade Azure-SDK. 

**Exempel med hjälp av Python-SDK:** för Python, måste du använda ”config”-attributet. Du kan bara lägga till en UserAgent. Här är ett exempel:

![](https://raw.githubusercontent.com/ellacroi/azure-docs-pr/lu-images-again-dangit-all/articles/marketplace/media/marketplace-publishers-guide/python-for-lu.PNG?token=Ak8ZDK5Um4J6oY-7x25tuBpa168BEiYMks5bbMuUwA%3D%3D)

>Detta måste göras för varje klient, det finns ingen global statisk konfiguration (du kan välja att göra en klientfabrik om du vill vara säker på att alla klienter fungerar. 
>[Ytterligare Referensinformation](https://github.com/Azure/azure-cli/blob/7402fb2c20be2cdbcaa7bdb2eeb72b7461fbcc30/src/azure-cli-core/azure/cli/core/commands/client_factory.py#L70-L79)

Tagga en distribution med Azure PowerShell eller Azure CLI: Om du har distribuerat resurser via AzurePowerShell kan du lägga till din GUID med hjälp av följande metod:

```

[Microsoft.Azure.Common.Authentication.AzureSession]::ClientFactory.AddUserAgent("pid-eb7927c8-dd66-43e1-b0cf-c346a422063")


```

Ställa in miljövariabeln AZURE_HTTP_USER_AGENT vill lägga till din GUID när du använder Azure CLI.  Du kan ange den här variabeln inom omfånget för ett skript eller att ange globalt för shell omfång användning:

```

export AZURE_HTTP_USER_AGENT='pid-eb7927c8-dd66-43e1-b0cf-c346a422063'


```

## <a name="registering-guidsoffers"></a>Registrera GUID/erbjudanden

För GUID som ska tas med i vår spårning, måste den vara registrerad.  

Alla registreringar för mall-GUID görs via Azure Marketplace Cloud Partner Portal (CPP). 

Gäller för [Azure Marketplace](http://aka.ms/listonazuremarketplace) idag och få åtkomst till Cloud Partner portal.

*   Partner kommer att behöva [har en profil i CPP](https://docs.microsoft.com/azure/marketplace/become-publisher) och uppmuntras att erbjudandet i Azure Marketplace eller AppSource-lista 
*   Partner kommer att kunna registrera flera GUID: er 
*   Partner kommer även att kunna registrera ett GUID för inte finns på Marketplace mallar/lösningserbjudanden

När du har lagt till GUID i mallen, eller i användaragenten och registrerat GUID i CPP alla distributioner kommer att spåras. 

## <a name="verification-of-guid-deployment"></a>Verifiering av GUID-distribution 

När du har ändrat din mall och utföra en test-distribution kan använda du följande PowerShell-skript för att hämta de resurser du har distribuerat och märks. 

Du kan använda den för att kontrollera om GUID som har lagts till Azure Resource Manager-mallen. Den gäller inte för Azure Resource Manager API-distribution.

Logga in på Azure och välj den prenumeration som innehåller den distribution du vill kontrollera innan du kör skriptet. Det måste köras inom ramen för prenumerationen för distributionen.

Namnet på GUID och resursgruppen för distributionen är obligatoriska parametrar.

Du kan hitta det ursprungliga skriptet [här](https://gist.github.com/bmoore-msft/ae6b8226311014d6e7177c5127c7eba1#file-verify-deploymentguid-ps1).

```

Param(
    [GUID][Parameter(Mandatory=$true)]$guid,
    [string][Parameter(Mandatory=$true)]$resourceGroupName'
)

#get the correlationId of the pid deployment

$correlationId = (Get-AzureRmResourceGroupDeployment -ResourceGroupName 
$resourceGroupName -Name "pid-$guid").correlationId

#find all deployments with that correlationId

$deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $resourceGroupName | Where-Object{$_.correlationId -eq $correlationId}

#find all deploymentOperations in a deployment by name (since PowerShell does not surface outputResources on the deployment or correlationId on the deploymentOperation)

foreach ($deployment in $deployments){

#get deploymentOperations by deploymentName and then the resourceId for any create operation

($deployment | Get-AzureRmResourceGroupDeploymentOperation | Where-Object{$_.properties.provisioningOperation -eq "Create" -and $_.properties.targetResource.resourceType -ne "Microsoft.Resources/deployments"}).properties.targetResource.id

}


```

## <a name="guidance-on-creating-guids"></a>Vägledning om hur du skapar GUID

En GUID (globalt unik identifierare) är ett unikt referensnummer 32 hexadecimala siffror. Om du vill skapa ett GUID, bör du använda en GUID-generator för att skapa GUID för spårning.  Det finns flera [online GUID generatorer](https://www.bing.com/search?q=guid%20generator&qs=n&form=QBRE&sp=-1&ghc=2&pq=guid%20g&sc=8-6&sk=&cvid=0BAFAFCD70B34E4296BB97FBFA3E1B4E) du kan använda.

Du kan skapa ett unikt GUID för varje erbjudande och distribution kanal.  Till exempel om du har två lösningar och båda har distribuerats via en mall och är tillgängliga i både Azure Marketplace och på GitHub.  Skapa fyra GUID:

*   Erbjud en i Azure Marketplace 
*   Erbjud en på GitHub
*   Erbjudandet B i Azure Marketplace 
*   Erbjudandet B på GitHub

Rapportering görs av partner (Microsoft Partner-ID) och GUID. 

Du kan också välja att spåra GUID på en mer detaljerad nivå d.v.s. SKU (där SKU: er är varianter av ett erbjudande).

## <a name="guidance-on-privacy-and-data-collection"></a>Information om sekretess och datainsamling

Partner bör ge meddelanden för att informera sina kunder distributioner som innehåller Azure Resource Manager-GUID-spårning kan Microsoft för att rapportera den användning av Azure som är associerade med dessa distributioner till partnern.  Vissa exempel språk finns nedan. Om du anger det ”PARTNER” bör du fylla i företagets namn. Partner bör också se till språket som överensstämmer med sina egna data principer för sekretess och samling, inklusive alternativ för kunder som ska undantas från track: 

**För malldistributioner av Azure Resource Manager**

När du distribuerar den här mallen kan Microsoft kan identifiera installation av PARTNER-program med Azure-resurser distribueras.  Microsoft kommer att kunna korrelera de Azure-resurser används för att stödja programvaran.  Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Dessa data ska samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter. 

**För SDK eller API-distributioner**

När programvara för distribution PARTNER, Microsoft kommer att ha identifiera installation av PARTNER-program med Azure-resurser distribueras.  Microsoft kommer att kunna korrelera de Azure-resurser används för att stödja programvaran.  Microsoft samlar in den här informationen för att tillhandahålla de bästa upplevelserna med sina produkter och att driva sin verksamhet. Dessa data ska samlas in och styrs av Microsofts sekretessprinciper, som finns på https://www.microsoft.com/trustcenter.

## <a name="support"></a>Support

Om du behöver hjälp, följer du de stegen nedan:
 1. Gå till supportsidan finns på [go.microsoft.com/fwlink/?linkid=844975](https://go.microsoft.com/fwlink/?linkid=844975)
 2. Välj problemtypen för problem med användning associering -: **Marketplace Onboarding** och kategori: **andra** och klicka sedan på **starta begäran.** 
>[!Note]
>För problem med åtkomst till Azure Marketplace Cloud Partner Portal - Välj problemtypen: **Marketplace Onboarding** och kategori: **problem med anslutningen** och klicka sedan på **starta begäran.**
 3. Slutför de obligatoriska fälten på nästa sida och klicka på **Fortsätt.**
 4. Fyll i fälten fritext på nästa sida.  
 

 
>[!Important] 
>Fyll i titeln Incident med **”ISV användning spåra”** och beskrivning av problemet i detalj i fältet stora fritext efter.  Slutför resten av formuläret och klicka på **skicka**.

## <a name="faqs"></a>Vanliga frågor och svar

**Vad är fördelen med att lägga till GUID för mallen?**

Microsoft får partner med en vy över kunders distributioner av sina mallar och insikter om sin influenced användning.  Både Microsoft och partner kan du också använda denna information för att öka närmare engagemanget mellan försäljningsteam för hjälp. Både Microsoft och partnern också använda den för att få en mer konsekvent överblick över en partner påverkan på Azure tillväxt. 

**Vem kan lägga till en GUID till en mall?**

Spårnings-resurs är avsedd att ansluta den Partnerlösningen för Azure-kunder användning.  Användningsdata är knutna till identitet för en partner Microsoft Partner Network (MPN-ID) och rapportering blir tillgängliga för partners i Cloud Partner Portal (CPP).  

**När ett GUID har lagts till kan den ändras?**
 
Ja, en kund eller implementering partner kan anpassa mallen och kan ändra eller ta bort GUID. Vi rekommenderar att partner proaktivt beskriver rollen för resursen och GUID för sina kunder och partner som förhindrar borttagning eller ändringar till spårning GUID.  Om du ändrar GUID påverkas endast nya, inte befintliga distributioner och resurser.

**När rapportering blir tillgängligt?**

En beta-versionen av reporting ska vara tillgänglig snart.  Reporting ska integreras i Cloud Partner Portal (CPP).

**Kan jag spåra mallar distribueras från en icke-Microsoft-lagringsplats som GitHub?**

Ja, så länge som GUID som är tillgängliga när mallen distribueras användning spåras.  
Partner måste ha en profil i partnerportalen i molnet registrera relaterade mallarna publicerats utanför Azure Marketplace. 

**Finns det någon skillnad om mallen har distribuerats från Azure Marketplace jämfört med andra lagringsplatser som GitHub?**

Ja, partner publicerar erbjudanden i Azure Marketplace kan få mer detaljerade data på distributioner från Azure Marketplace.  Partner kan dra nytta exponera sitt erbjudande till kunder på Azure Marketplace-portalen och i Azure-hanteringsportalen. Erbjudanden på Azure marketplace kan du också generera leads för partnern.

**Vad händer om jag skapar en anpassad mall för en enskild Kundengagemang?**

Du är fortfarande Välkommen att lägga till GUID för mallen.  Om du använder ett befintligt GUID som har registrerats kan inkluderas i reporting.  Om du skapar en ny GUID, kommer du behöver registrera nya GUID om du vill ha dem i spårning.

**Får kunden reporting samt?**

Kunder kan för närvarande spåra deras användning av enskilda resurser eller kunddefinierade resursgrupper i Azure-hanteringsportalen.   

**Påminner om den här metoden för spårning till den Digital Partner of Record (DPOR)?**

Den här nya metoden för att ansluta distribution och användning i ett partner-lösningen är avsedd att ge en mekanism för att länka en partnerlösning till Azure-användning.  DPOR är avsedd att associera en konsult (systemintegratör) eller hanteringspartnern (Managed Service Provider) med en kunds Azure-prenumeration.   
