---
title: Enkel inloggning för hantering för företagsappar i Azure Active Directory | Microsoft Docs
description: Hantera enkel inloggning-inställningar för enterprise-appar i din organisation från Azure Active Directory-programgalleriet
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/19/2017
ms.author: barbkess
ms.reviewer: asmalser
ms.openlocfilehash: 81b959a08f55f13fd0bcadce32b65ba64f9bb270
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39365499"
---
# <a name="managing-single-sign-on-for-enterprise-apps"></a>Hantera enkel inloggning för företagsappar

Den här artikeln beskriver hur du använder den [Azure-portalen](https://portal.azure.com) att hantera enkel inloggning-inställningar för företagsprogram. Enterprise-appar är appar som distribueras och används i din organisation. Den här artikeln gäller särskilt för appar som har lagts till från den [Azure Active Directory-programgalleriet](what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery). 

## <a name="finding-your-apps-in-the-portal"></a>Hitta dina appar i portalen
Alla företagsappar som har ställts in för enkel inloggning kan visas och hanteras i Azure-portalen. Programmen finns i den **alla tjänster** &gt; **företagsprogram** i portalen. 

![Bladet företagsprogram](./media/configure-single-sign-on-portal/enterprise-apps-blade.png)

Välj **alla program** att visa en lista över alla appar som har konfigurerats. Om du väljer en app visas resurser för appen, där rapporter kan visas för appen och en mängd olika inställningar kan hanteras.

För att hantera inställningar för enkel inloggning, Välj **enkel inloggning**.

![Programmet resursbladet](./media/configure-single-sign-on-portal/enterprise-apps-sso-blade.png)

## <a name="single-sign-on-modes"></a>Lägen för enkel inloggning
**Enkel inloggning** börjar med en **läge** menyn, där den enda inloggningsläge konfigureras. De tillgängliga alternativen är:

* **SAML-baserad inloggning** – det här alternativet är tillgängligt om programmet stöder fullständig federerad enkel inloggning med Azure Active Directory med hjälp av SAML 2.0-protokollet WS-Federation, eller OpenID connect-protokoll.
* **Lösenordsbaserad inloggning** – det här alternativet är tillgängligt om Azure AD stöder ifyllning av lösenord för det här programmet.
* **Länkad inloggning** -tidigare kallade ”befintliga enkel inloggning”, det här alternativet kan placera en länk till det här programmet i sin användarens startprogrammet för Azure AD-åtkomstpanelen eller Office 365-administratörer.

Läs mer om dessa lägen, [hur fungerar enkel inloggning med Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

## <a name="saml-based-sign-on"></a>SAML-baserad inloggning
Den **SAML-baserad inloggning** alternativet är indelad i fyra delar:

### <a name="domains-and-urls"></a>Domäner och webbadresser
Det här är där all information om programmets domän och URL: er läggs till i Azure AD-katalogen. Alla indata som krävs för att göra enkel inloggning work-appen visas direkt på skärmen, medan alla valfria indata kan ses genom att välja den **visa avancerade URL-inställningar** kryssrutan. En fullständig lista över stöds indata innehåller:

* **inloggnings-URL** – där användaren går för att logga in till programmet. Om programmet har konfigurerats för att utföra service-tjänstprovider-initierad enkel inloggning, när en användare öppnar den här URL: en, omdirigerar tjänstleverantör till Azure AD för att autentisera och logga in användaren. 
  * Om det här fältet fylls använder Azure AD URL: en för att starta programmet från Office 365 och Azure AD-åtkomstpanelen.
  * Om det här fältet utelämnas, sedan Azure AD i stället utför identity-tjänstprovider-initierad inloggning när appen startas från Office 365, Azure AD-åtkomstpanelen eller från Azure AD enkel inloggnings-URL.
* **Identifieraren** – den här URI: N ska identifiera programmet som enkel inloggning konfigureras. Detta är det värde som Azure AD skickar tillbaka till programmet som parametern målgrupp för SAML-token och programmet förväntas verifiera den. Det här värdet visas också som entitets-ID i alla SAML-metadata som tillhandahålls av programmet.
* **Svars-URL** -svars-URL är där programmet förväntas ta emot SAML-token. Detta kallas också Assertion konsument Service (ACS)-URL. När de har angetts, klickar du på Nästa för att fortsätta till nästa steg. Den här skärmen ger information om vad som behöver konfigureras på program på klientsidan så att den kan acceptera en SAML-token från Azure AD.
* **Vidarebefordransstatus** -tillståndet relay är en valfri parameter som kan hjälpa dig att berätta var du vill omdirigera användaren när autentiseringen är slutförd för programmet. Värdet är vanligtvis en giltig URL i programmet, men vissa program använder det här fältet på olika sätt (se appens enkel inloggning i dokumentationen för mer information). Möjligheten att ställa in tillståndet för relay är en ny funktion som är unik för den nya Azure-portalen.

### <a name="user-attributes"></a>Användarattribut
Det här är där administratörer kan visa och redigera attribut som ska skickas i SAML-token som Azure AD utfärdar till programmet varje gång användaren loggar in.

Attributet kan bara redigeras som stöds är de **användaridentifierare** attribut. Värdet för det här attributet är fältet i Azure AD som unikt identifierar varje användare i programmet. Till exempel om appen har distribuerats med ”e-postadress” som användarnamn och unik identifierare, ska sedan värdet anges till fältet ”user.mail” i Azure AD.

### <a name="saml-signing-certificate"></a>SAML-signeringscertifikat
Det här avsnittet visar information om det certifikat som Azure AD använder för att signera SAML-token som utfärdas till programmet varje gång användaren autentiseras. Det gör att egenskaperna för det aktuella certifikatet inspekteras, inklusive datum för upphörande.

### <a name="application-configuration"></a>Programkonfiguration
Det sista avsnittet innehåller dokumentationen och/eller kontroller som krävs för att konfigurera programmet att använda Azure Active Directory som en identitetsleverantör.

Den **konfigurera programmet** meny innehåller nya kortfattade, inbäddade instruktioner för konfiguration av programmet. Det här är en annan ny funktion som är unika för den nya Azure-portalen.

> [!NOTE]
> En komplett exempel på embedded-dokumentation finns i Salesforce.com-programmet. Dokumentation för ytterligare appar läggs hela tiden.
> 
> 

![Inbäddade docs](./media/configure-single-sign-on-portal/enterprise-apps-blade-embedded-docs.png)

## <a name="password-based-sign-on"></a>Lösenordsbaserad inloggning
Om stöds för programmet, välja lösenordsbaserad SSO-läge och välja **spara** direkt konfigureras för att göra lösenordsbaserad SSO. Läs mer om hur du distribuerar lösenordsbaserad SSO [hur fungerar enkel inloggning med Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Lösenordsbaserad inloggning](./media/configure-single-sign-on-portal/enterprise-apps-blade-password-sso.png)

## <a name="linked-sign-on"></a>Länkad inloggning
Om stöds för programmet, kan att välja det länkade SSO-läget du ange Webbadressen som du vill att Azure AD-åtkomstpanelen eller Office 365 som ska användas när användare klickar på den här appen. Läs mer om länkade SSO (som tidigare kallades ”befintliga SSO”), [hur fungerar enkel inloggning med Azure Active Directory](what-is-single-sign-on.md#how-does-single-sign-on-with-azure-active-directory-work).

![Länkad inloggning](./media/configure-single-sign-on-portal/enterprise-apps-blade-linked-sso.png)

## <a name="feedback"></a>Feedback

Vi hoppas att du t.ex. använder den förbättrade Azure AD-upplevelse. Håll din feedback kommer! Publicera din feedback och förslag för förbättring i den **Admin Portal** delen av vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Vi är taggade om att skapa coola nya grejer varje dag, och Använd din vägledning för att forma och definiera vad vi bygga härnäst.

