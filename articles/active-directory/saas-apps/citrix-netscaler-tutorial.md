---
title: 'Självstudie: Azure Active Directory enkel inloggning (SSO) med Citrix NetScaler (Kerberos-baserad autentisering) | Microsoft Docs'
description: Lär dig hur du konfigurerar enkel inloggning mellan Azure Active Directory och Citrix NetScaler.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: af501bd0-8ff5-468f-9b06-21e607ae25de
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 75e825f55a890be49000e209859670caa2c1c875
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/25/2019
ms.locfileid: "75431375"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-citrix-netscaler-kerberos-based-authentication"></a>Självstudie: Azure Active Directory enkel inloggning (SSO) med Citrix NetScaler (Kerberos-baserad autentisering)

I den här självstudien får du lära dig att integrera Citrix NetScaler med Azure Active Directory (Azure AD). När du integrerar Citrix NetScaler med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Citrix NetScaler.
* Gör det möjligt för användarna att logga in automatiskt till Citrix NetScaler med sina Azure AD-konton.
* Hantera dina konton på en central plats – Azure Portal.

Mer information om SaaS app integration med Azure AD finns i [Vad är program åtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande objekt:

* En Azure AD-prenumeration. Om du inte har någon prenumeration kan du få ett [kostnads fritt konto](https://azure.microsoft.com/free/).
* Citrix NetScaler-prenumeration med enkel inloggning (SSO) aktive rad.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en test miljö.

* Citrix NetScaler stöder **SP** -INITIERAd SSO

* Citrix NetScaler stöder **just-in-Time** User-etablering

- [Konfigurera Citrix NetScaler enkel inloggning för Kerberos-baserad autentisering](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Konfigurera Citrix NetScaler enkel inloggning för rubrik baserad autentisering](header-citrix-netscaler-tutorial.md)

## <a name="adding-citrix-netscaler-from-the-gallery"></a>Lägga till Citrix NetScaler från galleriet

Om du vill konfigurera en Citrix netscaleer-integrering i Azure AD, måste du lägga till Citrix NetScaler från galleriet i listan över hanterade SaaS-appar.

1. Logga in på [Azure-portalen](https://portal.azure.com) med ett arbets- eller skolkonto eller ett personligt Microsoft-konto.
1. I det vänstra navigerings fönstret väljer du tjänsten **Azure Active Directory** .
1. Navigera till **företags program** och välj sedan **alla program**.
1. Välj **nytt program**om du vill lägga till ett nytt program.
1. I avsnittet **Lägg till från galleriet** , Skriv **Citrix NetScaler** i sökrutan.
1. Välj **Citrix NetScaler** från panelen resultat och Lägg sedan till appen. Vänta några sekunder medan appen läggs till i din klient organisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurera och testa enkel inloggning med Azure AD för Citrix NetScaler

Konfigurera och testa Azure AD SSO med Citrix NetScaler med en test användare som heter **B. Simon**. För att SSO ska fungera måste du upprätta en länk relation mellan en Azure AD-användare och den relaterade användaren i Citrix NetScaler.

Om du vill konfigurera och testa Azure AD SSO med Citrix NetScaler, fyller du i följande Bygg stenar:

1. **[Konfigurera Azure AD SSO](#configure-azure-ad-sso)** – så att användarna kan använda den här funktionen.
    1. **[Skapa en Azure AD-test](#create-an-azure-ad-test-user)** för att testa enkel inloggning med Azure AD med B. Simon.
    1. **[Tilldela Azure AD-testuser](#assign-the-azure-ad-test-user)** -för att aktivera B. Simon för att använda enkel inloggning med Azure AD.
1. **[Konfigurera Citrix NetScaler SSO](#configure-citrix-netscaler-sso)** – för att konfigurera inställningarna för enkel inloggning på program sidan.
    1. **[Skapa Citrix NetScaler test User](#create-citrix-netscaler-test-user)** -för att få en motsvarighet till B. Simon i Citrix NetScaler som är länkad till Azure AD-representation av användare.
1. **[Testa SSO](#test-sso)** – för att kontrol lera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Följ de här stegen för att aktivera Azure AD SSO i Azure Portal.

1. I [Azure Portal](https://portal.azure.com/), på sidan om program integration i **Citrix NetScaler** , letar du upp avsnittet **Hantera** och väljer **enkel inloggning**.
1. På sidan **Välj metod för enkel inloggning** väljer du **SAML**.
1. På sidan **Konfigurera enkel inloggning med SAML** klickar du på ikonen Redigera/penna för **grundläggande SAML-konfiguration** för att redigera inställningarna.

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. I avsnittet **grundläggande SAML-konfiguration** , om du vill konfigurera programmet i **IDP** initierat läge, anger du värdena för följande fält:

    a. I textrutan **Identifierare** skriver du en URL med följande mönster: `https://<<Your FQDN>>`

    b. I textrutan **Svars-URL** skriver du en URL med följande mönster: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

1. Klicka på **Ange ytterligare URL:er** och gör följande om du vill konfigurera appen i **SP**-initierat läge:

    I textrutan **Inloggnings-URL** skriver du en URL med följande mönster: `https://<<Your FQDN>>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > Dessa värden är inte verkliga. Uppdatera de här värdena med den faktiska inloggnings-URL:en, identifieraren och svars-URL:en. Kontakta [Citrix NetScaler-klientens support team](https://www.citrix.com/contact/technical-support.html) för att hämta dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.

    > [!NOTE]
    > Dessa URL: er bör vara tillgängliga från offentliga platser för att få enkel inloggning att fungera. Du måste aktivera brand väggen eller andra säkerhets inställningar på NetScaler-sidan för att Enble Azure AD för att kunna publicera token på den konfigurerade ACS-URL: en.

1. På sidan **Konfigurera enkel inloggning med SAML** , i avsnittet **SAML-signeringscertifikat** , letar du reda på URL för **app Federation**, kopierar URL: en och sparar den i anteckningar.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. I avsnittet **Konfigurera Citrix Netscaleer** kopierar du lämpliga URL: er baserat på ditt krav.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en test användare i Azure Portal som kallas B. Simon.

1. I den vänstra rutan i Azure Portal väljer du **Azure Active Directory**, väljer **användare**och väljer sedan **alla användare**.
1. Välj **Ny användare** överst på skärmen.
1. I **användar** egenskaperna följer du de här stegen:
   1. I **Namn**-fältet skriver du `B.Simon`.  
   1. I fältet **användar namn** anger du username@companydomain.extension. Till exempel `B.Simon@contoso.com`.
   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned det värde som visas i rutan **Lösenord**.
   1. Klicka på **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet ska du aktivera B. Simon för att använda enkel inloggning med Azure genom att bevilja åtkomst till Citrix NetScaler.

1. I Azure Portal väljer du **företags program**och väljer sedan **alla program**.
1. I listan program väljer du **Citrix NetScaler**.
1. På sidan Översikt för appen letar du reda på avsnittet **Hantera** och väljer **användare och grupper**.

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**och välj sedan **användare och grupper** i dialog rutan **Lägg till tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. I dialog rutan **användare och grupper** väljer du **B. Simon** från listan användare och klickar sedan på knappen **Välj** längst ned på skärmen.
1. Om du förväntar dig ett roll värde i SAML Assertion, i dialog rutan **Välj roll** , väljer du lämplig roll för användaren i listan och klickar sedan på knappen **Välj** längst ned på skärmen.
1. I dialogrutan **Lägg till tilldelning** klickar du på knappen **Tilldela**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurera Citrix NetScaler SSO

- [Konfigurera Citrix NetScaler enkel inloggning för Kerberos-baserad autentisering](#configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication)

- [Konfigurera Citrix NetScaler enkel inloggning för rubrik baserad autentisering](header-citrix-netscaler-tutorial.md)

### <a name="publishing-web-server"></a>Publicerar webb server 

1. Skapa en **virtuell server**.

    a. Gå till **trafik hanterings > belastnings utjämning > Services**.
    
    b. Klicka på **Lägg till**.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/web01.png)

    c. Ange information om webb servern som kör programmen nedan:
    * **Tjänst namn**
    * **Server-IP/befintlig server**
    * **Protokoll**
    * **Port**

     ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/web01.png)

### <a name="configuring-load-balancer"></a>Konfigurera Load Balancer

1. Utför följande steg för att konfigurera Load Balancer:

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/load01.png)

    a. Gå till **Traffic Management > belastnings utjämning > virtuella servrar**.

    b. Klicka på **Lägg till**.

    c. Ange informationen nedan:

    * **Namn**
    * **Protokoll**
    * **IP-adress**
    * **Port**
    * Klicka på **OK**

### <a name="bind-virtual-server"></a>Bind virtuell server

Bind Load Balancer med den virtuella servern som skapades tidigare.

![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/bind01.png)

![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/bind02.png)

### <a name="bind-certificate"></a>Bind certifikat

Eftersom vi ska publicera den här tjänsten som SSL bind Server certifikatet och testa sedan ditt program.

![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/bind03.png)

![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

### <a name="create-authentication-policy"></a>Skapa autentiseringsprincip

1. Gå till **säkerhet > AAA – program trafik > principer > autentisering > autentiseringsprinciper**.

2. Klicka på **Lägg till** och ange sedan information.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/policy01.png)

    a. Namn på **principen för autentisering**.

    b. Uttryck: **True**.

    c. Åtgärds typ **SAML**.

    d. Action = Klicka på **Lägg till** (Följ guiden skapa autentisering av SAML-Server).
    
    e. Klicka på Skapa i **principen för autentisering**.

### <a name="create-authentication-saml-server"></a>Skapa SAML-Server för autentisering

1. Utför följande steg:

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/server01.png)

    a. Ange **namnet**.

    b. Importera metadata (ange URL: en för federationsmetadata från Azure SAML UI som du har kopierat från ovan).
    
    c. Ange **utfärdarens namn**.

    d. Klicka på **skapa**.

### <a name="create-authentication-virtual-server"></a>Skapa virtuell autentiserings Server

1.  Gå till **säkerhet > AAA-program trafik > > virtuella autentiserings servrar**.

2.  Klicka på **Lägg till** och utför följande steg:

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/server02.png)

    a.  Ange en **namn**.

    b.  Välj **icke-adresserbar**.

    c.  Protokoll- **SSL**.

    d.  Klicka på **OK**.

    e.  Klicka på **Fortsätt**.

### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurera den virtuella autentiserings servern så att den använder Azure AD

Du måste ändra de två avsnitten i den virtuella autentiserings servern.

1.  **Avancerade autentiseringsprinciper**

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/virtual01.png)

    a. Välj den **autentiseringsprincip** som du skapade tidigare.

    b. Klicka på **BIND**.

      ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/virtual02.png)

2. **Formulär baserade virtuella servrar**

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/virtual03.png)

    a.  Du måste ange ett **fullständigt domän namn** eftersom det upprätthålls av användar gränssnittet.

    b.  Välj den **virtuella Server Load Balancer** som du vill skydda med Azure AD-autentisering.

    c.  Klicka på **BIND**.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/virtual04.png)

    >[!NOTE]
    >Se till att du klickar på **färdig** på sidan konfiguration av virtuell server.

3. Verifiera ändringarna. Bläddra till programmets URL. Du bör se din klient inloggnings sida i stället för oautentiserad åtkomst tidigare.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-single-sign-on-for-kerberos-based-authentication"></a>Konfigurera Citrix NetScaler enkel inloggning för Kerberos-baserad autentisering

### <a name="create-a-kerberos-delegation-account-for-citrix-adc"></a>Skapa ett Kerberos Delegerings konto för Citrix ADC

1. Skapa ett användar konto (i det här exemplet AppDelegation).

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos01.png)

2. Konfigurera en värd-SPN för dessa konton.

    * Setspn-S HOST/AppDelegation. IDENTITIES. WORK identt\appdelegation
    
        I exemplet ovan

        a. Identiteten. Work (domän-FQDN)

        b. Identiteten (Domänens NetBIOS-namn)

        c. AppDelegation (namn på Delegerings användar konto)

3. Konfigurera delegering för webserver 
 
    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos02.png)

    >[!NOTE]
    >I exemplet ovan är det interna webb Server namnet som kör WIA-platsen cweb2

### <a name="citrix-aaa-kcd--kerberos-delegation-accounts"></a>Citrix AAA-KCD (Kerberos Delegerings konton)

1.  Gå till **Citrix Gateway > AAA KCD-konton (Kerberos-begränsad delegering)** .

2.  Klicka på Lägg till och ange informationen nedan:

    a.  Ange **namn**.

    b.  **Sfär**.

    c.  **Tjänst-SPN**`http/<host/fqdn>@DOMAIN.COM`.
    
    >[!NOTE]
    >@DOMAIN.com är obligatoriskt och i versaler.

    d.  Ange **delegerat användar konto**.

    e.  Kontrol lera lösen ordet för den delegerade användaren och ange **lösen ord**.

    f.  Klicka på **OK**.
 
    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos03.png)

### <a name="citrix-traffic-policy-and-traffic-profile"></a>Citrix Traffic policy och trafik profil

1.  Gå till **säkerhet > AAA-program trafik > principer > trafik principer, profiler och formulär SSO ProfilesTraffic-principer**.

2.  Välj **trafik profiler**.

3.  Klicka på **Lägg till**.

4.  Konfigurera trafik profil.

    a.  Ange **namn**.

    b.  Ange **enkel inloggning**.

    c.  Ange det **KCD-konto** som skapades i tidigare steg från List rutan.

    d.  Klicka på **OK**.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos04.png)
 
5.  Välj **trafik princip**.

6.  Klicka på **Lägg till**.

7.  Konfigurera trafik princip.

    a.  Ange **namn**.

    b.  Välj den tidigare skapade **trafik profilen** i list rutan.

    c.  Ange uttrycket till **Sant**.

    d.  Klicka på **OK**.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos05.png)

### <a name="citrix-bind-traffic-policy-to-virtual-servers"></a>Citrix bind Traffic policy till virtuella servrar

För att binda en trafik princip till en speciell virtuell server med hjälp av det grafiska användar gränssnittet.

* Gå till **trafik hanterings > belastnings utjämning > virtuella servrar**.

* I informations fönstret i listan med virtuella servrar väljer du den **virtuella server** som du vill binda den omskrivna principen till och klickar sedan på **Öppna**.

* I dialog rutan konfigurera virtuell server (belastnings utjämning) väljer du **fliken principer**. Alla principer som kon figurer ATS i NetScaler visas i listan.
 
    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos06.png)

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos07.png)

1.  Markera **kryss rutan** bredvid namnet på den princip som du vill binda till den här virtuella servern.
 
    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos08.png)

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos09.png)

1. Endast principen är kopplad, klicka på **färdig**.
 
    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos10.png)

1. Testa med den Windows-integrerade webbplatsen.

    ![Citrix NetScaler-konfiguration](./media/citrix-netscaler-tutorial/kerberos11.png)    

### <a name="create-citrix-netscaler-test-user"></a>Skapa Citrix NetScaler-test användare

I det här avsnittet skapas en användare som heter B. Simon i Citrix NetScaler. Citrix NetScaler stöder just-in-Time-användar etablering, som är aktiverat som standard. Det finns inget åtgärdsobjekt för dig i det här avsnittet. Om en användare inte redan finns i Citrix NetScaler, skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt måste du kontakta [support teamet för Citrix NetScaler-klienten](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du konfigurationen för enkel inloggning Azure AD med hjälp av åtkomstpanelen.

När du klickar på panelen Citrix NetScaler på åtkomst panelen, bör du loggas in automatiskt på den Citrix netscaleer som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [ Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Citrix NetScaler med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera Citrix NetScaler enkel inloggning för rubrik baserad autentisering](header-citrix-netscaler-tutorial.md)
