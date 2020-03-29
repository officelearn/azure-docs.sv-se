---
title: Så här fungerar SSO för lokala resurser på Azure AD-anslutna enheter | Microsoft-dokument
description: Lär dig att konfigurera Hybrid Azure Active Directory-anslutningsenheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: f9d8c0cd803424e117bd4dc7a3382b7b32df2d05
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672708"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Så här fungerar SSO för lokala resurser på Azure AD-anslutna enheter

Det är förmodligen ingen överraskning att en Azure Active Directory (Azure AD) ansluten enhet ger dig en enda inloggning (SSO) upplevelse till din klients molnappar. Om din miljö har en lokal Active Directory (AD) kan du utöka SSO-upplevelsen på dessa enheter till den.

I den här artikeln beskrivs hur detta fungerar.

## <a name="prerequisites"></a>Krav

 Om Azure AD-anslutna datorer inte är anslutna till organisationens nätverk krävs en VPN- eller annan nätverksinfrastruktur. Lokala SSO kräver direktkommunikation med dina lokala AD DS-domänkontrollanter.

## <a name="how-it-works"></a>Hur det fungerar 

Eftersom du bara behöver komma ihåg ett enda användarnamn och lösenord förenklar SSO åtkomsten till dina resurser och förbättrar säkerheten i din miljö. Med en Azure AD-ansluten enhet har användarna redan en SSO-upplevelse till molnapparna i din miljö. Om din miljö har en Azure AD och en lokal AD, vill du förmodligen utöka omfattningen av din SSO-upplevelse till dina lokala LOB-appar (Line Of Business), filresurser och skrivare.

Azure AD-anslutna enheter har ingen kunskap om din lokala AD-miljö eftersom de inte är anslutna till den. Du kan dock ge ytterligare information om din lokala AD till dessa enheter med Azure AD Connect.

En miljö som har både en Azure AD och en lokal AD, är också känd har hybridmiljö. Om du har en hybridmiljö är det troligt att du redan har Azure AD Connect distribuerat för att synkronisera din lokala identitetsinformation till molnet. Som en del av synkroniseringsprocessen synkroniserar Azure AD Connect lokal användarinformation till Azure AD. När en användare loggar in på en Azure AD-ansluten enhet i en hybridmiljö:

1. Azure AD skickar namnet på den lokala domänen som användaren är medlem i tillbaka till enheten.
1. Tjänsten LSA (Local Security Authority) aktiverar Kerberos-autentisering på enheten.

Under ett åtkomstförsök till en resurs som begär Kerberos i användarens lokala miljö, enheten:

1. Skickar lokal domäninformation och användarautentiseringsuppgifter till den lokaliserade domänkontrollanten för att få användaren autentiserad.
1. Tar emot en Kerberos [Ticket-Granting Ticket (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) som används för att komma åt AD-anslutna resurser. Om försöket att hämta TGT för AAD connect-domänen misslyckas (relaterad DCLocator-timeout kan orsaka en fördröjning), försöker Autentiseringshanterarens poster eller så kan användaren få en autentiseringspopup som begär autentiseringsuppgifter för målresursen.

Alla appar som är konfigurerade för **Windows-integrerad autentisering** får sömlöst SSO när en användare försöker komma åt dem.

Windows Hello för företag kräver ytterligare konfiguration för att aktivera lokala SSO från en Azure AD-ansluten enhet. Mer information finns i [Konfigurera Azure AD-anslutna enheter för lokala enstaka inloggningar med Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Vad du får

Med SSO kan du på en Azure AD-ansluten enhet: 

- Komma åt en UNC-sökväg på en AD-medlemsserver
- Få tillgång till en AD-medlemswebbserver som är konfigurerad för Windows-integrerad säkerhet 

Om du vill hantera din lokala AD från en Windows-enhet installerar du [administrationsverktygen för fjärrserver för Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Du kan använda:

- Snapin-modulen Active Directory -användare och datorer (ADUC) för att administrera alla AD-objekt. Du måste dock ange vilken domän som du vill ansluta till manuellt.
- DHCP-snapin-modulen för att administrera en AD-ansluten DHCP-server. Du kan dock behöva ange DHCP-serverns namn eller adress.
 
## <a name="what-you-should-know"></a>Det här bör du känna till

Du kan behöva justera din [domänbaserade filtrering](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) i Azure AD Connect för att säkerställa att data om de domäner som krävs synkroniseras.

Appar och resurser som är beroende av Active Directory-datorautentisering fungerar inte eftersom Azure AD-anslutna enheter inte har ett datorobjekt i AD. 

Du kan inte dela filer med andra användare på en Azure AD-ansluten enhet.

## <a name="next-steps"></a>Nästa steg

Mer information finns [i Vad är enhetshantering i Azure Active Directory?](overview.md) 
