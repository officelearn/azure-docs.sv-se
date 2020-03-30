---
title: Associera en brandvägg för webbprogram med en befintlig Azure Application Gateway
description: Lär dig hur du associerar en brandvägg för webbprogram med en befintlig Azure Application Gateway.
services: web-application-firewall
ms.topic: article
author: vhorne
ms.service: web-application-firewall
ms.date: 10/25/2019
ms.author: victorh
ms.openlocfilehash: 1ed2e0cf8cc8cd841d8779462d62ba4852774a3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083911"
---
# <a name="associate-a-waf-policy-with-an-existing-application-gateway"></a>Associera en WAF-princip med en befintlig Application Gateway

Du kan använda Azure PowerShell för att [skapa en WAF-princip,](create-waf-policy-ag.md)men du kanske redan har en Application Gateway och bara vill associera en WAF-princip till den. I den här artikeln gör du just det; du skapar en WAF-princip och associerar den till en redan befintlig Programgateway. 

1. Hämta din application gateway- och brandväggsprincip. Om du inte har en befintlig brandväggsprincip läser du steg 2. 

   ```azurepowershell-interactive
      Connect-AzAccount
      Select-AzSubscription -Subscription "<sub id>"`

      #Get Application Gateway and existing policy object or create new`
      $gw = Get-AzApplicationGateway -Name <Waf v2> -ResourceGroupName <RG name>`
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```

2. (Valfritt) Skapa en brandväggsprincip.

   ```azurepowershell-interactive
      New-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>'
      $policy = Get-AzApplicationGatewayFirewallPolicy -Name <policy name> -ResourceGroupName <RG name>`
   ```
   > [!NOTE]
   > Om du skapar denna WAF-policy för att gå över från en WAF Config till en WAF-policy måste policyn vara en exakt kopia av din gamla Config. Detta innebär att varje undantag, anpassad regel, inaktiverad regelgrupp, etc. måste vara exakt samma som det är i WAF Config.
3. (Valfritt) Du kan konfigurera WAF-principen så att den passar dina behov. Detta inkluderar anpassade regler, inaktivera regler /regelgrupper, undantag, ange filuppladdning gränser, etc. Om du hoppar över det här steget väljs alla standardvärden. 
   
4. Spara principen och bifoga den till programgatewayen. 
   
   ```azurepowershell-interactive
      #Save the policy itself
      Set-AzApplicationGatewayFirewallPolicy -InputObject $policy`
   
      #Attach the policy to an Application Gateway
      $gw.FirewallPolicy = $policy`
   
      #Save the Application Gateway
      Set-AzApplicationGateway -ApplicationGateway $gw`
   ```

## <a name="next-steps"></a>Nästa steg
[Läs mer om anpassade regler.](configure-waf-custom-rules.md)
