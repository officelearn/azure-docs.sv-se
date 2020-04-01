---
title: Exempel på Azure PowerShell-skript – skapa anpassade WAF-regler
description: Exempel på Azure PowerShell-skript – skapa anpassade regler för brandväggen för webbprogram
author: vhorne
ms.service: application-gateway
ms.topic: sample
ms.date: 6/7/2019
ms.author: victorh
ms.openlocfilehash: ffdde80598322222e2a8f000eee8be269becdd11
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "66743425"
---
# <a name="create-waf-custom-rules-with-azure-powershell"></a>Skapa WAF-anpassade regler med Azure PowerShell

Det här skriptet skapar en brandvägg för programgateway-webbprogram som använder anpassade regler. Den anpassade regeln blockerar trafik om begäranden innehåller User-Agent *evilbot*.

## <a name="prerequisites"></a>Krav

### <a name="azure-powershell-module"></a>Azure PowerShell-modul

Om du väljer att installera och använda Azure PowerShell lokalt kräver det här skriptet Azure PowerShell-modul version 2.1.0 eller senare.

1. Kör `Get-Module -ListAvailable Az` för att hitta versionen. Om du behöver uppgradera kan du läsa [Install Azure PowerShell module](/powershell/azure/install-az-ps) (Installera Azure PowerShell-modul).
2. Skapa en anslutning med Azure genom att köra `Connect-AzAccount`.

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

## <a name="sample-script"></a>Exempelskript

[!code-powershell[main](../../../powershell_scripts/application-gateway/waf-rules/waf-custom-rules.ps1 "Custom WAF rules")]

## <a name="clean-up-deployment"></a>Rensa distribution

Kör följande kommando för att ta bort resursgruppen, programgatewayen och alla relaterade resurser.

```powershell
Remove-AzResourceGroup -Name CustomRulesTest
```

## <a name="script-explanation"></a>Förklaring av skript

Det här skriptet använder följande kommandon för att skapa distributionen. Varje post i tabellen länkar till kommandospecifik dokumentation.

| Kommando | Anteckningar |
|---|---|
| [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) | Skapar en resursgrupp där alla resurser lagras. |
| [New-AzVirtualNetworkSubnetConfig](/powershell/module/az.network/new-azvirtualnetworksubnetconfig) | Skapar undernätskonfigurationen. |
| [New-AzVirtualNetwork](/powershell/module/az.network/new-azvirtualnetwork) | Skapar det virtuella nätverket med hjälp av undernätskonfigurationen. |
| [New-AzPublicIpAddress](/powershell/module/az.network/new-azpublicipaddress) | Skapar den offentliga IP-adressen för programgatewayen. |
| [Ny-AzApplicationGatewayIPKonfigurering](/powershell/module/az.network/new-azapplicationgatewayipconfiguration) | Skapar den konfiguration som associerar ett undernät med programgatewayen. |
| [Ny-AzApplicationGatewayFrontendIPConfig](/powershell/module/az.network/new-azapplicationgatewayfrontendipconfig) | Skapar den konfiguration som tilldelar en offentlig IP-adress till programgatewayen. |
| [Ny-AzApplicationGatewayFrontendPort](/powershell/module/az.network/new-azapplicationgatewayfrontendport) | Tilldelar en port som ska användas för åtkomst till programgatewayen. |
| [Ny-AzApplicationGatewayBackendAddressPool](/powershell/module/az.network/new-azapplicationgatewaybackendaddresspool) | Skapar en serverdelspool för en programgateway. |
| [Nya-AzApplicationGatewayBackendHttpSettings](/powershell/module/az.network/new-azapplicationgatewaybackendhttpsetting) | Konfigurerar inställningar för serverdelspoolen. |
| [Nya-AzApplicationGatewayHttpListener](/powershell/module/az.network/new-azapplicationgatewayhttplistener) | Skapar en lyssnare. |
| [Ny-AzApplicationGatewayRequestRoutingRule](/powershell/module/az.network/new-azapplicationgatewayrequestroutingrule) | Skapar en hanteringsregel. |
| [Nya-AzApplicationGatewaySku](/powershell/module/az.network/new-azapplicationgatewaysku) | Ange nivå och kapacitet för en programgateway. |
| [Ny-AzApplicationGateway](/powershell/module/az.network/new-azapplicationgateway) | Skapar en programgateway. |
|[Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup) | Tar bort en resursgrupp och alla resurser som ingår i gruppen. |
|[Ny-AzApplicationGatewayAutoscaleKonfigurering](/powershell/module/az.network/New-AzApplicationGatewayAutoscaleConfiguration)|Skapar en konfiguration för automatisk skalning för Programgatewayen.|
|[Nya-AzApplicationGatewayFirewallMatchVariable](/powershell/module/az.network/New-AzApplicationGatewayFirewallMatchVariable)|Skapar en matchningsvariabel för brandväggstillstånd.|
|[Ny-AzApplicationGatewayFirewallCondition](/powershell/module/az.network/New-AzApplicationGatewayFirewallCondition)|Skapar ett matchningsvillkor för anpassad regel.|
|[Ny-AzApplicationGatewayFirewallCustomRule](/powershell/module/az.network/New-AzApplicationGatewayFirewallCustomRule)|Skapar en ny anpassad regel för brandväggsprincipen för programgateway.|
|[Ny-AzApplicationGatewayFirewallPolicy](/powershell/module/az.network/New-AzApplicationGatewayFirewallPolicy)|Skapar en brandvägg för programgateway.|
|[Ny-AzApplicationGatewayWebApplicationFirewallKonfigurering](/powershell/module/az.network/New-AzApplicationGatewayWebApplicationFirewallConfiguration)|Skapar en WAF-konfiguration för en programgateway.|

## <a name="next-steps"></a>Nästa steg

- Mer information om anpassade WAF-regler finns i [Anpassade regler för brandvägg för webbprogram](../custom-waf-rules-overview.md)
- Mer information om Azure PowerShell-modulen finns i [Azure PowerShell-dokumentationen](/powershell/azure/overview).
- Ytterligare PowerShell-skriptexempel för programgatewayer finns i [dokumentationen för Azure Application Gateway](../powershell-samples.md).
