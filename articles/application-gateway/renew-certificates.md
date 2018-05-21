---
title: Förnya ett certifikat för Azure Programgateway
description: Lär dig mer om att förnya ett certifikat som är associerade med en gateway-lyssnare för programmet.
services: application-gateway
author: vhorne
manager: jpconnock
ms.service: application-gateway
ms.topic: article
ms.date: 05/17/2018
ms.author: victorh
ms.openlocfilehash: b125f707e8de17764701e981736a53492e5e756c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/20/2018
---
# <a name="renew-application-gateway-certificates"></a>Förnya certifikat för Programgateway

Vid något tillfälle måste du förnya certifikat om du har konfigurerat din Programgateway för SSL-kryptering.

Du kan förnya ett certifikat som är associerade med en lyssnare med Azure-portalen eller Azure PowerShell:

## <a name="azure-portal"></a>Azure Portal

Navigera till ditt program gateway-lyssnare för att förnya ett certifikat för lyssnare från portalen. Klicka på lyssnaren som har ett certifikat som behöver förnyas och klicka sedan på **förnya eller Redigera valda certifikatet**.

![Förnya certifikat](media/renew-certificate/ssl-cert.png)

Överför ditt nya PFX-certifikat, ge det ett namn, skriver du lösenordet och klicka sedan på **spara**.

## <a name="azure-powershell"></a>Azure PowerShell

Om du vill förnya certifikatet med hjälp av Azure PowerShell använder du följande cmdlet:

```PowerShell
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

## <a name="next-steps"></a>Nästa steg

Information om hur du konfigurerar SSL-avlastning med Azure Programgateway finns [Konfigurera SSL-avlastning](application-gateway-ssl-portal.md)
