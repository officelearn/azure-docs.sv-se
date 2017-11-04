---
title: "Utföra åtgärder på Azure Queue storage med PowerShell | Microsoft Docs"
description: "Hur du utför åtgärder på Azure Queue storage med PowerShell"
services: storage
documentationcenter: storage
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 09/14/2017
ms.author: robinsh
ms.openlocfilehash: 36eb6db83bb902b35efb8c9666ab06e0c618d602
ms.sourcegitcommit: 3ab5ea589751d068d3e52db828742ce8ebed4761
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/27/2017
---
# <a name="perform-azure-queue-storage-operations-with-azure-powershell"></a>Utföra Azure Queue storage-åtgärder med Azure PowerShell

Azure Queue Storage är en tjänst för att lagra stora mängder meddelanden som kan nås från var som helst i världen via autentiserade anrop med HTTP eller HTTPS. Detaljerad information finns i [introduktion till Azure köer](storage-queues-introduction.md). Den här artikeln beskrivs vanliga Queue storage-åtgärder. Lär dig att:

> [!div class="checklist"]
> * Skapa en kö
> * Hämta en kö
> * Lägga till ett meddelande
> * Läsa ett meddelande
> * Ta bort ett meddelande 
> * Ta bort en kö

Den här anvisningar kräver Azure PowerShell Modulversion 3,6 eller senare. Kör `Get-Module -ListAvailable AzureRM` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-azurerm-ps) (Installera Azure PowerShell-modul).

Det finns inga PowerShell-cmdletar för dataplan för köer. Om du vill utföra data plan operations som lägger till ett meddelande som läser ett meddelande och ta bort ett meddelande måste du använda storage-klientbiblioteket för .NET som det visas i PowerShell. Du skapar ett message-objekt och du kan sedan använda kommandon som AddMessage för att utföra åtgärder på meddelandet. Den här artikeln visar hur du gör.

## <a name="sign-in-to-azure"></a>Logga in på Azure

Logga in på Azure-prenumerationen med kommandot `Login-AzureRmAccount` och följ anvisningarna på skärmen.

```powershell
Login-AzureRmAccount
```

## <a name="retrieve-list-of-locations"></a>Hämta listan över platser

Om du inte vet vilken plats som du vill använda, kan du visa tillgängliga platser. När listan visas hitta det du vill använda. Den här övningen använder **eastus**. Lagras i variabeln **plats** för framtida användning.

```powershell
Get-AzureRmLocation | select Location 
$location = "eastus"
```

## <a name="create-resource-group"></a>Skapa resursgrupp

Skapa en resursgrupp med kommandot [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). 

En Azure-resursgrupp är en logisk behållare där Azure-resurser distribueras och hanteras. Lagra resursgruppens namn i en variabel för framtida användning. I det här exemplet en resursgrupp med namnet *howtoqueuesrg* skapas i den *eastus* region.

```powershell
$resourceGroup = "howtoqueuesrg"
New-AzureRmResourceGroup -ResourceGroupName $resourceGroup -Location $location
```

## <a name="create-storage-account"></a>Skapa lagringskonto

Skapa en standard Allmänt lagringskonto med lokalt redundant lagring (LRS) med hjälp av [New-AzureRmStorageAccount](/powershell/module/azurerm.storage/New-AzureRmStorageAccount). Hämta kontexten för lagringskontot som definierar lagringskontot som ska användas. När fungerar på ett lagringskonto, referera kontexten i stället för att tillhandahålla autentiseringsuppgifterna flera gånger.

```powershell
$storageAccountName = "howtoqueuestorage"
$storageAccount = New-AzureRmStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_LRS

$ctx = $storageAccount.Context
```

## <a name="create-a-queue"></a>Skapa en kö

I följande exempel upprättar en anslutning till Azure Storage med hjälp av kontexten för lagringskontot, som innehåller lagringskontonamn och dess åtkomstnyckel först. Därefter anropar [ny AzureStorageQueue](/powershell/module/azure.storage/new-azurestoragequeue) för att skapa en kö med namnet 'könamn'.

```powershell
$queueName = "howtoqueue"
$queue = New-AzureStorageQueue –Name $queueName -Context $ctx
```

Information om namngivningskonventioner för Azure-kötjänsten finns [namngivning av köer och Metadata](http://msdn.microsoft.com/library/azure/dd179349.aspx).

## <a name="retrieve-a-queue"></a>Hämta en kö

Du kan fråga efter och hämta en särskild kö eller en lista över alla köer i ett lagringskonto. Följande exempel visar hur du hämtar alla köer i lagringskontot och en särskild kö; båda kommandon använder den [Get-AzureStorageQueue](/powershell/module/azure.storage/get-azurestoragequeue) cmdlet.

```powershell
# Retrieve a specific queue
$queue = Get-AzureStorageQueue –Name $queueName –Context $ctx
# Show the properties of the queue
$queue

# Retrieve all queues and show their names
Get-AzureStorageQueue -Context $ctx | select Name
```

## <a name="add-a-message-to-a-queue"></a>Lägga till ett meddelande till en kö

Åtgärder som påverkar de faktiska meddelandena i kön använda storage-klientbiblioteket för .NET som visas i PowerShell. Om du vill lägga till ett meddelande till en kö, skapa en ny instans av meddelandeobjektet [Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage](http://msdn.microsoft.com/library/azure/jj732474.aspx) klass. Därefter anropar du [AddMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.addmessage.aspx)-metoden. En CloudQueueMessage kan skapas från en sträng (i UTF-8-format) eller en byte-matris.

Exemplet nedan visar hur du lägger till ett meddelande till kön.

```powershell
# Create a new message using a constructor of the CloudQueueMessage class
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 1"
# Add a new message to the queue
$queue.CloudQueue.AddMessage($QueueMessage)

# Add two more messages to the queue 
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 2"
$queue.CloudQueue.AddMessage($QueueMessage)
$queueMessage = New-Object -TypeName Microsoft.WindowsAzure.Storage.Queue.CloudQueueMessage `
  -ArgumentList "This is message 3"
$queue.CloudQueue.AddMessage($QueueMessage)
```

Om du använder den [Azure Lagringsutforskaren](http://storageexplorer.com), du kan ansluta till ditt Azure-konto och visa köerna i lagringskontot och detaljnivån i en kö för att visa meddelanden i kön. 

## <a name="read-a-message-from-the-queue-then-delete-it"></a>Läs ett meddelande från kön och tar sedan bort den

Meddelanden är skrivskyddade i bäst försök first i first out ordning. Detta är inte säkert. När du läser meddelandet från kön blir osynligt för andra processer som tittar på kön. Detta säkerställer att om din kod inte kan bearbeta meddelandet på grund av maskinvara eller programvara, kan en annan instans av koden hämta samma meddelande och försök igen.  

Detta **tidsgränsen för osynlighet** definierar hur länge meddelandet är osynlig innan den är tillgänglig igen för bearbetning. Standardvärdet är 30 sekunder. 

Koden läser ett meddelande från kön i två steg. När du anropar den [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.GetMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.getmessage.aspx) metod du hämtas nästa meddelande i kön. Ett meddelande som returneras från **GetMessage** blir osynligt för andra meddelanden som läser kod i den här kön. För att slutföra borttagningen av meddelandet från kön, du anropar den [Microsoft.WindowsAzure.Storage.Queue.CloudQueue.DeleteMessage](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.queue.cloudqueue.deletemessage.aspx) metod. 

I följande exempel du läsa igenom tre Kömeddelanden och sedan vänta 10 sekunder (tidsgränsen för osynlighet). Sedan läsa de tre meddelanden igen, ta bort meddelanden efter att ha läst dem genom att anropa **DeleteMessage**. Om du försöker läsa kön efter meddelanden raderas returneras $queueMessage som NULL.

```powershell
# Set the amount of time you want to entry to be invisible after read from the queue
# If it is not deleted by the end of this time, it will show up in the queue again
$invisibleTimeout = [System.TimeSpan]::FromSeconds(10)

# Read the message from the queue, then show the contents of the message. Read the other two messages, too.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queueMessage 

# After 10 seconds, these messages reappear on the queue. 
# Read them again, but delete each one after reading it.
# Delete the message.
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
$ queueMessage = $queue.CloudQueue.GetMessage($invisibleTimeout)
$ queue.CloudQueue.DeleteMessage($queueMessage)
```

## <a name="delete-a-queue"></a>Ta bort en kö
Anropa Remove-AzureStorageQueue cmdlet för att ta bort en kö och alla meddelanden i den. I följande exempel visas hur du tar bort den specifika kö som används i den här övningen med cmdlet Remove-AzureStorageQueue.

```powershell
# Delete the queue 
Remove-AzureStorageQueue –Name $queueName –Context $ctx
```

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort alla tillgångar som du har skapat i den här övningen genom att ta bort resursgruppen. Detta tar även bort alla resurser som ingår i gruppen. I det här fallet den tar bort lagringskonto som skapats och resursgruppen sig själv.

```powershell
Remove-AzureRmResourceGroup -Name $resourceGroup
```

## <a name="next-steps"></a>Nästa steg

I den här artikeln du lärt dig om grundläggande lagringshantering för kön med PowerShell, inklusive hur du:

> [!div class="checklist"]
> * Skapa en kö
> * Hämta en kö
> * Lägga till ett meddelande
> * Läsa nästa meddelande
> * Ta bort ett meddelande 
> * Ta bort en kö

### <a name="microsoft-azure-powershell-storage-cmdlets"></a>Microsoft Azure PowerShell lagrings-cmdletar
* [PowerShell-cmdlets för lagring](/powershell/module/azurerm.storage#storage)

### <a name="microsoft-azure-storage-explorer"></a>Microsoft Azure Storage Explorer
* [Microsoft Azure Storage Explorer](../../vs-azure-tools-storage-manage-with-storage-explorer.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json) är en kostnadsfri, fristående app från Microsoft som gör det möjligt att arbeta visuellt med Azure Storage-data i Windows, macOS och Linux.