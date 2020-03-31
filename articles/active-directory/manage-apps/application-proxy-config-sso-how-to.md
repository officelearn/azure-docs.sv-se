---
title: Konfigurera enkel inloggning till en programproxyapp
description: Så här konfigurerar du snabbt en inloggning till ditt programproxyprogram
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/12/2019
ms.author: mimart
ms.reviewer: japere, asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 589b3e51f27147f0a0432b61c22a024c202e388b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76712028"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Konfigurera enkel inloggning till ett programproxyprogram

Enkel inloggning (SSO) gör att användarna kan komma åt ett program utan att autentisera flera gånger. Det gör att den enda autentiseringen kan ske i molnet, mot Azure Active Directory, och gör det möjligt för tjänsten eller Connector att personifiera användaren för att slutföra eventuella ytterligare autentiseringsutmaningar från programmet.

## <a name="how-to-configure-single-sign-on"></a>Konfigurera enkel inloggning
Om du vill konfigurera SSO kontrollerar du först att ditt program är konfigurerat för förautentisering via Azure Active Directory. Om du vill utföra den här konfigurationen går du till **Azure Active Directory**  - &gt; **Enterprise Applications**  - &gt; **All Applications**  - &gt; Your ** - &gt; Application Application Proxy**. På den här sidan visas fältet "Förautentisering" och se till att den är inställd på "Azure Active Directory.On this page, you see the "Pre Authentication" field, and make sure that is set to "Azure Active Directory. 

Mer information om metoderna för förautentisering finns i steg 4 i [apppubliceringsdokumentet](application-proxy-add-on-premises-application.md).

   ![Metod för förautentisering i Azure-portalen](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkla inloggningslägen för programproxyprogram
Konfigurera den specifika typen av enkel inloggning. Inloggningsmetoderna klassificeras baserat på vilken typ av autentisering backend-programmet använder. App Proxy-program stöder tre typer av inloggning:

-   **Lösenordsbaserad inloggning:** Lösenordsbaserad inloggning kan användas för alla program som använder användarnamn och lösenordsfält för att logga in. Konfigurationssteg finns i [Konfigurera lösenord Enkel inloggning för ett Azure AD-galleriprogram](configure-password-single-sign-on-non-gallery-applications.md).

-   **Integrerad Windows-autentisering**: För program som använder integrerad Windows-autentisering (IWA) aktiveras enkel inloggning via Kerberos Constrained Delegation (KCD). Den här metoden ger application proxy connectors behörighet i Active Directory för att personifiera användare och att skicka och ta emot token för deras räkning. Information om hur du konfigurerar KCD finns i enkel [inloggning med KCD-dokumentation](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrikbaserad inloggning:** Rubrikbaserad inloggning aktiveras genom ett partnerskap och kräver ytterligare konfiguration. Mer information om partnerskaps- och steg-för-steg-instruktioner för att konfigurera enkel inloggning till ett program som använder rubriker för autentisering finns i [Dokumentationen till PingAccess för Azure AD](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML enkel inloggning:** Med SAML enkel inloggning autentiserar Azure AD till programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar inloggningsinformationen till programmet via ett anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika programroller baserat på regler som du definierar i dina SAML-anspråk. Information om hur du konfigurerar SAML-enkel inloggning finns i [SAML för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Vart och ett av dessa alternativ kan hittas genom att gå till din ansökan i "Enterprise Applications", och öppna **sidan Enkel inloggning** på den vänstra menyn. Observera att om ditt program har skapats i den gamla portalen kanske du inte ser alla dessa alternativ.

På den här sidan ser du också ytterligare ett inloggningsalternativ: Länkad inloggning. Det här alternativet stöds också av Programproxy. Det här alternativet lägger dock inte till enkel inloggning i programmet. Som sagt programmet kanske redan har enstaka inloggning genomförs med en annan tjänst som Active Directory Federation Services. 

Med det här alternativet kan en administratör skapa en länk till ett program som användarna först landar på när de öppnar programmet. Om det till exempel finns ett program som är konfigurerat för att autentisera användare med Active Directory Federation Services 2.0, kan en administratör använda alternativet "Länkad inloggning" för att skapa en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
- [Lösenordsvalv för enkel inloggning med Programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos begränsad delegering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Rubrikbaserad autentisering för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
