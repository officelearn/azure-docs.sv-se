---
title: Hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter | Microsoft Docs
description: Lär dig att konfigurera Hybrid Azure Active Directory-anslutningsenheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 149b01401cd6feb7610510efeb1ad9a3c69f3ecf
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/07/2020
ms.locfileid: "86024051"
---
# <a name="how-sso-to-on-premises-resources-works-on-azure-ad-joined-devices"></a>Hur SSO till lokala resurser fungerar på Azure AD-anslutna enheter

Det är förmodligen inte en överraskning att en Azure Active Directory (Azure AD) ansluten enhet ger dig en enkel inloggning (SSO) till klientens molnappar. Om din miljö har en lokal Active Directory (AD) kan du utöka SSO-upplevelsen på dessa enheter till resurser och program som är beroende av lokala AD också. 

Den här artikeln förklarar hur det fungerar.

## <a name="prerequisites"></a>Krav

 Om Azure AD-anslutna datorer inte är anslutna till din organisations nätverk krävs en VPN-eller annan nätverks infrastruktur. Lokal inloggning kräver detaljerad kommunikation med dina lokala AD DS-domänkontrollanter.

## <a name="how-it-works"></a>Så här fungerar det 

Med en Azure AD-ansluten enhet har användarna redan en SSO-upplevelse till molnappar i din miljö. Om din miljö har en Azure AD och en lokal AD kan du vilja expandera omfattningen av din SSO-upplevelse till dina lokala branschspecifika appar, fil resurser och skrivare.

Azure AD-anslutna enheter har ingen kunskap om din lokala AD-miljö eftersom de inte är anslutna till den. Du kan dock ange ytterligare information om din lokala AD till dessa enheter med Azure AD Connect.

En miljö med både en Azure AD och en lokal AD är även känd med hybrid miljö. Om du har en hybrid miljö är det troligt att du redan har Azure AD Connect distribuerat för att synkronisera din lokala identitets information till molnet. Som en del av synkroniseringsprocessen synkroniserar Azure AD Connect lokal användar information till Azure AD. När en användare loggar in på en Azure AD-ansluten enhet i en hybrid miljö:

1. Azure AD skickar information om användarens lokala domän tillbaka till enheten, tillsammans med den [primära uppdateringstoken](concept-primary-refresh-token.md)
1. Tjänsten Local Security Authority (LSA) möjliggör Kerberos-och NTLM-autentisering på enheten.

Under ett åtkomst försök till en resurs som begär Kerberos eller NTLM i användarens lokala miljö, enheten:

1. Skickar lokal domän information och användarautentiseringsuppgifter till den lokaliserade DOMÄNKONTROLLANTen för att få användaren autentiserad.
1. Tar emot en Kerberos [-biljett för biljett beviljande biljetter (TGT)](/windows/desktop/secauthn/ticket-granting-tickets) eller NTLM-token baserat på det protokoll som stöds av den lokala resursen eller programmet. Om försöket att hämta Kerberos-TGT eller NTLM-token för domänen Miss lyckas (en DCLocator tids gräns kan orsaka en fördröjning), görs ett försök av Autentiseringshanteraren-poster, eller så kan användaren få ett popup-meddelande om att begära autentiseringsuppgifter för mål resursen.

Alla appar som har kon figurer ATS för **Windows-integrerad autentisering** får sömlös inloggning när en användare försöker komma åt dem.

Windows Hello för företag kräver ytterligare konfiguration för att aktivera lokal SSO från en Azure AD-ansluten enhet. Mer information finns i [Konfigurera Azure AD-anslutna enheter för lokal enkel inloggning med Windows Hello för företag](/windows/security/identity-protection/hello-for-business/hello-hybrid-aadj-sso-base). 

## <a name="what-you-get"></a>Det här får du

Med enkel inloggning kan du på en Azure AD-ansluten enhet: 

- Få åtkomst till en UNC-sökväg på en AD-medlems Server
- Åtkomst till en AD-medlems server som kon figurer ATS för Windows-integrerad säkerhet 

Om du vill hantera din lokala AD från en Windows-enhet installerar du [verktyg för fjärrserveradministration för Windows 10](https://www.microsoft.com/download/details.aspx?id=45520).

Du kan använda:

- Snapin-modulen Active Directory användare och datorer (ADUC) för att administrera alla AD-objekt. Du måste dock ange den domän som du vill ansluta till manuellt.
- Snapin-modulen DHCP för att administrera en AD-ansluten DHCP-server. Du kan dock behöva ange DHCP-servernamnets namn eller adress.
 
## <a name="what-you-should-know"></a>Det här bör du veta

Du kan behöva justera din [domänbaserade filtrering](../hybrid/how-to-connect-sync-configure-filtering.md#domain-based-filtering) i Azure AD Connect för att säkerställa att data om de nödvändiga domänerna är synkroniserade.

Appar och resurser som är beroende av Active Directory datorns autentisering fungerar inte eftersom Azure AD-anslutna enheter inte har något dator objekt i AD. 

Du kan inte dela filer med andra användare på en Azure AD-ansluten enhet.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [Vad är enhets hantering i Azure Active Directory?](overview.md) 
