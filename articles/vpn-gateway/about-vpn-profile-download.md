---
title: 'Azure VPN Gateway: om P2S VPN-klientinställningar'
description: Detta hjälper dig att arbeta med klient profil filen
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: article
ms.date: 11/04/2019
ms.author: cherylmc
ms.openlocfilehash: 5386cace7191be60534f0d2fbf4a85b592d1ecdd
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151636"
---
# <a name="about-p2s-vpn-client-profiles"></a>Om P2S VPN-klientinställningar

Den hämtade profil filen innehåller information som krävs för att konfigurera en VPN-anslutning. Den här artikeln hjälper dig att få och förstå den information som krävs för en VPN-klient profil.

## <a name="1-download-the-file"></a>1. Hämta filen

Kör följande kommandon. Kopiera resultat-URL: en till webbläsaren för att ladda ned profil zip-filen.

```azurepowershell-interactive
$profile = New-AzVpnClientConfiguration -ResourceGroupName AADAuth -Name AADauthGW -AuthenticationMethod "EapTls"
   
$PROFILE.VpnProfileSASUrl
```

## <a name="2-extract-the-zip-file"></a>2. extrahera zip-filen

Extrahera zip-filen. Filen innehåller följande mappar:

* AzureVPN
* Generisk
* OpenVPN (om du har aktiverat inställningarna OpenVPN och Azure AD-autentisering på gatewayen. Se [skapa en klient](openvpn-azure-ad-tenant.md).)

## <a name="3-retrieve-information"></a>3. Hämta information

I mappen **AzureVPN** navigerar du till filen ***azurevpnconfig. XML*** och öppnar den med anteckningar. Anteckna texten mellan följande taggar.

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

* **Mappen OpenVPN** innehåller den *OVPN* -profil som måste ändras för att inkludera nyckeln och certifikatet. Mer information finns i [konfigurera OpenVPN-klienter för Azure VPN gateway](vpn-gateway-howto-openvpn-clients.md#windows).

* Den **allmänna mappen** innehåller det offentliga Server certifikatet och filen VpnSettings. xml. Filen VpnSettings. xml innehåller information som krävs för att konfigurera en allmän klient.

* Den hämtade ZIP-filen kan också innehålla **WindowsAmd64** -och **WindowsX86** -mappar. Dessa mappar innehåller installations programmet för SSTP och IKEv2 för Windows-klienter. Du måste ha administratörs behörighet på klienten för att installera dem.

## <a name="next-steps"></a>Nästa steg

Mer information om punkt-till-plats finns i [om punkt-till-plats](point-to-site-about.md).
