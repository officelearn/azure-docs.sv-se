---
title: Konfigurera en regel för IP-begränsning med brandväggsregeln för web application för Azure ytterdörren
description: Lär dig hur du konfigurerar en IP-adress WAF Begränsningsregel för en befintlig ytterdörren slutpunkt.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/25/2019
ms.author: kumud;tyao
ms.openlocfilehash: 514c034c23eed3a87111331724f3a33104651a43
ms.sourcegitcommit: e729629331ae10097a081a03029398525f4147a4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/25/2019
ms.locfileid: "64514911"
---
# <a name="configure-an-ip-restriction-rule-with-web-application-firewall-for-azure-front-door-preview"></a>Konfigurera en regel för IP-begränsning med Brandvägg för webbaserade program för Azure ytterdörren (förhandsversion)
 Den här artikeln visar hur du konfigurerar regler för IP-begränsning i Azure web application firewall (WAF) för åtkomsten med hjälp av Azure CLI, Azure PowerShell eller Azure Resource Manager-mall.

Regeln för åtkomstkontroll en IP-adressbaserad är en anpassad WAF-regel som låter dig styra åtkomst till dina webbprogram genom att ange en lista över IP-adresser eller IP-adressintervall i Classless Inter-Domain Routing CIDR-form.

Webbprogrammet är tillgänglig från internet. Om du vill begränsa åtkomsten till dina webbprogram endast för klienter från en lista över kända IP-adresser eller IP-adressintervall, måste du skapa två IP-matchningsregler. Första IP-matchande regeln innehåller listan över IP-adresser som matchande värden och ange åtgärden som ska ”TILLÅT”. Den andra med lägre prioritet är att blockera alla andra IP-adresser genom att använda ”alla” och ange åtgärden som ska ”BLOCKERA”. När en regel för IP-begränsning har tillämpats några förfrågningar som kommer från adresser utanför den här listan får svaret 403 (förbjudet).  

> [!IMPORTANT]
> Funktionen WAF IP-begränsning för Azure ytterdörren är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="configure-waf-policy-with-azure-cli"></a>Konfigurera WAF-princip med Azure CLI

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du konfigurerar en princip för IP-begränsning kan konfigurera din CLI-miljö och skapa en ytterdörren-profil.

#### <a name="set-up-azure-cli-environment"></a>Konfigurera Azure CLI-miljö
1. Installera den [Azure CLI](/cli/azure/install-azure-cli), eller använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och konfigurerats för användning med ditt konto. Välj den **prova** knappen i CLI-kommandon som följer. Att välja **prova** anropar en Cloud Shell som du kan logga in på Azure-kontot med. När en cloud shell-sessionen startar, ange `az extension add --name front-door` att lägga till ytterdörren tillägget.
 2. Om du använder CLI lokalt i Bash, logga in på Azure med `az login`.

#### <a name="create-front-door-profile"></a>Skapa ytterdörren profil
Skapa en profil för åtkomsten genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en ytterdörren-profil](quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med de [az waf-nätverksprincip skapa](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) kommando. I det exemplet nedan ersätter du namnet på principen *IPAllowPolicyExampleCLI* med ett unikt principnamn.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-custom-ip-access-control-rule"></a>Lägg till anpassad IP-regeln för åtkomstkontroll

Lägga till en anpassad regel för IP-åtkomstkontroll i WAF-principen som skapades i föregående steg med den [az nätverket waf-policy anpassad-regel skapar](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) kommando. 

I det exemplet nedan:
-  Ersätt *IPAllowPolicyExampleCLI* med din unika princip som skapades tidigare.
-  Ersätt *ip-adress-intervallet-1*, *ip-adress-intervallet-2* med en egen rad.

Börja med att skapa IP-Adressen Tillåt-regel för de angivna adresserna.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch ["<ip-address-range-1>","<ip-address-range-2>"] \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Skapa sedan ett block alla IP-regel med lägre prioritet än den tidigare IP-Adressen Tillåt-regel. Ersätt den *IPAllowPolicyExampleCLI* med din unika princip som skapades tidigare.

```azurecli
az network waf-policy custom-rule create \
  --name IPDenyAllRule\
  --priority 2 \
  --rule-type MatchRule \
  --match-condition RemoteAddr Any
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
 ```

### <a name="find-waf-policy-id"></a>Hitta WAF princip-ID
Hitta ID för en WAF-princip med de [az nätverket waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) kommando. Ersätt den *IPAllowPolicyExampleCLI* med din unika princip som skapades tidigare.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Länken WAF-princip till en ytterdörren frontend-värd

Ange ytterdörren *WebApplicationFirewallPolicyLink* ID princip-ID: t med den [az nätverket ytterdörren uppdatering](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) kommando. Ersätt den *IPAllowPolicyExampleCLI* med din unika princip som skapades tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet används WAF-principen för FrontendEndpoints [0]. Du kan länka WAF principen till någon av dina klientdelar.
> [!Note]
> Du behöver bara ange den **WebApplicationFirewallPolicyLink** egenskap en gång för att länka en WAF-princip till en frontend ytterdörren. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.

## <a name="configure-waf-policy-with-azure-powershell"></a>Konfigurera WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du konfigurerar en princip för IP-begränsning kan ställa in din PowerShell-miljö och skapa en ytterdörren-profil.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)-modellen för att hantera dina Azure-resurser. 

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan, för att logga in med dina autentiseringsuppgifter för Azure, och installerar Az PowerShell-modulen.

##### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Anslut till Azure med en interaktiv dialogruta för inloggning
```
Connect-AzAccount

```
Innan du installerar ytterdörren modul bör du kontrollera att du har den aktuella versionen av installerat PowerShellGet. Kör kommandot nedan och öppna PowerShell.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 

##### <a name="install-azfrontdoor-module"></a>Installera Az.FrontDoor-modulen 

```
Install-Module -Name Az.FrontDoor
```
### <a name="create-a-front-door-profile"></a>Skapa en ytterdörren-profil
Skapa en profil för åtkomsten genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en ytterdörren-profil](quickstart-create-front-door.md)

### <a name="define-ip-match-condition"></a>Definiera IP matchningsvillkor
Använd den [New AzFrontDoorMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoormatchconditionobject) kommando för att definiera en IP-matchningsvillkor. I det exemplet nedan ersätter *ip-adress-intervallet-1*, *ip-adress-intervallet-2* med en egen rad.

```powershell
  $IPMatchCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty IPMatch `
    -MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Skapa en IP-matchning alla villkor-regel
```powershell
  $IPMatchALlCondition = New-AzFrontDoorMatchConditionObject `
    -MatchVariable  RemoteAddr `
    -OperatorProperty Any
    
```

### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad regel för att tillåta IP
   Använd den [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-AzFrontDoorCustomRuleObject) kommando för att definiera en åtgärd och ange prioritet. I följande exempel får förfrågningar från klienten IP-adresser som matchar listan. 

```powershell
  $IPAllowRule = New-AzFrontDoorCustomRuleObject `
    -Name "IPAllowRule" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchCondition `
    -Action Allow -Priority 1
```
Skapa ett Block alla IP-regel med lägre prioritet än den tidigare IP-Adressen Tillåt-regel.

```powershell
  $IPBlockAll = New-AzFrontDoorCustomRuleObject `
    -Name "IPDenyAll" `
    -RuleType MatchRule `
    -MatchCondition $IPMatchALlCondition `
    -Action Block `
    -Priority 2
   ```

### <a name="configure-waf-policy"></a>Konfigurera WAF-princip
Hitta namnet på resursgruppen som innehåller ytterdörren profil med `Get-AzResourceGroup`. Konfigurera en WAF-policyn med de IP-block med [New AzFrontDoorFireWallPolicy](/powershell/module/Az.FrontDoor/New-AzFrontDoorFireWallPolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorFireWallPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-waf-policy-to-a-front-door-front-end-host"></a>Länken WAF-princip till en ytterdörren frontend-värd

Länka WAF-policyobjekt till en befintlig klient ytterdörren-värd och uppdatera ytterdörren egenskaper. Först hämta det ytterdörren objekt genom att använda [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Nu ska vi Konfigurera klient *WebApplicationFirewallPolicyLink* egenskap till resurs-ID för den *$IPAllowPolicyExamplePS* skapade i föregående steg med den [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor) kommando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet används WAF-principen för FrontendEndpoints [0]. Du kan länka WAF principen till någon av dina klientdelar. Du behöver bara ange *WebApplicationFirewallPolicyLink* egenskap en gång för att länka en WAF-princip till en frontend ytterdörren. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.


## <a name="configure-waf-policy-with-resource-manager-template"></a>Konfigurera WAF-princip med Resource Manager-mall
Visa den mall som skapar en ytterdörren och en WAF-princip med anpassade regler för IP-begränsning [här](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en profil för ytterdörren](quickstart-create-front-door.md).
