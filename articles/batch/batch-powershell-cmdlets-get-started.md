<properties
   pageTitle="Komma igång med PowerShell för Azure Batch | Microsoft Azure"
   description="Få en snabb introduktion till Azure PowerShell-cmdlets som du kan använda för att hantera Azure Batch-tjänsten"
   services="batch"
   documentationCenter=""
   authors="dlepow"
   manager="timlt"
   editor=""/>

<tags
   ms.service="batch"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="powershell"
   ms.workload="big-compute"
   ms.date="04/21/2016"
   ms.author="danlep"/>

# Komma igång med PowerShell för Azure Batch
Det här är en snabb introduktion till de Azure PowerShell-cmdlets som du kan använda för att hantera Batch-konton och arbeta med Batch-resurser, till exempel pooler, jobb och aktiviteter. Många av aktiviteterna som du kan utföra med Batch-API:er, Azure-portalen och Azure-kommandoradsgränssnitt (CLI) kan du även utföra med Batch-cmdlets. Den här artikeln baseras på cmdlets i Azure PowerShell-version 1.3.2 eller senare.

En fullständig lista över alla Batch-cmdlets och en detaljerad cmdlet-syntax finns i [Cmdlet-referens för Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx). 


## Krav

* **Azure PowerShell** – Anvisningar för hur du laddar ned och installerar Azure PowerShell finns i [Installera och konfigurera Azure PowerShell](../powershell-install-configure.md). 
   
    * Eftersom uppsättningen Azure Batch-cmdlets levereras med modulen Azure Resource Manager måste du köra cmdleten **Login-AzureRmAccount** för att ansluta till din prenumeration. 
    
    * Vi rekommenderar att du uppdaterar Azure PowerShell ofta för att dra nytta av tjänstuppdateringar och förbättringar. 
    
* **Registrera med Batch-providernamnrymden (engångsåtgärd)** – Innan du börjar arbeta med dina Batch-konton måste du registrera dem med Batch-providernamnrymden. Den här åtgärden behöver bara utföras en gång per prenumeration. Kör följande cmdlet:

        Register-AzureRMResourceProvider -ProviderNamespace Microsoft.Batch


## Hantera Batch-konton och nycklar

### Skapa ett Batch-konto

**New-AzureRmBatchAccount** skapar ett nytt Batch-konto i en angiven resursgrupp. Om du inte redan har en resursgrupp skapar du en genom att köra cmdleten [New-AzureRmResourceGroup](https://msdn.microsoft.com/library/azure/mt603739.aspx) och anger någon av Azure-regionerna i parametern **Location**, till exempel ”Central US”. Till exempel:


    New-AzureRmResourceGroup –Name MyBatchResourceGroup –location "Central US"


Skapa sedan ett nytt Batch-konto i resursgruppen och ange även ett kontonamn för <*account_name*> och en plats där Batch-tjänsten är tillgänglig. Det kan ta flera minuter innan kontot har skapats. Till exempel:


    New-AzureRmBatchAccount –AccountName <account_name> –Location "Central US" –ResourceGroupName MyBatchResourceGroup

> [AZURE.NOTE] Batch-kontonamnet måste vara unikt för Azure-regionen för resursgruppen, innehålla mellan 3 och 24 tecken och endast bestå av små bokstäver och siffror.

### Hämta kontoåtkomstnycklar
**Get-AzureRmBatchAccountKeys** visar åtkomstnycklarna som är associerade med ett Azure Batch-konto. Kör till exempel följande för att hämta de primära och sekundära nycklarna för det konto som du skapade.

    $Account = Get-AzureRmBatchAccountKeys –AccountName <accountname>

    $Account.PrimaryAccountKey

    $Account.SecondaryAccountKey


### Generera en ny åtkomstnyckel
**New-AzureRmBatchAccountKey** genererar en ny primär eller sekundär kontonyckel för ett Azure Batch-konto. Om du till exempel vill skapa en ny primär nyckel för Batch-kontot skriver du:


    New-AzureRmBatchAccountKey -AccountName <account_name> -KeyType Primary


> [AZURE.NOTE] Om du vill skapa en ny sekundär nyckel anger du ”Secondary” för parametern **KeyType**. Du måste återskapa de primära och sekundära nycklarna separat.

### Ta bort ett Batch-konto
**Remove-AzureRmBatchAccount** tar bort ett Batch-konto. Till exempel:


    Remove-AzureRmBatchAccount -AccountName <account_name>

När du uppmanas att göra det bekräftar du att du vill ta bort kontot. Det kan ta en stund innan kontot tagits bort.

## Skapa ett BatchAccountContext-objekt

Om du vill autentisera med Batch PowerShell-cmdlets när du skapar och hanterar pooler, jobb, aktiviteter och andra resurser i Batch skapar du ett BatchAccountContext-objekt för att lagra ditt kontonamn och dina nycklar:

    $context = Get-AzureRmBatchAccountKeys -AccountName <account_name>

Du skickar BatchAccountContext-objektet till cmdlets som använder parametern **BatchContext**.

> [AZURE.NOTE] Som standard används kontots primära nyckel för autentisering, men du kan uttryckligen välja vilken nyckel som ska användas genom att ändra BatchAccountContext-objektets **KeyInUse**-egenskap: `$context.KeyInUse = "Secondary"`.



## Skapa och ändra Batch-resurser
Använd cmdlets som **New-AzureBatchPool**, **New-AzureBatchJob** och **New-AzureBatchTask** för att skapa resurser under ett Batch-konto. Det finns motsvarande cmdlets för **Get-** och **Set-** som du kan använda för att uppdatera egenskaperna för befintliga resurser, och cmdlets för **Remove-** som du använder om du vill ta bort resurser under ett Batch-konto. 

### Skapa en Batch-pool

Följande cmdlet skapar till exempel en ny Batch-pool, som konfigurerats att använda virtuella datorer med storleken Small med en avbildning av den senaste operativsystemversionen i familj 3 (Windows Server 2012) och vars beräkningsnoder bestäms av en formel för automatisk skalning. I det här fallet är formeln bara **$TargetDedicated=3**, som anger att antalet beräkningsnoder i poolen är tre som mest. Parametern **BatchContext** anger en tidigare definierad variabel, *$context*, som BatchAccountContext-objektet.


    New-AzureBatchPool -Id "MyAutoScalePool" -VirtualMachineSize "Small" -OSFamily "3" -TargetOSVersion "*" -AutoScaleFormula '$TargetDedicated=3;' -BatchContext $Context

>[AZURE.NOTE]För närvarande stöder Batch PowerShell-cmdlets endast Cloud Services-konfigurationen för beräkningsnoder. Det betyder att du kan välja att en av Azure-gästoperativsystemversionerna av Windows Server-operativsystemet ska köra på beräkningsnoderna. För andra konfigurationsalternativ för beräkningsnoder för Batch-pooler använder du Batch-SDK:er eller CLI Azure.

## Köra frågor mot pooler, jobb, aktiviteter eller för annan information

Använd cmdlets som **Get-AzureBatchPool**, **Get-AzureBatchJob** och **Get-AzureBatchTask** om du vill fråga efter entiteter som skapats under ett Batch-konto.


### Fråga efter data

Använd till exempel **Get-AzureBatchPools** om du vill hämta dina pooler. Som standard returneras alla pooler i ditt konto, förutsatt att du redan har lagrat BatchAccountContext-objektet i *$context*:


    Get-AzureBatchPool -BatchContext $context

### Använda ett OData-filter

Du kan lägga till ett OData-filter med parametern **Filter** om du bara vill söka efter de objekt som du är intresserad av. Du kan till exempel hämta alla pooler med ID:n som börjar med ”myPool”:


    $filter = "startswith(id,'myPool')"

    Get-AzureBatchPool -Filter $filter -BatchContext $context


Den här metoden är inte lika flexibel som ”Where-Object”-metoden i en lokal pipeline. Frågan skickas dock direkt till Batch-tjänsten så att all filtrering utförs på serversidan, vilket sparar Internetbandbredd.

### Använda Id-parametern

Ett alternativ till ett OData-filter är att använda parametern **Id**. Så här frågar du efter en specifik pool med ID:t ”myPool”:


    Get-AzureBatchPool -Id "myPool" -BatchContext $context


Parametern **Id** stöder endast sökningar efter fullständiga ID:n, inte jokertecken eller OData-filter.



### Använda parametern MaxCount

Som standard returnerar varje cmdlet högst 1 000 objekt. Om du når den här gränsen kan du antingen förfina filtret så att färre objekt returneras eller uttryckligen ställa in ett högsta antal med parametern **MaxCount**. Till exempel:


    Get-AzureBatchTask -MaxCount 2500 -BatchContext $context

Om du vill ta bort den övre gränsen anger du **MaxCount** till 0 eller mindre.

### Använda pipelinen

Batch-cmdlets kan utnyttja PowerShell-pipelinen för att skicka data mellan cmdlets. Detta har samma effekt som om du anger en parameter men gör det enklare att visa flera entiteter. Exempelvis returnerar följande alla aktiviteter i ditt konto:


    Get-AzureBatchJob -BatchContext $context | Get-AzureBatchTask -BatchContext $context


## Nästa steg
* Detaljerad cmdlet-syntax och exempel finns i [Cmdlet-referens för Azure Batch](https://msdn.microsoft.com/library/azure/mt125957.aspx).

* Mer information om hur du minskar antalet objekt och vilken typ av information som returneras för frågor till Batch finns i [Skicka effektiva frågor till Batch-tjänsten](batch-efficient-list-queries.md). 



<!--HONumber=jun16_HO2-->


