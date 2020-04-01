---
title: Utföra Azure-kölagringsåtgärder i PowerShell
description: Så här utför du åtgärder på Azure Queue storage med PowerShell
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/15/2019
ms.service: storage
ms.subservice: queues
ms.topic: conceptual
ms.reviewer: cbrooks
ms.openlocfilehash: 96828a854c340b89c26023ce60f9c85dd1bb4cdd
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/01/2020
ms.locfileid: "80473925"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Utför Azure Queue Storage-åtgärder med Azure PowerShell

Azure Queue storage är en tjänst för lagring av ett stort antal meddelanden som kan nås var som helst i världen via HTTP eller HTTPS. Detaljerad information finns i [Introduktion till Azure-köer](storage-queues-introduction.md). Den här artikeln innehåller vanliga kölagringsåtgärder. Lär dig att:

> [!div class="checklist"]
>
> * Skapa en kö
> * Hämta en kö
> * Lägg till ett meddelande
> * Läsa ett meddelande
> * Ta bort ett meddelande
> * Ta bort en kö

Den här inaktiveringen kräver Azure PowerShell-modulen Az version 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

Det finns inga PowerShell-cmdletar för dataplanet för köer. Om du vill utföra dataplansåtgärder som att lägga till ett meddelande, läsa ett meddelande och ta bort ett meddelande måste du använda .NET-lagringsklientbiblioteket när det exponeras i PowerShell. Du skapar ett meddelandeobjekt och sedan kan du använda kommandon som AddMessage för att utföra åtgärder på det meddelandet. Den här artikeln visar hur du gör det.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. Denna övning kommer att använda **eastus**. Lagra detta på **variabelplatsen** för framtida bruk.

```powershell
Get-AzLocation | Select-Object Location
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Lagra resursgruppsnamnet i en variabel för framtida bruk. I det här exemplet skapas en resursgrupp med namnet *howtoqueuesrg* i *regionen eastus.*

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett standardkonto för allmänt bruk med lokalt redundant lagring (LRS) med [New-AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Hämta den lagringskontokontext som definierar lagringskontot som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Skapa en kö

I följande exempel upprättas först en anslutning till Azure Storage med hjälp av lagringskontokontexten, som innehåller lagringskontonamnet och dess åtkomstnyckel. Därefter kallas [New-AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet för att skapa en kö med namnet "howtoqueue".

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Information om namngivningskonventioner för Azure Queue Service finns i [Namnköer och metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Hämta en kö

Du kan fråga efter och hämta en viss kö eller en lista över alla köer i ett lagringskonto. Följande exempel visar hur du hämtar alla köer i lagringskontot och en specifik kö. Båda kommandona använder [cmdleten Get-AzStorageQueue.](/powershell/module/az.storage/Get-AzStorageQueue)

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | Select-Object Name
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande i en kö

Åtgärder som påverkar de faktiska meddelandena i kön använder .NET-lagringsklientbiblioteket som exponerat i PowerShell. Om du vill lägga till ett meddelande i en kö skapar du en ny instans av meddelandeobjektet, klassen [Microsoft.Azure.Storage.Queue.CloudQueueMessage.](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueuemessage) Därefter anropar du [AddMessage](https://docs.microsoft.com/java/api/com.microsoft.azure.storage.queue.cloudqueue.addmessage)-metoden. Du kan skapa ett CloudQueueMessage antingen från en sträng (i UTF-8-format) eller från en byte-matris.

I följande exempel visas hur du lägger till ett meddelande i kön.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 1")

# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue
$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 2")
$queue.CloudQueue.AddMessageAsync($QueueMessage)

$queueMessage = [Microsoft.Azure.Storage.Queue.CloudQueueMessage]::new("This is message 3")
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Om du använder [Azure Storage Explorer](https://storageexplorer.com)kan du ansluta till ditt Azure-konto och visa köerna i lagringskontot och öka detaljnivån i en kö för att visa meddelandena i kön.

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Läsa ett meddelande från kön och ta sedan bort det

Meddelanden läss i best-try första-i-första-ut-ordning. Detta är inte garanterat. När du läser meddelandet från kön blir det osynligt för alla andra processer som tittar på kön. Detta säkerställer att om koden inte bearbetar meddelandet på grund av maskinvaru- eller programvarufel kan en annan instans av koden få samma meddelande och försöka igen.  

Den här **tidsgränsen för osynlighet** definierar hur länge meddelandet förblir osynligt innan det är tillgängligt igen för bearbetning. Standardvärdet är 30 sekunder.

Koden läser ett meddelande från kön i två steg. När du anropar metoden [Microsoft.Azure.Storage.Queue.CloudQueue.GetMessage](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.getmessage) får du nästa meddelande i kön. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Om du vill ta bort meddelandet från kön anropar du metoden [Microsoft.Azure.Storage.Queue.CloudQueue.DeleteMessage.](/dotnet/api/microsoft.azure.storage.queue.cloudqueue.deletemessage)

I följande exempel läser du igenom de tre kömeddelandena och väntar sedan 10 sekunder (tidsgränsen för osynlighet). Sedan kan du läsa de tre meddelandena igen, ta bort meddelandena efter att ha läst dem genom att ringa **DeleteMessage**. Om du försöker läsa kön när meddelandena har tagits bort returneras $queueMessage som NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result

# After 10 seconds, these messages reappear on the queue.
# Read them again, but delete each one after reading it.
# Delete the message.
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
$queueMessage = $queue.CloudQueue.GetMessageAsync($invisibleTimeout,$null,$null)
$queueMessage.Result
$queue.CloudQueue.DeleteMessageAsync($queueMessage.Result.Id,$queueMessage.Result.popReceipt)
```

## <a name="delete-a-queue"></a>Ta bort en kö

Om du vill ta bort en kö och alla meddelanden i den anropar du cmdleten Ta bort AzStorageQueue. I följande exempel visas hur du tar bort den specifika kön som används i den här övningen med cmdlet Ta bort AzStorageQueue.

```powershell
# Delete the queue
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla tillgångar som du har skapat i den här övningen tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas det skapade lagringskontot och resursgruppen bort.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln har du lärt dig om grundläggande hantering av kölagring med PowerShell, inklusive hur du:

> [!div class="checklist"]
>
> * Skapa en kö
> * Hämta en kö
> * Lägg till ett meddelande
> * Läs nästa meddelande
> * Ta bort ett meddelande
> * Ta bort en kö

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell-lagrings-cmdlets

* [Storage PowerShell cmdletar](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
