---
title: 'Självstudier: Azure Active Directory-integrering med Shmoop för skolor | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Shmoop för skolor.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: 5c1dbf6429387c02f226e4005164e8824abdc102
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/23/2019
ms.locfileid: "54818956"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Självstudier: Azure Active Directory-integrering med Shmoop för skolor

I den här självstudien får du lära dig hur du integrerar Shmoop för skolor med Azure Active Directory (AD Azure).

Integrera Shmoop för skolor med Azure AD ger dig följande fördelar:

- Du kan styra i Azure AD som har åtkomst till Shmoop för skolor.
- Du kan aktivera användarna att få loggas in automatiskt Shmoop för skolor med sina Azure AD-konton.
- Du kan hantera dina konton på en central plats – Azure portal.

Mer information om integrering av SaaS-app med Azure AD finns i [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Shmoop för skolor, behöver du följande objekt:

- En Azure AD-prenumeration
- En Shmoop för skolor enkel inloggning aktiverat prenumeration

> [!NOTE]
> Vi rekommenderar inte att du använder en produktionsmiljö för att testa stegen i den här självstudien.

Vi rekommenderar följande för att testa stegen i den här självstudien:

- Med hjälp av din produktion evironment endast om det behövs.
- Hämta en [kostnadsfri utvärderingsmånad](https://azure.microsoft.com/pricing/free-trial/) om du inte redan har en Azure AD-utvärderingsmiljö.

## <a name="scenario-description"></a>Scenariobeskrivning
I den här självstudien kan du testa Azure AD enkel inloggning i en testmiljö. Det scenario som beskrivs i den här självstudien består av två viktigaste byggstenarna:

1. Att lägga till Shmoop för skolor från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Lägg till Shmoop för skolor från galleriet
För att konfigurera integrering av Shmoop för skolor i Azure AD, som du behöver lägga till Shmoop för skolor från galleriet i din lista över hanterade SaaS-appar.

**Om du vill lägga till Shmoop för skolor från galleriet, gör du följande:**

1. I den [Azure-portalen](https://portal.azure.com), i rutan till vänster väljer du den **Azure Active Directory** ikon. 

    ![Azure Active Directory-knappen][1]

2. Gå till **företagsprogram**. Gå till **alla program**.

    ![Bladet Företagsprogram][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** knappen överst i dialogrutan.

    ![Knappen Nytt program][3]

4. I sökrutan skriver **Shmoop för skolor**. Välj **Shmoop för skolor** från resultatet därefter den **Lägg till** för att lägga till programmet.

    ![Shmoop för skolor i resultatlistan](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa enkel inloggning med Azure AD

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Shmoop för skolor, baserat på en testanvändare som kallas ”Britta Simon”.

För enkel inloggning att fungera, behöver Azure AD du veta vilken användaren motsvarighet i Shmoop för skolor är att en användare i Azure AD. Med andra ord måste du upprätta en anslutning mellan Azure AD-användare och relaterade användaren i Shmoop för skolor.

Om du vill konfigurera och testa Azure AD enkel inloggning med Shmoop för skolor, utför du följande byggblock:

1. [Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on) vill tillåta att användarna använda den här funktionen.
2. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) att testa Azure AD enkel inloggning med Britta Simon.
3. [Skapa en testanvändare Shmoop för skolor](#create-a-shmoop-for-schools-test-user) har en motsvarighet för Britta Simon i Shmoop för skolor som är länkad till en Azure AD-representation av användaren.
4. [Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user) att aktivera Britta Simon att använda Azure AD enkel inloggning.
5. [Testa enkel inloggning](#test-single-sign-on) att kontrollera att konfigurationen fungerar.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera enkel inloggning med Azure AD

I det här avsnittet Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i ditt program för Shmoop för skolor.

**Om du vill konfigurera Azure AD enkel inloggning med Shmoop för skolor, utför du följande steg:**

1. I Azure-portalen på den **Shmoop för skolor** application integration markerar **enkel inloggning**.

    ![Konfigurera länk för enkel inloggning][4]

2. I den **enkel inloggning** dialogrutan i listrutan under **läge för enkel inloggning**väljer **SAML-baserad inloggning**.
 
    ![Enkel inloggning för dialogrutan](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. I den **Shmoop för skolor domän och URL: er** avsnittet, gör följande:

    ![Konfigurera enkel inloggning](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. I den **inloggnings-URL** skriver du en URL med följande mönster: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. I den **identifierare** skriver du en URL med följande mönster: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Dessa värden är inte verkliga. Uppdatera dessa värden med faktiska inloggnings-URL och identifierare. Kontakta den [Shmoop för skolor klienten supportteamet](mailto:support@shmoop.com) att hämta dessa värden. 
 
4. Shmoop för skolor programmet förväntar sig SAML-intyg i ett visst format. Konfigurera följande anspråk för det här programmet. Du kan hantera värdena för dessa attribut från den **användarattribut** på sidan för program-integrering. Följande skärmbild visar hur du konfigurerar intyg:

    ![Konfigurera enkel inloggning](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Shmoop för skolan stöder två roller för användare: **Lärare** och **Student**. Ställa in dessa roller i Azure AD så att användare kan tilldelas till rätt roller. Information om hur du konfigurerar roller i Azure AD finns i [hantera åtkomst med RBAC och Azure portal](../../role-based-access-control/role-assignments-portal.md).
    
5. I den **användarattribut** i avsnittet den **enkel inloggning** dialogrutan Konfigurera attributet som SAML-token som visas i föregående bild.  Utför sedan följande steg:

    | Attributnamn | Attributvärde |
    | -------------- | --------------- |
    | roll           | user.assignedroles |

    a. Öppna den **lägga till attributet** dialogrutan **Lägg till attribut**.
    
    ![Konfigurera enkel inloggning ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Konfigurera enkel inloggning](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. I den **namn** skriver attributets namn som visas för den raden.
    
    c. Från den **värdet** väljer attribut-värde som visas för den raden.

    d. Lämna den **Namespace** tom.
    
    e. Välj **OK**.

6. Välj knappen **Spara**.

    ![Konfigurera enkel inloggning](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. På den **SAML-signeringscertifikat** klickar du på kopieringsknappen för att kopiera **Appfederationsmetadata** och klistra in den i anteckningar.

    ![Länk för nedladdning av certifikatet](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Att konfigurera enkel inloggning på den **Shmoop för skolor** sida, som du behöver skicka den **Appfederationsmetadata** till den [Shmoop för skolor supportteam](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

Målet med det här avsnittet är att skapa en testanvändare kallas Britta Simon i Azure-portalen.

   ![Skapa en Azure AD-testanvändare][100]

**Om du vill skapa en testanvändare i Azure AD, gör du följande:**

1. I Azure-portalen, i rutan till vänster väljer du den **Azure Active Directory** knappen.

    ![Azure Active Directory-knappen](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över användare, gå till **användare och grupper**. Välj sedan **alla användare**.

    ![Länkarna ”Användare och grupper” och ”Alla grupper”](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Öppna den **användaren** dialogrutan **Lägg till** överst i den **alla användare** dialogrutan.

    ![Knappen Lägg till](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. I den **användaren** dialogrutan rutan, gör följande:

    ![Dialogrutan Användare](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** skriver användarens Britta Simon e-postadress.

    c. Välj den **visa lösenord** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** box.

    d. Välj **Skapa**.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Skapa en testanvändare Shmoop för skolor

Målet med det här avsnittet är att skapa en användare som kallas Britta Simon i Shmoop för skolor. Shmoop för skolor stöder just-in-time-etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en ny användare ännu inte finns skapas den under försök att komma åt Shmoop för skolor.

>[!NOTE]
>Om du vill skapa en användare manuellt kan du kontakta den [Shmoop för skolor supportteam](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska aktivera du Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till Shmoop för skolor.

![Tilldela rollen][200] 

**Om du vill tilldela Britta Simon Shmoop för skolor, gör du följande:**

1. Öppna program i Azure-portalen. Gå till **företagsprogram** i vyn directory.  Välj sedan **alla program**.

    ![Tilldela användare][201] 

2. I listan med program väljer **Shmoop för skolor**.

    ![Länken Shmoop för skolor i listan med program](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. På menyn till vänster väljer du **Användare och grupper**.

    ![Länken ”Användare och grupper”][202]

4. Välj knappen **Lägg till**. I den **Lägg till tilldelning** dialogrutan **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** dialogrutan **Britta Simon** på användarlistan.

6. I den **användare och grupper** dialogrutan klickar du på den **Välj** knappen. 

7. I den **Lägg till tilldelning** dialogrutan den **tilldela** knappen.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **Shmoop för skolor** panelen i åtkomstpanelen, du bör få loggas in automatiskt programmets Shmoop för skolor.

Läs mer om åtkomstpanelen [introduktion till åtkomstpanelen](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier att integrera SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

