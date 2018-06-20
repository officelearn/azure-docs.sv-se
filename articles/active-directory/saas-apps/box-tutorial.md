---
title: 'Självstudier: Azure Active Directory-integrering med | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och rutan.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3b565c8d-35e2-482a-b2f4-bf8fd7d8731f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: jeedes
ms.openlocfilehash: c15894f8cd559a08a1d75e2ac29cc0da0fc9e963
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226586"
---
# <a name="integrate-azure-active-directory-with-box"></a>Integrera Azure Active Directory med

I kursen får lära du att integrera Azure Active Directory (AD Azure) med.

Genom att integrera Azure AD med kan få du följande fördelar:

- Du kan styra i Azure AD som har åtkomst till rutan.
- Du kan aktivera användarna att hämta loggar in automatiskt på rutan (enkel inloggning eller SSO) med sina Azure AD-konton.
- Du kan hantera dina konton i en central plats, Azure-portalen.

Mer information om integrering av SaaS-app med Azure AD, se [vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med behöver du följande:

- En Azure AD-prenumeration
- En ruta SSO-aktiverade prenumeration

> [!NOTE]
> När du testar stegen i den här självstudiekursen, rekommenderar vi att du *inte* använder en produktionsmiljö.

Följ dessa rekommendationer för att testa stegen i den här självstudiekursen:

- Använd inte i produktionsmiljön, om det är nödvändigt.
- Om du inte har en utvärderingsversion Azure AD-miljö kan du [hämta en utvärderingsversion för en månad](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Scenariobeskrivning
I kursen får testa du Azure AD enkel inloggning i en testmiljö. 

Det scenario som beskrivs i den här kursen består av två huvudsakliga byggblock:

1. Att lägga till fält från galleriet
2. Konfigurera och testa Azure AD enkel inloggning

## <a name="add-box-from-the-gallery"></a>Lägg till fält från galleriet
Om du vill konfigurera Azure AD-integrering med Lägg till fält från galleriet i listan över hanterade SaaS-appar genom att göra följande:

1. I den [Azure-portalen](https://portal.azure.com), i den vänstra rutan, Välj **Azure Active Directory**. 

    ![Azure Active Directory-knappen][1]

2. Välj **företagsprogram** > **alla program**.

    ![Fönstret ”företagsprogram”][2]
    
3. Om du vill lägga till ett nytt program, Välj den **nytt program** längst upp i fönstret.

    ![”Det nya programmet” knappen][3]

4. I sökrutan skriver **rutan**väljer **rutan** i resultatlistan och välj sedan **Lägg till**.

    ![Rutan i resultatlistan](./media/box-tutorial/tutorial_box_search.png)
### <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet kan du konfigurera och testa Azure AD enkel inloggning med utifrån en testanvändare som kallas ”Britta Simon”.

För enkel inloggning ska fungera, måste Azure AD att identifiera användaren rutan och dess motsvarighet i Azure AD. Med andra ord måste en länk förhållandet mellan en Azure AD-användare och samma användare i rutan upprättas.

Om du vill upprätta en länk relation, tilldela som rutan *användarnamn* värdet för den *användarnamn* i Azure AD.

Slutför byggblock i följande fem avsnitt för att konfigurera och testa Azure AD enkel inloggning med.

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

Aktivera Azure AD enkel inloggning i Azure-portalen och konfigurera enkel inloggning i rutan programmet genom att göra följande:

1. I Azure-portalen i den **rutan** -integration fönstret, Välj **enkel inloggning**.

    ![Länken ”enkel inloggning”][4]

2. I den **enkel inloggning** fönster i den **läget för enkel inloggning** väljer **SAML-baserade inloggning**.
 
    ![Fönstret ”enkel inloggning”](./media/box-tutorial/tutorial_box_samlbase.png)

3. Under **rutan domän och URL: er**, gör du följande:

    ![”Rutan domän och URL: er” enkel inloggning information](./media/box-tutorial/url3.png)

    a. I den **inloggnings-URL** skriver en URL i följande format: *https://\<subdomain >. box.com*.

    b. I den **identifierare** textruta typen **box.net**.
     
    > [!NOTE] 
    > Föregående värden är inte verkliga. Uppdatera dem med de faktiska inloggnings-URL och identifierare. För att få värdena kan kontakta den [rutan klienten supportteamet](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire). 

4. Under **SAML-signeringscertifikat**väljer **XML-Metadata för**, och spara sedan metadatafilen på datorn.

    ![Länken hämta certifikatet](./media/box-tutorial/tutorial_box_certificate.png) 

5. Välj **Spara**.

    ![Konfigurera enkel inloggning spara](./media/box-tutorial/tutorial_general_400.png)
    
6. Om du vill konfigurera enkel inloggning för ditt program följer du proceduren i [Konfigurera enkel inloggning på din egen](https://community.box.com/t5/How-to-Guides-for-Admins/Setting-Up-Single-Sign-On-SSO-for-your-Enterprise/ta-p/1263#ssoonyourown).

> [!NOTE] 
> Om du inte aktiverar SSO-inställningarna för Box-konto kan du behöva kontakta den [rutan klienten supportteamet](https://community.box.com/t5/custom/page/page-id/submit_sso_questionaire) och ange den hämta XML-filen.

> [!TIP]
> När du konfigurerar appen, kan du läsa en kortare version av föregående anvisningarna i den [Azure-portalen](https://portal.azure.com). När du har lagt till appen i den **Active Directory** > **företagsprogram** väljer den **enkel inloggning** fliken och sedan ansluta till den inbäddade dokumentation i den **Configuration** avsnittet längst ned. Mer information om funktionen inbäddade dokumentation finns [Azure AD inbäddade dokumentationen]( https://go.microsoft.com/fwlink/?linkid=845985).
>

### <a name="create-an-azure-ad-test-user"></a>Skapa en testanvändare i Azure AD

I det här avsnittet skapar du testanvändare Britta Simon i Azure-portalen genom att göra följande:

![Skapa en testanvändare i Azure AD][100]

1. I Azure-portalen i den vänstra rutan, Välj **Azure Active Directory**.

    ![Azure Active Directory-länk](./media/box-tutorial/create_aaduser_01.png)

2. Om du vill visa en lista över aktuella användare, Välj **användare och grupper** > **alla användare**.

    ![”Användare och grupper” och ”alla användare” länkar](./media/box-tutorial/create_aaduser_02.png)

3. Längst upp i den **alla användare** väljer **Lägg till**.

    ![Knappen Lägg till](./media/box-tutorial/create_aaduser_03.png)

    Den **användaren** öppnas.

4. I den **användaren** fönster, gör du följande:

    ![Fönstret användare](./media/box-tutorial/create_aaduser_04.png)

    a. I den **namn** skriver **BrittaSimon**.

    b. I den **användarnamn** Skriv användarens Britta Simon e-postadress.

    c. Välj den **visa lösenordet** kryssrutan och sedan skriva ned det värde som visas i den **lösenord** rutan.

    d. Välj **Skapa**.
 
### <a name="create-a-box-test-user"></a>Skapa en testanvändare i rutan

I det här avsnittet skapar du testanvändare Britta Simon i rutan. Rutan stöder just-in-time-allokering som är aktiverad som standard. Om en användare inte redan finns, skapas en ny när du försöker komma åt rutan. Ingen åtgärd krävs för att skapa användaren.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet kan du låta användare Britta Simon att använda Azure enkel inloggning genom att bevilja åtkomst till rutan. Det gör du genom att göra följande:

![Tilldela rollen][200]

1. I Azure-portalen öppnar den **program** visa, gå till den **Directory** visa och välj sedan **företagsprogram** > **alla program**.

    ![”Företagsprogram” och ”alla program” länkar][201] 

2. I den **program** väljer **rutan**.

    ![Länken](./media/box-tutorial/tutorial_box_app.png)  

3. I den vänstra rutan, Välj **användare och grupper**.

    ![Länken ”användare och grupper”][202]

4. Välj **Lägg till** och sedan, i den **Lägg uppdrag** väljer **användare och grupper**.

    ![Fönstret Lägg till tilldelning][203]

5. I den **användare och grupper** fönster i den **användare** väljer **Britta Simon**.

6. Välj den **Välj** knappen.

7. I den **Lägg uppdrag** väljer **tilldela**.
    
### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet testa du Azure AD enkel inloggning konfigurationen med hjälp av åtkomstpanelen.

När du väljer den **rutan** panelen i åtkomstpanelen du öppna inloggningssidan för att logga in i tillämpningsprogrammet rutan.

## <a name="additional-resources"></a>Ytterligare resurser

* [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](tutorial-list.md)
* [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](../manage-apps/what-is-single-sign-on.md)
* [Konfigurera användaretablering](box-userprovisioning-tutorial.md)



<!--Image references-->

[1]: ./media/box-tutorial/tutorial_general_01.png
[2]: ./media/box-tutorial/tutorial_general_02.png
[3]: ./media/box-tutorial/tutorial_general_03.png
[4]: ./media/box-tutorial/tutorial_general_04.png

[100]: ./media/box-tutorial/tutorial_general_100.png

[200]: ./media/box-tutorial/tutorial_general_200.png
[201]: ./media/box-tutorial/tutorial_general_201.png
[202]: ./media/box-tutorial/tutorial_general_202.png
[203]: ./media/box-tutorial/tutorial_general_203.png

