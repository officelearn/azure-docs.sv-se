---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 09/12/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1c2525b352c25f470814ce909a8d10ff821d9e32
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "70961592"
---
Generera certifikatutfärdarcertifikatet.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Skriv ut certifikatutfärdarcertifikatet i base64-format. Det här är formatet som stöds av Azure. Du överför det här certifikatet till Azure som en del av [P2S-konfigurationsstegen](../articles/vpn-gateway/vpn-gateway-howto-point-to-site-resource-manager-portal.md).

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```

Generera användarcertifikatet.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```

Generera ett p12-paket som innehåller användarcertifikatet. Det här paketet kommer att användas i nästa steg när du arbetar med klientkonfigurationsfilerna.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```