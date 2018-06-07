---
title: Förnya ett certifikat för Azure Programgateway
description: Lär dig mer om att förnya ett certifikat som är associerade med en gateway-lyssnare för programmet.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/18/2018
ms.author: victorh
ms.openlocfilehash: b44a57fe8ebcc985d3ab66ea04936a1558d00863
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598274"
---
# <a name="renew-application-gateway-certificates"></a>Förnya certifikat för Programgateway

Vid något tillfälle måste du förnya certifikat om du har konfigurerat din Programgateway för SSL-kryptering.

Du kan förnya ett certifikat som är associerade med en lyssnare med antingen Azure-portalen, Azure PowerShell eller Azure CLI:

## <a name="azure-portal"></a>Azure Portal

Navigera till ditt program gateway-lyssnare för att förnya ett certifikat för lyssnare från portalen. Klicka på lyssnaren som har ett certifikat som behöver förnyas och klicka sedan på **förnya eller Redigera valda certifikatet**.

![Förnya certifikat](media/renew-certificate/ssl-cert.png)

Överför ditt nya PFX-certifikat, ge det ett namn, skriver du lösenordet och klicka sedan på **spara**.

## <a name="azure-powershell"></a>Azure PowerShell

Om du vill förnya certifikatet med hjälp av Azure PowerShell använder du följande cmdlet:

```azurepowershell-interactive
$appgw = Get-AzureRmApplicationGateway `
  -ResourceGroupName <ResourceGroup> `
  -Name <AppGatewayName>

$password = ConvertTo-SecureString `
  -String "<password>" `
  -Force `
  -AsPlainText

set-azureRmApplicationGatewaySSLCertificate -Name <oldcertname> `
-ApplicationGateway $appgw -CertificateFile <newcertPath> -Password $password
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

Information om hur du konfigurerar SSL-avlastning med Azure Programgateway finns [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)
