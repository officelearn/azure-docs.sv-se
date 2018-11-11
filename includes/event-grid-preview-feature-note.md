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
ms.openlocfilehash: 85a1579e32b4c216f234f77c76316bedeaea77b0
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51285737"
---
Den här funktionen är en förhandsversion. För att använda den, måste du installera en förhandsversion av tillägg eller modul.

### <a name="install-extension-for-azure-cli"></a>Installera tillägget för Azure CLI

För Azure CLI, behöver du den [Event Grid-tillägget](/cli/azure/azure-cli-extensions-list).

I [CloudShell](/azure/cloud-shell/quickstart):

* Om du har installerat tillägget tidigare kan du uppdatera den `az extension update -n eventgrid`
* Om du inte tidigare har installerat tillägget, installerar du det `az extension add -n eventgrid`

För en lokal installation:

1. Avinstallera Azure CLI lokalt.
1. Installera den [senaste versionen](/cli/azure/install-azure-cli) av Azure CLI.
1. Starta Kommandotolken.
1. Avinstallera tidigare versioner av tillägget `az extension remove -n eventgrid`
1. Installera tillägget `az extension add -n eventgrid`

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
