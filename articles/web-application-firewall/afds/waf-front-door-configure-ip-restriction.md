---
title: Konfigurera WAF-regeln för IP-begränsning för Azure Front Door
description: Lär dig hur du konfigurerar en brandväggsregel för webbprogram för att begränsa IP-adresser för en befintlig Slutpunkt för Ytterdörren i Azure.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 03/26/2020
ms.author: tyao
ms.openlocfilehash: 077f127648688b25d45b433fa2bc94ee011b3f2d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336075"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Konfigurera en IP-begränsningsregel med en brandvägg för webbprogram för Azure Front Door

Den här artikeln visar hur du konfigurerar IP-begränsningsregler i en brandvägg för webbprogram (WAF) för Azure Front Door med hjälp av Azure-portalen, Azure CLI, Azure PowerShell eller en Azure Resource Manager-mall.

En IP-adressbaserad åtkomstkontrollregel är en anpassad WAF-regel som gör att du kan styra åtkomsten till dina webbprogram. Detta gör detta genom att ange en lista över IP-adresser eller IP-adressintervall i klasslös cidr-format (Inter-Domain Routing).

Som standard är webbprogrammet tillgängligt från Internet. Om du vill begränsa åtkomsten till klienter från en lista över kända IP-adresser eller IP-adressintervall kan du skapa en IP-matchningsregel som innehåller listan över IP-adresser som matchande värden och anger operatorn till "Not" (negate är true) och åtgärden att **blockera**. När en IP-begränsningsregel har tillämpats får begäranden som kommer från adresser utanför den här tillåtna listan ett 403 Forbidden-svar.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Konfigurera en WAF-princip med Azure-portalen

### <a name="prerequisites"></a>Krav

Skapa en Azure Front Door-profil genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en ytterdörr för ett globalt webbprogram med högtillgänglig.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

1. På Azure-portalen väljer du **Skapa en resurs,** skriver **webbprogrambrandvägg** i sökrutan och väljer sedan **WAF (Web Application Firewall).**
2. Välj **Skapa**.
3. På sidan **Skapa en WAF-princip** använder du följande värden för att fylla i fliken **Grunderna:**
   
   |Inställning  |Värde  |
   |---------|---------|
   |Policy för     |Global WAF (ytterdörr)|
   |Prenumeration     |Välj din prenumeration|
   |Resursgrupp     |Välj den resursgrupp där ytterdörren är.|
   |Principnamn     |Ange ett namn på principen|
   |Principtillstånd     |Enabled|

   Välj **Nästa: Principinställningar**

1. Välj **Förebyggande**på fliken **Principinställningar** . För **blocksvarstexten**skriver *du Du har blockerats!* så att du kan se att din anpassade regel är i kraft.
2. Välj **Nästa: Hanterade regler**.
3. Välj **Nästa: Anpassade regler**.
4. Välj **Lägg till anpassad regel**.
5. På sidan **Lägg till anpassad regel** använder du följande testvärden för att skapa en anpassad regel:

   |Inställning  |Värde  |
   |---------|---------|
   |Anpassat regelnamn     |FdWafCustRule|
   |Status     |Enabled|
   |Typ av regel     |Matchning|
   |Prioritet    |100|
   |Matchningstyp     |IP-adress|
   |Matcha variabel|RemoteAddr|
   |Åtgärd|Innehåller inte|
   |IP-adress eller -intervall|10.10.10.0/24|
   |Sedan|Neka trafik|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Anpassad regel":::

   Välj **Lägg till**.
6. Välj **Nästa: Association**.
7. Välj **Lägg till klientdelsvärd**.
8. För **Frontend-värden**väljer du din frontend-värd och väljer **Lägg till**.
9. Välj **Granska + skapa**.
10. När principvalideringen har gått igenom väljer du **Skapa**.

### <a name="test-your-waf-policy"></a>Testa din WAF-policy

1. När waf-principdistributionen är klar bläddrar du till värdnamnet för frontdörrens klientnamn.
2. Du bör se ditt anpassade blockeringsmeddelande.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="TEST AV WAF-regeln":::

   > [!NOTE]
   > En privat IP-adress användes avsiktligt i den anpassade regeln för att garantera att regeln skulle utlösa. Skapa *tillåta* och *neka* regler med hjälp av IP-adresser för just din situation i en faktisk distribution.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Konfigurera en WAF-princip med Azure CLI

### <a name="prerequisites"></a>Krav
Innan du börjar konfigurera en IP-begränsningsprincip konfigurerar du din CLI-miljö och skapar en Azure Front Door-profil.

#### <a name="set-up-the-azure-cli-environment"></a>Konfigurera Azure CLI-miljön
1. Installera [Azure CLI](/cli/azure/install-azure-cli)eller använd Azure Cloud Shell. Azure Cloud Shell är ett kostnadsfritt Bash-gränssnitt som du kan köra direkt i Azure Portal. Den har Azure CLI förinstallerat och har konfigurerats för användning med ditt konto. Välj knappen **Prova** i CLI-kommandona som följer och logga sedan in på ditt Azure-konto i Cloud Shell-sessionen som öppnas. När sessionen har `az extension add --name front-door` startat anger du att lägga till Azure Front Door-tillägget.
 2. Om du använder CLI lokalt i Bash loggar du `az login`in på Azure med .

#### <a name="create-an-azure-front-door-profile"></a>Skapa en Azure Front Door-profil
Skapa en Azure Front Door-profil genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en ytterdörr för ett globalt webbprogram med högtillgänglig.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Skapa en WAF-princip

Skapa en WAF-princip med hjälp av kommandot [az network front-door waf-policy create.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-create) I exemplet som följer ersätter du principnamnet *IPAllowPolicyExampleCLI* med ett unikt principnamn.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Lägga till en anpassad IP-åtkomstkontrollregel

Använd kommandot [az-network front-door waf-policy custom-rule create](/cli/azure/ext/front-door/network/front-door/waf-policy/rule?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-rule-create) för att lägga till en anpassad IP-åtkomstkontrollregel för WAF-principen som du just skapade.

I följande exempel:
-  Ersätt *IPAllowPolicyExampleCLI* med din unika princip som skapats tidigare.
-  Ersätt *ip-address-range-1*, *ip-address-range-2* med ditt eget sortiment.

Skapa först en IP-tillåtregel för principen som skapats från föregående steg. 
> [!NOTE]
> **--uppskov** krävs eftersom en regel måste ha ett matchningsvillkor som ska läggas till i nästa steg.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Lägg sedan till matchningsvillkor i regeln:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>Hitta ID för en WAF-princip 
Hitta ett WAF-princips ID med hjälp av kommandot [az network front-door waf-policy show.](/cli/azure/ext/front-door/network/front-door/waf-policy?view=azure-cli-latest#ext-front-door-az-network-front-door-waf-policy-show) Ersätt *IPAllowPolicyExampleCLI* i följande exempel med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till en Azure Front Door-klientdelsvärd

Ange Azure Front Door *WebApplicationFirewallPolicyLink* ID till princip-ID med hjälp av kommandot [az-nätverksåtkomstuppdatering.](/cli/azure/ext/front-door/network/front-door?view=azure-cli-latest#ext-front-door-az-network-front-door-update) Ersätt *IPAllowPolicyExampleCLI* med din unika princip som du skapade tidigare.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
I det här exemplet tillämpas WAF-principen på **FrontendEndpoints[0]**. Du kan länka WAF-principen till någon av dina främre ändar.
> [!Note]
> Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** för att länka en WAF-princip till en Azure Front Door-klient. Efterföljande principuppdateringar tillämpas automatiskt på klientdelen.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Konfigurera en WAF-princip med Azure PowerShell

### <a name="prerequisites"></a>Krav
Innan du börjar konfigurera en IP-begränsningsprincip konfigurerar du powershell-miljön och skapar en Azure Front Door-profil.

#### <a name="set-up-your-powershell-environment"></a>Konfigurera PowerShell-miljön
Azure PowerShell innehåller en uppsättning cmdlets som använder [Azure Resource Manager-modellen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) för hantering av Azure-resurser.

Du kan installera [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview) på en lokal dator och använda det i alla PowerShell-sessioner. Följ instruktionerna på sidan för att logga in på PowerShell med hjälp av dina Azure-autentiseringsuppgifter och installera sedan Az-modulen.

1. Anslut till Azure med hjälp av följande kommando och använd sedan en interaktiv dialogruta för att logga in.
    ```
    Connect-AzAccount
    ```
 2. Innan du installerar en Azure Front Door-modul kontrollerar du att du har den aktuella versionen av PowerShellGet-modulen installerad. Kör följande kommando och öppna sedan PowerShell igen.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installera Modulen Az.FrontDoor med hjälp av följande kommando. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Skapa en Azure Front Door-profil
Skapa en Azure Front Door-profil genom att följa instruktionerna som beskrivs i [Snabbstart: Skapa en ytterdörr för ett globalt webbprogram med högtillgänglig.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Definiera ett IP-matchningsvillkor
Använd kommandot [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) för att definiera ett IP-matchningsvillkor.
I följande exempel ersätter *du ip-address-range-1*, *ip-address-range-2* med ditt eget intervall.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Skapa en anpassad IP-tillåtregel

Använd kommandot [Nytt AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) för att definiera en åtgärd och ange en prioritet. I följande exempel blockeras begäranden som inte kommer från klient-IPS som matchar listan.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Konfigurera en WAF-princip
Hitta namnet på resursgruppen som innehåller Azure Front `Get-AzResourceGroup`Door-profilen med hjälp av . Konfigurera sedan en WAF-princip med IP-regeln med hjälp av [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy).

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Länka en WAF-princip till en Azure Front Door-klientdelsvärd

Länka ett WAF-principobjekt till en befintlig frontend-värd och uppdatera Azure Front Door-egenskaper. Hämta först Azure Front [Door-objektet med Get-AzFrontDoor](/powershell/module/Az.FrontDoor/Get-AzFrontDoor). Ange sedan egenskapen **WebApplicationFirewallPolicyLink** till resurs-ID *för $IPAllowPolicyExamplePS*, som skapades i föregående steg, med kommandot [Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> I det här exemplet tillämpas WAF-principen på **FrontendEndpoints[0]**. Du kan länka en WAF-policy till någon av dina främre ändar. Du behöver bara ange egenskapen **WebApplicationFirewallPolicyLink** för att länka en WAF-princip till en Azure Front Door-klient. Efterföljande principuppdateringar tillämpas automatiskt på klientdelen.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Konfigurera en WAF-princip med en Resource Manager-mall
Om du vill visa mallen som skapar en Azure Front Door-princip och en WAF-princip med anpassade IP-begränsningsregler går du till [GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [skapar en Azure Front Door-profil](../../frontdoor/quickstart-create-front-door.md).
