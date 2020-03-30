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
ms.openlocfilehash: d32beb2d799a60cb9c5be061c39e4ec834da8dcf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "66814841"
---
Den här funktionen är en förhandsversion. Om du vill använda den måste du installera ett förhandsgranskningstillägg eller en modul.

### <a name="install-extension-for-azure-cli"></a>Installationstillägg för Azure CLI

För Azure CLI behöver du [tillägget Event Grid](/cli/azure/azure-cli-extensions-list).

I [CloudShell:](/azure/cloud-shell/quickstart)

* Om du har installerat tillägget tidigare uppdaterar du det`az extension update -n eventgrid`
* Om du inte har installerat tillägget tidigare installerar du det`az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI](/cli/azure/install-azure-cli). Kontrollera att du har den senaste `az --version`versionen genom att kontrollera med .
1. Avinstallera tidigare versioner av tillägget`az extension remove -n eventgrid`
1. Installera `eventgrid` tillägget med`az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Installera modul för PowerShell

För PowerShell behöver du [modulen AzureRM.EventGrid](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

I [CloudShell:](/azure/cloud-shell/quickstart-powershell)

* Installera modulen`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

För en lokal installation:

1. Öppna PowerShell-konsolen som administratör
1. Installera modulen`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Om `-AllowPrerelease` parametern inte är tillgänglig gör du så här:

1. Kör `Install-Module PowerShellGet -Force`
1. Kör `Update-Module PowerShellGet`
1. Stänga PowerShell-konsolen
1. Starta om PowerShell som administratör
1. Installera modulen`Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
