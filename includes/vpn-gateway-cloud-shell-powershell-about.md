---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 06/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 01358d13d30358a9950cbe35622df065fc5a6de5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67133660"
---
Den här artikeln används PowerShell-cmdletar. Om du vill köra cmdlets, kan du använda Azure Cloud Shell, ett interaktivt gränssnitt-miljö i Azure och används via webbläsaren. Azure Cloud Shell levereras med Azure PowerShell-cmdlets som redan är installerat.

För att köra kod i den här artikeln om Azure Cloud Shell, öppna Cloud Shell-sessionen, använda den **kopia** knappen i ett kodblock att kopiera koden och klistra in den i Cloud Shell-sessionen med __Ctrl + Skift + V__ på Windows och Linux, eller __Cmd + SKIFT + V__ på macOS. Inklistrade texten körs inte automatiskt, trycker du på **RETUR** att köra kod.

Du kan starta Azure Cloud Shell med:

|  |   |
|-----------------------------------------------|---|
| Välj **Prova** i det övre högra hörnet av ett kodblock. Detta __inte__ automatiskt kopiera text till Cloud Shell. | ![Exempel på Testa för Azure Cloudshell](./media/cloud-shell-try-it/cli-try-it.png) |
| Öppna [shell.azure.com](https://shell.azure.com) i webbläsaren. | [![Starta Azure Cloud Shell-knappen](./media/cloud-shell-try-it/launchcloudshell.png)](https://shell.azure.com) |
| Välj knappen **Cloud Shell** på menyn längst upp till höger i [Azure Portal](https://portal.azure.com). | ![Cloud Shell-knappen i Azure Portal](./media/cloud-shell-try-it/cloud-shell-menu.png) |

**Kör PowerShell lokalt**

Du kan även installera och köra Azure PowerShell-cmdlets lokalt på datorn. PowerShell-cmdlets uppdateras ofta. Om du inte kör den senaste versionen, misslyckas värdena som anges i instruktionerna. Använd för att hitta versionerna av Azure PowerShell installerad på datorn, den `Get-Module -ListAvailable Az` cmdlet. Om du vill installera eller uppdatera kan se [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).
