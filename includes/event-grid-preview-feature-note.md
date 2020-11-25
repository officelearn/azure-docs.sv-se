---
title: ta med fil
description: ta med fil
services: event-grid
author: tfitzmac
ms.service: event-grid
ms.topic: include
ms.date: 11/06/2018
ms.author: tomfitz
ms.custom: include file
ms.openlocfilehash: 644669ea27938e385e11f3b1911a23ab30829a95
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96027569"
---
Den här funktionen är en förhandsversion. Om du vill använda den måste du installera ett för hands versions tillägg eller en modul.

### <a name="install-extension-for-azure-cli"></a>Installera tillägg för Azure CLI

För Azure CLI behöver du Event Grid- [tillägget](/cli/azure/azure-cli-extensions-list).

I [CloudShell](../articles/cloud-shell/quickstart.md):

* Om du har installerat tillägget tidigare uppdaterar du det `az extension update -n eventgrid`
* Om du inte har installerat tillägget tidigare installerar du det `az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI](/cli/azure/install-azure-cli). Kontrol lera att du har den senaste versionen genom att kontrol lera med `az --version` .
1. Avinstallera tidigare versioner av tillägget `az extension remove -n eventgrid`
1. Installera `eventgrid` tillägget med `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Installera modul för PowerShell

För PowerShell behöver du [modulen AzureRM. EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

I [CloudShell](../articles/cloud-shell/quickstart-powershell.md):

* Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

För en lokal installation:

1. Öppna PowerShell-konsolen som administratör
1. Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Om `-AllowPrerelease` parametern inte är tillgänglig använder du följande steg:

1. Kör `Install-Module PowerShellGet -Force`
1. Kör `Update-Module PowerShellGet`
1. Stäng PowerShell-konsolen
1. Starta om PowerShell som administratör
1. Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`