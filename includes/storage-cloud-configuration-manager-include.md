---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 761c3a9aecadd9c1eabdb586f95c47e2988720d8
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/02/2019
ms.locfileid: "57251983"
---
[Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil. [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/mt634650.aspx) parsar konfigurationsinställningar oavsett om klientprogrammet körs på skrivbordet, på en mobil enhet, i en virtuell Azure-dator eller i en Azure-molntjänst.

Om du vill referera till CloudConfigurationManager-paketet lägger du till följande `using`-direktiv:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
using Microsoft.WindowsAzure.Storage;
```

Här följer ett exempel som visar hur du kan hämta en anslutningssträng från en konfigurationsfil:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Du måste inte använda Azure Configuration Manager. Du kan också använda ett API som .NET Frameworks [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)-klass.

