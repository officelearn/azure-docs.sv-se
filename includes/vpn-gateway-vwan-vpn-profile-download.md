---
title: ta med fil
description: ta med fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/17/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: c61378510fbfc8bdc13f35ba1063a0d9316d88e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066200"
---
## <a name="1-download-the-file"></a>1. Ladda ner filen

Kör följande kommandon. Kopiera resultatadressen till din webbläsare för att hämta zip-filen för profilen.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. Extrahera zip-filen

Extrahera zip-filen. Filen innehåller följande mappar:

* AzureVPN
* Allmänna
* OpenVPN (Om du har aktiverat openvpn- och Azure AD-autentiseringsinställningarna på gatewayen. För VPN Gateway finns i [Skapa en klient .](../articles/vpn-gateway/openvpn-azure-ad-tenant.md) För Virtuellt WAN finns i [Skapa en klient - VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Hämta information

I **AzureVPN-mappen** navigerar du till filen ***azurevpnconfig.xml*** och öppnar den med Anteckningar. Anteckna texten mellan följande taggar.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profilinformation

När du lägger till en anslutning använder du informationen som du samlade in i föregående steg för profilinformationssidan. Fälten motsvarar följande information:

   * **Publik:** Identifierar den mottagarresurs som token är avsedd för
   * **Emittent:** Identifierar security token service (STS) som avgav token samt Azure AD-klienten
   * **Hyresgäst:** Innehåller en oföränderlig, unik identifierare för katalogklienten som utfärdade token
   * **FQDN:** Det fullständigt kvalificerade domännamnet (FQDN) på Azure VPN-gatewayen
   * **ServerSecret:** VPN-gatewayen fördelade nyckeln

## <a name="folder-contents"></a>Mappinnehåll

* Den **allmänna mappen** innehåller det offentliga servercertifikatet och filen VpnSettings.xml. Filen VpnSettings.xml innehåller information som behövs för att konfigurera en allmän klient.

* Den nedladdade zip-filen kan också innehålla **WindowsAmd64-** och **WindowsX86-mappar.** Dessa mappar innehåller installationsprogrammet för SSTP- och IKEv2 för Windows-klienter. Du behöver administratörsrättigheter på klienten för att installera dem.