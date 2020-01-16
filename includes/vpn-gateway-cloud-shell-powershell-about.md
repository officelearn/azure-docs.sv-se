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
ms.openlocfilehash: 684b212ca771af6c336cf6239e18ea367f2da5ce
ms.sourcegitcommit: 05cdbb71b621c4dcc2ae2d92ca8c20f216ec9bc4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/16/2020
ms.locfileid: "76045109"
---
Den här artikeln använder PowerShell-cmdletar. Om du vill köra cmdlets kan du använda Azure Cloud Shell, en interaktiv gränssnitts miljö som finns i Azure och som används via webbläsaren. Azure Cloud Shell levereras med Azure PowerShell-cmdletar förinstallerade.

Om du vill köra en kod som finns i den här artikeln på Azure Cloud Shell öppnar du en Cloud Shell-session, använder **kopierings** knappen i ett kodblock för att kopiera koden och klistrar in den i Cloud Shell-sessionen med __CTRL + SHIFT + v__ på Windows och Linux, eller __cmd + SHIFT + V__ på MacOS. Inklistrad text körs inte automatiskt, så tryck på **RETUR** för att köra koden.

Du kan starta Azure Cloud Shell med:

|  |   |
|-----------------------------------------------|---|
| Välj **Prova** i det övre högra hörnet av ett kodblock. Texten kopieras __inte__ automatiskt till Cloud Shell. | ![Exempel på hur du provar Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Öppna [Shell.Azure.com](https://shell.azure.com) i webbläsaren. | [knappen ![starta Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Välj knappen **Cloud Shell** på menyn längst upp till höger i [Azure Portal](https://portal.azure.com). | ![Cloud Shell-knappen i Azure Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Kör PowerShell lokalt**

Du kan också installera och köra Azure PowerShell-cmdlets lokalt på datorn. PowerShell-cmdletar uppdateras ofta. Om du inte kör den senaste versionen kan de värden som anges i instruktionerna Miss lyckas. Ta reda på vilka versioner av Azure PowerShell som är installerade på datorn genom att använda `Get-Module -ListAvailable Az`-cmdleten. Information om hur du installerar eller uppdaterar finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).

Om du kör PowerShell lokalt, se till att köra "Connect-AzAccount" för att skapa din anslutning till Azure.