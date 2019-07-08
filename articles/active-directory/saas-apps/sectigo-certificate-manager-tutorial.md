---
title: 'Självstudier: Azure Active Directory-integrering med Sectigo Certificate Manager | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Sectigo Certifikathanteraren.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 62cd6987-3373-4b58-b1ff-589f4a3d70a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/15/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0447a8dd464363ae7e076dde2520565005d7c0a5
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/05/2019
ms.locfileid: "67588236"
---
# <a name="tutorial-azure-active-directory-integration-with-sectigo-certificate-manager"></a>Självstudier: Azure Active Directory-integrering med Sectigo Certificate Manager

I den här självstudien får du lära dig hur du integrerar Sectigo Certificate Manager med Azure Active Directory (AD Azure).

Integrera Sectigo Certificate Manager med Azure AD ger dig följande fördelar:

* Du kan använda Azure AD för att kontrollera vem som har åtkomst till Sectigo Certifikathanteraren.
* Användare kan logga in automatiskt till Sectigo Certifikathanteraren med sina Azure AD-konton (enkel inloggning).
* Du kan hantera dina konton på en central plats, Azure-portalen.

Mer information om programvara som en tjänst (SaaS) app-integrering med Azure AD finns i [enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Förutsättningar

Om du vill konfigurera Azure AD-integrering med Sectigo Certifikathanteraren, behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har en Azure AD-prenumeration kan du skapa en [kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar.
* Sectigo Certifikathanteraren prenumeration med enkel inloggning aktiverat.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar du och testa Azure AD enkel inloggning i en testmiljö och integrera Sectigo Certificate Manager med Azure AD.

Sectigo Certificate Manager har stöd för följande funktioner:

* **SP-initierad enkel inloggning**
* **IDP-initierad enkel inloggning**

## <a name="add-sectigo-certificate-manager-in-the-azure-portal"></a>Lägg till Sectigo Certifikathanteraren i Azure portal

Om du vill integrera Sectigo Certificate Manager med Azure AD, måste du lägga till Sectigo Certificate Manager i din lista över hanterade SaaS-appar.

1. Logga in på [Azure Portal](https://portal.azure.com).

1. I den vänstra menyn väljer du **Azure Active Directory**.

    ![Azure Active Directory-alternativet](common/select-azuread.png)

1. Välj **Företagsprogram** > **Alla program**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. Om du vill lägga till ett program, Välj **nytt program**.

    ![Alternativet nytt program](common/add-new-app.png)

1. I sökrutan anger **Sectigo Certifikathanteraren**. I sökresultaten väljer **Sectigo Certifikathanteraren**, och välj sedan **Lägg till**.

    ![Sectigo Certifikathanteraren i resultatlistan](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Konfigurera och testa Azure AD enkel inloggning

I det här avsnittet ska du konfigurera och testa Azure AD enkel inloggning med Sectigo Certificate Manager baserat på en användare med namnet **Britta Simon**. För enkel inloggning ska fungera, måste du upprätta ett länkat förhållande mellan en Azure AD-användare och relaterade användaren i Sectigo Certifikathanteraren.

Om du vill konfigurera och testa Azure AD enkel inloggning med Sectigo Certifikathanteraren, måste du utföra följande byggblock:

| Aktivitet | Beskrivning |
| --- | --- |
| **[Konfigurera Azure AD enkel inloggning](#configure-azure-ad-single-sign-on)** | Gör det möjligt för användarna att använda den här funktionen. |
| **[Konfigurera Sectigo Certifikathanteraren enkel inloggning](#configure-sectigo-certificate-manager-single-sign-on)** | Konfigurerar inställningar för enkel inloggning i programmet. |
| **[Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user)** | Med namnet Britta Simon tester Azure AD enkel inloggning för en användare. |
| **[Tilldela Azure AD-testanvändare](#assign-the-azure-ad-test-user)** | Gör det möjligt för Britta Simon att använda Azure AD enkel inloggning. |
| **[Skapa en testanvändare Sectigo Certificate Manager](#create-a-sectigo-certificate-manager-test-user)** | Skapar en motsvarighet för Britta Simon i Sectigo Certificate Manager som är länkad till en Azure AD-representation av användaren. |
| **[Testa enkel inloggning](#test-single-sign-on)** | Kontrollerar att konfigurationen fungerar. |

### <a name="configure-azure-ad-single-sign-on"></a>Konfigurera Azure AD enkel inloggning

I det här avsnittet ska konfigurera du Azure AD enkel inloggning med Sectigo Certifikathanteraren i Azure-portalen.

1. I den [Azure-portalen](https://portal.azure.com/)i den **Sectigo Certifikathanteraren** application integration väljer **enkel inloggning**.

    ![Konfigurera alternativ för enkel inloggning](common/select-sso.png)

1. I den **väljer du en metod för enkel inloggning** fönstret Välj **SAML** eller **SAML/WS-Fed** läge för att aktivera enkel inloggning.

    ![Välja läge för enkel inloggning](common/select-saml-option.png)

1. I den **ange in enkel inloggning med SAML** väljer **redigera** (på pennikonen) att öppna den **SAML grundkonfiguration** fönstret.

    ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I den **SAML grundkonfiguration** fönstret Konfigurera *IDP-initierad läge*, gör du följande:

    1. I den **identifierare** anger någon av dessa webbadresser:
       * https:\//cert-manager.com/shibboleth
       * https:\//hard.cert-manager.com/shibboleth

    1. I den **svars-URL** anger någon av dessa webbadresser:
        * https:\//cert-manager.com/Shibboleth.sso/SAML2/POST
        * https:\//hard.cert-manager.com/Shibboleth.sso/SAML2/POST

    1. Välj **ange ytterligare webbadresser**.

    1. I den **Vidarebefordransstatus** anger någon av dessa webbadresser:
       * https:\//cert-manager.com/customer/SSLSupport/idp
       * https:\//hard.cert-manager.com/customer/SSLSupport/idp

    ![Sectigo Certificate Manager-domän och URL: er med enkel inloggning för information](common/idp-relay.png)

1.  Konfigurera programmet i *SP-initierat läge*, gör du följande:

    * I den **inloggnings-URL** anger någon av dessa webbadresser:
      * https:\//cert-manager.com/Shibboleth.sso/Login
      * https:\//hard.cert-manager.com/Shibboleth.sso/Login

      ![Sectigo Certificate Manager-domän och URL: er med enkel inloggning för information](common/both-signonurl.png)

1. I den **ange in enkel inloggning med SAML** fönstret i den **SAML-signeringscertifikat** väljer **hämta** bredvid **certifikat (Base64)** . Markera ett nedladdningsalternativ baserat på dina krav. Spara certifikatet på datorn.

    ![Hämtningsalternativet certifikat (Base64)](common/certificatebase64.png)

1. I den **konfigurera Sectigo Certificate Manager** avsnittet, kopiera följande URL: er utifrån dina behov:

    * Inloggningswebbadress
    * Microsoft Azure Active Directory-identifierare
    * Utloggnings-URL

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="configure-sectigo-certificate-manager-single-sign-on"></a>Konfigurera Sectigo Certifikathanteraren enkel inloggning

Om du vill konfigurera enkel inloggning på Sectigo Certifikathanteraren sida, skicka den hämta filen för certifikat (Base64) och de relevanta webbadresser som du kopierade från Azure portal för att den [Sectigo Certifikathanteraren supportteamet](https://sectigo.com/support). Supportteamet Sectigo Certificate Manager använder informationen som du skickar dem till att kontrollera att SAML enkel inloggning för anslutningen är korrekt inställd på båda sidorna.

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare 

I det här avsnittet skapar du en testanvändare med namnet Britta Simon i Azure-portalen.

1. I Azure-portalen väljer du **Azure Active Directory** > **Användare** > **Alla användare**.

    ![Användarna och alternativ för alla användare](common/users.png)

1. Välj **Ny användare**.

    ![Alternativet ny användare](common/new-user.png)

1. I fönstret **Användare** utför du följande steg:

    1. I rutan **Namn** anger du **BrittaSimon**.
  
    1. I den **användarnamn** anger **brittasimon\@\<din företagsdomän >.\< tillägget\>** . Till exempel **brittasimon\@contoso.com**.

    1. Välj den **Show lösenord** markerar du kryssrutan. Skriv ned värdet som visas i den **lösenord** box.

    1. Välj **Skapa**.

    ![Fönstret användare](common/user-properties.png)

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändare

I det här avsnittet ska bevilja du Britta Simon åtkomst till Sectigo Certificate Manager så att hon kan använda Azure enkel inloggning.

1. I Azure-portalen väljer du **företagsprogram** > **alla program** > **Sectigo Certifikathanteraren**.

    ![Fönstret Företagsprogram](common/enterprise-applications.png)

1. I listan med program väljer **Sectigo Certifikathanteraren**.

    ![Sectigo Certifikathanteraren i programlistan](common/all-applications.png)

1. På menyn väljer du **Användare och grupper**.

    ![Alternativet användare och grupper](common/users-groups-blade.png)

1. Välj **Lägg till användare**. I fönstret **Lägg till tilldelning** väljer du **Användare och grupper**.

    ![Fönstret Lägg till tilldelning](common/add-assign-user.png)

1. I den **användare och grupper** väljer **Britta Simon** i listan över användare. Välj **Välj**.

1. Om du förväntar dig ett rollvärde i SAML-försäkran i den **Välj roll** fönstret Välj rollen relevant för användaren i listan. Välj **Välj**.

1. I den **Lägg till tilldelning** väljer **tilldela**.

### <a name="create-a-sectigo-certificate-manager-test-user"></a>Skapa en testanvändare Sectigo Certificate Manager

I det här avsnittet skapar du en användare med namnet Britta Simon i Sectigo Certifikathanteraren. Arbeta med den [Sectigo Certifikathanteraren supportteamet](https://sectigo.com/support) att lägga till användaren i Sectigo Certificate Manager-plattformen. Användare måste skapas och aktiveras innan du använder enkel inloggning.

### <a name="test-single-sign-on"></a>Testa enkel inloggning

I det här avsnittet ska testa du Azure AD enkel inloggning för konfigurationen med hjälp av Mina appar-portalen.

När du har konfigurerat enkel inloggning, när du väljer **Sectigo Certifikathanteraren** i portalen Mina appar du loggas automatiskt in till Sectigo Certifikathanteraren. Läs mer om portalen Mina appar [öppna och använda appar i portalen Mina appar](../user-help/my-apps-portal-end-user-access.md).

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer kan du granska dessa artiklar:

- [Lista över självstudier för att integrera SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Enkel inloggning till program i Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Vad är villkorlig åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)


