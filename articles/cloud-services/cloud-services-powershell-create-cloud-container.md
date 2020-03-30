---
title: Skapa en molntjänstbehållare med PowerShell | Microsoft-dokument
description: I den här artikeln beskrivs hur du skapar en molntjänstbehållare med PowerShell. Behållaren är värd för webb- och arbetsroller.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "68359500"
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använda ett Azure PowerShell-kommando för att skapa en tom molntjänstbehållare

I den här artikeln beskrivs hur du snabbt skapar en Cloud Services-behållare med Azure PowerShell-cmdlets. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdleten från sidan [Azure PowerShell-hämtningar.](https://aka.ms/webpi-azps)
2. Öppna kommandotolken i PowerShell.
3. Använd [Lägg till AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-4.0.0) för att logga in.

   > [!NOTE]
   > Mer information om hur du installerar Azure PowerShell-cmdlet och ansluta till din Azure-prenumeration finns i [Så här installerar och konfigurerar](/powershell/azure/overview)du Azure PowerShell .
   >
   >
4. Använd cmdleten **New-AzureService** för att skapa en tom Azure-molntjänstbehållare.

   ```
   New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```

5. Följ det här exemplet om du vill anropa cmdlet:

   ```powershell
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Om du vill ha mer information om hur du skapar Azure-molntjänsten kör du:

```powershell
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg

* Om du vill hantera molntjänstdistributionen läser du kommandona [Get-AzureService](/powershell/module/servicemanagement/azure/Get-AzureService?view=azuresmps-4.0.0), [Remove-AzureService](/powershell/module/servicemanagement/azure/Remove-AzureService?view=azuresmps-4.0.0)och [Set-AzureService.](/powershell/module/servicemanagement/azure/set-azureservice?view=azuresmps-4.0.0) Du kan också läsa [Så här konfigurerar du molntjänster](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Information om hur du publicerar ditt molntjänstprojekt på Azure finns i kodexmplet **PublishCloudService.ps1** från [databasen för arkiverade molntjänster](https://github.com/MicrosoftDocs/azure-cloud-services-files/tree/master/Scripts/cloud-services-continuous-delivery).