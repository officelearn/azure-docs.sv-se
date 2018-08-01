---
title: Problem som loggar in på en galleriprogram som konfigurerats för federerad enkel inloggning | Microsoft Docs
description: Riktlinjer för de specifika felen när du loggar in i ett program som du har konfigurerat för SAML-baserad federerad enkel inloggning med Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: b5380a361244257d02cb0db56cf87abc8d655bd3
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/31/2018
ms.locfileid: "39363604"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Problem som loggar in på en galleriprogram som konfigurerats för federerad enkel inloggning

Om du vill felsöka problemet måste du verifiera programkonfiguration i Azure AD som följer:

-   Du har följt alla konfigurationssteg för Azure AD-galleriprogram.

-   Identifierare och svars-URL som konfigurerats i AAD matchar de förväntade värdena i programmet

-   Du har tilldelat användare till programmet

## <a name="application-not-found-in-directory"></a>Program som inte finns i katalogen

*Fel AADSTS70001: Program med identifierare 'https://contoso.com' hittades inte i katalogen*.

**Möjlig orsak**

Utfärdaren attributet skickar från programmet till Azure AD i SAML-begäran matchar inte det identifierarvärde som konfigurerats i Azure AD-programmet.

**Lösning**

Se till att attributet utfärdaren i SAML-begäran som den matchar identifieraren värdet som konfigurerats i Azure AD:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **domän och URL: er** avsnittet. Kontrollera att värdet i textrutan identifierare som matchar värdet för ID-värde som visas i felet.

När du har uppdaterat ID-värde i Azure AD och den matchar värdet skickar av programmet i SAML-begäran, bör du kunna logga in till programmet.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Svarsadressen matchar inte svars-adresser som har konfigurerats för programmet.

*Fel AADSTS50011: Svarsadressenhttps://contoso.com' matchar inte svars-adresser som har konfigurerats för programmet*

**Möjlig orsak**

AssertionConsumerServiceURL värdet i SAML-begäran matchar inte den svars-URL-värde eller mönster som konfigurerats i Azure AD. AssertionConsumerServiceURL värdet i SAML-begäran är den URL som visas i felet.

**Lösning**

Kontrollera att värdet som AssertionConsumerServiceURL i SAML-begäran som den matchar svars-URL värdet som konfigurerats i Azure AD.

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Gå till **domän och URL: er** avsnittet. Kontrollera eller uppdatera värdet i textrutan svars-URL så att de matchar AssertionConsumerServiceURL värdet i SAML-begäran.  
    * Om du inte ser svars-URL-textrutan, väljer du den **visa avancerade URL-inställningar** kryssrutan.

När du har uppdaterat svars-URL-värdet i Azure AD och den matchar värdet skickar av programmet i SAML-begäran, bör du kunna logga in till programmet.

## <a name="user-not-assigned-a-role"></a>Användare som inte har tilldelats en roll

*Fel AADSTS50105: Den inloggade användaren 'brian@contoso.com' har inte tilldelats en roll för programmet*.

**Möjlig orsak**

Användaren har inte beviljats åtkomst till programmet i Azure AD.

**Lösning**

Följ stegen nedan om du vill tilldela en eller flera användare till ett program direkt:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill tilldela en användare i listan.

7.  När programmet har lästs in klickar du på **användare och grupper** från programmets vänstra navigeringsmenyn.

8.  Klicka på den **Lägg till** knappen ovanpå den **användare och grupper** listan för att öppna den **Lägg till tilldelning** fönstret.

9.  Klicka på den **användare och grupper** selector från den **Lägg till tilldelning** fönstret.

10. Ange den **fullständigt namn** eller **e-postadress** för den användare som du vill tilldela till den **Sök efter namn eller e-postadress** sökrutan.

11. Hovra över den **användaren** i listan för att visa en **kryssrutan**. Klicka på kryssrutan bredvid användarens profilfoto eller logotyp för att lägga till dina användare i den **valda** lista.

12. **Valfritt:** om du skulle vilja **lägga till flera användare**, typ i en annan **fullständigt namn** eller **e-postadress** till den **Sök efter namn eller e-postadress** sökrutan och klicka på kryssrutan för att lägga till den här användaren till den **valda** lista.

13. När du har valt användare klickar du på den **Välj** för att lägga till dem i listan över användare och grupper som ska tilldelas till programmet.

14. **Valfritt:** klickar du på den **Välj roll** väljare i den **Lägg till tilldelning** fönstret för att välja en roll att tilldela till användare som du har valt.

15. Klicka på den **tilldela** knappen för att tilldela programmet till de valda användarna.

De användare som du har valt att kunna starta dessa program med hjälp av metoderna som beskrivs i avsnittet lösning beskrivning efter en kort tidsperiod.

## <a name="not-a-valid-saml-request"></a>Inte en giltig SAML-begäran

*Fel AADSTS75005: Begäran är inte ett giltigt Saml2-protokollmeddelande.*

**Möjlig orsak**

Azure AD stöder inte SAML-begäran som skickades av programmet för enkel inloggning. Några vanliga problem är:

-   Saknar obligatoriska fält i SAML-begäran

-   SAML-begäranskodad metod

**Lösning**

1.  Avbilda SAML-begäran. Följ självstudien [Felsök SAML-baserad enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) och lär dig att samla in SAML-begäran.

2.  Kontakta programvaruleverantören och dela:

   -   SAML-begäran

   -   [Azure AD enkel inloggning SAML-protokollkrav](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

De bör verifiera de stöder Azure AD SAML-implementering för enkel inloggning.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Ingen resurs i requiredResourceAccess lista

*Fel AADSTS65005: klientprogrammet har begärt åtkomst till resursen ' 00000002-0000-0000-c000-000000000000'. Den här begäran misslyckades eftersom klienten inte har angett den här resursen requiredResourceAccess listan över*.

**Möjlig orsak**

Programobjektet är skadad.

**Lösning: alternativ 1**

Lös problemet genom att lägga till det unika ID-värdet i Azure AD-konfigurationen. Följ stegen nedan för att lägga till ett värde för Visningsidentifierare:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du har konfigurerat för enkel inloggning.

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn

8.  Under den **domän och URL: en** avsnittet, kontrollera den **visa avancerade URL-inställningar**.

9.  i den **identifierare** textrutan skriver du en unik identifierare för programmet.

10. **Spara** konfigurationen.


**Lösningsalternativet 2**

Om alternativ 1 ovan inte fungerar för dig tar du bort programmet från katalogen. Sedan kan lägga till och konfigurera om programmet, Följ stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

  * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  Klicka på **ta bort** längst upp till vänster i programmet **översikt** fönstret.

8.  Uppdatera Azure AD och lägga till programmet från Azure AD-galleriet. Konfigurera sedan programmet

<span id="_Hlk477190176" class="anchor"></span>När du konfigurerar om programmet ska du logga in till programmet.

## <a name="certificate-or-key-not-configured"></a>Certifikat eller nyckel har inte angetts

*Fel AADSTS50003: Inga signeringsnyckeln konfigurerats.*

**Möjlig orsak**

Programobjektet är skadad och Azure AD kan identifiera inte certifikatet som konfigurerats för programmet.

**Lösning**

Ta bort och skapa ett nytt certifikat genom att följa stegen nedan:

1.  Öppna den [ **Azure-portalen** ](https://portal.azure.com/) och logga in som en **Global administratör** eller **Medadministratör.**

2.  Öppna den **Azure Active Directory-tillägget** genom att klicka på **alla tjänster** överst i den huvudsakliga vänstra navigeringsmenyn.

3.  Skriv i **”Azure Active Directory**” i sökrutan för filter och välj den **Azure Active Directory** objekt.

4.  Klicka på **företagsprogram** från den vänstra navigeringsmenyn i Azure Active Directory.

5.  Klicka på **alla program** att visa en lista över alla dina program.

 * Om du inte ser programmet som du vill visa här använder du den **Filter** kontroll högst upp på den **listan över alla program** och ange den **visa** alternativet att **alla Program.**

6.  Välj det program som du vill konfigurera enkel inloggning

7.  När programmet har lästs in klickar du på den **enkel inloggning** från programmets vänstra navigeringsmenyn.

8.  Klicka på **Skapa nytt certifikat** under den **SAML-signeringscertifikat** avsnittet.

9.  Välj utgångsdatum. Klicka sedan på **spara.**

10. Kontrollera **gör nytt certifikat aktivt** att åsidosätta det aktiva certifikatet. Klicka sedan på **spara** överst i fönstret och acceptera för att aktivera förnyelsecertifikatet.

11. Under den **SAML-signeringscertifikat** klickar du på **ta bort** att ta bort den **används inte** certifikat.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problem när du anpassar SAML-anspråk som skickas till ett program

Läs hur du anpassar SAML attributet anspråk som skickas till programmet i [Anspråksmappning i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) för mer information.

## <a name="next-steps"></a>Nästa steg
[Så här felsöker du SAML-baserad enkel inloggning till program i Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
