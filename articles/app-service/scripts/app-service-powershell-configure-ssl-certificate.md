---
title: Azure PowerShell skriptexempel - bindning som ett anpassat SSL-certifikatet till en webbapp | Microsoft Docs
description: "Azure PowerShell skriptexempel - binda ett SSL-certifikat som är anpassade till en webbapp"
services: app-service\web
documentationcenter: 
author: cephalin
manager: erikre
editor: 
tags: azure-service-management
ms.assetid: 23e83b74-614a-49a0-bc08-7542120eeec5
ms.service: app-service-web
ms.workload: web
ms.devlang: na
ms.topic: sample
ms.date: 03/20/2017
ms.author: cephalin
ms.custom: mvc
ms.openlocfilehash: 851b172cd9218c9ade692e4c9e50a59b4b677ac5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="bind-a-custom-ssl-certificate-to-a-web-app"></a>Koppla en anpassad SSL-certifikatet till en webbapp

Det här exempelskriptet skapar en webbapp i App Service med dess relaterade resurser och sedan binda SSL-certifikatet för ett anpassat domännamn till den. 

Om det behövs installerar du Azure PowerShell med hjälp av anvisningarna i den [Azure PowerShell guiden](/powershell/azure/overview), och kör sedan `Login-AzureRmAccount` att skapa en anslutning med Azure. Se också till att:

- En anslutning med Azure har skapats med hjälp av den `az login` kommando.
- Du har åtkomst till din domänregistrator DNS-konfigurationssidan.
- Du har en giltig. PFX-filen och lösenordet för SSL-certifikatet som du vill ladda upp och binda.

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/app-service/configure-ssl-certificate/configure-ssl-certificate.ps1?highlight=1-3 "Bind a custom SSL certificate to a web app")]

## <a name="clean-up-deployment"></a>Rensa distribution 

Följande kommando kan användas för att ta bort resursgruppen, webbprogram och alla relaterade resurser efter skriptexempel har körts.

```powershell
Remove-AzureRmResourceGroup -Name myResourceGroup -Force
```

## <a name="script-explanation"></a>Skriptet förklaring

Det här skriptet använder följande kommandon. Varje kommando i tabellen länkar till kommandot viss dokumentation.

| Kommando | Anteckningar |
|---|---|
| [Ny AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup) | Skapar en resursgrupp som är lagrade i alla resurser. |
| [Ny AzureRmAppServicePlan](/powershell/module/azurerm.websites/new-azurermappserviceplan) | Skapar en App Service-plan. |
| [Ny AzureRmWebApp](/powershell/module/azurerm.websites/new-azurermwebapp) | Skapar ett webbprogram. |
| [Ange AzureRmAppServicePlan](/powershell/module/azurerm.websites/set-azurermappserviceplan) | Ändrar en App Service-plan för att ändra dess prisnivå. |
| [Ange AzureRmWebApp](/powershell/module/azurerm.websites/set-azurermwebapp) | Ändrar någon konfiguration för ett webbprogram. |
| [Ny AzureRmWebAppSSLBinding](/powershell/module/azurerm.websites/new-azurermwebappsslbinding) | Skapar en SSL-certifikat-bindning för en webbapp. |

## <a name="next-steps"></a>Nästa steg

Mer information om Azure PowerShell-modulen finns [Azure PowerShell dokumentationen](/powershell/azure/overview).

Ytterligare Azure Powershell-exempel för Azure App Service Web Apps finns i den [Azure PowerShell-exempel](../app-service-powershell-samples.md).
