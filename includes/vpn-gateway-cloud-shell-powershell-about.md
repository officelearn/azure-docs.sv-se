---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/25/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: b878d54f0f52768459dbfc810e47d294b9c8d996
ms.sourcegitcommit: 9d7391e11d69af521a112ca886488caff5808ad6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/25/2018
ms.locfileid: "50097774"
---
Den här artikeln används PowerShell-cmdletar. Om du vill köra cmdlets, kan du använda Azure Cloud Shell, ett interaktivt gränssnitt. Den har vanliga Azure-verktyg förinstallerat och har konfigurerats för användning med ditt konto. Klicka helt enkelt på **Kopiera** för att kopiera koden, klistra sedan in den i Cloud Shell och tryck på RETUR för att köra den. Det finns flera olika sätt att starta Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Klicka på **Prova** i det övre högra hörnet av ett kodblock. | ![Cloud Shell i den här artikeln](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Öppna Cloud Shell i din webbläsare. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Klicka på knappen **Cloud Shell** på menyn längst upp till höger på Azure Portal. | [![Cloud Shell på portalen](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

Om du inte vill använda Azure Cloud Shell, kan du installera PowerShell lokalt i stället. Om du väljer att installera och använda PowerShell lokalt måste du installera den senaste versionen av Azure Resource Managers PowerShell-cmdlet: ar. PowerShell-cmdlets uppdateras ofta och vanligen måste du uppdatera dina PowerShell-cmdlets för att få den senaste funktionen. Om du inte uppdaterar dina PowerShell-cmdletar misslyckas värdena som har angetts. Använd cmdleten ”Get-Module - ListAvailable AzureRM' om du vill ta reda på vilken version av PowerShell som du kör lokalt. Om du behöver uppgradera kan du läsa [Installera Azure PowerShell-modulen](/powershell/azure/install-azurerm-ps). Mer information finns i [Installera och konfigurera Azure PowerShell](/powershell/azure/overview).