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
ms.openlocfilehash: fe7e617ae2263f1388feef3754f6cd16130ed436
ms.sourcegitcommit: 0fab4c4f2940e4c7b2ac5a93fcc52d2d5f7ff367
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059825"
---
Den här artikeln använder PowerShell-cmdletar. Om du vill köra cmdlets kan du använda Azure Cloud Shell, en interaktiv gränssnitts miljö som finns i Azure och som används via webbläsaren. Azure Cloud Shell levereras med Azure PowerShell-cmdletar förinstallerade.

Om du vill köra en kod som finns i den här artikeln på Azure Cloud Shell öppnar du en Cloud Shell-session, använder **kopierings** knappen i ett kodblock för att kopiera koden och klistrar in den i Cloud Shell-sessionen med __CTRL + SHIFT + v__ på Windows och Linux, eller __cmd + SHIFT + V__ på macOS. Inklistrad text körs inte automatiskt, så tryck på **RETUR** för att köra koden.

Du kan starta Azure Cloud Shell med:

|  |   |
|-----------------------------------------------|---|
| Välj **Prova** i det övre högra hörnet av ett kodblock. Texten kopieras __inte__ automatiskt till Cloud Shell. | ![Exempel på hur du provar Azure Cloud Shell](./media/cloud-shell-try-it/hdi-azure-cli-try-it.png) |
| Öppna [Shell.Azure.com](https://shell.azure.com) i webbläsaren. | [![Knappen starta Azure Cloud Shell](./media/cloud-shell-try-it/hdi-launch-cloud-shell.png)](https://shell.azure.com) |
| Välj knappen **Cloud Shell** på menyn längst upp till höger i [Azure Portal](https://portal.azure.com). | ![Cloud Shell-knappen i Azure Portal](./media/cloud-shell-try-it/hdi-cloud-shell-menu.png) |

**Kör PowerShell lokalt**

Du kan också installera och köra Azure PowerShell-cmdlets lokalt på datorn. PowerShell-cmdletar uppdateras ofta. Om du inte kör den senaste versionen kan de värden som anges i instruktionerna Miss lyckas. Använd `Get-Module -ListAvailable Az` cmdleten för att hitta de versioner av Azure PowerShell som är installerade på datorn. Information om hur du installerar eller uppdaterar finns i [installera Azure PowerShell-modulen](/powershell/azure/install-az-ps).
