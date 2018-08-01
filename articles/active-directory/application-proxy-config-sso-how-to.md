---
title: Så här konfigurerar du enkel inloggning till ett program med Application Proxy | Microsoft Docs
description: Hur du kan konfigurera enkel inloggning till din application proxy-program snabbt
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
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 178737a5dec1aace43de9ddb8dcfac73530a250f
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363185"
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Så här konfigurerar du enkel inloggning till ett Application Proxy-program

Enkel inloggning (SSO) kan användarna komma åt ett program utan autentiseras flera gånger. Det tillåter enkel autentisering kan förekomma i molnet mot Azure Active Directory, och gör att tjänsten eller anslutningen att personifiera användare för att slutföra alla ytterligare autentiseringsfrågor från programmet.

## <a name="how-to-configure-single-sign-on"></a>Hur du konfigurerar enkel inloggning på
Om du vill konfigurera enkel inloggning, se till att ditt program är konfigurerad för förautentisering via Azure Active Directory. Om du vill göra den här konfigurationen, gå till **Azure Active Directory**  - &gt; **företagsprogram**  - &gt; **alla program**   - &gt; Programmets  **- &gt; Application Proxy**. På den här sidan finns i fältet ”förautentisering” och kontrollerar som är inställt på ”Azure Active Directory. 

Mer information om de före autentiseringsmetoderna finns i steg 4 i den [publishing appdokument](manage-apps/application-proxy-publish-azure-portal.md).

   ![Förautentiseringsmetod i Azure-portalen](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkel inloggning lägen för Application Proxy-program
Konfigurera vilken typ av enkel inloggning. Metoderna som inloggning klassificeras baserat på vilken typ av autentisering backend-programmet använder. App Proxy-program stöder tre typer av inloggning:

-   **Lösenordsbaserad inloggning**: lösenordsbaserad inloggning kan användas för alla program som använder fälten användarnamn och lösenord för att logga in. Konfigurationsstegen finns i den [lösenord SSO dokumentationen för configuration](active-directory-enterprise-apps-whats-new-azure-portal.md#bring-your-own-password-sso-applications).

-   **Integrerad Windows-autentisering**: för program som använder integrerad Windows autentisering (IWA), enkel inloggning är aktiverat via Kerberos-begränsad delegering (KCD). Den här metoden ger Programproxyns-kopplingar behörighet i Active Directory att personifiera användare, och för att skicka och ta emot token för deras räkning. Information om hur du konfigurerar KCD finns i den [enkel inloggning med KCD-dokumentation](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md).

-   **Rubrikbaserad inloggning**: rubrikbaserad inloggning är aktiverat via ett partnerskap och kräver ytterligare konfigurering. Mer information om partnerskap och stegvisa instruktioner för att konfigurera enkel inloggning till ett program som använder rubriker för autentisering finns i den [PingAccess för Azure AD-dokumentationen](manage-apps/application-proxy-configure-single-sign-on-with-ping-access.md).

Var och en av dessa alternativ kan hittas genom att gå till ditt program i ”program” och öppna den **enkel inloggning** sida på den vänstra menyn. Observera att om ditt program har skapats i den gamla portalen kan du inte kanske ser alla dessa alternativ.

På den här sidan kan du också ser något alternativ för ytterligare inloggning: länkad inloggning. Det här alternativet stöds också av programproxy. Det här alternativet läggs dock inte enkel inloggning till programmet. Men programmet kan redan ha enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services. 

Det här alternativet kan en administratör för att skapa en länk till ett program att första mark användare på vid åtkomst till programmet. Om det finns ett program som är konfigurerad för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör exempelvis använda alternativet ”länkad inloggning” för att skapa en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](manage-apps/application-proxy-configure-single-sign-on-with-kcd.md)
