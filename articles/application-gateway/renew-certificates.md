---
title: Förnya ett Azure Application-Gateway-certifikat
description: Lär dig hur du förnyar ett certifikat som är associerat med en Application Gateway-lyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: how-to
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: de57a58f7c891009d2e0cc43b351c2cad42a2766
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "84807879"
---
# <a name="renew-application-gateway-certificates"></a>Förnya Application Gateway certifikat

Vid något tillfälle måste du förnya dina certifikat om du har konfigurerat din Application Gateway för TLS/SSL-kryptering.

Du kan förnya ett certifikat som är associerat med en lyssnare med antingen Azure Portal, Azure PowerShell eller Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Om du vill förnya ett lyssnar certifikat från portalen navigerar du till dina Application Gateway-lyssnare. Klicka på den lyssnare som har ett certifikat som behöver förnyas och klicka sedan på **förnya eller redigera det valda certifikatet**.

![Förnya certifikat](media/renew-certificate/ssl-cert.png)

Överför ditt nya PFX-certifikat, ge det ett namn, ange lösen ordet och klicka sedan på **Spara**.

## <a name="azure-powershell"></a>Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Om du vill förnya certifikatet med Azure PowerShell använder du följande skript:

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

Information om hur du konfigurerar TLS-avlastning med Azure Application Gateway finns i [Konfigurera TLS-avläsning](application-gateway-ssl-portal.md)
