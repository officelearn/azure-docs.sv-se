---
title: Ge B2B-användare åtkomst till dina lokala appar – Azure AD
description: Visar hur du ger moln-B2B-användare åtkomst till lokala appar med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.collection: M365-identity-device-management
ms.openlocfilehash: 098f464b6af5f10866403e1cd1549d571d883ac1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74272799"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Bevilja B2B-användare i Azure AD-åtkomst till dina lokala program

Som en organisation som använder Azure Active Directory (Azure AD) B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD kan du nu ge dessa B2B-användare åtkomst till lokala appar. Dessa lokala appar kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

## <a name="access-to-saml-apps"></a>Tillgång till SAML-appar

Om din lokala app använder SAML-baserad autentisering kan du enkelt göra dessa appar tillgängliga för dina Azure AD B2B-samarbetsanvändare via Azure-portalen.

Du måste göra båda följande:

- Integrera SAML-appen med hjälp av programmallen som inte är galleri, enligt beskrivningen i [Konfigurera enkel inloggning till program som inte finns i Azure Active Directory-programgalleriet](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Se till att notera vad du använder för **url-värdet för inloggning.**
-  Använd Azure AD Application Proxy för att publicera den lokala appen, med **Azure Active Directory** konfigurerat som autentiseringskälla. Instruktioner finns i [Publicera program med Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md). 

   När du konfigurerar inställningen **Intern url** använder du inloggnings-URL:en som du angav i programmallen för program som inte är galleri. På så sätt kan användare komma åt appen utanför organisationsgränsen. Application Proxy utför SAML-inloggningen för den lokala appen.
 
   ![Visar lokala appinställningar intern URL och autentisering](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Tillgång till IWA- och KCD-appar

Om du vill ge B2B-användare åtkomst till lokala program som är skyddade med integrerad Windows-autentisering och Kerberos-begränsad delegering behöver du följande komponenter:

- **Autentisering via Azure AD Application Proxy**. B2B-användare måste kunna autentisera till den lokala applikationen. För att göra detta måste du publicera den lokala appen via Azure AD Application Proxy. Mer information finns i [Komma igång med programproxy och installera anslutnings-](../manage-apps/application-proxy-enable.md) och [publiceringsprogrammen med Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md).
- **Auktorisering via ett B2B-användarobjekt i den lokala katalogen**. Programmet måste kunna utföra kontroller för användaråtkomst och bevilja åtkomst till rätt resurser. IWA och KCD kräver att ett användarobjekt i den lokala Active Directory i Windows Server slutför den här auktoriseringen. Som beskrivs i [Hur enkel inloggning med KCD fungerar](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works)behöver Application Proxy det här användarobjektet för att personifiera användaren och få en Kerberos-token till appen. 

   För användarscenariot B2B finns det två metoder som du kan använda för att skapa gästanvändarobjekt som krävs för auktorisering i den lokala katalogen:

   - Microsoft Identity Manager (MIM) och MIM-hanteringsagenten för Microsoft Graph. 
   - [Ett PowerShell-skript](#create-b2b-guest-user-objects-through-a-script-preview). Att använda skriptet är en mer lätt lösning som inte kräver MIM. 

Följande diagram ger en översikt på hög nivå över hur Azure AD Application Proxy och genereringen av B2B-användarobjektet i den lokala katalogen arbetar tillsammans för att ge B2B-användare åtkomst till dina lokala IWA- och KCD-appar. De numrerade stegen beskrivs i detalj under diagrammet.

![Diagram över MIM- och B2B-skriptlösningar](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  En användare från en partnerorganisation (Fabrikam-klienten) är inbjuden till Contoso-klienten.
2.  Ett gästanvändarobjekt skapas i Contoso-klienten (till exempel ett användarobjekt med ett UPN@contoso.onmicrosoft.compå guest_fabrikam.com#EXT# ).
3.  Fabrikam-gästen importeras från Contoso via MIM eller via B2B PowerShell-skriptet.
4.  En representation eller "fotavtryck" av Fabrikam gästanvändarobjektet (Guest#EXT#) skapas i den lokala katalogen, Contoso.com, via MIM eller via B2B PowerShell-skriptet.
5.  Gästanvändaren kommer åt det lokala programmet, app.contoso.com.
6.  Autentiseringsbegäran auktoriseras via Programproxy med Kerberos begränsad delegering. 
7.  Eftersom gästanvändarobjektet finns lokalt lyckas autentiseringen.

### <a name="lifecycle-management-policies"></a>Principer för livscykelhantering

Du kan hantera lokala B2B-användarobjekt via livscykelhanteringsprinciper. Ett exempel:

- Du kan ställa in MFA-principer (Multi Factor Authentication) för gästanvändaren så att MFA används under programproxyautentisering. Mer information finns i [Villkorlig åtkomst för B2B-samarbetsanvändare](conditional-access.md).
- Alla sponsring, åtkomst recensioner, kontoverifieringar, etc. som utförs på molnet B2B användare gäller för lokala användare. Om molnanvändaren till exempel tas bort via dina livscykelhanteringsprinciper tas den lokala användaren också bort av MIM Sync eller via Azure AD Connect-synkronisering. Mer information finns i [Hantera gäståtkomst med Azure AD-åtkomstgranskningar](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Skapa B2B-gästanvändarobjekt via MIM

Information om hur du använder MIM 2016 Service Pack 1 och MIM-hanteringsagenten för Microsoft Graph för att skapa gästanvändarobjekten i den lokala katalogen finns i [Azure AD business-to-business (B2B) samarbete med Microsoft Identity Manager (MIM) 2016 SP1 med Azure Application Proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Skapa B2B-gästanvändarobjekt via ett skript (förhandsgranskning)

Det finns ett PowerShell-exempelskript som du kan använda som utgångspunkt för att skapa gästanvändarobjekten i den lokala Active Directory.

Du kan hämta skriptet och Readme-filen från [Download Center](https://www.microsoft.com/download/details.aspx?id=51495). Välj **skript och readme för att hämta Azure AD B2B-användare på-prem.zip-filen.**

Innan du använder skriptet ska du se till att du granskar förutsättningarna och viktiga överväganden i den associerade Readme-filen. Förstå också att skriptet endast görs tillgängligt som ett exempel. Utvecklingsteamet eller en partner måste anpassa och granska skriptet innan du kör det.

## <a name="license-considerations"></a>Licensöverväganden

Kontrollera att du har rätt klientåtkomstlicenser (CALs) för externa gästanvändare som har åtkomst till lokala appar. Mer information finns i avsnittet "Externa anslutningsappar" i [klientåtkomstlicenser och hanteringslicenser](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Kontakta din Microsoft-representant eller lokala återförsäljare om dina specifika licensbehov.

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybridorganisationer](hybrid-organizations.md)

- En översikt över Azure AD Connect finns i [Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

