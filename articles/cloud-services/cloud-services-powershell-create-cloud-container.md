---
title: Skapa en moln tjänst behållare med PowerShell | Microsoft Docs
description: Den här artikeln beskriver hur du skapar en moln tjänst behållare med PowerShell. Behållaren är värd för webb-och arbets roller.
services: cloud-services
documentationcenter: .net
author: cawaMS
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: ef9d3c7c479df9c71a855f0a243b5b9d0da947d7
ms.sourcegitcommit: 4b647be06d677151eb9db7dccc2bd7a8379e5871
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/19/2019
ms.locfileid: "68359500"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använd ett Azure PowerShell-kommando för att skapa en tom moln tjänst behållare

Den här artikeln förklarar hur du snabbt skapar en Cloud Services behållare med hjälp av Azure PowerShell-cmdletar. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdleten från sidan [Azure PowerShell nedladdningar](https://aka.ms/webpi-azps) .
2. Öppna PowerShell-Kommandotolken.
3. Använd [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) för att logga in.

   > [!NOTE]
   > Mer information om hur du installerar Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration finns i så [här installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Använd cmdleten **New-AzureService** för att skapa en tom moln tjänst behållare i Azure.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Följ det här exemplet för att anropa cmdleten:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure Cloud Service får du genom att köra:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg

* Om du vill hantera moln tjänst distributionen läser du kommandona [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)och [set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) . Du kan också se [hur du konfigurerar Cloud Services](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Information om hur du publicerar ditt moln tjänst projekt till Azure finns i kod exemplet **PublishCloudService. ps1** från [arkived Cloud Services](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery)-lagringsplatsen.