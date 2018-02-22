---
title: "Skapa en cloud service-behållare med PowerShell | Microsoft Docs"
description: "Den här artikeln beskriver hur du skapar en cloud service-behållare med PowerShell. Behållaren är värd för webb-och arbetsroller."
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: 
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: b55a0dd7800448c50897af784092b4a60fa7a25e
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använd en Azure PowerShell-kommando för att skapa en tom cloud service-behållare
Den här artikeln beskriver hur du snabbt skapa en behållare för molntjänster med hjälp av Azure PowerShell-cmdlets. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdlet från den [Azure PowerShell laddar ned](http://aka.ms/webpi-azps) sidan.
2. Öppna PowerShell-Kommandotolken.
3. Använd den [Add-AzureAccount](/powershell/module/Azure/Add-AzureAccount?view=azuresmps-4.0.0) att logga in.

   > [!NOTE]
   > För mer information om att installera Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration, referera till [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Använd den **New-AzureService** för att skapa en tom Azure cloud service-behållare.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Så här gör att anropa cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure-Molntjänsten, kör du:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg
* För att hantera molndistribution för tjänsten, referera till den [Get-AzureService](/powershell/module/Azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/Azure/Remove-AzureService?view=azuresmps-4.0.0), och [Set-AzureService](/powershell/module/azure/set-azureservice?view=azuresmps-4.0.0) kommandon. Du kan också läsa [hur du konfigurerar molntjänster](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Om du vill publicera ditt molntjänstprojekt till Azure, referera till den **PublishCloudService.ps1** kodexempel från [arkiverade cloud services-databasen](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
