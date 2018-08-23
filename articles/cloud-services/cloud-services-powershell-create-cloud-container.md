---
title: Skapa en cloud service-behållare med PowerShell | Microsoft Docs
description: Den här artikeln förklarar hur du skapar en molntjänstbehållare med PowerShell. Behållaren är värd för webb-och arbetsroller.
services: cloud-services
documentationcenter: .net
author: cawaMS
manager: timlt
editor: ''
ms.assetid: c8f32469-610e-4f37-a3aa-4fac5c714e13
ms.service: cloud-services
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: powershell
ms.workload: na
ms.date: 11/18/2016
ms.author: cawa
ms.openlocfilehash: 0a05b4fadf2cd7e794680907d39f355882a4330c
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/10/2018
ms.locfileid: "42058772"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använda Azure PowerShell-kommando för att skapa en tom molntjänstbehållare
Den här artikeln förklarar hur du snabbt skapar en Cloud Services-behållare med Azure PowerShell-cmdlets. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdlet från den [Azure PowerShell hämtar](http://aka.ms/webpi-azps) sidan.
2. Öppna PowerShell-Kommandotolken.
3. Använd den [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) att logga in.

   > [!NOTE]
   > Mer information om att installera Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration finns [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Använd den **New-AzureService** cmdlet för att skapa en tom Azure molntjänstbehållare.

   ```powershell
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Följ det här exemplet för att anropa cmdleten:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure-molntjänst, kör du:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg
* För att hantera molntjänstdistribution, referera till den [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0), och [Set-AzureService](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) kommandon. Du kan också referera till [så här konfigurerar du molntjänster](cloud-services-how-to-configure-portal.md) för ytterligare information.
* För att publicera ditt molntjänstprojekt till Azure måste referera till den **PublishCloudService.ps1** kodexemplet från [arkiverade cloud services-databas](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).
