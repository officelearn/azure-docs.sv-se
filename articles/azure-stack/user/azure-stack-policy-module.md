---
title: Använda Azure Stack principmodul | Microsoft Docs
description: Lär dig att begränsa en Azure-prenumeration fungerar som en prenumeration på Azure-stacken
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 937ef34f-14d4-4ea9-960b-362ba986f000
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: mabrigg
ms.openlocfilehash: 538cf0eb0f9f2351f7a71a1dd24aab05938963c5
ms.sourcegitcommit: 96089449d17548263691d40e4f1e8f9557561197
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/17/2018
---
# <a name="manage-azure-policy-using-the-azure-stack-policy-module"></a>Hantera Azure principer som använder Azure Stack-principmodulen

*Gäller för: Azure Stack integrerat system och Azure-stacken Development Kit*

Azure-stacken principmodulen kan du konfigurera en Azure-prenumeration med samma version och tjänsttillgänglighet som Azure-stacken.  Modulen använder den **ny AzureRMPolicyAssignment** för att skapa en Azure-princip som begränsar de typer av resurser och tjänster som är tillgängliga i en prenumeration.  Du kan använda din Azure-prenumeration när du har konfigurerat principen för att utveckla appar som mål för Azure-stacken.

## <a name="install-the-module"></a>Installera modulen

1. Installera rätt version av AzureRM PowerShell-modulen enligt beskrivningen i steg 1 av [installera PowerShell för Azure-stacken](azure-stack-powershell-install.md).
2. [Hämta Azure Stack-verktyg från GitHub](azure-stack-powershell-download.md)
3. [Konfigurera PowerShell för användning med Azure Stack](azure-stack-powershell-configure-user.md)

4. Importera modulen AzureStack.Policy.psm1:

   ```PowerShell
   Import-Module .\Policy\AzureStack.Policy.psm1
   ```

## <a name="apply-policy-to-azure-subscription"></a>Tillämpa principer på Azure-prenumeration

Du kan använda följande kommando för att tillämpa en standardprincip för Azure-stacken mot din Azure-prenumeration. Innan du kör det här kommandot ersätter *Azure prenumerationsnamn* med din Azure-prenumeration.

```PowerShell
Add-AzureRmAccount
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
$subscriptionID = $s.Subscription.SubscriptionId
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID

```

## <a name="apply-policy-to-a-resource-group"></a>Tillämpa principer på en resursgrupp

Du kanske vill tillämpa principer som är mer detaljerade. Exempelvis kanske vissa resurser som körs i samma prenumeration. Du kan definiera principen programmet till en viss resursgrupp, vilket gör att du kan testa dina appar för Azure-stacken använder Azure-resurser. Innan du kör följande kommando ersätter *Azure prenumerationsnamn* med namn på din Azure-prenumeration.

```PowerShell
Add-AzureRmAccount
$rgName = 'myRG01'
$s = Select-AzureRmSubscription -SubscriptionName "<Azure Subscription Name>"
$policy = New-AzureRmPolicyDefinition -Name AzureStackPolicyDefinition -Policy (Get-AzsPolicy)
New-AzureRmPolicyAssignment -Name AzureStack -PolicyDefinition $policy -Scope /subscriptions/$subscriptionID/resourceGroups/$rgName

```

## <a name="policy-in-action"></a>Principen i åtgärd

När du har distribuerat Azure principen, du får ett felmeddelande när du försöker distribuera en resurs som förbjuden av principen.

![Resultatet av resursen distributionen misslyckades på grund av princip begränsning](./media/azure-stack-policy-module/image1.png)

## <a name="next-steps"></a>Nästa steg

* [Distribuera mallar med PowerShell](azure-stack-deploy-template-powershell.md)
* [Distribuera mallar med Azure CLI](azure-stack-deploy-template-command-line.md)
* [Distribuera mallar med Visual Studio](azure-stack-deploy-template-visual-studio.md)
