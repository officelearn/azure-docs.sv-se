---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/13/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c8f45e4bb16c05c9f322dd04d2c80f6144744e64
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60320249"
---
Den här artikeln används PowerShell-cmdletar. Om du vill köra cmdlets, kan du använda Azure Cloud Shell. Azure Cloud Shell är ett interaktivt gränssnitt som har vanliga Azure-verktyg som är förinstallerat och har konfigurerats för att använda med ditt konto. Klicka bara på **kopiera** för att kopiera koden, klistra in den i Cloud Shell och tryck sedan på RETUR för att köra den. Det finns flera olika sätt att starta Cloud Shell:

|  |   |
|-----------------------------------------------|---|
| Klicka på **Prova** i det övre högra hörnet av ett kodblock. | ![Cloud Shell i den här artikeln](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-powershell-try-it.png) |
| Öppna Cloud Shell i din webbläsare. | [![https://shell.azure.com/powershell](./media/vpn-gateway-cloud-shell-powershell/launchcloudshell.png)](https://shell.azure.com/powershell) |
| Klicka på knappen **Cloud Shell** på menyn längst upp till höger på Azure Portal. | [![Cloud Shell på portalen](./media/vpn-gateway-cloud-shell-powershell/cloud-shell-menu.png)](https://portal.azure.com) |
|  |  |

**Kör PowerShell lokalt**

Du kan även installera och köra Azure PowerShell-cmdlets lokalt på datorn. PowerShell-cmdlets uppdateras ofta. Om du inte kör den senaste versionen, misslyckas värdena som anges i instruktionerna. Använd för att hitta versionen av PowerShell som du kör lokalt, den `Get-Module -ListAvailable Az` cmdlet. Om du vill installera eller uppdatera kan se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).