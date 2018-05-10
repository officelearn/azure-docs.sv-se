---
title: Grant B2B-användare i Azure AD åtkomst till lokala program | Microsoft Docs
description: Visar hur du ger molnet B2B-användare åtkomst till lokala appar med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: article
ms.date: 04/20/2018
ms.author: twooley
author: twooley
manager: mtillman
ms.reviewer: sasubram
ms.openlocfilehash: 1c13da977021538651084ec4462cf8bd32f131d7
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/10/2018
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Grant B2B-användare i Azure AD åtkomst till lokala program

Som en organisation som använder Azure Active Directory (AD Azure) B2B-samarbetesfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD, kan du nu ange dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

## <a name="access-to-saml-apps"></a>Åtkomst till SAML-appar

Om appen lokalt använder SAML-baserad autentisering, kan du enkelt göra apparna tillgängliga för Azure AD B2B-samarbete användarna via Azure-portalen.

Du måste göra följande:

- Integrera SAML-app med hjälp av programmallen icke-galleriet enligt beskrivningen i [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](active-directory-saas-custom-apps.md). Se till att Observera att du använder för den **inloggnings-URL** värde.
-  Använda Azure AD Application Proxy för att publicera appen lokalt med **Azure Active Directory** konfigurerad som autentiseringskälla. Instruktioner finns i [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md). 

   När du konfigurerar den **intern Url** inställningen genom att använda den inloggnings-URL som du angav i programmallen inte galleriet. På så sätt kan användare komma åt appen från utanför gränsen för organisationen. Application Proxy som utför den SAML enkel inloggning för appen lokalt.
 
   ![Visar lokala inställningar interna Webbadress och autentisering](media/active-directory-b2b-hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Åtkomst till appar IWA och KCD

Om du vill ge B2B-användare åtkomst till lokala program som skyddas med integrerad Windows-autentisering och Kerberos-begränsad delegering, måste följande komponenter:

- **Autentisering via Azure AD Application Proxy**. B2B-användare måste kunna autentisera till programmet lokalt. Om du vill göra detta måste du publicera lokala appen via Azure AD Application Proxy. Mer information finns i [komma igång med Application Proxy och installera connector](active-directory-application-proxy-enable.md) och [publicera program med Azure AD Application Proxy](application-proxy-publish-azure-portal.md).
- **Auktorisering via ett B2B användarobjekt i den lokala katalogen**. Programmet måste kunna utföra åtkomstkontroller för användare och bevilja åtkomst till rätt resurser. IWA och KCD kräver ett användarobjekt i den lokala Windows Server Active Directory att slutföra detta tillstånd. Enligt beskrivningen i [hur enkel inloggning med KCD fungerar](active-directory-application-proxy-sso-using-kcd.md#how-single-sign-on-with-kcd-works), Application Proxy måste det här objektet för att personifiera användaren och få en Kerberos-token till appen. 

   Det finns två metoder som kan användas för att skapa gästen användarobjekt som krävs för auktorisering i den lokala katalogen för Användarscenario B2B:

   - Microsoft Identity Manager (MIM) och [MIM-hanteringsagenten för Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Ett PowerShell-skript](#create-b2b-guest-user-objects-through-a-script-preview). Med hjälp av skript är en mer enkel lösning som inte kräver MIM. 

Följande diagram ger en översikt över hur Azure AD Application Proxy och generering av B2B-användarobjektet i lokalt katalogen fungerar tillsammans för att bevilja B2B-användare åtkomst till din lokala IWA och KCD appar. Numrerade steg beskrivs i detalj nedan diagrammet.

![Diagram över MIM och B2B skript-lösningar](media/active-directory-b2b-hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  En användare från en partnerorganisation (Fabrikam-klient) uppmanas att Contoso-klienten.
2.  Ett gäst-användarobjekt skapas i Contoso-klienten (till exempel ett användarobjekt med UPN guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Fabrikam gästen importeras från Contoso via MIM eller B2B PowerShell-skript.
4.  En representation av eller ”storleken” på Fabrikam gäst användarobjektet (gäst EXT #) har skapats i den lokala katalogen Contoso.com, via MIM eller B2B PowerShell-skript.
5.  Gästen användaren ansluter till lokalt program, app.contoso.com.
6.  Autentiseringsbegäran är behörighet via tillämpningsproxy, med hjälp av Kerberos-begränsad delegering. 
7.  Eftersom gästen användarobjektet finns lokalt, är autentiseringen lyckas.

### <a name="lifecycle-management-policies"></a>Principer för hantering av livscykel

Du kan hantera de lokala B2B användarobjekt via principer för hantering av livscykel. Exempel:

- Du kan konfigurera principer för multifaktorautentisering (MFA) för gästanvändaren så att MFA används under Application Proxy-autentisering. Mer information finns i [villkorlig åtkomst för användare för B2B-samarbete](active-directory-b2b-mfa-instructions.md).
- Alla sponsring, åtkomst granskningar, konto kontroller, etc. som utförs på molnet B2B användaren gäller för lokala användare. Till exempel om molnet användaren tas bort via principer för hantering av livscykel, tas lokala användare även bort via MIM Sync eller Azure AD Connect-synkronisering. Mer information finns i [hantera gästbehörighet med åtkomst till Azure AD granskar](active-directory-azure-ad-controls-manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim-preview"></a>Skapa B2B gäst användarobjekt via MIM (förhandsgranskning)

Information om hur du använder MIM 2016 Service Pack 1 och MIM-hanteringsagenten för Microsoft Graph för att skapa användarobjekt gäst i den lokala katalogen finns [Azure AD-business-to-business (B2B) samarbete med Microsoft Identity Manager (MIM) 2016 SP1 med Azure Application Proxy (förhandsversion)](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Skapa B2B gäst användarobjekt via ett skript (förhandsgranskning)

Det finns ett PowerShell-exempelskript som kan användas som utgångspunkt för att skapa användarobjekt gäst i din lokala Active Directory.

Du kan hämta skriptet och Readme-filen från den [Download Center](https://www.microsoft.com/download/details.aspx?id=51495). Välj den **skript och viktig information för att hämta Azure AD B2B användare på-prem.zip** fil.

Innan du använder skriptet kontrollerar du att du läser igenom förutsättningarna och viktiga överväganden i associerade Readme-filen. Förstå också som skriptet är gjort tillgängliga enbart som ett exempel. Utvecklingsgruppen eller en partner måste anpassa och granska skriptet innan du kör den.

## <a name="license-considerations"></a>Licens-överväganden

Kontrollera att du har rätt klientåtkomstlicenser (CAL) för externa gästanvändare som har åtkomst till lokala appar. Mer information finns i avsnittet ”externa anslutningar” i [klientåtkomstlicenser och Hanteringslicenser](https://www.microsoft.com/en-us/licensing/product-licensing/client-access-license.aspx). Kontakta din Microsoft-representant eller din lokala återförsäljare angående dina specifika behov för licensiering.

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybrid organisationer](active-directory-b2b-hybrid-organizations.md)

- En översikt av Azure AD Connect finns [integrera dina lokala kataloger med Azure Active Directory](connect/active-directory-aadconnect.md).

