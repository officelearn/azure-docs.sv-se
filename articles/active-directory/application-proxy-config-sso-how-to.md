---
title: Hur du konfigurerar enkel inloggning till ett program med Application Proxy | Microsoft Docs
description: Hur du kan konfigurera enkel inloggning i tillämpningsprogrammet application proxy snabbt
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 977f8af1f625548cb37c7deba7d368d45bded6ef
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/23/2018
ms.locfileid: "36332668"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Hur du konfigurerar enkel inloggning till ett program med Application Proxy

Enkel inloggning (SSO) kan användarna komma åt ett program utan autentiseras flera gånger. Det kan den enda-autentiseringen i molnet, mot Azure Active Directory, och tjänsten eller koppling att personifiera användaren Slutför eventuella ytterligare autentiseringsfrågor från programmet.

## <a name="how-to-configure-single-sign-on"></a>Hur du konfigurerar enkel inloggning på
Om du vill konfigurera enkel inloggning, kontrollera först att programmet har konfigurerats för förautentisering via Azure Active Directory. Om du vill göra den här konfigurationen, gå till **Azure Active Directory**  - &gt; **företagsprogram**  - &gt; **alla program**   - &gt; Programmet  **- &gt; Application Proxy**. På den här sidan finns i fältet ”förautentisering” och se till att är inställt på ”Azure Active Directory. 

Mer information om metoder för autentisering finns i steg 4 i den [app publishing dokumentet](manage-apps/application-proxy-publish-azure-portal.md).

   ![Före autentiseringsmetod i Azure-portalen](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkel inloggning lägen för Application Proxy-program
Konfigurera den speciella typ av enkel inloggning. Metoderna inloggning klassificeras baserat på vilken typ av autentisering används för backend-programmet. Appen Proxy program stöder tre typer av inloggning:

-   **Lösenordsbaserade inloggning**: lösenordsbaserade inloggning kan användas för alla program som använder fälten användarnamn och lösenord för att logga in. Konfigurationssteg som görs i den [lösenord SSO configuration dokumentationen](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Integrerad Windows-autentisering**: för program som använder integrerad autentisering IWA (Windows), enkel inloggning är aktiverat via Kerberos-begränsad delegering (KCD). Den här metoden ger Application Proxy kopplingar behörigheter i Active Directory för att personifiera användare, och för att skicka och ta emot token åt. Information om hur du konfigurerar KCD finns i den [enkel inloggning med KCD dokumentation](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrik-baserade inloggning**: huvud-baserade inloggning aktiveras via ett partnerskap och kräver ytterligare konfiguration. Mer information om partnerskap och stegvisa instruktioner för hur du konfigurerar enkel inloggning till ett program som använder huvuden för autentisering finns i [PingAccess för Azure AD-dokumentationen](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md).

Dessa alternativ kan hittas genom att gå till ditt program i ”företagsprogram” och öppna den **enkel inloggning** sida på den vänstra menyn. Observera att om programmet har skapats i den gamla portalen kan du inte kanske ser alla dessa alternativ.

På den här sidan kan du också se en ytterligare Sign-On-alternativet: länkade inloggning. Det här alternativet stöds också av Application Proxy. Det här alternativet läggs dock inte enkel inloggning till programmet. Som säger att programmet kanske redan har enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services. 

Det här alternativet kan en administratör för att skapa en länk till ett program att första mark användare på att få åtkomst till programmet. Om det finns ett program som har konfigurerats för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör till exempel använda alternativet ”länkade inloggning” så här skapar du en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
