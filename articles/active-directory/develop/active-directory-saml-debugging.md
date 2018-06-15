---
title: Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory | Microsoft Docs
description: 'Lär dig att felsöka SAML-baserade enkel inloggning till program i Azure Active Directory '
services: active-directory
author: CelesteDG
documentationcenter: na
manager: mtillman
ms.assetid: edbe492b-1050-4fca-a48a-d1fa97d47815
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/20/2017
ms.author: celested
ms.custom: aaddev
ms.reviewer: dastrock; smalser
ms.openlocfilehash: 1a33b5ab9e26ed497e3be2d430f66ef41402733d
ms.sourcegitcommit: e14229bb94d61172046335972cfb1a708c8a97a5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/14/2018
ms.locfileid: "34157902"
---
# <a name="how-to-debug-saml-based-single-sign-on-to-applications-in-azure-active-directory"></a>Felsöka SAML-baserade enkel inloggning till program i Azure Active Directory

När du felsöker en SAML-baserade programintegration, är det ofta praktiskt att använda ett verktyg som [Fiddler](http://www.telerik.com/fiddler) SAML-begäran och SAML-svar som innehåller en SAML-token som har utfärdats till ett program. 

![Fiddler][1]

Vanliga är problem relaterade till en felaktig konfiguration på Azure Active Directory eller på klientsidan för programmet. Beroende på om du ser felet, som du behöver granska SAML-begäran eller svar:

- Ett felmeddelande visas i mitt företag inloggningssidan [länkar till avsnitt]
- Ett fel visas på sidan i programmet när du har registrerat i [länkar till avsnitt]

## <a name="i-see-an-error-in-my-company-sign-in-page"></a>Det finns ett fel i mitt företag inloggningssidan.

Du hittar en mappning mellan felkoden och Lösningssteg i [inloggningsproblem till ett gallery-program som konfigurerats för federerad enkel inloggning](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML).

Om du ser ett fel i ditt företag inloggningssidan måste felmeddelande och SAML-begäran att felsöka problemet.

### <a name="how-can-i-get-the-error--message"></a>Hur kan jag få ett felmeddelande?

Titta på det nedre högra hörnet på sidan för att få ett felmeddelande. Du ser ett fel som innehåller:

- En CorrelationID
- En tidsstämpel
- Ett meddelande

![Fel][2] 

 
Korrelations-Id och tidsstämpeln utgör en unik identifierare som du kan använda för att hitta backend-loggarna som är associerade med Misslyckad inloggning. Dessa värden är viktigt när du skapar ett supportärende hos Microsoft, eftersom de hjälper tekniker att tillhandahålla en snabbare lösning på problemet.

Meddelandet är orsaken till problemet inloggning. 


### <a name="how-can-i-review-the-saml-request"></a>Hur kan jag granska SAML-begäran?

SAML-begäran som skickades av programmet till Azure Active Directory är vanligtvis senaste 200 HTTP-svaret från [ https://login.microsoftonline.com ](https://login.microsoftonline.com).
 
Med Fiddler kan du visa SAML-begäran genom att markera den här raden och sedan välja den **kontrollanter > WebForms** fliken i den högra panelen. Högerklicka på den **SAMLRequest** värdet och välj sedan **skicka till TextWizard**. Välj sedan **från Base64** från den **transformera** menyn för att avkoda token och se innehållet. 

Du kan dessutom också kopiera värdet i SAMLrequest och använda en annan Base64 avkodarens.

    <samlp:AuthnRequest
    xmlns="urn:oasis:names:tc:SAML:2.0:metadata"
    ID="id6c1c178c166d486687be4aaf5e482730"
    Version="2.0" IssueInstant="2013-03-18T03:28:54.1839884Z"
    Destination=https://login.microsoftonline.com/<Tenant-ID>/saml2
    AssertionConsumerServiceURL="https://contoso.applicationname.com/acs"
    xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol">
    <Issuer xmlns="urn:oasis:names:tc:SAML:2.0:assertion">https://www.contoso.com</Issuer>
    </samlp:AuthnRequest>

Med SAML-begäran avkodas, kontrollerar du följande:

1. **Mål** motsvarar SAML enkel inloggning tjänstens Webbadress från Azure Active Directory i SAML-begäran.
 
2. **Utfärdaren** i SAML-begäran som är samma **identifierare** du har konfigurerat för programmet i Azure Active Directory. Azure AD används utfärdaren för att hitta ett program i din katalog.

3. **AssertionConsumerServiceURL** är där programmet förväntas ta emot SAML-token från Azure Active Directory. Du kan konfigurera det här värdet i Azure Active Directory men det är inte obligatoriskt om det är en del av SAML-begäran.


## <a name="i-see-an-error-on-the-applications-page-after-signing-in"></a>Ett fel visas på sidan i programmet när du loggar in

I det här scenariot programmet inte kan ta emot svar som utfärdats av Azure AD. Det är viktigt att du kontrollerar svaret från Azure AD som innehåller SAML-token med leverantören av programmet du känna till saknas eller är fel. 

### <a name="how-can-i-get-and-review-the-saml-response"></a>Hur kan hämta och granska SAML-svaret?

Svaret från Azure AD som innehåller SAML-token är vanligtvis en som inträffar efter ett HTTP 302-omdirigering från https://login.microsoftonline.com och skickas till den konfigurerade **Reply URL** av programmet. 

Du kan visa SAML-token genom att välja den här raden och sedan välja den **kontrollanter > WebForms** fliken i den högra panelen. Därifrån kan du högerklicka på den **SAMLResponse** värdet och välj **skicka till TextWizard**. Välj sedan **från Base64** från den **transformera** menyn för att avkoda token och se innehållet som använder en annan Base64 avkodarens. 

Du kan också kopiera värdet i den **SAMLrequest** och använda en annan Base64 avkodarens.

Besök [fel på sidan för programmet när du har registrerat](https://docs.microsoft.com/azure/active-directory/application-sign-in-problem-federated-sso-gallery?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML) felsökningsanvisningar mer information om vad kanske saknas eller fel i SAML-svaret.

Mer information om hur du granskar SAML svar finns i artikeln [enkel inloggning SAML protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference?/?WT.mc_id=DOC_AAD_How_to_Debug_SAML#response).


## <a name="related-articles"></a>Relaterade artiklar
* [Artikelindex för programhantering i Azure Active Directory](../active-directory-apps-index.md)
* [Konfigurera enkel inloggning för program som inte ingår i Azure Active Directory-programgalleriet](../application-config-sso-how-to-configure-federated-sso-non-gallery.md)
* [Anpassa anspråk som utfärdats i SAML-Token för förintegrerade appar](active-directory-saml-claims-customization.md)

<!--Image references-->
[1]: ../media/active-directory-saml-debugging/fiddler.png
[2]: ../media/active-directory-saml-debugging/error.png
