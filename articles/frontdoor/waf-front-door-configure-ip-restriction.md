---
title: Konfigurera en regel för IP-begränsning med en brandväggsregel för web-program för Azure ytterdörren Service
description: Lär dig hur du konfigurerar en brandväggsregel för web-programmet för att begränsa IP-adresser för en befintlig Azure ytterdörren Service-slutpunkt.
services: frontdoor
documentationcenter: ''
author: KumudD
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/31/2019
ms.author: kumud;tyao
ms.openlocfilehash: 88c5c284f26203ff3d6c39810a7b2810c1ebbc5a
ms.sourcegitcommit: 7042ec27b18f69db9331b3bf3b9296a9cd0c0402
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66743169"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door-service"></a>Konfigurera en regel för IP-begränsning med en brandvägg för webbaserade program för Azure ytterdörren Service
Den här artikeln visar hur du konfigurerar regler för IP-begränsning i en brandvägg för webbaserade program (WAF) för Azure ytterdörren-tjänsten med hjälp av Azure CLI, Azure PowerShell eller en Azure Resource Manager-mall.

En IP-adress – baserad regeln för åtkomstkontroll är en anpassad WAF-regel som låter dig styra åtkomsten till dina webbprogram. Det gör du genom att ange en lista över IP-adresser eller IP-adressintervall i Classless Inter-Domain Routing CIDR-format.

Webbprogrammet är tillgänglig från internet. Om du vill begränsa åtkomsten till klienter från en lista över kända IP-adresser eller IP-adressintervall, måste du skapa två IP-matchningsregler. Den första IP-regeln innehåller listan över IP-adresser som matchar värden och anger åtgärden till **Tillåt**. Den andra mallen med lägre prioritet, blockerar alla andra IP-adresser med hjälp av den **alla** operatorn och ställa in åtgärden **blockera**. När en regel för IP-begränsning används, får begäranden som kommer från adresser utanför den här listan över tillåtna 403 Forbidden svar.  

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurera en WAF-princip med Azure CLI

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar konfigurera en princip för IP-begränsning, Ställ in din CLI-miljö och skapa en profil för Azure ytterdörren Service.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurera Azure CLI-miljö
1. Installera den [Azure CLI](/cli/azure/install-azure-cli), eller använda Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure-portalen. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj den **prova** knappen i CLI-kommandona som följer och logga sedan in på ditt Azure-konto i Cloud Shell-sessionen som öppnas. När sessionen startar anger `az extension add --name front-door` att lägga till tillägget Azure ytterdörren Service.
 2. Om du använder CLI lokalt i Bash, loggar du in till Azure med hjälp av `az login`.

#### <a name="create-an-azure-front-door-service-profile"></a>Skapa en Azure ytterdörren Service-profil
Skapa en Azure ytterdörren Service-profil genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en ytterdörren för en högtillgänglig globala webbprogram](quickstart-create-front-door.md).

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med hjälp av den [az waf-nätverksprincip skapa](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-create) kommando. I det här exemplet som följer, ersätter du namnet på *IPAllowPolicyExampleCLI* med ett unikt principnamn.

```azurecli-interactive 
az network waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Lägg till en anpassad regel för IP-åtkomstkontroll

Använd den [az nätverket waf-policy anpassad-regel skapar](/cli/azure/ext/front-door/network/waf-policy/custom-rule?view=azure-cli-latest#ext-front-door-az-network-waf-policy-custom-rule-create) att lägga till en anpassad IP-kontroll åtkomstregel för WAF-principen du nyss skapade.

I följande exempel:
-  Ersätt *IPAllowPolicyExampleCLI* med din unika princip som skapades tidigare.
-  Ersätt *ip-adress-intervallet-1*, *ip-adress-intervallet-2* med en egen rad.

Börja med att skapa IP-Adressen Tillåt-regel för de angivna adresserna.

```azurecli
az network waf-policy custom-rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --match-condition RemoteAddr IPMatch "<ip-address-range-1>","<ip-address-range-2>" \
  --action Allow \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI
```
Skapa sedan en **blockera alla** regel med lägre prioritet än den tidigare **Tillåt** regeln. Ersätter *IPAllowPolicyExampleCLI* i följande exempel med din unika princip som du skapade tidigare.

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
    
### <a name="find-the-id-of-a-waf-policy"></a>Hitta en WAF-princip-ID 
Hitta en WAF-princip-ID genom att använda den [az nätverket waf-policy show](/cli/azure/ext/front-door/network/waf-policy?view=azure-cli-latest#ext-front-door-az-network-waf-policy-show) kommando. Ersätt *IPAllowPolicyExampleCLI* i följande exempel med din unika princip som du skapade tidigare.

   ```azurecli
   az network waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Länka en WAF-princip till en Azure ytterdörren frontend tjänstevärden

Ange tjänsten Azure ytterdörren *WebApplicationFirewallPolicyLink* ID princip-ID: t med hjälp av den [az nätverket ytterdörren uppdatering](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) kommando. Ersätt *IPAllowPolicyExampleCLI* med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/<resource- name>/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet WAF-principen tillämpas på **FrontendEndpoints [0]** . Du kan länka WAF-principen till någon av dina klientdelar.
> [!Note]
> Du måste ange den **WebApplicationFirewallPolicyLink** egenskapen bara en gång för att länka en WAF-princip till en Azure ytterdörren Service-klientdel. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurera en WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Nödvändiga komponenter
Innan du börjar konfigurera en princip för IP-begränsning, Ställ in din PowerShell-miljö och skapa en Azure ytterdörren Service-profil.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell tillhandahåller en uppsättning cmdletar som använder den [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) modellen för att hantera Azure-resurser.

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ anvisningarna på sidan för att logga in till PowerShell med dina autentiseringsuppgifter för Azure och sedan installera Az-modulen.

1. Anslut till Azure med hjälp av följande kommando och sedan använda en interaktiv dialogruta för inloggning.
    ```
    Connect-AzAccount
    ```
 2. Innan du installerar en Azure ytterdörren Service-modul bör du kontrollera att du har den aktuella versionen av modulen PowerShellGet installerad. Kör följande kommando och öppna PowerShell.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installera modulen Az.FrontDoor med hjälp av följande kommando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-service-profile"></a>Skapa en Azure ytterdörren Service-profil
Skapa en Azure ytterdörren Service-profil genom att följa anvisningarna som beskrivs i [snabbstarten: Skapa en ytterdörren för en högtillgänglig globala webbprogram](quickstart-create-front-door.md).

### <a name="define-an-ip-match-condition"></a>Definiera en IP-matchningsvillkor
Använd den [New AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) kommando för att definiera en IP-matchningsvillkor.
I följande exempel ersätter *ip-adress-intervallet-1*, *ip-adress-intervallet-2* med en egen rad.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue ["ip-address-range-1", "ip-address-range-2"]
```
Skapa en IP-adress *matchar alla villkor* regeln med hjälp av följande kommando:
```powershell
$IPMatchALlCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty Any        
  ```
    
### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad regel för att tillåta IP

Använd den [New AzFrontDoorCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) kommando för att definiera en åtgärd och ange prioritet. I följande exempel får förfrågningar från klienten IP-adresser som matchar listan.

```powershell
$IPAllowRule = New-AzFrontDoorCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Allow -Priority 1
```
Skapa en **blockera alla** regel med lägre prioritet än den tidigare IP-Adressen **Tillåt** regeln.
```powershell
$IPBlockAll = New-AzFrontDoorCustomRuleObject `
-Name "IPDenyAll" `
-RuleType MatchRule `
-MatchCondition $IPMatchALlCondition `
-Action Block `
-Priority 2
```

### <a name="configure-a-waf-policy"></a>Konfigurera en WAF-princip
Hitta namnet på resursgruppen som innehåller Azure ytterdörren tjänstprofilen med hjälp av `Get-AzResourceGroup`. Konfigurera en WAF-policyn med IP-Adressen **blockera alla** regeln med hjälp av [New AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```powershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule $IPBlockAll `
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-service-front-end-host"></a>Länka en WAF-princip till en Azure ytterdörren frontend tjänstevärden

Länka ett grupprincipobjekt för WAF till en befintlig frontend-värd och uppdatera Azure ytterdörren tjänstegenskaper. Hämta först Azure ytterdörren Service-objekt med hjälp av [Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Nu ska vi konfigurera den **WebApplicationFirewallPolicyLink** egenskap till resurs-ID för *$IPAllowPolicyExamplePS*, skapade i föregående steg, med hjälp av den [Set-AzFrontDoor](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)kommando.

```powershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet WAF-principen tillämpas på **FrontendEndpoints [0]** . Du kan länka en WAF-princip till någon av dina klientdelar. Du måste ange den **WebApplicationFirewallPolicyLink** egenskapen bara en gång för att länka en WAF-princip till en Azure ytterdörren Service-klientdel. Efterföljande uppdateringar tillämpas automatiskt på klientdelen.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurera en WAF-princip med en Resource Manager-mall
Om du vill visa den mall som skapar en princip för Azure ytterdörren Service och en WAF-princip med anpassade regler för IP-begränsning, gå till [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapa en profil för Azure ytterdörren Service](quickstart-create-front-door.md).
