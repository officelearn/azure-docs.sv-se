---
title: Grant B2B-användare i Azure AD åtkomst till dina lokala program | Microsoft Docs
description: Visar hur du ge molnet B2B-användare åtkomst till lokala appar med Azure AD B2B-samarbete.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 10/10/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: sasubram
ms.openlocfilehash: ef70b9e58a669695f3bdec7ad8ea4bab93e7e4b9
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/26/2019
ms.locfileid: "55082052"
---
# <a name="grant-b2b-users-in-azure-ad-access-to-your-on-premises-applications"></a>Grant B2B-användare i Azure AD åtkomst till dina lokala program

Som en organisation som använder Azure Active Directory (Azure AD) B2B-samarbetsfunktioner för att bjuda in gästanvändare från partnerorganisationer till din Azure AD nu kan du ange dessa B2B-användare åtkomst till lokala appar. De här lokala apparna kan använda SAML-baserad autentisering eller integrerad Windows-autentisering (IWA) med Kerberos-begränsad delegering (KCD).

## <a name="access-to-saml-apps"></a>Åtkomst till SAML-appar

Om din lokala app använder SAML-baserad autentisering, kan du enkelt göra apparna tillgängliga till dina användare med Azure AD B2B-samarbete via Azure portal.

Du måste göra följande:

- Integrera SAML-app med hjälp av mallen för icke-galleriprogram, enligt beskrivningen i [Konfigurera enkel inloggning till program som inte ingår i Azure Active Directory-programgalleriet](../manage-apps/configure-single-sign-on-non-gallery-applications.md). Se till att Observera det du använder för den **inloggnings-URL** värde.
-  Använda Azure AD-programproxy för att publicera appen lokalt med **Azure Active Directory** konfigurerad som autentiseringskälla. Anvisningar finns i [publicera program med Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md). 

   När du konfigurerar den **interna URL: en** Använd inloggnings-URL: en som du angav i mallen icke-galleriprogram. På så sätt kan komma användarna åt programmet från utanför gränsen för organisationen. Programproxy utför den SAML enkel inloggning för appen lokalt.
 
   ![Visar den lokala appen inställningar interna URL: en och autentisering](media/hybrid-cloud-to-on-premises/OnPremAppSettings.PNG)

## <a name="access-to-iwa-and-kcd-apps"></a>Åtkomst till IWA och KCD appar

Om du vill ge B2B-användare åtkomst till lokala program som skyddas med integrerad Windows-autentisering och Kerberos-begränsad delegering, måste följande komponenter:

- **Autentisering via Azure AD-programproxyn**. B2B-användare måste kunna autentisera till dina lokala program. Om du vill göra detta måste du publicera lokala appen via Azure AD-programproxyn. Mer information finns i [Kom igång med Application Proxy och installera connector](../manage-apps/application-proxy-enable.md) och [publicera program med Azure AD Application Proxy](../manage-apps/application-proxy-publish-azure-portal.md).
- **Auktorisering via en B2B-användarobjektet i den lokala katalogen**. Programmet måste kunna utföra åtkomstkontroller för användaren och ge åtkomst till rätt resurser. IWA och KCD kräver ett användarobjekt i den lokala Windows Server Active Directory att slutföra auktoriseringen. Mer information finns i [hur enkel inloggning med KCD fungerar](../manage-apps/application-proxy-configure-single-sign-on-with-kcd.md#how-single-sign-on-with-kcd-works), Application Proxy måste det här objektet att personifiera användaren och få en Kerberos-token till appen. 

   Det finns två metoder som kan användas för att skapa gäst-användarobjekt som krävs för auktorisering i den lokala katalogen för Användarscenario B2B:

   - Microsoft Identity Manager (MIM) och [MIM-hanteringsagenten för Microsoft Graph](#create-b2b-guest-user-objects-through-mim-preview). 
   - [Ett PowerShell-skript](#create-b2b-guest-user-objects-through-a-script-preview). Med hjälp av skript är en enklare lösning som inte kräver MIM. 

I följande diagram ger en översikt över hur Azure AD-programproxy och generering av B2B-användarobjektet i lokalt katalogen arbete tillsammans för att bevilja B2B-användare åtkomst till dina lokala IWA och KCD-appar. Stegen beskrivs i detalj nedan diagrammet.

![Diagram över MIM och B2B-skript-lösningar](media/hybrid-cloud-to-on-premises/MIMScriptSolution.PNG)

1.  En användare från en partnerorganisation (Fabrikam-klient) bjuds in till Contoso-klienten.
2.  Ett gäst-användarobjekt skapas i Contoso-klienten (till exempel ett användarobjekt med ett UPN för guest_fabrikam.com#EXT#@contoso.onmicrosoft.com).
3.  Fabrikam-gästen importeras från Contoso via MIM eller B2B PowerShell-skriptet.
4.  En representation eller ”avtryck” Fabrikam gäst användarobjektet (gäst EXT #) skapas i den lokala katalogen för Contoso.com, via MIM eller B2B PowerShell-skriptet.
5.  Gästen användaren åtkomst till lokalt program, app.contoso.com.
6.  Autentiseringsbegäran är auktoriserad via programproxy med hjälp av Kerberos-begränsad delegering. 
7.  Eftersom gäst-användarobjektet finns lokalt, har i autentiseringen lyckats.

### <a name="lifecycle-management-policies"></a>Principer för hantering av livscykeln

Du kan hantera de lokala B2B användarobjekt via principer för hantering av livscykeln. Exempel:

- Du kan konfigurera principer för multifaktorautentisering (MFA) för gästanvändaren så att MFA används under Application Proxy-autentisering. Mer information finns i [villkorlig åtkomst för användare i B2B-samarbetet](conditional-access.md).
- Alla sponsring, åtkomstgranskningar, konto kontroller och annat som utförs på molnet B2B användaren gäller för lokala användare. Till exempel om molnet användaren tas bort via principer för hantering av livscykeln för raderas lokal användare också av MIM Sync eller Azure AD Connect sync. Mer information finns i [hantera gäståtkomst med Azure AD åtkomst går igenom](../governance/manage-guest-access-with-access-reviews.md).

### <a name="create-b2b-guest-user-objects-through-mim"></a>Skapa användarobjekt för B2B-gäst via MIM

Information om hur du använder MIM 2016 Service Pack 1 och MIM-hanteringsagenten för Microsoft Graph för att skapa användarobjekt gäst i din lokala katalog finns i [Azure AD-business-to-business (B2B) samarbete med Microsoft Identity Manager (MIM) 2016 SP1 med Azure Application Proxy](https://docs.microsoft.com/microsoft-identity-manager/microsoft-identity-manager-2016-graph-b2b-scenario).

### <a name="create-b2b-guest-user-objects-through-a-script-preview"></a>Skapa användarobjekt för B2B-gäst via ett skript (förhandsversion)

Det finns ett PowerShell-exempelskript som att du kan användas som utgångspunkt för att skapa användarobjekt gäst i din lokala Active Directory.

Du kan ladda ned skriptet och Readme-filen från den [Download Center](https://www.microsoft.com/download/details.aspx?id=51495). Välj den **skriptet och viktigt för att hämta Azure AD B2B-användare på-prem.zip** fil.

Innan du använder skriptet, se till att du läser igenom de krav och viktiga överväganden i den tillhörande Readme-filen. Förstå dessutom att skriptet är tillgängliga enbart som ett exempel. Utvecklingsteamet eller en partner måste anpassa och granska skriptet innan du kör den.

## <a name="license-considerations"></a>Överväganden för licens

Se till att du har rätt klientåtkomstlicenser (CAL) för externa gästanvändare som har åtkomst till lokala appar. Mer information finns i avsnittet ”externa anslutningar” i [klientåtkomstlicenser och Hanteringslicenser](https://www.microsoft.com/licensing/product-licensing/client-access-license.aspx). Kontakta din Microsoft-representant eller din lokala återförsäljare angående dina behov för licensiering.

## <a name="next-steps"></a>Nästa steg

- [Azure Active Directory B2B-samarbete för hybridorganisationer](hybrid-organizations.md)

- En översikt av Azure AD Connect finns i [integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

