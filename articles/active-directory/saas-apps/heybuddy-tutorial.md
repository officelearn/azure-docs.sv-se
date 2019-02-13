---
title: 'Självstudier: Azure Active Directory-integrering med HeyBuddy | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och HeyBuddy.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d51b5af6-018e-4678-9a3f-b70438394f67
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/06/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 67c3bf168ae056b1205d6eeaa8e5676c880eab97
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/13/2019
ms.locfileid: "56167124"
---
# <a name="tutorial-azure-active-directory-integration-with-heybuddy"></a>Självstudier: Azure Active Directory-integrering med HeyBuddy

I den här självstudien får du lära dig hur du integrerar HeyBuddy med Azure Active Directory (AD Azure).

Integrera HeyBuddy med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till HeyBuddy.
- Du kan aktivera användarna att automatiskt få loggat in på HeyBuddy (Single Sign-On) med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure-portalen.

Om du vill veta mer om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med HeyBuddy, behöver du följande objekt:

- En Azure AD-prenumeration
- En HeyBuddy enkel inloggning aktiverat prenumeration

> [!NOTE]
> Om du vill testa stegen i den här självstudien rekommenderar vi inte med hjälp av en produktionsmiljö.

Du bör följa de här rekommendationerna när du testar stegen i självstudien:

- Använd inte din produktionsmiljö om det inte behövs.
- Om du inte har en Azure AD-utvärderingsmiljö, kan du [få en månads utvärdering](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till HeyBuddy från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="adding-heybuddy-from-the-gallery"></a>Att lägga till HeyBuddy från galleriet

För att konfigurera integrering av HeyBuddy i Azure AD, som du behöver lägga till HeyBuddy från galleriet i din lista över hanterade SaaS-appar.

**Utför följande steg för att lägga till HeyBuddy från galleriet:**

1. I den **[Azure-portalen](https://portal.azure.com)**, klicka på den vänstra navigeringspanelen **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet för Enterprise-program][2]

3. Lägg till ett nytt program genom att klicka på knappen **Nytt program** högst upp i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **HeyBuddy**väljer **HeyBuddy** resultatet panelen klickar **Lägg till** för att lägga till programmet.

    ![HeyBuddy i resultatlistan](./media/heybuddy-tutorial/tutorial_heybuddy_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med HeyBuddy baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vad användaren motsvarighet i HeyBuddy är till en användare i Azure AD. Med andra ord måste en länk relationen mellan en Azure AD-användare och relaterade användaren i HeyBuddy upprättas.

Om du vill konfigurera och testa Azure AD enkel inloggning med HeyBuddy, måste du utföra följande byggblock:

1. **[Konfigurera Azure AD enkel inloggning](#configuring-azure-ad-single-sign-on)**  – om du vill ge användarna använda den här funktionen.
2. **[Skapa en Azure AD-testanvändare](#creating-an-azure-ad-test-user)**  – om du vill testa Azure AD enkel inloggning med Britta Simon.
3. **[Skapa en testanvändare HeyBuddy](#creating-a-heybuddy-test-user)**  – du har en motsvarighet för Britta Simon i HeyBuddy som är länkad till en Azure AD-representation av användaren.
4. **[Tilldela Azure AD-testanvändare](#assigning-the-azure-ad-test-user)**  – om du vill aktivera Britta Simon att använda Azure AD enkel inloggning.
5. **[Testa enkel inloggning](#testing-single-sign-on)**  – om du vill kontrollera om konfigurationen fungerar.

### <a name="configuring-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt HeyBuddy program.

**Utför följande steg för att konfigurera Azure AD enkel inloggning med HeyBuddy:**

1. I Azure-portalen på den **HeyBuddy** program integration-sidan klickar du på **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. På den **väljer du en metod för enkel inloggning** dialogrutan klickar du på **Välj** för **SAML** läge för att aktivera enkel inloggning.

    ![Konfigurera enkel inloggning](common/tutorial_general_301.png)

3. På den **ange in enkel inloggning med SAML** klickar du på **redigera** ikonen för att öppna **SAML grundkonfiguration** dialogrutan.

    ![Konfigurera enkel inloggning](common/editconfigure.png)

4. I avsnittet **Grundläggande SAML-konfiguration** utför du följande steg:

    ![HeyBuddy domän och URL: er med enkel inloggning för information](./media/heybuddy-tutorial/tutorial_heybuddy_url.png)

    a. I den **inloggnings-URL** textrutan anger du ett URL med hjälp av följande mönster: `https://api.heybuddy.com/auth/<ENTITY ID>`

    b. I den **identifierare (entitets-ID)** textrutan anger du ett URL med hjälp av följande mönster: `YourCompanyInstanceofHeyBuddy`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera dessa värden med de faktiska inloggnings-URL och identifierare (entitets-ID). Den `Entity ID` i URL: en för inloggning är automatiskt genererad för varje organisation. Kontakta [HeyBuddy klienten supportteamet](mailto:support@heybuddy.com) att hämta dessa värden.

5. HeyBuddy program som förväntar SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut i avsnittet om **användarattribut och anspråk** på sidan för programintegrering. På den **ange in enkel inloggning med SAML** klickar du på **redigera** knappen för att öppna **användarattribut och anspråk** dialogrutan.

    ![image](./media/heybuddy-tutorial/tutorial_heybuddy_attribute.png)

    >[!NOTE]
    >Se detta [länk](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) på hur du konfigurerar och konfigurera roller för programmet.

6. I den **användaranspråk** avsnittet på den **användarattribut och anspråk** dialogrutan Konfigurera SAML-token attributet som visas i bilden ovan och utför följande steg:
    
    | Namn  | Källattribut  |
    | --------------- | --------------- |
    | Roller               | user.assignedroles |

    a. Klicka på **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    ![image](./common/new_save_attribute.png)

    ![image](./common/new_attribute_details.png)

    b. I textrutan **Namn** skriver du det attributnamn som visas för den raden.

    c. Lämna den **Namespace** värdet tomt.

    d. Välj Källa som **Attribut**.

    e. Från listan över **Källattribut** skriver du det attributvärde som visas för den raden.

    f. Klicka på **Spara**.

7. På den **SAML-signeringscertifikat** sidan den **SAML-signeringscertifikat** klickar du på kopian **ikonen** att kopiera **Appfederationsmetadata**  och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/heybuddy-tutorial/tutorial_heybuddy_certificate.png) 

8. Att konfigurera enkel inloggning på **HeyBuddy** sida, som du behöver skicka den kopierade **Appfederationsmetadata** till [HeyBuddy supportteamet](mailto:support@heybuddy.com). De anger inställningen så att SAML SSO-anslutningen ställs in korrekt på båda sidorna.

### <a name="creating-an-azure-ad-test-user"></a>Skapa en Azure AD-användare för testning

Målet med det här avsnittet är att skapa en testanvändare i Azure-portalen med namnet Britta Simon.

1. Gå till den vänstra rutan i Azure-portalen och välj **Azure Active Directory**, välj **Users** och sedan **Alla användare**.

    ![Skapa en Azure AD-användare][100]

2. Välj **ny användare** överst på skärmen.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_01.png) 

3. Utför följande steg i egenskaperna för användaren.

    ![Skapa en Azure AD-användare för testning](common/create_aaduser_02.png)

    a. I fältet **Namn** anger du **BrittaSimon**.
  
    b. I fältet **Användarnamn** anger du **brittasimon@yourcompanydomain.extension**  
    Till exempel, BrittaSimon@contoso.com

    c. Välj **egenskaper**väljer den **Show lösenord** kryssrutan och sedan skriva ned det värde som visas i rutan lösenord.

    d. Välj **Skapa**.

### <a name="creating-a-heybuddy-test-user"></a>Skapa en HeyBuddy testanvändare

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i HeyBuddy. HeyBuddy stöder just-in-time-etablering, vilket är som standard aktiverat. Det finns inget åtgärdsobjekt för dig i det här avsnittet. En ny användare har skapats under ett försök att komma åt HeyBuddy om det inte finns ännu.
>[!Note]
>Om du vill skapa en användare manuellt kan du kontakta [HeyBuddy supportteamet](mailto:support@heybuddy.com).

### <a name="assigning-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till HeyBuddy.

1. I Azure-portalen väljer du **företagsprogram**väljer **alla program**.

    ![Tilldela användare][201]

2. I listan med program väljer **HeyBuddy**.

    ![Konfigurera enkel inloggning](./media/heybuddy-tutorial/tutorial_heybuddy_app.png)

3. I menyn till vänster, klickar du på **användare och grupper**.

    ![Tilldela användare][202]

4. Klicka på **Lägg till** knappen. Välj sedan **användare och grupper** på **Lägg till tilldelning** dialogrutan.

    ![Tilldela användare][203]

5. I den **användare och grupper** dialogrutan Välj **Britta Simon** i listan över användare och klicka på den **Välj** längst ned på skärmen.

6. I den **Lägg till tilldelning** dialogrutan Välj den **tilldela** knappen.

### <a name="testing-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du klickar på panelen HeyBuddy i åtkomstpanelen du bör få automatiskt loggat in på ditt HeyBuddy program.
Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över guider om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: common/tutorial_general_01.png
[2]: common/tutorial_general_02.png
[3]: common/tutorial_general_03.png
[4]: common/tutorial_general_04.png

[100]: common/tutorial_general_100.png

[201]: common/tutorial_general_201.png
[202]: common/tutorial_general_202.png
[203]: common/tutorial_general_203.png
