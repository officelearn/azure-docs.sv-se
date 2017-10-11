---
title: "Villkorlig åtkomst till lokala appar – Azure AD | Microsoft Docs"
description: "Beskriver hur du konfigurerar villkorlig åtkomst för program som du publicerar för att kunna nås med hjälp av Azure AD Application Proxy."
services: active-directory
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 2e97722b-eb4e-4078-b607-9fed210d8a0f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2017
ms.author: kgremban
ms.reviewer: harshja
ms.custom: it-pro; oldportal
ms.openlocfilehash: 463946256f9e335fa6d98fc904835e5c3dc2725e
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/03/2017
---
# <a name="working-with-conditional-access-in-azure-ad-application-proxy"></a>Arbeta med villkorlig åtkomst i Azure AD Application Proxy

>[!NOTE]
>Den här artikeln gäller för den klassiska Azure-portalen som dras. Vi rekommenderar att du använder den [Azure-portalen](https://portal.azure.com). Programproxy appar har samma funktioner för villkorlig åtkomst som andra SaaS-app i Azure-portalen. Läs mer om villkorlig åtkomst i [Kom igång med villkorlig åtkomst i Azure Active Directory](active-directory-conditional-access-azure-portal-get-started.md).

Du kan konfigurera åtkomstregler för att ge villkorad tillgång till program som publicerats med Application Proxy. Detta innebär att du kan:

* Kräv Multi-Factor authentication per program
* Kräv Multi-Factor authentication endast när användare inte är på arbetet
* Blockera användare från att komma åt programmet när de inte är på arbetet

De här reglerna kan användas för alla användare och grupper eller endast för specifika användare och grupper. Denna regel gäller för alla användare som har åtkomst till programmet som standard. Regeln kan dock också begränsas till användare som är medlemmar i de angivna säkerhetsgrupperna.  

Åtkomstregler utvärderas när en användare ansluter till en federerad program som använder OAuth 2.0, OpenID Connect, SAML och WS-Federation. Dessutom utvärderas åtkomstregler med OAuth 2.0 och OpenID Connect när en uppdateringstoken används för att få en åtkomst-token.

## <a name="conditional-access-prerequisites"></a>Krav för villkorlig åtkomst
* Azure Active Directory Premium-prenumeration
* En federerade eller hanterade Azure Active Directory-klient
* Federerade klienter kräver Multi-Factor authentication (MFA)  
    ![Konfigurera regler för åtkomst - kräver Multi-Factor authentication](./media/active-directory-application-proxy-conditional-access/application-proxy-conditional-access.png)

## <a name="configure-per-application-multi-factor-authentication"></a>Konfigurera Multi-Factor authentication per program
1. Logga in som administratör i den klassiska Azure-portalen.
2. Gå till Active Directory och välj den katalog som du vill aktivera Application Proxy i.
3. Klicka på **program** och rulla ned till den **åtkomstregler** avsnitt. Avsnittet åtkomst regler visas endast för program som publiceras med hjälp av Webbprogramproxy som använder federerad autentisering.
4. Aktivera regeln genom att välja **aktivera åtkomstregler** till **på**.
5. Ange de användare och grupper som gäller. Använd den **Lägg till grupp** för att välja en eller flera grupper som regeln gäller. Den här dialogrutan kan också användas för att ta bort valda grupper.  När reglerna har markerats att gälla grupper har åtkomstregler tillämpas endast för användare som hör till en av de angivna säkerhetsgrupperna.  

   * Om du vill exkludera uttryckligen säkerhetsgrupper från regeln, kontrollera **utom** och ange en eller flera grupper. Användare som är medlemmar i en grupp i listan över Except krävs inte för att utföra multifaktorautentisering.  
   * Om en användare har konfigurerats med hjälp av funktionen per användare multifaktorautentisering, åsidosätter den här inställningen programmet multifaktorautentisering regler. En användare som har konfigurerats för multifaktorautentisering för per användare behöver utföra multifaktorautentisering, även om de har undantagits från programmets multifaktorautentisering regler. Lär dig mer om [Multi-Factor autentisering och användarspecifika inställningar](../multi-factor-authentication/multi-factor-authentication.md).
6. Välj den åtkomstregel som du vill ange:

   * **Kräver Multi-Factor authentication**: användare som åtkomstregler gälla behöver fullständiga multifaktorautentisering innan åtkomst till programmet som regeln gäller.
   * **Kräver Multi-Factor authentication när de inte är på arbetet**: användare som försöker få åtkomst till programmet från en betrodd IP-adress inte behöver utföra multifaktorautentisering. Betrodda IP-adressintervall kan konfigureras på inställningssidan för multifaktorautentisering.
   * **Blockera åtkomst när de inte är på arbetet**: användare som försöker få åtkomst till programmet från utanför företagets nätverk inte åtkomst till programmet.

## <a name="configuring-mfa-for-federation-services"></a>Konfigurera MFA för federation services
För federerade klienter multifaktorautentisering (MFA) kan utföras genom Azure Active Directory eller lokal AD FS-servern. Som standard sker MFA på valfri sida hos Azure Active Directory. För att konfigurera MFA på lokalt, kör du Windows PowerShell och Ställ in Azure AD-modulen med hjälp av egenskapen – SupportsMFA.

I följande exempel visas hur du aktiverar Multifaktorautentisering för lokalt med hjälp av den [cmdlet Set-MsolDomainFederationSettings](https://msdn.microsoft.com/library/azure/dn194088.aspx) för contoso.com innehavaren:`Set-MsolDomainFederationSettings -DomainName contoso.com -SupportsMFA $true `

Utöver att ställa in den här flaggan måste federerade klient AD FS-instans konfigureras för att utföra multifaktorautentisering. Följ instruktionerna för [distribuerar Microsoft Azure Multi-Factor authentication lokalt](../multi-factor-authentication/multi-factor-authentication-get-started-server.md).

## <a name="see-also"></a>Se även
* [Arbeta med anspråksmedvetna program](active-directory-application-proxy-claims-aware-apps.md)
* [Publicera program med Application Proxy](active-directory-application-proxy-publish.md)
* [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
* [Publicera program med ditt domännamn](active-directory-application-proxy-custom-domains.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)
