---
title: Visa THOMAS användning från Azure Cloud Shell | Microsoft Docs
description: Lär dig mer om att hämta information om användning i Azure Cloud Shell för THOMAS.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/08/2017
ms.author: v-geberr
ms.openlocfilehash: 2de25645e5377efdd53bcc980695804d34db5ee2
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "35354567"
---
# <a name="manage-luis-service-from-azure-cloud-shell"></a>Hantera THOMAS tjänsten från Azure Cloud Shell
Azure-portalen kan du använda PowerShell-cmdlets för att arbeta med THOMAS resurser. 

Dessa cmdletar kan du [skapa](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/new-azurermcognitiveservicesaccount?view=azurermps-6.0.0) THOMAS-prenumeration, hämta information om prenumerationen, inklusive [användning](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0), och [ta bort](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/remove-azurermcognitiveservicesaccount?view=azurermps-6.0.0) prenumerationen. 

## <a name="cloud-shell-storage-account-and-authentication"></a>Molnet shell storage-konto och autentisering
För att kunna använda PowerShell i Azure portal [molnet shell](https://docs.microsoft.com/azure/cloud-shell/quickstart-powershell), måste du ha ett Azure storage-konto. Om du inte har en [lagringskonto](https://docs.microsoft.com/en-us/azure/cloud-shell/persisting-shell-storage#set-up-a-clouddrive-file-share), uppmanas du att skapa en. Storage-konto kan du spara PowerShell-skript i molnet-gränssnittet.  

Du måste också att autentisera till Azure i molnet shell åtkomst till resurser. 

När du har ett lagringskonto och autentiseras, kan du köra PowerShell-cmdlets.

## <a name="open-cloud-shell"></a>Öppna Cloud Shell
När du använder Azure portal cloud-gränssnittet kan är du alltid den senaste versionen av PowerShell. 

Använd den **starta molnet Shell** för att öppna moln-gränssnittet eller öppna en webbläsare med [ https://shell.azure.com ](https://shell.azure.com). 

<a style="cursor:pointer" onclick='javascript:window.open("https://shell.azure.com", "_blank", "toolbar=no,scrollbars=yes,resizable=yes,menubar=no,location=no,status=no")'><image src="https://shell.azure.com/images/launchcloudshell.png" /></a>

## <a name="luis-endpoint-usage-information"></a>THOMAS endpoint användningsinformation

PowerShell-cmdlet för 6.x `Get-AzureRmCognitiveServicesAccountUsage`, innehåller information om Microsoft kognitiva tjänster, inklusive THOMAS användning. [Get-AzureRmCognitiveServicesAccountUsage](https://docs.microsoft.com/powershell/module/azurerm.cognitiveservices/get-azurermcognitiveservicesaccountusage?view=azurermps-6.0.0) kräver resursgrupp och resursnamn av tjänsten. 

Kommandosyntaxen är:

```
Get-AzureRmCognitiveServicesAccountUsage -ResourceGroupName my-resource-group -Name my-luis-service-name
```

I följande exempel resursgruppens namn är `luis-westus-rg` och THOMAS prenumeration tjänstnamnet är `luis-westus-1`. Båda dessa namn är valt när tjänsten THOMAS skapas. 

Cmdlet returnerar information om användning av 16 i 10 000 endpoint träffar som används i en 30-dagarsperiod med perioden som slutar på 7 juni:

```
CurrentValue  : 16
Name          : LUIS.Calls
Limit         : 10000
Status        : Included
Unit          : Count
QuotaPeriod   : 30.00:00:00
NextResetTime : 2018-06-07T18:28:52Z
```

Spara kommandot som en PowerShell-fil, *.ps1 i Azure storage-konto som är kopplade till molnet shell och kör när som helst. 

![Kör skriptet från lagring](./media/luis-how-to-manage-from-powershell/run-script-from-storage.png)

När skriptet har sparats på molnet-enhet kan kan du köra PowerShell-skriptet från Azure phone app molnet shell. 

![Kör skriptet från lagring i telefonapp](./media/luis-how-to-manage-from-powershell/phone-app.png)