---
author: mgoedtel
ms.service: log-analytics
ms.topic: include
ms.date: 11/09/2018
ms.author: magoedte
ms.openlocfilehash: 6890c71ac7c265d46cc77751786fea4d0b228588
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "67187736"
---
### <a name="troubleshoot-azure-diagnostics"></a>Felsöka Azure Diagnostics

Resursprovidern Microsoft.insights har inte registrerats om följande felmeddelande visas:

`Failed to update diagnostics for 'resource'. {"code":"Forbidden","message":"Please register the subscription 'subscription id' with Microsoft.Insights."}`

Utför följande steg för att registrera resursprovidern i Azure Portal:

1.  Klicka på *Prenumerationer* i navigeringsfönstret till vänster
2.  Välj den prenumeration som anges i felmeddelandet
3.  Klicka på *Resursprovidrar*
4.  Leta reda på *Microsoft Insights*-providern
5.  Klicka på *Registrera*

![Registrera resursprovidern microsoft.insights](./media/log-analytics-troubleshoot-azure-diagnostics/log-analytics-register-microsoft-diagnostics-resource-provider.png)

När resursprovidern *Microsoft.insights* har registrerats kan du prova att konfigurera diagnostik på nytt.


I PowerShell måste du uppdatera versionen av PowerShell om du får följande felmeddelande:

`Set-AzDiagnosticSetting : A parameter cannot be found that matches parameter name 'WorkspaceId'.`

Uppdatera din version av Azure PowerShell, följ instruktionerna i artikeln [Installera Azure PowerShell.](/powershell/azure/install-az-ps)
