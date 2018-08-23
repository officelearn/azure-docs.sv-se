---
title: Bädda in Azure Cloudshell | Microsoft Docs
description: Lär dig att bädda in Azure Cloud Shell.
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
ms.openlocfilehash: 0bd5382e5ea37f7c3c52d119e9d39fe7e0bfdc7c
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/09/2018
ms.locfileid: "42056917"
---
# <a name="embed-azure-cloud-shell"></a>Bädda in Azure Cloudshell

Bädda in Cloud Shell kan utvecklare och innehåll skrivare direkt öppna Cloud Shell från en dedikerad URL [shell.azure.com](https://shell.azure.com). Detta öppnar omedelbart alla fördelar med Cloud Shell-autentisering, verktyg, och uppdaterade Azure CLI/Azure PowerShell-verktygen till dina användare.

Vanliga storlekar knappen

[![](https://shell.azure.com/images/launchcloudshell.png "Starta Azure Cloudshell")](https://shell.azure.com)

Stor storlek knapp

[![](https://shell.azure.com/images/launchcloudshell@2x.png "Starta Azure Cloudshell")](https://shell.azure.com)

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
|Bash                       |[Shell.Azure.com/Bash](https://shell.azure.com/bash)       |
|PowerShell                 |[Shell.Azure.com/PowerShell](https://shell.azure.com/powershell) |

## <a name="next-steps"></a>Nästa steg
[Bash i Cloud Shell-Snabbstart](quickstart.md)<br>
[PowerShell i Cloud Shell-Snabbstart](quickstart-powershell.md)
