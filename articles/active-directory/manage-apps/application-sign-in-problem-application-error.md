---
title: Fel på sidan för ett program när du har registrerat | Microsoft Docs
description: Hur du löser problem med Azure AD-inloggningen när programmet genererar ett fel
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 438f4f60c80cb798c3c40f57e22c9194a9fd5f86
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/19/2019
ms.locfileid: "58110859"
---
# <a name="error-on-an-applications-page-after-signing-in"></a>Fel på sidan för ett program efter inloggning

Azure AD har signerats av användaren i i det här scenariot, men programmet visar ett fel som inte tillåter att användaren har avsluta flödet för inloggning. I det här scenariot medger programmet inte svar problemet av Azure AD.

Det finns några möjliga orsaker till varför programmet inte godkände svar från Azure AD. Om felet i programmet inte är tillräckligt tydliga veta vad som saknas i svaret, sedan:

-   Om programmet är Azure AD-Galleriprogram, kontrollerar du att du har följt alla steg i artikeln [Felsök SAML-baserad enkel inloggning till program i Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Använd ett verktyg som [Fiddler](https://www.telerik.com/fiddler) att avbilda SAML-begäran, SAML-svar och SAML-token.

-   Dela SAML-svar med programleverantören för att vet vad som saknas.

## <a name="missing-attributes-in-the-saml-response"></a>Saknade attribut i SAML-svar

Följ dessa steg för att lägga till ett attribut i Azure AD-konfiguration som ska skickas i Azure AD-svaret:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Klicka på **visa och redigera alla andra användarattribut under** den **användarattribut** avsnittet om du vill redigera attribut som ska skickas till programmet i SAML-token när användare loggar in.

   Lägga till ett attribut:

   * Klicka på **Lägg till attribut**. Ange den **namn** och klicka sedan på **värdet** i listrutan.

   * Klicka på **spara.** Det nya attributet visas i tabellen.

9. Spara konfigurationen.

Nästa gång användaren loggar in till programmet, Azure AD skickar det nya attributet i SAML-svar.

## <a name="the-application-expects-a-different-user-identifier-value-or-format"></a>Programmet förväntar sig ett annat användar-ID-värde eller format

Inloggning till programmet misslyckas eftersom SAML-svar saknar attribut, till exempel roller eller eftersom programmet förväntar sig ett annat format för attributet EntityID.

## <a name="add-an-attribute-in-the-azure-ad-application-configuration"></a>Lägg till ett attribut i konfigurationen av Azure AD-program:

Följ dessa steg om du vill ändra värdet för användar-ID:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Under den **användarattribut**, Välj den unika identifieraren för dina användare i den **användaridentifierare** listrutan.

## <a name="change-entityid-user-identifier-format"></a>Ändra EntityID (användaridentifierare)-format

Om programmet förväntar sig ett annat format för attributet EntityID. Sedan kan du inte väljer formatet EntityID (UID) som Azure AD skickar till programmet i svaret efter autentisering av användare.

Azure AD väljer du formatet för NameID-attributet (användaridentifierare) baserat på värdet valt eller formatet som begärs av programmet i SAML-AuthRequest. Mer information finns i artikeln [enkel inloggning SAML-protokoll](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference#authnrequest) under avsnittet NameIDPolicy.

## <a name="the-application-expects-a-different-signature-method-for-the-saml-response"></a>Programmet förväntar sig en annan signatur-metod för SAML-svar

Du vill ändra vilka delar av SAML-token signeras digitalt av Azure Active Directory. Följ de här stegen:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Klicka på **visa avancerade inställningar för signering av certifikat** under den **SAML-signeringscertifikat** avsnittet.

9. Välj lämplig **signering alternativet** förväntas av programmet:

   * Signera SAML-svar

   * Signera SAML-svar och försäkran

   * Signera SAML-försäkran

Nästa gång användaren loggar in till programmet, Azure AD logga en del av SAML-svar som valts.

## <a name="the-application-expects-the-signing-algorithm-to-be-sha-1"></a>För program som förväntar Signeringsalgoritm är SHA-1

Som standard signerar Azure AD med hjälp av algoritmen för de flesta security SAML-token. Du bör inte ändra logga algoritmen till SHA-1 såvida inte programmet.

Följ dessa steg om du vill ändra signeringsalgoritmen som:

1. Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2. Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3. Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4. Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5. Klicka på **alla program** att visa en lista över alla dina program.

   * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6. Välj det program som du vill konfigurera enkel inloggning.

7. När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8. Klicka på **visa avancerade inställningar för signering av certifikat** under den **SAML-signeringscertifikat** avsnittet.

9. Välj SHA-1, i den **Signeringsalgoritm**.

Nästa gång användaren loggar in till programmet, Azure AD logga SAML-token med hjälp av algoritmen SHA-1.

## <a name="next-steps"></a>Nästa steg
[Så här felsöker du SAML-baserad enkel inloggning till program i Azure Active Directory](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging)
