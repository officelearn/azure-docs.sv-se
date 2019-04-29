---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: WenJason
ms.service: vpn-gateway
ms.topic: include
origin.date: 01/16/2019
ms.date: 03/04/2019
ms.author: v-jay
ms.custom: include file
ms.openlocfilehash: c6f9065786879749eee6187e93283f4c026b7fff
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60766185"
---
Följande Datorkonfiguration har använts för stegen nedan:

  | | |
  |---|---|
  |Computer| Ubuntu Server 16.04<br>ID_LIKE = debian<br>PRETTY_NAME="Ubuntu 16.04.4 LTS"<br>VERSION_ID="16.04" |
  |Beroenden| strongSwan |

#### <a name="1-install-strongswan"></a>1. Installera strongSwan

Använd följande kommandon för att installera den nödvändiga strongSwan-konfigurationen:

```
apt-get install strongswan-ikev2 strongswan-plugin-eap-tls
```

```
apt-get install libstrongswan-standard-plugins
```

```
apt-get install strongswan-pki
```

#### <a name="2-generate-keys-and-certificate"></a>2. Generera nycklar och certifikat

Generera CA-certifikatet.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```

Skriva ut CA-certifikat i base64-format. Detta är det format som stöds av Azure. Du kommer senare att överföra det till Azure som en del av din P2S-konfiguration.

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

Generera en p12-paket som innehåller användarcertifikatet. Det här paketet ska användas i nästa steg när du arbetar med klient-konfigurationsfiler.

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```