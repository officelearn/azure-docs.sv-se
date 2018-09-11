---
title: 'Skapa och exportera certifikat för punkt-till-plats: Linux: CLI: Azure | Microsoft Docs'
description: Skapa ett självsignerat rotcertifikat, exportera den offentliga nyckeln och generera klientcertifikat Linux (strongSwan) CLI.
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 09/06/2018
ms.author: cherylmc
ms.openlocfilehash: 8647b3b3eda980dbd5d5ec368b6b4b13949ecaf1
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2018
ms.locfileid: "44305746"
---
# <a name="generate-and-export-certificates-for-point-to-site-using-linux-strongswan-cli"></a>Skapa och exportera certifikat för punkt-till-plats med hjälp av Linux strongSwan CLI

Punkt-till-plats-anslutningar använder certifikat för autentisering. Den här artikeln visar hur du skapar ett självsignerat rotcertifikat och generera klientcertifikat med hjälp av Linux CLI och strongSwan. Om du letar efter olika certifikat anvisningar finns i den [Powershell](vpn-gateway-certificates-point-to-site.md) eller [MakeCert](vpn-gateway-certificates-point-to-site-makecert.md) artiklar.

> [!NOTE]
> Stegen i den här artikeln kräver strongSwan.
>

Konfigurationen för datorn som används för stegen i den här artikeln var följande:

| | |
|---|---|
|**Dator**| Ubuntu Server 16.04<br>ID_LIKE = debian<br>PRETTY_NAME = ”Ubuntu 16.04.4 LTS”<br>VERSION_ID = ”16.04” |
|**Beroenden**| Apt-get install strongswan ikev2 strongswan-plugin-programmet-eap-tls<br>Apt-get install libstrongswan-standard-plugin-program |

## <a name="install-strongswan"></a>Installera strongSwan

1. `apt-get install strongswan-ikev2 strongswan-plugin-eap-tls`
2. `apt-get install libstrongswan-standard-plugins`

Information om hur du installerar strongSwan med hjälp av det grafiska Användargränssnittet finns i stegen i den [klientkonfigurationen](point-to-site-vpn-client-configuration-azure-cert.md#install) artikeln.

## <a name="generate-keys-and-certificate"></a>Generera nycklar och certifikat

1. Generera CA-certifikatet.

  ```
  ipsec pki --gen --outform pem > caKey.pem
  ipsec pki --self --in caKey.pem --dn "CN=VPN CA" --ca --outform pem > caCert.pem
  ```
2. Skriva ut CA-certifikat i base64-format. Detta är det format som stöds av Azure. Du kommer senare att överföra det till Azure som en del av din P2S-konfiguration.

  ```
  openssl x509 -in caCert.pem -outform der | base64 -w0 ; echo
  ```
3. Generera användarcertifikatet.

  ```
  export PASSWORD="password"
  export USERNAME="client"

  ipsec pki --gen --outform pem > "${USERNAME}Key.pem"
  ipsec pki --pub --in "${USERNAME}Key.pem" | ipsec pki --issue --cacert caCert.pem --cakey caKey.pem --dn "CN=${USERNAME}" --san "${USERNAME}" --flag clientAuth --outform pem > "${USERNAME}Cert.pem"
  ```
4. Generera en p12-paket som innehåller användarcertifikatet. Det här paketet ska användas i nästa steg när du arbetar med den [-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).

  ```
  openssl pkcs12 -in "${USERNAME}Cert.pem" -inkey "${USERNAME}Key.pem" -certfile caCert.pem -export -out "${USERNAME}.p12" -password "pass:${PASSWORD}"
  ```

## <a name="next-steps"></a>Nästa steg

Fortsätt med din punkt-till-plats-konfiguration och [skapa och installera VPN-klientkonfigurationsfiler](point-to-site-vpn-client-configuration-azure-cert.md#linuxinstallcli).