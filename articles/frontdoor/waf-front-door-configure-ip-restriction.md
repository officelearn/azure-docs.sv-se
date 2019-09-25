---
title: Konfigurera en regel för IP-begränsning med en brand Väggs regel för webb program för Azure-tjänsten för front dörr
description: Lär dig hur du konfigurerar en brand Väggs regel för webb program för att begränsa IP-adresser för en befintlig slut punkt för Azure frontend dörr service.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud
ms.reviewer: tyao
ms.openlocfilehash: adca1bdd0cf525627cc284b1c0d3509beddef131
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2019
ms.locfileid: "71219382"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurera en regel för IP-begränsning med en brand vägg för webbaserade program för Azure-tjänsten för front dörr
I den här artikeln beskrivs hur du konfigurerar regler för IP-begränsning i en brand vägg för webbaserade program (WAF) för Azure-tjänsten för frontend-tjänsten med hjälp av Azure CLI, Azure PowerShell eller en Azure Resource Manager mall.

En IP-adress – baserad åtkomst kontroll regel är en anpassad WAF-regel som låter dig styra åtkomsten till dina webb program. Det gör du genom att ange en lista över IP-adresser eller IP-adressintervall i CIDR-format (Classless Inter-Domain routing).

Som standard är ditt webb program tillgängligt från Internet. Om du vill begränsa åtkomsten till klienter från en lista med kända IP-adresser eller IP-adressintervall kan du skapa en IP-matchande regel som innehåller listan med IP-adresser som matchande värden och anger operatorn till "inte" (negation är sant) och åtgärden som ska **blockeras**. När en regel för IP-begränsning har tillämpats får begär Anden som härstammar från adresser utanför listan över tillåtna 403 ett svar som inte tillåts.  

En klients IP-adress kan skilja sig från IP-WAF, till exempel när en klient ansluter till WAF via en proxy. Du kan skapa regler för IP-begränsning baserat på antingen klientens IP-adresser (RemoteAddr) eller socket IP-adresser som visas av WAF (SocketAddr). 

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurera en WAF-princip med Azure CLI

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning ställer du in CLI-miljön och skapar en profil för Azure frontend-tjänsten.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurera Azure CLI-miljön
1. Installera [Azure CLI](/cli/azure/install-azure-cli)eller Använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **prova** i CLI-kommandona som följer och logga sedan in på ditt Azure-konto i Cloud Shell-sessionen som öppnas. När sessionen har startats anger `az extension add --name front-door` du för att lägga till Azures tjänst tillägg för front dörr.
 2. Om du använder CLI lokalt i bash loggar du in på Azure med hjälp av `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Skapa en tjänst profil för Azure frontend-dörren
Skapa en profil för Azure frontend-tjänsten genom att följa anvisningarna i [snabb start: Skapa en frontend-dörr för ett globalt webb program](quickstart-create-front-door.md)med hög tillgänglighet.

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med hjälp av kommandot [AZ Network frontend-dörr WAF-policy Create](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) . I exemplet nedan ersätter du princip namnet *IPAllowPolicyExampleCLI* med ett unikt princip namn.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Lägg till en anpassad kontroll regel för IP-åtkomst

Använd kommandot [AZ Network front-dörr WAF-policy "Create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) " för att lägga till en anpassad kontroll regel för IP-åtkomst för WAF-principen som du nyss skapade.

I följande exempel:
-  Ersätt *IPAllowPolicyExampleCLI* med din unika princip som skapats tidigare.
-  Ersätt *IP-Address-Range-1*, *IP-Address-Range-2* med ditt eget intervall.

Börja med att skapa en regel för IP-Tillåt för principen som skapats från föregående steg. Obs!- **uppskjutning** krävs eftersom en regel måste innehålla minst ett matchnings villkor. 

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Lägg sedan till IP-matchnings villkor för klient i regeln:

```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable RemoteAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
Matchnings villkor för socket IP (SocketAddr):
  ```azurecli
az network front-door waf-policy rule match-condition add\
--match-variable SocketAddr \
--operator IPMatch
--values "ip-address-range-1" "ip-address-range-2"
--negate true\
--name IPAllowListRule\
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI                                                  

### Find the ID of a WAF policy 
Find a WAF policy's ID by using the [az network front-door waf-policy show](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) command. Replace *IPAllowPolicyExampleCLI* in the following example with your unique policy that you created earlier.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Länka en WAF-princip till en front-end-värd i Azure frontend-tjänsten

Ställ in *WebApplicationFirewallPolicyLink* -ID: t för Azure frontend-tjänsten i princip-ID med hjälp av kommandot [AZ Network front-dörr Update](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) . Ersätt *IPAllowPolicyExampleCLI* med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet tillämpas WAF-principen på **FrontendEndpoints [0]** . Du kan länka WAF-principen till vilken som helst av dina klient delar.
> [!Note]
> Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en klient del i Azure frontend-tjänsten. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurera en WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Förutsättningar
Innan du börjar konfigurera en princip för IP-begränsning ställer du in PowerShell-miljön och skapar en profil för Azure frontend-tjänsten.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell innehåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) -modellen för att hantera Azure-resurser.

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in på PowerShell med dina Azure-autentiseringsuppgifter och installera sedan AZ-modulen.

1. Anslut till Azure med hjälp av följande kommando och Använd sedan en interaktiv dialog ruta för att logga in.
    ```
    Connect-AzAccount
    ```
 2. Innan du installerar en Azure frontend service-modul kontrollerar du att du har den aktuella versionen av PowerShellGet-modulen installerad. Kör följande kommando och öppna PowerShell igen.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installera modulen AZ. ytterdörr med hjälp av följande kommando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Skapa en tjänst profil för Azure frontend-dörren
Skapa en profil för Azure frontend-tjänsten genom att följa anvisningarna i [snabb start: Skapa en frontend-dörr för ett globalt webb program](quickstart-create-front-door.md)med hög tillgänglighet.

### <a name="define-an-ip-match-condition"></a>Definiera ett villkor för IP-matchning
Använd kommandot [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) för att definiera ett villkor för IP-matchning.
I följande exempel ersätter du *IP-Address-Range-1*, *IP-Address-Range-2* med ditt eget intervall.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```

Matchnings villkor för socket IP (SocketAddr):   
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  SocketAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```


### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad IP-regel för Tillåt

Använd kommandot [New-AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) för att definiera en åtgärd och ange en prioritet. I följande exempel kommer begär Anden som inte från klient-IP-adresser som matchar listan att blockeras.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurera en WAF-princip
Hitta namnet på den resurs grupp som innehåller Azures tjänst profil för frontend-dörren med `Get-AzResourceGroup`hjälp av. Konfigurera sedan en WAF-princip med IP-regeln med hjälp av [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Länka en WAF-princip till en front-end-värd i Azure frontend-tjänsten

Länka ett WAF-princip objekt till en befintlig klient dels värd och uppdatera egenskaperna för Azure frontend-tjänsten. Börja med att hämta objektet Azure frontend dörr service med hjälp av [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ange sedan egenskapen **WebApplicationFirewallPolicyLink** till resurs-ID för *$IPAllowPolicyExamplePS*, som skapades i föregående steg, med hjälp av kommandot [set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) .

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet tillämpas WAF-principen på **FrontendEndpoints [0]** . Du kan länka en WAF-princip till någon av dina klient delar. Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** en gång för att länka en WAF-princip till en klient del i Azure frontend-tjänsten. Efterföljande princip uppdateringar tillämpas automatiskt på klient delen.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurera en WAF-princip med en Resource Manager-mall
Om du vill visa mallen som skapar en Azure frontend service-princip och en WAF-princip med anpassade regler för IP-begränsning går du till [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en profil för Azure frontend-tjänsten](quickstart-create-front-door.md).
