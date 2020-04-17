---
title: 'PowerShell: Ladda upp och binda TLS/SSL'
description: Lär dig hur du använder Azure PowerShell för att automatisera distribution och hantering av App Service. Det här exemplet visar hur du binder ett anpassat TLS/SSL-certifikat till en app.
author: msangapu-msft
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.topic: sample
ms.date: 03/20/2017
ms.custom: mvc, seodec18
ms.openlocfilehash: 5116585b701717a82b757cae70f938c321a1f7d1
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532569"
---
# <a name="bind-a-custom-tlsssl-certificate-to-a-web-app-using-powershell"></a>Binda ett anpassat TLS/SSL-certifikat till en webbapp med PowerShell

Det här exempelskriptet skapar en webbapp i App Service med sina relaterade resurser och binder sedan TLS/SSL-certifikatet för ett anpassat domännamn till den. 

Om det behövs installerar du Azure PowerShell med hjälp av instruktionen som finns i [Azure PowerShell-guiden](/powershell/azure/overview)och kör `Connect-AzAccount` sedan för att skapa en anslutning med Azure. Se också till att:

- En anslutning har skapats med Azure med hjälp av kommandot `az login`.
- Du har åtkomst till din domänregistrators DNS-konfigurationssida.
- Du har en giltig . PFX-filen och dess lösenord för TLS/SSL-certifikatet som du vill ladda upp och binda.

## <a name="sample-script"></a>Exempelskript

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

[!code-azurepowershell-interactive[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom TLS/SSL certificate to a web app")]

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
| [New-AzWebAppSSLBinding](/powershell/module/az.websites/new-azwebappsslbinding) | Skapar en TLS/SSL-certifikatbindning för en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).

Fler Azure Powershell-exempel för Azure App Service Web Apps finns i [Azure PowerShell-exempel](../samples-powershell.md).
