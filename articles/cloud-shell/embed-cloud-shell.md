---
title: Bädda in Azure Cloudshell | Microsoft Docs
description: Lär dig att bädda in Azure Cloud Shell.
services: cloud-shell
documentationcenter: ''
author: maertendMSFT
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: damaerte
ms.openlocfilehash: bbf6c1f3049265961559ea34c8b748b2b4d263b6
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57995980"
---
# <a name="embed-azure-cloud-shell"></a>Embed Azure Cloud Shell

Bädda in Cloud Shell kan utvecklare och innehåll skrivare direkt öppna Cloud Shell från en dedikerad URL [shell.azure.com](https://shell.azure.com). Detta öppnar omedelbart alla fördelar med Cloud Shell-autentisering, verktyg, och uppdaterade Azure CLI/Azure PowerShell-verktygen till dina användare.

Vanliga storlekar knappen

[![Vanliga starta](https://shell.azure.com/images/launchcloudshell.png "starta Azure Cloud Shell")](https://shell.azure.com)

Stor storlek knapp

[![Stora starta](https://shell.azure.com/images/launchcloudshell@2x.png "starta Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Så här gör du

Integrera Cloud Shell Start-knappen i markdown-filer genom att kopiera följande:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML att bädda in ett popup-fönster Cloud Shell är nedan:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Anpassa upplevelsen

Ange en specifik skalupplevelse genom att utöka din URL.

|Upplevelse   |URL   |
|---|---|
|Senast använda shell   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md)<br>
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md)
