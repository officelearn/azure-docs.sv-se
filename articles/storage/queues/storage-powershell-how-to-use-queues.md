---
title: Utföra åtgärder på Azure-kölagring med PowerShell | Microsoft Docs
description: Hur du utför åtgärder på Azure-kölagring med PowerShell
services: storage
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/14/2017
ms.author: rogarana
ms.subservice: queues
ms.openlocfilehash: dddd82cd80d853607b28db906d298d5c301ab9df
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/06/2019
ms.locfileid: "55770711"
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Utföra åtgärder för Azure Queue storage med Azure PowerShell

Azure Queue storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via HTTP eller HTTPS. Detaljerad information finns i [introduktion till Azure-köer](storage-queues-introduction.md). I den här artikeln beskriver vanliga Queue storage-åtgärder. Lär dig att:

> [!div class="checklist"]
> * Skapa en kö
> * Hämta en kö
> * Lägg till ett meddelande
> * Läsa ett meddelande
> * Ta bort ett meddelande 
> * Ta bort en kö

Den här anvisningen kräver Azure PowerShell-modulen Az 0.7 eller senare. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-Az-ps) (Installera Azure PowerShell-modul).

Det finns ingen PowerShell-cmdletar för dataplanet för köer. Om du vill utföra data kontrollplansåtgärder till exempel lägga till ett meddelande, läsa ett meddelande och ta bort ett meddelande, du måste använda lagringsklientbiblioteket för .NET som det visas i PowerShell. Du skapar ett meddelandeobjekt och du kan sedan använda kommandon, till exempel AddMessage för att utföra åtgärder på meddelandet. Den här artikeln visar hur du gör.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på din Azure-prenumeration med kommandot `Connect-AzAccount` och följ anvisningarna på skärmen.

```powershell
Connect-AzAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta lista över platser

Om du inte vet vilken plats du vill använda kan du visa en lista med tillgängliga platser. Hitta den du vill använda i listan som visas. Den här övningen använder **eastus**. Store detta i variabeln **plats** för framtida användning.

```powershell
Get-AzLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med det [New AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) kommando. 

En Azure-resursgrupp är en logisk container där Azure-resurser distribueras och hanteras. Store resursgruppens namn i en variabel för framtida användning. I det här exemplet, en resursgrupp med namnet *howtoqueuesrg* skapas i den *eastus* region.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa ett allmänt standardlagringskonto med lokalt redundant lagring (LRS) med hjälp av [New AzStorageAccount](/powershell/module/az.storage/New-azStorageAccount). Hämta lagringskontokontexten som definierar lagringskontot som ska användas. När du arbetar med lagringskonton refererar du till kontexten i stället för att ange autentiseringsuppgifterna flera gånger.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Skapa en kö

I följande exempel skapar först en anslutning till Azure Storage med kontexten för lagringskontot, som innehåller namnet på lagringskontot och dess åtkomstnyckel. Därefter anropar [New AzStorageQueue](/powershell/module/az.storage/New-AzStorageQueue) cmdlet för att skapa en kö med namnet ”könamn'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzStorageQueue –Name $queueName -Context $ctx
```

Information om namngivningskonventioner för Azure-kötjänsten finns [namngivning av köer och Metadata](https://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Hämta en kö

Du kan fråga efter och hämta en särskild kö eller en lista över alla köer i ett lagringskonto. Följande exempel visar hur du hämtar alla köer i lagringskontot och en särskild kö; båda kommandon använder den [Get-AzStorageQueue](/powershell/module/az.storage/Get-AzStorageQueue) cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Lägg till ett meddelande till en kö

Åtgärder som påverkar själva meddelandena i kön använda lagringsklientbiblioteket för .NET som visas i PowerShell. Om du vill lägga till ett meddelande till en kö, skapa en ny instans av meddelandeobjektet [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](https://msdn.microsoft.com/library/azure/jj732474.aspx) klass. Därefter anropar du [AddMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)-metoden. En CloudQueueMessage kan skapas från en sträng (i UTF-8-format) eller en bytematris.

I följande exempel visar hur du lägger till ett meddelande i kön.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessageAsync($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessageAsync($QueueMessage)
```

Om du använder den [Azure Storage Explorer](http://storageexplorer.com), du kan ansluta till ditt Azure-konto och visa köer i lagringskontot och granska nedåt i kö för att visa meddelanden i kön. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Läs ett meddelande från kön och tar bort den

Meddelanden läses i bästa Försök först-in-först-ut ordning. Detta är inte säkert. När du läser meddelandet från kön blir det osynligt för andra processer som tittar på kön. Detta säkerställer att om din kod inte kan bearbeta meddelandet på grund av maskin- eller programvarufel, kan en annan instans av koden hämta samma meddelande och försök igen.  

Detta **tidsgränsen för osynlighet** definierar hur länge meddelandet är osynlig innan den blir tillgänglig igen för bearbetning. Standardvärdet är 30 sekunder. 

Din kod läser meddelandet från kön i två steg. När du anropar den [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metoden du hämtas nästa meddelande i kön. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. Om du vill ta bort meddelandet från kön är klar kan du anropa den [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metod. 

I följande exempel du läsa igenom tre Kömeddelanden och vänta 10 sekunder (tidsgränsen för osynlighet). Och sedan läsa de tre meddelanden igen, ta bort meddelanden när du har läst dem genom att anropa **DeleteMessage**. Om du försöker läsa kön när meddelanden tas bort returneras $queueMessage som NULL.

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

Ta bort en kö och alla meddelanden som finns i den genom att anropa cmdleten Remove-AzStorageQueue. I följande exempel visas hur du tar bort den specifika kö som används i den här övningen med hjälp av cmdleten Remove-AzStorageQueue.

```powershell
# Delete the queue 
Remove-AzStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Rensa resurser

Om du vill ta bort alla resurser som du har skapat i den här övningen, tar du bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet tas bort lagringskontot som skapas och själva resursgruppen.

```powershell
Remove-AzResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln berättade om grundläggande Queue storage management med PowerShell, inklusive hur du:

> [!div class="checklist"]
> * Skapa en kö
> * Hämta en kö
> * Lägg till ett meddelande
> * Läs nästa meddelande
> * Ta bort ett meddelande 
> * Ta bort en kö

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell Storage-cmdletar

* [Storage PowerShell cmdletar](/powershell/module/az.storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer

* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fqueues%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.
