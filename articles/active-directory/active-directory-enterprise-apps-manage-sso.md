---
title: Enkel inloggning hantering för företagsappar i Azure Active Directory | Microsoft Docs
description: Hantera enkel inloggning-inställningar för företagsappar inom organisationen från Azure Active Directory-programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: baf437294dbbca7f63f9d4cdc80ac1cb33a67e42
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/11/2018
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Hantera enkel inloggning för företagsappar i

Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att hantera enkel inloggning-inställningar för företagsprogram. Enterprise-appar är appar som har distribuerats och används i din organisation. Den här artikeln gäller särskilt för appar som har lagts till från den [Azure Active Directory-programgalleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen
Alla program som har ställts in för enkel inloggning kan visas och hanteras i Azure-portalen. Programmen kan hittas i den **alla tjänster** &gt; **företagsprogram** avsnitt i portalen. 

![Företagsprogram bladet](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade.png)

Välj **alla program** att visa en lista över alla appar som har konfigurerats. Om du väljer en app visas resurser för appen, där rapporter kan visas för appen och en rad olika inställningar som kan hanteras.

För att hantera inställningar för enkel inloggning, Välj **enkel inloggning**.

![Programmet resursbladet](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Lägen för enkel inloggning
**Enkel inloggning** börjar med en **läge** menyn där enkel inloggning läget konfigureras. Tillgängliga alternativ inkluderar:

* **SAML-baserade inloggning** -det här alternativet är tillgängligt om programmet stöder fullständig federerad enkel inloggning med Azure Active Directory med SAML 2.0-protokollet WS-Federation, eller OpenID connect protokoll.
* **Lösenordsbaserade inloggning** -det här alternativet är tillgängligt om Azure AD stöder ifyllning av lösenordet för det här programmet.
* **Länkad inloggning** -kallades ”befintliga enkel inloggning”, det här alternativet gör att administratörer kan placera en länk till det här programmet i startprogrammet för sina användare Azure AD-åtkomstpanelen eller Office 365.

Mer information om dessa lägen finns [hur fungerar enkel inloggning med Azure Active Directory fungerar](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-baserade inloggning
Den **SAML-baserade inloggning** alternativet är indelat i fyra avsnitt:

### <a name="domains-and-urls"></a>Domäner och webbadresser
Detta är där all information om programmets URL: er och domänen har lagts till i Azure AD-katalogen. Alla indata som krävs för att göra arbetet för enkel inloggning app visas direkt på skärmen, medan alla valfria indata kan visas genom att välja den **visa avancerade inställningar för URL: en** kryssrutan. En fullständig lista över stöds indata innehåller:

* **URL: en inloggning** – där användaren går för att logga in på programmet. Om programmet har konfigurerats för att utföra service provider initieras enkel inloggning, när en användare öppnar den här URL: en omdirigerar tjänstleverantör till Azure AD för att autentisera och logga in användaren. 
  * Om det här fältet fylls använder Azure AD URL: en för att starta programmet från Office 365 och Azure AD-åtkomstpanelen.
  * Om det här fältet utelämnas sedan Azure AD i stället utför identity-provider-initierad inloggning när appen startas från Office 365, Azure AD-åtkomstpanelen eller från Azure AD enkel inloggning URL.
* **Identifierare** -den här URI: N ska identifiera programmet där enkel inloggning som ska konfigureras. Detta är det värde som Azure AD skickar tillbaka till programmet som parametern målgruppen för SAML-token och programmet förväntas att verifiera den. Det här värdet visas också som enhets-ID i eventuella SAML-metadata som tillhandahålls av programmet.
* **Reply URL** -reply-URL är där programmet förväntas ta emot SAML-token. Detta kallas även URL: en för Assertion konsumenten Service (ACS). Klicka på Nästa om du vill fortsätta till nästa sida när de har angetts. Den här skärmen innehåller information om vad som måste konfigureras på program på klientsidan att godkänna en SAML-token från Azure AD.
* **Relay tillstånd** -relay-tillståndet är en valfri parameter som kan hjälpa dig att ange programmet var användaren ska omdirigeras när autentiseringen är slutförd. Värdet är oftast en giltig URL i programmet, men vissa program använder det här fältet på olika sätt (se appens enkel inloggning Info i dokumentation för). Möjlighet att ställa in tillståndet relay är en ny funktion som är unik för den nya Azure-portalen.

### <a name="user-attributes"></a>Användarattribut
Detta är där administratörer kan visa och redigera attribut som ska skickas i SAML-token som Azure AD utfärdar till programmet varje gång användaren loggar in.

Endast redigerbara attributet som stöds är de **användar-ID** attribut. Värdet för det här attributet är fältet i Azure AD som unikt identifierar varje användare i programmet. Till exempel om appen har distribuerats med ”e-postadress” som unik identifierare och användarnamn, ska sedan värdet anges till fältet ”user.mail” i Azure AD.

### <a name="saml-signing-certificate"></a>SAML-signeringscertifikat
Det här avsnittet visar information om det certifikat som används i Azure AD för att signera SAML-token som utfärdas till programmet varje gång användaren autentiseras. Det gör att egenskaperna för det aktuella certifikatet kontrolleras, inklusive upphör att gälla.

### <a name="application-configuration"></a>Konfiguration av program
Det sista avsnittet innehåller dokumentationen och/eller kontroller som krävs för att konfigurera programmet att använda Azure Active Directory som en identitetsleverantör.

Den **konfigurera programmet** meny innehåller nya kortfattat och inbäddade instruktioner för att konfigurera programmet. Det här är en annan ny funktion som är unik för den nya Azure-portalen.

> [!NOTE]
> En komplett exempel på inbäddade dokumentation finns i Salesforce.com-programmet. Dokumentation för fler appar läggs kontinuerligt.
> 
> 

![Inbäddade dokument](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Lösenordsbaserade inloggning
Om stöds för programmet att välja lösenordsbaserade SSO-läge och välja **spara** omedelbart konfigureras för att göra lösenordsbaserade enkel inloggning. Mer information om hur du distribuerar lösenordsbaserade SSO finns [hur fungerar enkel inloggning med Azure Active Directory fungerar](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Lösenordsbaserade inloggning](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Länkade inloggning
Om stöds för programmet kan att välja det länkade SSO-läget du ange den URL som du vill att Azure AD-åtkomstpanelen eller Office 365 ska användas när användarna klickar du på den här appen. Läs mer om länkade SSO (kallades tidigare ”befintliga SSO”), [hur fungerar enkel inloggning med Azure Active Directory fungerar](active-directory-appssoaccess-whatis.md#how-does-single-sign-on-with-azure-active-directory-work).

![Länkade inloggning](./media/active-directory-enterprise-apps-manage-sso/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Feedback

Vi hoppas att du vill med hjälp av den förbättrade Azure AD-upplevelse. Skriv ned feedback kommer! Publicera din feedback och förslag på förbättringar i den **administrationsportalen** avsnitt i vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Vi är glada om hur du skapar nya nya produkter varje dag, och använder din information i form och definiera vad vi bygga härnäst.

