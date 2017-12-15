---
title: "Bädda in Azure-molnet Shell | Microsoft Docs"
description: "Lär dig att bädda in Shell för Azure-molnet."
services: cloud-shell
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 12/11/2017
ms.author: juluk
ms.openlocfilehash: af5e5b536ced96680b65e2463c9dea159a26ca7b
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure-molnet Shell

Bädda in molnet Shell kan utvecklare och innehåll skrivare direkt öppna moln-gränssnittet från en dedikerad URL [shell.azure.com](https://shell.azure.com). Alla fördelar med molnet Shell-autentisering, tooling, nu omedelbart och uppdaterade Azure CLI-/ Azure PowerShell verktyg för användarna.

[![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure-molnet Shell")](https://shell.azure.com)

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
|Senast använda shell   |Shell.Azure.com           |
|Bash                       |Shell.Azure.com/Bash       |
|PowerShell                 |Shell.Azure.com/PowerShell |

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell-Snabbstart](quickstart.md)<br>
[PowerShell i molnet Shell-Snabbstart](quickstart-powershell.md)