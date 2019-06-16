---
title: 'Självstudier: Azure Active Directory-integrering med monday.com | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och monday.com.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9e8ad807-0664-4e31-91de-731097c768e2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 08108a1718ee917a317b4864de81959ae139eaa5
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67097013"
---
# <a name="tutorial-azure-active-directory-integration-with-mondaycom"></a>Självstudier: Azure Active Directory-integrering med monday.com

I den här självstudien får du lära dig hur du integrerar monday.com med Azure Active Directory (AD Azure).

Integrera monday.com med Azure AD ger dig följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till monday.com.
* Användare kan vara loggas in automatiskt till monday.com med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Nödvändiga komponenter

Om du vill konfigurera Azure AD-integrering med monday.com, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* En monday.com prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera monday.com med Azure AD.

MONDAY.com har stöd för följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**
* **just-in-time-användaretablering**

## <a name="add-mondaycom-in-the-azure-portal"></a>Lägg till monday.com i Azure portal

Om du vill integrera monday.com med Azure AD, måste du lägga till monday.com i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn väljer du **Azure Active Directory**.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program, Välj **nytt program**.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **monday.com**. I sökresultaten väljer **monday.com**, och välj sedan **Lägg till**.

    ![MONDAY.com i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med monday.com baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i monday.com.

Om du vill konfigurera och testa Azure AD enkel inloggning med monday.com, måste du utföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera monday.com enkel inloggning](#configure-mondaycom-single-sign-on)** | Konfigurerar inställningar för enkel inloggning i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Med namnet Britta Simon tester Azure AD enkel inloggning för en användare. |
| **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda Azure AD enkel inloggning. |
| **[Skapa en monday.com testanvändare](#create-a-mondaycom-test-user)** | Skapar en motsvarighet för Britta Simon i monday.com som är länkad till en Azure AD-representation av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Kontrollerar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet konfigurerar du Azure AD enkel inloggning med monday.com i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)i den **monday.com** application integration väljer **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML** eller **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret om du har en metadatafil för service provider och du vill konfigurera *IDP-initierad läge*, gör du följande:

    1. Välj **ladda upp metadatafilen**.

       ![Alternativet ladda upp metadata för fil](common/upload-metadata.png)

    1. Välj mappikonen för att välja metadatafilen, och välj sedan **överför**.

       ![Välj metadatafilen och välj sedan knappen ladda upp](common/browse-upload-metadata.png)

    1. När metadatafilen har överförts den **identifierare** och **svars-URL** värden fylls i automatiskt i den **SAML grundkonfiguration** fönstret:

       ![IDP-värden i fönstret grundläggande SAML-konfiguration](common/idp-intiated.png)

       > [!Note]
       > Om den **identifierare** och **svars-URL** värden inte har fyllts i automatiskt, ange värdena manuellt.

1. Konfigurera programmet i *SP-initierat läge*:

    1. Välj **ange ytterligare webbadresser**.
    
    1. I den **inloggnings-URL** anger en URL som har följande mönster: https:\//\<din-domän >. monday.com. Kontakta den [monday.com klienten supportteamet](mailto:support@monday.com) att hämta inloggnings URL.

        ![Set-alternativ för ytterligare URL: er](common/metadata-upload-additional-signon.png)

1. Monday.com programmet förväntar sig SAML-intyg kan på ett specifikt format. Konfigurera följande anspråk för det här programmet. Att hantera dessa attributvärden i den **ange in enkel inloggning med SAML** väljer **redigera** att öppna den **användarattribut** fönstret.

    ![Fönstret attribut för användare](common/edit-attribute.png)

1. Under **användaranspråk**väljer **redigera** redigera anspråk. Om du vill lägga till ett anspråk, Välj **Lägg till nytt anspråk**, och sedan konfigurera attributet som SAML-token som visas i föregående bild. Utför sedan följande steg: 

    1. Välj **Lägg till nytt anspråk**.

        ![Lägg till ny anspråk alternativ i fönstret användarens anspråk](common/new-save-attribute.png)

    1. I den **hantera användaranspråk** fönstret anger du följande värden:
        
       1. I den **namn** Anger attributets namn visas för användaren anspråk raden.

       1. Lämna **Namespace** tom.

       1. För **källa**väljer **attributet**.

       1. I den **källattribut** väljer attribut-värde som visas för användaren anspråk raden.

       1. Välj **OK**, och välj sedan **spara**.

       ![Hantera användaranspråk](common/new-attribute-details.png)

1. I den **ange in enkel inloggning med SAML** fönstret under **SAML-signeringscertifikat**väljer **hämta** bredvid **certifikat (Base64)** . Markera ett nedladdningsalternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtningsalternativet certifikat (Base64)](common/certificatebase64.png)

1. I den **konfigurera monday.com** avsnittet, kopiera följande URL: er utifrån dina behov:

    * Inloggningswebbadress
    * Microsoft Azure Active Directory-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-mondaycom-single-sign-on"></a>Konfigurera monday.com enkel inloggning

Om du vill konfigurera enkel inloggning på monday.com sida, skicka den hämta filen för certifikat (Base64) och de relevanta webbadresser som du kopierade från Azure portal för att den [monday.com supportteamet](mailto:support@monday.com). Supportteamet monday.com använder informationen som du skickar dem till att kontrollera att SAML enkel inloggning för anslutningen är korrekt inställd på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och alternativ för alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **brittasimon\@\<din företagsdomän >.\< tillägget >** . Till exempel **brittasimon\@contoso.com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska bevilja du Britta Simon åtkomst till monday.com, så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **monday.com**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan med program väljer **monday.com**.

    ![MONDAY.com i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** fönstret Välj rollen relevant för användaren i listan. Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-mondaycom-test-user"></a>Skapa en monday.com testanvändare

I det här avsnittet skapas en användare med namnet Britta Simon i monday.com-programmet. MONDAY.com stöder etableringen av just-in-time-användare som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i monday.com, skapas en ny efter autentisering.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du har konfigurerat enkel inloggning, när du väljer **monday.com** i portalen Mina appar loggas du automatiskt till monday.com. Läs mer om portalen Mina appar [öppna och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du granska dessa artiklar:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
