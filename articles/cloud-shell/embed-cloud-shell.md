---
title: Bädda in Azure Cloud Shell | Microsoft-dokument
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "60614314"
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure Cloud Shell

Inbäddning Cloud Shell gör det möjligt för utvecklare och innehållsförfattare att direkt öppna Cloud Shell från en dedikerad URL, [shell.azure.com](https://shell.azure.com). Detta ger omedelbart den fulla kraften i Cloud Shells autentisering, verktyg och uppdaterade Azure CLI/Azure PowerShell-verktyg till användarna.

Knappen Normal storlek

[![Regelbunden lansering](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloud Shell")](https://shell.azure.com)

Knappen Stor storlek

[![Stor lansering](https://shell.azure.com/images/launchcloudshell@2x.png "Starta Azure Cloud Shell")](https://shell.azure.com)

## <a name="how-to"></a>Så här gör du

Integrera Cloud Shells startknapp i markdown-filer genom att kopiera följande:

```markdown
[![Launch Cloud Shell](https://shell.azure.com/images/launchcloudshell.png "Launch Cloud Shell")](https://shell.azure.com)
```

HTML för att bädda in ett popup-molnskal finns nedan:
```html
<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><img alt="Launch Azure Cloud Shell" src="https://shell.azure.com/images/launchcloudshell.png" /></a>
```

## <a name="customize-experience"></a>Anpassa upplevelsen

Ange en specifik skalupplevelse genom att utöka webbadressen.

|Erfarenhet   |URL   |
|---|---|
|Senast använda skal   |[shell.azure.com](https://shell.azure.com)           |
|Bash                       |[shell.azure.com/bash](https://shell.azure.com/bash)       |
|PowerShell                 |[shell.azure.com/powershell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell snabbstart](quickstart.md)<br>
[PowerShell i Cloud Shell snabbstart](quickstart-powershell.md)
