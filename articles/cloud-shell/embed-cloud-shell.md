---
title: Bädda in Azure-molnet Shell | Microsoft Docs
description: Lär dig att bädda in Shell för Azure-molnet.
services: cloud-shell
documentationcenter: ''
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: ''
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: 3ceddb94336fc2703e6f916f05ab1ec3676cb50d
ms.sourcegitcommit: f1c1789f2f2502d683afaf5a2f46cc548c0dea50
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/18/2018
ms.locfileid: "27864892"
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure-molnet Shell

Bädda in molnet Shell kan utvecklare och innehåll skrivare direkt öppna moln-gränssnittet från en dedikerad URL [shell.azure.com](https://shell.azure.com). Alla fördelar med molnet Shell-autentisering, tooling, nu omedelbart och uppdaterade Azure CLI-/ Azure PowerShell verktyg för användarna.

Vanliga storlek knappen

[![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure-molnet Shell")](https://shell.azure.com)

Stor storlek knapp

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Starta Azure-molnet Shell")](https://shell.azure.com)

## <a name="how-to"></a>Så här gör du

Integrera molnet Shell Start-knappen i markdown-filer genom att kopiera följande:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML för att bädda in ett popup-gränssnitt för molnet understiger:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Anpassa upplevelsen

Ange en specifik shell-miljö genom att utöka din URL.
|Upplevelse   |Webbadress   |
|---|---|
|Senast använda shell   |shell.azure.com           |
|Bash                       |shell.azure.com/bash       |
|PowerShell                 |shell.azure.com/powershell |

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell-Snabbstart](quickstart.md)<br>
[PowerShell i molnet Shell-Snabbstart](quickstart-powershell.md)
