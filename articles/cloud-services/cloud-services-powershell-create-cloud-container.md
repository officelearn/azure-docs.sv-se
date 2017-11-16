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
ms.openlocfilehash: cd703feb7bf5af765fc3a5448464499aa7b48d6a
ms.sourcegitcommit: afc78e4fdef08e4ef75e3456fdfe3709d3c3680b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/16/2017
---
# <a name="use-an-azure-powershell-command-to-create-an-empty-cloud-service-container"></a>Använd en Azure PowerShell-kommando för att skapa en tom cloud service-behållare
Den här artikeln beskriver hur du snabbt skapa en behållare för molntjänster med hjälp av Azure PowerShell-cmdlets. Följ stegen nedan:

1. Installera Microsoft Azure PowerShell-cmdlet från den [Azure PowerShell laddar ned](http://aka.ms/webpi-azps) sidan.
2. Öppna PowerShell-Kommandotolken.
3. Använd den [Add-AzureAccount](https://msdn.microsoft.com/library/dn495128.aspx) att logga in.

   > [!NOTE]
   > För mer information om att installera Azure PowerShell-cmdlet och ansluter till din Azure-prenumeration, referera till [hur du installerar och konfigurerar du Azure PowerShell](/powershell/azure/overview).
   >
   >
4. Använd den **New-AzureService** för att skapa en tom Azure cloud service-behållare.

    ```
    New-AzureService [-ServiceName] <String> [-AffinityGroup] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
    New-AzureService [-ServiceName] <String> [-Location] <String> [[-Label] <String>] [[-Description] <String>] [[-ReverseDnsFqdn] <String>] [<CommonParameters>]
   ```
5. Så här gör att anropa cmdlet:

   ```
   New-AzureService -ServiceName "mytestcloudservice" -Location "Central US" -Label "mytestcloudservice"
   ```

Mer information om hur du skapar Azure-Molntjänsten, kör du:

```
Get-help New-AzureService
```

### <a name="next-steps"></a>Nästa steg
* För att hantera molndistribution för tjänsten, referera till den [Get-AzureService](https://msdn.microsoft.com/library/azure/dn495131.aspx), [Remove-AzureService](https://msdn.microsoft.com/library/azure/dn495120.aspx), och [Set-AzureService](https://msdn.microsoft.com/library/azure/dn495242.aspx) kommandon. Du kan också läsa [hur du konfigurerar molntjänster](cloud-services-how-to-configure-portal.md) för ytterligare information.
* Om du vill publicera ditt molntjänstprojekt till Azure, referera till den **PublishCloudService.ps1** kodexempel från [kontinuerlig leverans för Molntjänsten i Azure](cloud-services-dotnet-continuous-delivery.md).
