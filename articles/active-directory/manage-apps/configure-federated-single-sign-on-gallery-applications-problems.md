---
title: Problem med att konfigurera federerad enkel inloggning för ett Azure AD-galleriprogram | Microsoft Docs
description: Åtgärda några av de vanliga problem som kan uppstå när du konfigurerar federerad enkel inloggning med hjälp av SAML för program som listas i Azure AD-Programgalleriet
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: celested
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31e9746c0739a2ddd6267428f428e977151077b6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58099788"
---
# <a name="problem-configuring-federated-single-sign-on-for-an-azure-ad-gallery-application"></a>Problem med att konfigurera federerad enkel inloggning för ett Azure AD-galleriprogram

Om det uppstår ett problem när du konfigurerar ett program. Kontrollera att du har följt alla steg i självstudien för programmet. I programmets konfiguration har du infogad dokumentation om hur du konfigurerar programmet. Du kan också komma åt den [lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saas-tutorial-list/) för en stegvis vägledning i detalj.

## <a name="cant-add-another-instance-of-the-application"></a>Det går inte att lägga till en annan instans av programmet

Om du vill lägga till en andra instans av ett program, måste du kunna:

-   Konfigurera en unik identifierare för den andra instansen. Du kan inte konfigurera samma identifieraren som används för den första instansen.

-   Konfigurera ett annat certifikat än den som används för den första instansen.

Om programmet inte stöder någon av ovanstående. Sedan kan du inte konfigurera en andra instans.

## <a name="cant-add-the-identifier-or-the-reply-url"></a>Det går inte att lägga till det ID: T eller svars-URL

Om du inte kunna konfigurera identifierare eller svars-URL kan du bekräfta identifierare och svars-URL-värdena matchar de mönster som redan har konfigurerats för programmet.

Du behöver veta de mönster som redan har konfigurerats för programmet:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.** Gå till steg 7. Om du redan är i programbladet på Azure AD.

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Välj **SAML-baserad inloggning** från den **läge** listrutan.

9. Gå till den **identifierare** eller **svars-URL** textrutan under den **domän och URL: er.**

10. Det finns tre sätt att veta stöds mönster för programmet:

    * I textrutan, visas stöds pattern(s) som platshållare *exempel:* <https://contoso.com>.

    * Om mönstret inte stöds, visas ett rött utropstecken vid försök att ange värdet i textrutan. Om du håller muspekaren över rött utropstecken, ser du mönster som stöds.

    * I självstudien för programmet, kan du också få information om mönster som stöds. Under den **konfigurera Azure AD enkel inloggning** avsnittet. Gå till steg för konfigurerade värdena under den **domän och URL: er** avsnittet.

Om värdena inte matchar de mönster som redan har konfigurerats i Azure AD. Du kan:

-   Arbeta med programleverantören för att hämta värden som matchar de mönster som redan har konfigurerats i Azure AD

-   Eller kontakta Azure AD-teamet på <aadapprequest@microsoft.com> eller lämna en kommentar i självstudiekursen och begär uppdatering av mönster som stöds för programmet

## <a name="where-do-i-set-the-entityid-user-identifier-format"></a>Där anger formatet EntityID (användaridentifierare)

Du kommer inte att kunna välja formatet EntityID (UID) som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD väljer du formatet för NameID-attributet (användaridentifierare) baserat på värdet valt eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy,

## <a name="cant-find-the-azure-ad-metadata-to-complete-the-configuration-with-the-application"></a>Det går inte att hitta Azure AD-metadata för att slutföra konfigurationen med programmet

Följ dessa steg för att ladda ned programmetadata eller certifikat från Azure AD:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du har konfigurerat för enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Gå till **SAML-signeringscertifikat** och sedan klicka på **hämta** kolumnvärde. Beroende på vilka programmet kräver att du konfigurerar enkel inloggning, se du antingen välja att hämta Metadata XML eller certifikatet.

Azure AD tillhandahåller inte en URL för att hämta metadata. Metadata kan endast hämtas som en XML-fil.

## <a name="dont-know-how-to-customize-saml-claims-sent-to-an-application"></a>Vet inte hur du anpassar SAML-anspråk som skickas till ett program

Läs hur du anpassar SAML attributet anspråk som skickas till programmet i [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Hantera program med Azure Active Directory](what-is-application-management.md)
