---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 3a065e5cd6e951544b3147d5833b4ad300ae5e30
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/26/2018
ms.locfileid: "50166158"
---
[Microsoft Azure-konfigurationshanterarens bibliotek för .NET](https://www.nuget.org/packages/Microsoft.WindowsAzure.ConfigurationManager/) erbjuder en klass för parsning av en anslutningssträng från en konfigurationsfil. [CloudConfigurationManager-klassen](https://msdn.microsoft.com/library/azure/mt634650.aspx) parsar konfigurationsinställningar oavsett om klientprogrammet körs på skrivbordet, på en mobil enhet, i en virtuell Azure-dator eller i en Azure-molntjänst.

Om du vill referera till CloudConfigurationManager-paketet lägger du till följande `using`-direktiv:

```csharp
using Microsoft.Azure; //Namespace for CloudConfigurationManager
```

Här följer ett exempel som visar hur du kan hämta en anslutningssträng från en konfigurationsfil:

```csharp
// Parse the connection string and return a reference to the storage account.
CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
    CloudConfigurationManager.GetSetting("StorageConnectionString"));
```

Du måste inte använda Azure Configuration Manager. Du kan också använda ett API som .NET Frameworks [ConfigurationManager](https://msdn.microsoft.com/library/system.configuration.configurationmanager.aspx)-klass.

