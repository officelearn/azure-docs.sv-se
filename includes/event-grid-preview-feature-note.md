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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "66814841"
---
Den här funktionen är en förhandsversion. För att använda den, måste du installera en förhandsversion av tillägg eller modul.

### <a name="install-extension-for-azure-cli"></a>Installera tillägget för Azure CLI

För Azure CLI, behöver du den [Event Grid-tillägget](/cli/azure/azure-cli-extensions-list).

I [CloudShell](/azure/cloud-shell/quickstart):

* Om du har installerat tillägget tidigare kan du uppdatera den `az extension update -n eventgrid`
* Om du inte tidigare har installerat tillägget, installerar du det `az extension add -n eventgrid`

För en lokal installation:

1. [Installera Azure CLI](/cli/azure/install-azure-cli). Se till att du har den senaste versionen genom att kontrollera med `az --version`.
1. Avinstallera tidigare versioner av tillägget `az extension remove -n eventgrid`
1. Installera den `eventgrid` tillägg med `az extension add -n eventgrid`

### <a name="install-module-for-powershell"></a>Installera modulen för PowerShell

För PowerShell, måste den [AzureRM.EventGrid modulen](https://www.powershellgallery.com/packages/AzureRM.EventGrid/0.4.1-preview).

I [CloudShell](/azure/cloud-shell/quickstart-powershell):

* Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

För en lokal installation:

1. Öppna PowerShell-konsol som administratör
1. Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`

Om den `-AllowPrerelease` parametern är inte tillgängligt, Använd följande steg:

1. Kör `Install-Module PowerShellGet -Force`
1. Kör `Update-Module PowerShellGet`
1. Stäng konsolen PowerShell
1. Starta om PowerShell som administratör
1. Installera modulen `Install-Module -Name AzureRM.EventGrid -AllowPrerelease -Force -Repository PSGallery`
