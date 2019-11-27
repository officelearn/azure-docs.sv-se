---
title: Så här konfigurerar du enkel inloggning till en Application Proxy-app
description: Hur du kan konfigurera enkel inloggning till din application proxy-program snabbt
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
ms.openlocfilehash: 8b5eca08e0b736937af42e58d81148636e3269df
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275640"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Så här konfigurerar du enkel inloggning till ett Application Proxy-program

Enkel inloggning (SSO) kan användarna komma åt ett program utan autentiseras flera gånger. Det tillåter enkel autentisering kan förekomma i molnet mot Azure Active Directory, och gör att tjänsten eller anslutningen att personifiera användare för att slutföra alla ytterligare autentiseringsfrågor från programmet.

## <a name="how-to-configure-single-sign-on"></a>Hur du konfigurerar enkel inloggning på
Om du vill konfigurera enkel inloggning, se till att ditt program är konfigurerad för förautentisering via Azure Active Directory. För att utföra den här konfigurationen går du till **Azure Active Directory** -&gt; **Enterprise-program** -&gt; **alla program** -&gt; ditt program **-&gt; programproxy**. På den här sidan finns i fältet ”förautentisering” och kontrollerar som är inställt på ”Azure Active Directory. 

Mer information om metoderna för autentisering finns i steg 4 i [appens publicerings dokument](application-proxy-add-on-premises-application.md).

   ![Förautentiseringsmetod i Azure-portalen](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkel inloggning lägen för Application Proxy-program
Konfigurera vilken typ av enkel inloggning. Metoderna som inloggning klassificeras baserat på vilken typ av autentisering backend-programmet använder. App Proxy-program stöder tre typer av inloggning:

-   **Lösenordsbaserad inloggning**: lösenordsbaserad inloggning kan användas för alla program som använder fälten användar namn och lösen ord för att logga in. Konfigurations steg finns i [Konfigurera enkel inloggning med lösen ord för ett Azure AD Gallery-program](configure-password-single-sign-on-gallery-applications.md).

-   **Integrerad Windows-autentisering**: för program som använder integrerad Windows-autentisering (IWA) aktive ras enkel inloggning via Kerberos-begränsad delegering (KCD). Den här metoden ger Programproxyns-kopplingar behörighet i Active Directory att personifiera användare, och för att skicka och ta emot token för deras räkning. Information om hur du konfigurerar KCD finns i [enkel inloggning med KCD-dokumentationen](application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrik-baserad inloggning**: en rubrik baserad inloggning har Aktiver ATS via ett partnerskap och kräver ytterligare konfiguration. Mer information om partnerskap och stegvisa anvisningar för att konfigurera enkel inloggning till ett program som använder rubriker för autentisering finns i [PingAccess för Azure AD-dokumentationen](application-proxy-configure-single-sign-on-with-ping-access.md).

-   **SAML enkel inloggning**: med enkel inloggning med SAML, autentiserar Azure AD programmet med hjälp av användarens Azure AD-konto. Azure AD kommunicerar information inloggning till programmet via en anslutningsprotokoll. Med SAML-baserad enkel inloggning kan du mappa användare till specifika program roller baserat på regler som du definierar i dina SAML-anspråk. Information om hur du konfigurerar SAML enkel inloggning finns i [SAML för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-on-premises-apps.md).

Vart och ett av dessa alternativ finns i ditt program i "företags program" och öppnar sidan för **enkel inloggning** på den vänstra menyn. Observera att om ditt program skapades på den gamla portalen kanske du inte ser alla dessa alternativ.

På den här sidan kan du också ser något alternativ för ytterligare inloggning: länkad inloggning. Det här alternativet stöds också av programproxy. Det här alternativet läggs dock inte enkel inloggning till programmet. Men programmet kan redan ha enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services. 

Det här alternativet kan en administratör för att skapa en länk till ett program att första mark användare på vid åtkomst till programmet. Om det finns ett program som är konfigurerad för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör exempelvis använda alternativet ”länkad inloggning” för att skapa en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
- [Lösen ords valv för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-password-vaulting.md)
- [Kerberos-begränsad delegering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md)
- [Huvud-baserad autentisering för enkel inloggning med Application Proxy](application-proxy-configure-single-sign-on-with-ping-access.md) 
- [SAML för enkel inloggning med programproxy](application-proxy-configure-single-sign-on-on-premises-apps.md).
