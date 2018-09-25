---
title: Azure Cloud Shell-användningsdata för LUIS
titleSuffix: Azure Cognitive Services
description: Lär dig mer om att hämta information om användningen i Azure Cloud Shell för LUIS.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2017
ms.author: diberry
ms.openlocfilehash: b0a02d2e5e0a1f1765098e1b1e258424b6443878
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "47039589"
---
# <a name="usage-data-for-luis-service-from-azure-cloud-shell"></a>Användningsdata för LUIS-tjänsten från Azure Cloud Shell
Azure-portalen kan du använda PowerShell-cmdlets för att arbeta med LUIS-resurser. 

Dessa cmdletar kan du [skapa](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) en LUIS-prenumeration, hämta information om prenumerationen, inklusive [användning](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), och [ta bort](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) prenumerationen. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Cloud shell storage-konto och autentisering
För att kunna använda PowerShell i Azure-portalen [cloud shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), måste du ha ett Azure storage-konto. Om du inte har en [lagringskonto](https://docs.microsoft.com/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), uppmanas du att skapa en. Storage-konto kan du spara PowerShell-skript i cloudshell.  

Du måste också autentisera till Azure i cloudshell för att komma åt några resurser. 

När du har ett lagringskonto och har autentiserats kan köra du PowerShell-cmdletar.

## <a name="open-cloud-shell"></a>Öppna Cloud Shell
När du använder Azure portal cloudshell är du alltid den mest aktuella versionen av PowerShell. 

Använd den **starta Cloud Shell** knappen för att öppna Cloud Shell eller öppna en webbläsare med [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>Användning av LUIS slutpunktsinformation

PowerShell-cmdleten för 6.x `Get-AzureRmCognitiveServicesAccountUsage`, innehåller användningsinformation om för Microsoft Cognitive Services, inklusive LUIS. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) kräver resursgruppen och resursnamnet för tjänsten. 

Kommandosyntax är:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

I följande exempel, resursgruppens namn är `luis-westus-rg` och LUIS prenumeration tjänstnamnet är `luis-westus-1`. Båda dessa namn är valt när LUIS-tjänst skapas. 

Cmdleten returnerar information om användningen av 16 i 10 000 endpoint träffar som används i en 30 dagars period med perioden som slutar på 7 juni:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Spara kommandot som *.ps1 i Azure storage-kontot som är associerade med cloudshell en PowerShell-fil och kör när som helst. 

![Köra skript från storage](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

När skriptet har sparats på cloud enhet, kan du köra PowerShell-skriptet från Azure phone app-molnskalet. 

![Köra skript från storage i telefonapp](./media/luis-how-to-manage-from-powershell/phone-app.png)