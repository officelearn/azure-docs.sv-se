---
title: Konfigurera enkel inloggning – Azure Active Directory | Microsoft Docs
description: I den här självstudiekursen används Azure Portal till att konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 12/06/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: aceacdea8b3c86a5c4f26a5f082f4c6cf0b3805d
ms.sourcegitcommit: 698ba3e88adc357b8bd6178a7b2b1121cb8da797
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/07/2018
ms.locfileid: "53011985"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Självstudie: Konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory

I den här självstudiekursen används [Azure Portal](https://portal.azure.com) till att konfigurera SAML-baserad enkel inloggning för ett program med Azure Active Directory (Azure AD). Använd den här självstudien när det inte finns någon [programspecifik självstudie](../saas-apps/tutorial-list.md) tillgänglig. 

I den här självstudiekursen används Azure Portal till att:

> [!div class="checklist"]
> * välja läge för SAML-baserad enkel inloggning
> * konfigurera programspecifika domäner och URL:er
> * konfigurera användarattribut
> * skapa ett SAML-signeringscertifikat
> * tilldela användare till programmet
> * konfigurera programmet för SAML-baserad enkel inloggning
> * testa SAML-inställningarna

## <a name="before-you-begin"></a>Innan du börjar

1. Om programmet inte har lagts till din Azure AD-klientorganisation går du till [Snabbstart: Lägga till ett program till din Azure AD-klientorganisation](add-application-portal.md).

2. Fråga din programleverantör om informationen som beskrivs i [Konfigurera domän och URL:er](#configure-domain-and-urls).

3. För testning av stegen i den här självstudien rekommenderar vi att du använder en icke-produktionsmiljö. Om du inte har en icke-produktionsmiljö för Azure AD kan du [få en månads kostnadsfri utvärdering](https://azure.microsoft.com/pricing/free-trial/).

4. Logga in på [Azure-portalen](https://portal.azure.com) som administratör för molnprogram eller programadministratör för din Azure AD-klientorganisation.

## <a name="select-a-single-sign-on-mode"></a>Välja ett läge för enkel inloggning

När programmet har lagts till i Azure AD-klientorganisationen är du redo konfigurera enkel inloggning för programmet.

Öppna inställningarna för enkel inloggning:

1. I [Azure-portalen](https://portal.azure.com) går du till den vänstra navigeringspanelen och klickar på **Azure Active Directory**. 

2. På bladet **Azure Active Directory** klickar du på **Företagsprogram**. Bladet **Alla program** öppnas och visar ett slumpmässigt urval av programmen i din Azure AD-klientorganisation. 

3. Välj **Alla program** på menyn **Programtyp** och klicka på **Använd**.

4. Ange namnet på det program som du vill konfigurera enkel inloggning för. Välj ditt eget program eller ange **GitHub-test** för att konfigurera det program du lade till i snabbstarten [lägga till program](add-application-portal.md).

5. Klicka på **Enkel inloggning**. Under **Läge för enkel inloggning** visas **SAML-baserad inloggning** som standardalternativ. 

    ![Konfigurationsalternativ](media/configure-single-sign-on-portal/config-options.png)

6. Klicka på **Spara** överst på bladet. 

## <a name="configure-domain-and-urls"></a>Konfigurera domän och URL:er

Konfigurera domänen och URL:erna:

1. Kontakta programvaruleverantören för att få rätt information för följande inställningar:

    | Konfigurationsuppsättning | SP-initierad | idP-initierad | Beskrivning |
    |:--|:--|:--|:--|
    | Inloggnings-URL | Krävs | Ange inte | När en användare öppnar den här URL:en omdirigerar tjänstleverantören till Azure AD för att autentisera och logga in användaren. Azure AD använder URL:en för att starta programmet från Office 365 eller Azure AD-åtkomstpanelen. När den är tom är Azure AD beroende av att identitetsprovidern initierar enkel inloggning när en användare startar programmet.|
    | Identifierare (entitets-ID) | Krävs för vissa appar | Krävs för vissa appar | Identifierar unikt programmet som enkel inloggning har konfigurerats för. Azure AD skickar identifieraren till programmet som målgruppsparametern för SAML-token. Programmet förväntas verifiera den. Detta värde visas även som entitets-ID i alla SAML-metadata som anges av programmet.|
    | Svars-URL | Valfri | Krävs | Anger var programmet förväntas ta emot SAML-token. Svars-URL:en kallas även för URL för konsumenttjänst för försäkran (ACS-URL). |
    | Vidarebefordransstatus | Valfri | Valfri | Anger för programmet var användaren ska omdirigeras när autentiseringen har slutförts. Normalt är värdet en giltig URL för programmet men vissa program använder det här fältet ett annat sätt. Kontakta programleverantören om du vill ha mer information.

2. Ange informationen. Om du vill se alla inställningar klickar du på **Visa avancerade URL-inställningar**.

    ![Konfigurationsalternativ](media/configure-single-sign-on-portal/config-urls.png)

3. Klicka på **Spara** överst på bladet.

4. Det finns en knapp för att **testa SAML-inställningar** i det här avsnittet. Kör det här testet senare i självstudiekursen i avsnittet [Testa enkel inloggning](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Konfigurera användarattribut

Med användarattribut kan du kontrollera vilken information som Azure AD skickar till programmet i SAML-token varje gång en användare loggar in. Till exempel kan Azure AD skicka namnet, e-postadressen och anställnings-ID:t för användaren till programmet. 

Dessa attribut kan vara obligatoriska eller valfria för att göra så att enkel inloggning fungerar som det ska. Mer information finns i den [programspecifika självstudiekursen](../saas-apps/tutorial-list.md), eller fråga programleverantören.

1. Klicka på **Visa och redigera alla andra användarattribut** om du vill visa alla alternativ.

    ![Konfigurera användarattribut](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Ange **Användaridentifierare**.

    Användaridentifieraren identifierar unikt varje användare i programmet. Exempel: om e-postadressen är både användarnamnet och den unika identifieraren anger du värdet *user.mail*.

3. Om du vill ha fler SAML-tokenattribut klickar du på **Visa och redigera alla andra användarattribut**.

4. Om du vill lägga till ett attribut i **SAML-tokenattribut** klickar du på **Lägg till attribut**. Ange **namnet** och välj **värdet** i menyn.

5. Klicka på **Spara**. Det nya attributet visas i tabellen.
 
## <a name="create-a-saml-signing-certificate"></a>Skapa ett SAML-signeringscertifikat

Azure AD använder ett certifikat för att signera de SAML-token som det skickar till programmet. 

1. Om du vill se alla alternativ klickar du på **Visa avancerade alternativ för certifikatsignering**.

    ![Konfigurera certifikat](media/configure-single-sign-on-portal/config-certificate.png)

2. Konfigurera ett certifikat genom att klicka på **Skapa nytt certifikat**.

3. På bladet **Skapa nytt certifikat** anger du **utgångsdatum** och klickar på **Spara**.

4. Klicka på **Gör nytt certifikat aktivt**.

5. Mer information finns i [Avancerade alternativ för certifikatsignering](certificate-signing-options.md).

6. Om du vill behålla ändringarna du har gjort hittills ska du klicka på **Spara** högst upp på bladet **Enkel inloggning**. 

## <a name="assign-users-to-the-application"></a>Tilldela användare till programmet

Microsoft rekommenderar att testa enkel inloggning med flera användare eller grupper innan programmet distribueras i organisationen.

Tilldela en användare eller grupp till programmet:

1. Öppna programmet i portalen om det inte redan är öppet.
2. I det vänstra programbladet klickar du på **Användare och grupper**.
3. Klicka på **Lägg till användare**.
4. På bladet **Lägg till tilldelning** klickar du på **Användare och grupper**.
5. Om du vill hitta en viss användare skriver du användarnamnet i rutan **Välj**, klickar på kryssrutan bredvid användarens profilfoto eller logotyp och klickar på **Välj**. 
6. Hitta ditt aktuella användarnamn och välj det. Om du vill kan du välja flera användare.
7. På bladet **Lägg till tilldelning** klickar du på **Tilldela**. När du är klar visas de valda användarna i listan **Användare och grupper**.

## <a name="configure-the-application-to-use-azure-ad"></a>Konfigurera programmet att använda Azure AD

Nästan klart.  Som sista steg måste du konfigurera programmet så att det använder Azure AD som SAML-identitetsprovider. 

1. Rulla ned till slutet av bladet **Enkel inloggning** för ditt program. 

    ![Konfigurera program](media/configure-single-sign-on-portal/configure-app.png)

2. Klicka på **Konfigurera program** i portalen och följ instruktionerna.
3. Skapa användarkonton manuellt i programmet för att testa enkel inloggning. Skapa de användarkonton du har tilldelat till programmet i [föregående avsnitt](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Testa enkel inloggning

Du är redo att testa dina inställningar.  

1. Öppna inställningarna för enkel inloggning för programmet. 
2. Rulla till avsnittet **Konfigurera domän och URL:er**.
2. Klicka på **Testa SAML-inställningar**. Testningsalternativen visas.

    ![Testa alternativ för enkel inloggning](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Klicka på **Logga in aktuell användare**. Med det här testet kan du först se om enkel inloggning fungerar för dig, administratören.
4. Om det finns något fel visas ett felmeddelande. Kopiera och klistra in informationen i rutan **Hur ser felet ut?**.

    ![Få råd om lösning](media/configure-single-sign-on-portal/error-guidance.png)

5. Klicka på **Få råd om lösning**. Rotorsaken och råd om lösning visas.  I det här exemplet hade användaren inte tilldelats till programmet.

    ![Åtgärda fel](media/configure-single-sign-on-portal/fix-error.png)

6. Läs rådet om lösning och klicka sedan, om det behövs, på **Korrigera det**.

7. Kör testet igen tills det slutförts helt.



## <a name="next-steps"></a>Nästa steg
I den här självstudien konfigurerade du inställningarna för enkel inloggning för ett program. När du slutförde konfigurationen tilldelade du en användare till programmet och konfigurerade programmet så att det använder SAML-baserad enkel inloggning. När allt detta hade slutförts verifierade du att SAML-inloggningen fungerade som skulle.

Du gjorde detta:
> [!div class="checklist"]
> * Valde SAML för enkel inloggning-läget
> * Kontaktade programleverantören för att konfigurera domän och URL:er
> * Konfigurerade användarattribut
> * Skapade ett SAML-signeringscertifikat
> * Tilldelade manuellt användare eller grupper till programmet
> * Konfigurerade programmet så att det använder Azure AD som SAML-identitetsprovider
> * Testade SAML-baserad enkel inloggning

Om du vill distribuera programmet till fler användare i organisationen rekommenderar vi att du använder automatisk användaretablering.

> [!div class="nextstepaction"]
>[Lär dig hur du tilldelar användare med automatisk etablering](configure-automatic-user-provisioning-portal.md)


