---
title: 'PowerShell: Ladda upp och bind SSL'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du binder ett anpassat SSL-certifikat till en app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: seodec18
ms.openlocfilehash: 4e5e1835927b135037d416ebaca84157c851d361
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/02/2019
ms.locfileid: "74685598"
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app-using-powershell"></a>Binda ett anpassat SSL-certifikat till en webbapp med hjälp av PowerShell

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och binder sedan SSL-certifikatet för ett anpassat domännamn till den. 

Om det behövs installerar du Azure PowerShell med hjälp av instruktionerna i [Azure PowerShell-guiden](/powershell/azure/overview) och kör sedan `Connect-AzAccount` för att skapa en anslutning till Azure. Se också till att:

- En anslutning har skapats med Azure med hjälp av kommandot `az login`.
- Du har åtkomst till din domänregistrators DNS-konfigurationssida.
- Du har en giltig .PFX-fil och dess lösenord för SSL-certifikatet du vill ladda upp och binda.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Rensa distribution 

När skriptet har körts kan följande kommando användas för att ta bort resursgruppen, webbappen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) | Skapar en App Service-plan. |
| [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) | Skapar en webbapp. |
| [Set-AzAppServicePlan](/powershell/module/az.websites/set-azappserviceplan) | Ändrar en App Service-plan för att ändra prisnivån. |
| [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) | Ändrar konfigurationen för en webbapp. |
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Skapar en SSL-certifikatbindning för en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
