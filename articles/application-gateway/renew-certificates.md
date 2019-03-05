---
title: Förnya ett certifikat för Azure Application Gateway
description: Lär dig mer om att förnya ett certifikat som är associerade med en application gateway-lyssnare.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 90200f7be6c71346441922365fc4439111dd8701
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/04/2019
ms.locfileid: "57314525"
---
# <a name="renew-application-gateway-certificates"></a>Förnya certifikat för Application Gateway

Vid en viss tidpunkt måste du förnya certifikat om du har konfigurerat din Programgateway för SSL-kryptering.

Du kan förnya ett certifikat som är associerade med en lyssnare med antingen Azure portal, Azure PowerShell eller Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Om du vill förnya ett certifikat för lyssnare från portalen, navigera till din application gateway-lyssnare. Klicka på den lyssnare som har ett certifikat som krävs för att förnya och klicka sedan på **förnya eller Redigera valda certifikatet**.

![Förnya certifikat](media/renew-certificate/ssl-cert.png)

Ladda upp nya PFX-certifikatet, ge den ett namn, skriver du lösenordet och klicka sedan på **spara**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill förnya certifikatet med hjälp av Azure PowerShell, Använd följande skript:

```azurepowershell-interactive
$appgw = Get-AzApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-AzApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password

Set-AzApplicationGateway -ApplicationGateway $appgw
```
## <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
az network application-gateway ssl-cert update \
  -n "<CertName>" \
  --gateway-name "<AppGatewayName>" \
  -g "ResourceGroupName>" \
  --cert-file <PathToCerFile> \
  --cert-password "<password>"
```

## <a name="next-steps"></a>Nästa steg

Läs hur du konfigurerar SSL-avlastning med Azure Application Gateway i [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)
