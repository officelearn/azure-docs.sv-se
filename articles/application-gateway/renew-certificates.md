---
title: Förnya ett Azure Application Gateway-certifikat
description: Lär dig hur du förnyar ett certifikat som är associerat med en programgatewaylyssnare.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: article
ms.date: 8/15/2018
ms.author: victorh
ms.openlocfilehash: 4c0c0ab84e60335f58ac075459b95cfabb9135ac
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311950"
---
# <a name="renew-application-gateway-certificates"></a>Förnya certifikat för programgateway

Vid något tillfälle måste du förnya dina certifikat om du har konfigurerat programgatewayen för TLS/SSL-kryptering.

Du kan förnya ett certifikat som är associerat med en lyssnare med antingen Azure-portalen, Azure PowerShell eller Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Om du vill förnya ett lyssnarcertifikat från portalen navigerar du till programgatewaylyssnare. Klicka på lyssnaren som har ett certifikat som behöver förnyas och klicka sedan på **Förnya eller redigera det markerade certifikatet**.

![Förnya certifikat](media/renew-certificate/ssl-cert.png)

Ladda upp ditt nya PFX-certifikat, ge det ett namn, skriv lösenordet och klicka sedan på **Spara**.

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

Mer information om hur du konfigurerar TLS-avlastning med Azure Application Gateway finns i [Konfigurera TLS-avlastning](application-gateway-ssl-portal.md)
