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
ms.openlocfilehash: 7c06a51e7f9f402b2ec10e440ca98125a7f2a7cf
ms.sourcegitcommit: 922687d91838b77c038c68b415ab87d94729555e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/13/2017
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure-molnet Shell

Bädda in molnet Shell kan utvecklare och innehåll skrivare direkt öppna moln-gränssnittet från en dedikerad URL [shell.azure.com](https://shell.azure.com). Det här låser upp alla fördelar med molnet Shell-autentisering, verktygsuppsättning och uppdaterade Azure CLI-/ Azure PowerShell-verktyg.

## <a name="how-to"></a>Så här gör du

Integrera molnet Shell Start-knappen i markdown-filer genom att kopiera följande:

```markdown
[![Launch Cloud Shell](https:shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML för att bädda in ett popup-gränssnitt för molnet understiger:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="next-steps"></a>Nästa steg
[Bash i molnet Shell-Snabbstart](quickstart.md)<br>
[PowerShell i molnet Shell-Snabbstart](quickstart-powershell.md)