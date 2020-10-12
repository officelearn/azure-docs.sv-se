---
title: inkludera fil
description: inkludera fil
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 07/31/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: fc2393cfe87e2639ce40e66e6053d4d430518719
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "87515331"
---
## <a name="1-download-the-file"></a>1. Hämta filen

Kör följande kommandon. Kopiera resultat-URL: en till webbläsaren för att ladda ned profil zip-filen.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. extrahera zip-filen

Extrahera zip-filen. Filen innehåller följande mappar:

* AzureVPN
* Allmänna
* OpenVPN (om du har aktiverat OpenVPN med inställningar för **Azure-certifikat** eller **RADIUS-autentisering** på gatewayen). Mer VPN Gateway finns i [skapa en klient](../articles/vpn-gateway/openvpn-azure-ad-tenant.md). För virtuellt WAN-nätverk, se [skapa en klient-VWAN](../articles/virtual-wan/openvpn-azure-ad-tenant.md).

## <a name="3-retrieve-information"></a>3. Hämta information

I mappen **AzureVPN** navigerar du till ***azurevpnconfig.xml*** -filen och öppnar den med anteckningar. Anteckna texten mellan följande taggar.

```
<audience>          </audience>
<issuer>            </issuer>
<tennant>           </tennant>
<fqdn>              </fqdn>
<serversecret>      </serversecret>
```

## <a name="profile-details"></a>Profil information

När du lägger till en anslutning använder du den information som du samlade in i föregående steg för profil informations sidan. Fälten motsvarar följande information:

   * **Mål grupp:** Identifierar mottagar resursen som token är avsedd för
   * **Utfärdare:** Identifierar säkerhetstokentjänst som utsänt token samt Azure AD-klienten
   * **Klient organisation:** Innehåller en oföränderlig, unik identifierare för den katalog klient som utfärdade token
   * **Fullständigt domän namn:** Det fullständigt kvalificerade domän namnet (FQDN) på Azure VPN-gatewayen
   * **ServerSecret:** Nyckeln för fördelad VPN-gateway

## <a name="folder-contents"></a>Mappinnehåll

* Den **allmänna mappen** innehåller det offentliga Server certifikatet och den VpnSettings.xml filen. VpnSettings.xml-filen innehåller information som krävs för att konfigurera en allmän klient.

* Den hämtade ZIP-filen kan också innehålla **WindowsAmd64** -och **WindowsX86** -mappar. Dessa mappar innehåller installations programmet för SSTP och IKEv2 för Windows-klienter. Du måste ha administratörs behörighet på klienten för att installera dem.
