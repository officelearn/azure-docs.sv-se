---
title: Hur du konfigurerar enkel inloggning till ett program med Application Proxy | Microsoft Docs
description: "Hur du kan konfigurera enkel inloggning i tillämpningsprogrammet application proxy snabbt"
services: active-directory
documentationcenter: 
author: ajamess
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: asteen
ms.openlocfilehash: ccab427857b1439f37f3d9f193e35a4fc2237014
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="how-to-configure-single-sign-on-to-an-application-proxy-application"></a>Hur du konfigurerar enkel inloggning till ett program med Application Proxy

Enkel inloggning (SSO) kan användarna komma åt ett program utan autentiseras flera gånger. Det kan den enda-autentiseringen i molnet, mot Azure Active Directory, och tjänsten eller koppling att personifiera användaren Slutför eventuella ytterligare autentiseringsfrågor från programmet.

## <a name="how-to-configure-single-sign-on"></a>Hur du konfigurerar enkel inloggning på
Om du vill konfigurera enkel inloggning, kontrollera först att programmet har konfigurerats för förautentisering via Azure Active Directory. Det gör du genom att gå till **Azure Active Directory**  - &gt; **företagsprogram**  - &gt; **alla program**  - &gt; programmet  **- &gt; Application Proxy**. På den här sidan finns i fältet ”förautentisering” och se till att är inställt på ”Azure Active Directory. 

Mer information om de före autentiseringsmetoderna finns i steg fyra i den [app publishing dokumentet](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).

   ![Före autentiseringsmetod i Azure Portal](./media/application-proxy-config-sso-how-to/app-proxy.png)

## <a name="configuring-single-sign-on-modes-for-application-proxy-applications"></a>Konfigurera enkel inloggning lägen för Application Proxy-program
Därefter konfigurera vi den speciella typ av enkel inloggning. Metoderna inloggning klassificeras baserat på vilken typ av autentisering används för backend-programmet. Appen Proxy program stöder tre typer av inloggning:

-   **Lösenordsbaserade inloggning**: lösenordsbaserade inloggning kan användas för alla program som använder fälten användarnamn och lösenord för inloggning. Konfigurationssteg finns i vår [lösenord SSO configuration dokumentationen](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-apps-whats-new-azure-portal#bring-your-own-password-sso-applications).

-   **Integrerad Windows-autentisering**: för program som använder integrerad autentisering IWA (Windows), enkel inloggning är aktiverat via Kerberos-begränsad delegering (KCD). Detta ger Application Proxy kopplingar behörigheter i Active Directory för att personifiera användare, och för att skicka och ta emot token åt. Information om hur du konfigurerar KCD finns i den [enkel inloggning med KCD dokumentation](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-sso-using-kcd).

-   **Rubrik-baserade inloggning**: huvud-baserade inloggning aktiveras genom ett partnerskap och kräver ytterligare konfiguration. Mer information om partnerskap och stegvisa instruktioner för hur du konfigurerar enkel inloggning till ett program som använder huvuden för autentisering finns i [PingAccess för Azure AD-dokumentationen](https://docs.microsoft.com/azure/active-directory/application-proxy-ping-access).

Dessa alternativ kan hittas genom att gå till ditt program i ”företagsprogram” och öppna den **enkel inloggning** sida på den vänstra menyn. Observera att om programmet har skapats i den gamla portalen kan du inte kanske ser alla dessa alternativ.

På den här sidan kan du också se en ytterligare Sign-On-alternativet: länkade inloggning. Detta stöds också av Application Proxy. Tänk på att det här alternativet inte lägger till enkel inloggning till programmet. Som säger att programmet kanske redan har enkel inloggning implementeras med hjälp av en annan tjänst, till exempel Active Directory Federation Services. 

Det här alternativet kan en administratör för att skapa en länk till ett program att första mark användare på att få åtkomst till programmet. Om det finns ett program som har konfigurerats för att autentisera användare som använder Active Directory Federation Services 2.0, kan en administratör till exempel använda alternativet ”länkade inloggning” så här skapar du en länk till den på åtkomstpanelen.

## <a name="next-steps"></a>Nästa steg
[Tillhandahålla enkel inloggning till dina appar med Application Proxy](active-directory-application-proxy-sso-using-kcd.md)
