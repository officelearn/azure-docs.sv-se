---
title: 'Självstudiekurs: Azure Active Directory enkel inloggningsintegrering med Citrix NetScaler (rubrikbaserad autentisering) | Microsoft-dokument'
description: Lär dig hur du konfigurerar enkel inloggning (SSO) mellan Azure Active Directory och Citrix NetScaler med hjälp av rubrikbaserad autentisering.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b0adc7bf-696d-44c9-a57a-f9e9471b8710
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 87f1c884edf6841b65495cd31ed4c7d6e63aedd9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75977934"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-citrix-netscaler-header-based-authentication"></a>Självstudiekurs: Azure Active Directory enkel inloggningsintegrering med Citrix NetScaler (rubrikbaserad autentisering)

I den här självstudien får du lära dig hur du integrerar Citrix NetScaler med Azure Active Directory (Azure AD). När du integrerar Citrix NetScaler med Azure AD kan du:

* Kontroll i Azure AD som har åtkomst till Citrix NetScaler.
* Gör att användarna automatiskt loggas in på Citrix NetScaler med sina Azure AD-konton.
* Hantera dina konton på en central plats - Azure-portalen.

Mer information om integrering av SaaS-appar (Software as a Service) med Azure AD finns i [Vad är programåtkomst och enkel inloggning med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Krav

För att komma igång behöver du följande:

* En Azure AD-prenumeration. Om du inte har en prenumeration kan du få ett [gratis konto](https://azure.microsoft.com/free/).
* Citrix NetScaler enkel inloggning (SSO) aktiverad prenumeration.

## <a name="scenario-description"></a>Scenariobeskrivning

I den här självstudien konfigurerar och testar du Azure AD SSO i en testmiljö. Självstudien innehåller följande scenarier:

* **SP-initierad** SSO för Citrix NetScaler

* **Precis i tid** användare provisionering för Citrix NetScaler

* [Rubrikbaserad autentisering för Citrix NetScaler](#publish-the-web-server)

* [Kerberos-baserad autentisering för Citrix NetScaler](citrix-netscaler-tutorial.md#publish-the-web-server)

## <a name="add-citrix-netscaler-from-the-gallery"></a>Lägg till Citrix NetScaler från galleriet

Om du vill integrera Citrix NetScaler med Azure AD lägger du först till Citrix NetScaler i listan över hanterade SaaS-appar från galleriet:

1. Logga in på [Azure-portalen](https://portal.azure.com) med antingen ett arbets- eller skolkonto eller ett personligt Microsoft-konto.

1. Välj Azure Active **Directory**på den vänstra menyn .

1. Gå till **Företagsprogram** och välj sedan **Alla program**.

1. Om du vill lägga till ett nytt program väljer du **Nytt program**.

1. I avsnittet **Lägg till från galleriet** anger du **Citrix NetScaler** i sökrutan.

1. I resultatet väljer du **Citrix NetScaler**och lägger sedan till appen. Vänta några sekunder medan appen läggs till i din klientorganisation.

## <a name="configure-and-test-azure-ad-single-sign-on-for-citrix-netscaler"></a>Konfigurera och testa en azure AD-inloggning för Citrix NetScaler

Konfigurera och testa Azure AD SSO med Citrix NetScaler med hjälp av en testanvändare som heter **B.Simon**. För att SSO ska fungera måste du upprätta en länkrelation mellan en Azure AD-användare och den relaterade användaren i Citrix NetScaler.

Så här konfigurerar och testar du Azure AD SSO med Citrix NetScaler:

1. [Konfigurera Azure AD SSO](#configure-azure-ad-sso) – så att användarna kan använda den här funktionen.

    1. [Skapa en Azure AD-testanvändare](#create-an-azure-ad-test-user) – för att testa Azure AD SSO med B.Simon.

    1. [Tilldela Azure AD-testanvändaren](#assign-the-azure-ad-test-user) - så att B.Simon kan använda Azure AD SSO.

1. [Konfigurera Citrix NetScaler SSO](#configure-citrix-netscaler-sso) - för att konfigurera SSO-inställningarna på programsidan.

    * [Skapa en Citrix NetScaler-testanvändare](#create-a-citrix-netscaler-test-user) – om du vill ha en motsvarighet till B.Simon i Citrix NetScaler som är länkad till Azure AD-representationen av användaren.

1. [Testa SSO](#test-sso) - för att kontrollera om konfigurationen fungerar.

## <a name="configure-azure-ad-sso"></a>Konfigurera Azure AD SSO

Så här aktiverar du Azure AD SSO med hjälp av Azure-portalen:

1. I [Azure-portalen](https://portal.azure.com/)väljer du **Enkel inloggning**i programintegreringsfönstret **i Citrix NetScaler** under **Hantera**.

1. Välj **SAML**i fönstret **Välj en enskild inloggningsmetod** .

1. I fönstret Konfigurera enkel inloggning med SAML väljer du ikonen **Redigera** penna för **grundläggande SAML-konfiguration** för att redigera inställningarna. **Set up Single Sign-On with SAML**

   ![Redigera grundläggande SAML-konfiguration](common/edit-urls.png)

1. Konfigurera programmet i **IDP-initierat** läge i avsnittet **Grundläggande SAML-konfiguration:**

    1. Ange en URL med följande mönster i textrutan **Identifierare:**`https://<Your FQDN>`

    1. Ange en URL med följande mönster i textrutan **Svara** url:`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

1. Om du vill konfigurera programmet i **SP-initierat** läge väljer du **Ange ytterligare webbadresser** och slutför följande steg:

    * Ange en URL med följande mönster i textrutan **Sign-on-URL:**`https://<Your FQDN>/CitrixAuthService/AuthService.asmx`

    > [!NOTE]
    > * Webbadresserna som används i det här avsnittet är inte verkliga värden. Uppdatera dessa värden med de faktiska värdena för identifierare, svars-URL och inloggnings-URL. Kontakta [Citrix NetScalers kundsupportteam](https://www.citrix.com/contact/technical-support.html) för att få dessa värden. Du kan även se mönstren som visas i avsnittet **Grundläggande SAML-konfiguration** i Azure-portalen.
    > * För att konfigurera SSO måste webbadresserna vara tillgängliga från offentliga webbplatser. Du måste aktivera brandväggen eller andra säkerhetsinställningar på Citrix NetScaler-sidan för att kunna skriva in Token på den konfigurerade URL:en.

1. Kopiera **URL:en** i Anteckningar i FÖNSTRET KONFIGURERA enkel inloggning med SAML i fönstret **SAML-signeringscertifikat** för Url till **appfederationsmetadata**och spara den i Anteckningar.

    ![Länk för nedladdning av certifikatet](common/certificatebase64.png)

1. Citrix NetScaler-programmet förväntar sig att SAML-påståenden är i ett visst format, vilket kräver att du lägger till anpassade attributmappningar i konfigurationen av SAML-tokenattribut. I följande skärmbild visas listan över standardattribut. Markera ikonen **Redigera** och ändra attributmappningarna.

    ![Redigera SAML-attributmappningen](common/edit-attribute.png)

1. Citrix NetScaler-programmet förväntar sig också att ytterligare några attribut skickas tillbaka i SAML-svaret. I dialogrutan **Användarattribut** under **Användaranspråk**utför du följande steg för att lägga till SAML-tokenattributen som visas i tabellen:

    | Namn | Källattribut|
    | ---------------| --------------- |
    | mySecretID  | user.userprincipalname |
    
    1. Välj **Lägg till nytt anspråk** för att öppna dialogrutan **Hantera användaranspråk**.

    1. I textrutan **Namn** anger du det attributnamn som visas för den raden.

    1. Lämna **Namnrymd** tom.

    1. För **Attribut**väljer du **Källa**.

    1. I **attributlistan Källa** anger du det attributvärde som visas för den raden.

    1. Välj **OK**.

    1. Välj **Spara**.

1. I avsnittet **Konfigurera Citrix NetScaler kopierar** du relevanta webbadresser baserat på dina behov.

    ![Kopiera konfigurations-URL:er](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Skapa en Azure AD-testanvändare

I det här avsnittet ska du skapa en testanvändare i Azure-portalen som heter B.Simon.

1. På den vänstra menyn i Azure-portalen väljer du **Azure Active Directory**, väljer **Användare**och väljer sedan **Alla användare**.

1. Välj **Ny användare** högst upp i fönstret.

1. Gör så här i **Användaregenskaper:**

   1. För **Namn** `B.Simon`anger du .  

   1. För **Användarnamn**anger _username@companydomain.extension_du . Till exempel `B.Simon@contoso.com`.

   1. Markera kryssrutan **Visa lösenord** och skriv sedan ned eller kopiera värdet som visas i **Lösenord**.

   1. Välj **Skapa**.

### <a name="assign-the-azure-ad-test-user"></a>Tilldela Azure AD-testanvändaren

I det här avsnittet kan du aktivera användaren B.Simon att använda Azure SSO genom att ge användaren åtkomst till Citrix NetScaler.

1. I Azure-portalen väljer du **Enterprise Applications**och väljer sedan **Alla program**.

1. Välj **Citrix NetScaler**i programlistan .

1. Välj **Användare och grupper**under **Hantera**i appöversikten .

   ![Länken ”Användare och grupper”](common/users-groups-blade.png)

1. Välj **Lägg till användare**. Välj sedan **Användare och grupper**i dialogrutan Lägg till **tilldelning** .

    ![Länken Lägg till användare](common/add-assign-user.png)

1. Välj **B.Simon** i listan **Användare** **och grupper.** Välj **Välj**.

1. Om du förväntar dig något rollvärde i SAML-påståendet väljer du den relevanta rollen för användaren i listan i dialogrutan **Välj roll** och väljer sedan **Välj**.

1. I dialogrutan **Lägg till tilldelning** väljer du **Tilldela**.

## <a name="configure-citrix-netscaler-sso"></a>Konfigurera Citrix NetScaler SSO

Välj en länk för steg för den typ av autentisering som du vill konfigurera:

- [Konfigurera Citrix NetScaler SSO för rubrikbaserad autentisering](#publish-the-web-server)

- [Konfigurera Citrix NetScaler SSO för Kerberos-baserad autentisering](citrix-netscaler-tutorial.md#publish-the-web-server)

### <a name="publish-the-web-server"></a>Publicera webbservern 

Så här skapar du en virtuell server:

1. Välj > **Trafikhanteringsbelastningsutjämningstjänster** > **Services**. **Traffic Management**
    
1. Välj **Lägg till**.

    ![Konfiguration av Citrix NetScaler - fönstret Tjänster](./media/header-citrix-netscaler-tutorial/web01.png)

1. Ange följande värden för webbservern som kör programmen:

   * **Tjänstnamn**
   * **Server IP/ befintlig server**
   * **Protokollet**
   * **Port**

     ![Konfigurationsfönstret Citrix NetScaler](./media/header-citrix-netscaler-tutorial/web01.png)

### <a name="configure-the-load-balancer"></a>Konfigurera belastningsutjämnaren

Så här konfigurerar du belastningsutjämnaren:

1. Gå till Virtuella**servrar**för > **trafikhanteringsbelastningsutjämning** > . **Traffic Management**

1. Välj **Lägg till**.

1. Ange följande värden enligt beskrivningen i följande skärmbild:

    * **Namn**
    * **Protokollet**
    * **IP-adress**
    * **Port**

1. Välj **OK**.

    ![Citrix NetScaler-konfiguration - Fönstret Grundläggande inställningar](./media/header-citrix-netscaler-tutorial/load01.png)

### <a name="bind-the-virtual-server"></a>Bind den virtuella servern

Så här binder du belastningsutjämnaren med den virtuella servern:

1. I fönstret **Tjänster och tjänstgrupper** väljer du **Ingen belastningsutjämning av virtual server-tjänstbindning**.

   ![Citrix NetScaler-konfiguration – bindningsfönstret för virtuell servertjänst för belastningsutjämning](./media/header-citrix-netscaler-tutorial/bind01.png)

1. Kontrollera inställningarna som visas i följande skärmbild och välj sedan **Stäng**.

   ![Citrix NetScaler-konfiguration - Verifiera bindningen av virtuella servertjänster](./media/header-citrix-netscaler-tutorial/bind02.png)

### <a name="bind-the-certificate"></a>Bind certifikatet

Om du vill publicera den här tjänsten som SSL binder du servercertifikatet och testar sedan programmet:

1. Under **Certifikat**väljer du **Inget servercertifikat**.

   ![Citrix NetScaler-konfiguration – fönstret Servercertifikat](./media/header-citrix-netscaler-tutorial/bind03.png)

1. Kontrollera inställningarna som visas i följande skärmbild och välj sedan **Stäng**.

   ![Citrix NetScaler-konfiguration - Verifiera certifikatet](./media/header-citrix-netscaler-tutorial/bind04.png)

## <a name="citrix-adc-saml-profile"></a>Citrix ADC SAML-profil

Så här konfigurerar du Citrix ADC SAML-profilen:

### <a name="create-an-authentication-policy"></a>Skapa en autentiseringsprincip

Så här skapar du en autentiseringsprincip:

1. Gå till **säkerhet** > **AAA – Autentiseringsprinciper för** > **autentiseringsprinciper** >  > **för programtrafikprinciper**.**Authentication Policies**

1. Välj **Lägg till**.

1. Ange eller välj följande värden i fönstret **Skapa autentiseringsprincip:**

    * **Namn**: Ange ett namn för autentiseringsprincipen.
    * **Åtgärd**: Ange **SAML**och välj sedan **Lägg till**.
    * **Uttryck**: Ange **sant**.     
    
    ![Citrix NetScaler-konfiguration - Skapa autentiseringsprincipfönster](./media/header-citrix-netscaler-tutorial/policy01.png)

1. Välj **Skapa**.

### <a name="create-an-authentication-saml-server"></a>Skapa en SAML-autentiseringsserver

Om du vill skapa en SAML-autentiseringsserver går du till fönstret **Skapa autentiseringsSAML-server** och utför sedan följande steg:

1. För **Namn**anger du ett namn för SAML-servern för autentisering.

1. Under **Exportera SAML-metadata:**

   1. Markera kryssrutan **Importera metadata.**

   1. Ange url:en för federationsmetadata från Azure SAML-användargränssnittet som du kopierade tidigare.
    
1. För **Utfärdarens namn**anger du relevant URL.

1. Välj **Skapa**.

![Citrix NetScaler-konfiguration - Skapa autentiserings-SAML-serverfönster](./media/header-citrix-netscaler-tutorial/server01.png)

### <a name="create-an-authentication-virtual-server"></a>Skapa en virtuell autentiseringsserver

Så här skapar du en virtuell autentiseringsserver:

1.  Gå till **Säkerhet** > **AAA - Program Traffic** > **Principer** > **Autentisering** > **Autentisering virtuella servrar**.

1.  Välj **Lägg till**och slutför sedan följande:

    1. För **Namn**anger du ett namn för den virtuella autentiseringsservern.

    1. Markera kryssrutan **Ej adresserbar.**

    1. För **Protokoll**väljer du **SSL**.

    1. Välj **OK**.

    ![Citrix NetScaler-konfiguration – fönstret Virtuell autentiseringsserver](./media/header-citrix-netscaler-tutorial/server02.png)
    
### <a name="configure-the-authentication-virtual-server-to-use-azure-ad"></a>Konfigurera den virtuella autentiseringsservern så att den använder Azure AD

Ändra två avsnitt för den virtuella autentiseringsservern:

1.  Välj **Ingen autentiseringsprincip**i fönstret **Avancerade autentiseringsprinciper** .

    ![Citrix NetScaler-konfiguration – fönstret Avancerade autentiseringsprinciper](./media/header-citrix-netscaler-tutorial/virtual01.png)

1. Markera autentiseringsprincipen i fönstret **Principbindning** och välj sedan **Bind**.

    ![Citrix NetScaler-konfiguration - fönstret Principbindning](./media/header-citrix-netscaler-tutorial/virtual02.png)

1. Välj **Ingen virtuell server för belastningsutjämning**i fönstret **Formulärbaserade virtuella servrar** .

    ![Konfiguration av Citrix NetScaler – fönstret Formulärbaserade virtuella servrar](./media/header-citrix-netscaler-tutorial/virtual03.png)

1. För **Autentisering FQDN**anger du ett fullständigt kvalificerat domännamn (FQDN) (obligatoriskt).

1. Välj den virtuella server för belastningsutjämning som du vill skydda med Azure AD-autentisering.

1. Välj **Bindning**.

    ![Citrix NetScaler-konfiguration – organisatörsfönstret för virtuell virtuell server som balanserar belastning](./media/header-citrix-netscaler-tutorial/virtual04.png)

    > [!NOTE]
    > Var noga med att välja **Klar** i fönstret Konfiguration av **virtuell autentisering.**

1. Om du vill verifiera dina ändringar går du till program-URL:en i en webbläsare. Du bör se din klient inloggningssida i stället för oautentiserade åtkomst som du skulle ha sett tidigare.

    ![Citrix NetScaler-konfiguration - En inloggningssida i en webbläsare](./media/header-citrix-netscaler-tutorial/virtual05.png)

## <a name="configure-citrix-netscaler-sso-for-header-based-authentication"></a>Konfigurera Citrix NetScaler SSO för rubrikbaserad autentisering

### <a name="configure-citrix-adc"></a>Konfigurera Citrix ADC

Om du vill konfigurera Citrix ADC för rubrikbaserad autentisering fyller du i följande avsnitt.

#### <a name="create-a-rewrite-action"></a>Skapa en skrivåtgärd

1. Gå till **AppExpert** > **Omskrivningsåtgärder** > **Rewrite Actions**.
 
    ![Konfiguration av Citrix NetScaler - Fönstret Skriv om åtgärder](./media/header-citrix-netscaler-tutorial/header01.png)

1.  Välj **Lägg till**och slutför sedan följande:

    1. För **Namn**anger du ett namn för omskrivningsåtgärden.

    1. För **Text**anger **du INSERT_HTTP_HEADER**.

    1. För **Sidhuvudnamn**anger du ett rubriknamn (i det här exemplet använder vi _SecretID_).

    1. För **Uttryck**anger du **aaa. Användaren. ATTRIBUT("mySecretID")**, där **mySecretID** är Azure AD SAML-anspråk som skickades till Citrix ADC.

    1. Välj **Skapa**.

    ![Konfiguration av Citrix NetScaler - Fönstret Skapa omskrivningsåtgärd](./media/header-citrix-netscaler-tutorial/header02.png)
 
#### <a name="create-a-rewrite-policy"></a>Skapa en omskrivningsprincip

1.  Gå till **AppExpert** > **Omskrivningsprinciper****Rewrite** > .
 
    ![Konfiguration av Citrix NetScaler - Rutan Skriv om principer](./media/header-citrix-netscaler-tutorial/header03.png)

1.  Välj **Lägg till**och slutför sedan följande:

    1. För **Namn**anger du ett namn för omskrivningsprincipen.

    1. För **Åtgärd**väljer du den omskrivningsåtgärd som du skapade i föregående avsnitt.

    1. För **Uttryck**anger du **sant**.

    1. Välj **Skapa**.

    ![Konfiguration av Citrix NetScaler - Fönstret Skapa omskrivningsprincip](./media/header-citrix-netscaler-tutorial/header04.png)

### <a name="bind-a-rewrite-policy-to-a-virtual-server"></a>Binda en omskrivningsprincip till en virtuell server

Så här binder du en omskrivningsprincip till en virtuell server med hjälp av det grafiska gränssnittet:

1. Gå till Virtuella**servrar**för > **trafikhanteringsbelastningsutjämning** > . **Traffic Management**

1. I listan över virtuella servrar väljer du den virtuella server som du vill binda omskrivningsprincipen till och väljer sedan **Öppna**.

1. Välj **Principer**under **Avancerade inställningar**i fönstret **Lastutjämning virtuell server** . Alla principer som är konfigurerade för NetScaler-instansen visas i listan.
 
    ![Citrix NetScaler-konfiguration – fönstret Virtuell server för belastningsutjämning](./media/header-citrix-netscaler-tutorial/header05.png)

    ![Citrix NetScaler-konfiguration – fönstret Virtuell server för belastningsutjämning](./media/header-citrix-netscaler-tutorial/header06.png)

1.  Markera kryssrutan bredvid namnet på den princip som du vill binda till den här virtuella servern.
 
    ![Citrix NetScaler-konfiguration – organisatör av trafikprincipbindning av belastningsutjämning för virtuell server](./media/header-citrix-netscaler-tutorial/header08.png)

1. I dialogrutan **Välj typ:**

    1. För **Välj princip**väljer du **Trafik**.

    1. För **Välj typ**väljer du **Begär**.

    ![Konfiguration av Citrix NetScaler – dialogrutan Principer](./media/header-citrix-netscaler-tutorial/header07.png)

1.  Välj **OK**. Ett meddelande i statusfältet anger att principen har konfigurerats.

### <a name="modify-the-saml-server-to-extract-attributes-from-a-claim"></a>Ändra SAML-servern för att extrahera attribut från ett anspråk

1.  Gå till **Säkerhet** > **AAA - Program** > **trafikprinciper** > **Autentisering** > **Avancerade principer** > **Åtgärder** > **servrar**.

1.  Välj lämplig autentiseringsSAML-server för programmet.
 
    ![Citrix NetScaler-konfiguration – konfigurera autentiserings-SAML-serverfönster](./media/header-citrix-netscaler-tutorial/header09.png)

1. I **attributen smärta** anger du de SAML-attribut som du vill extrahera, avgränsade med kommatecken. I vårt exempel anger `mySecretID`vi attributet .
 
    ![Konfiguration av Citrix NetScaler - fönstret Attribut](./media/header-citrix-netscaler-tutorial/header10.png)

1. Om du vill verifiera åtkomsten söker du efter SAML-attributet under **Headers Collection**i URL:en i en webbläsare .

    ![Citrix NetScaler-konfiguration - Headers Collection på WEBBADRESSEN](./media/header-citrix-netscaler-tutorial/header11.png)

### <a name="create-a-citrix-netscaler-test-user"></a>Skapa en Citrix NetScaler-testanvändare

I det här avsnittet skapas en användare som heter B.Simon i Citrix NetScaler. Citrix NetScaler stöder just-in-time-användaretablering, vilket är aktiverat som standard. Det finns ingen åtgärd för dig att vidta i det här avsnittet. Om en användare inte redan finns i Citrix NetScaler skapas en ny efter autentisering.

> [!NOTE]
> Om du behöver skapa en användare manuellt kontaktar du [Citrix NetScalers supportteam](https://www.citrix.com/contact/technical-support.html).

## <a name="test-sso"></a>Testa SSO 

I det här avsnittet testar du din Azure AD SSO-konfiguration med hjälp av åtkomstpanelen.

När du väljer Citrix NetScaler-panelen på åtkomstpanelen ska du automatiskt loggas in på den Citrix NetScaler som du ställer in SSO för. Mer information om åtkomstpanelen finns i [introduktionen till åtkomstpanelen](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>Ytterligare resurser

- [Lista över självstudier om hur du integrerar SaaS-appar med Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Vad är programåtkomst och enkel inloggning med Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Vad är villkorsstyrd åtkomst i Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Prova Citrix NetScaler med Azure AD](https://aad.portal.azure.com/)

- [Konfigurera Citrix NetScaler enkel inloggning för Kerberos-baserad autentisering](citrix-netscaler-tutorial.md)
