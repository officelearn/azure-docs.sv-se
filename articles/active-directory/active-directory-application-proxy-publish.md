<properties
    pageTitle="Publicera appar med Azure AD Application Proxy | Microsoft Azure"
    description="Publicera lokala program till molnet med Azure AD Application Proxy."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="06/01/2016"
    ms.author="kgremban"/>


# Publicera program med Azure AD Application Proxy


När du aktiverar Microsoft Azure Active Directory (AD) Application Proxy kan du publicera lokala program så att fjärranvändare kan komma åt dem utanför det privata nätverket.

Den här artikeln beskriver hur du publicerar program som körs i nätverket och hur du tillhandahåller säker fjärråtkomst utanför nätverket. Om du inte har konfigurerat Application Proxy eller installerat några anslutningsappar följer du stegen i [Aktivera Application Proxy på Azure-portalen](active-directory-application-proxy-enable.md) innan du fortsätter här.

Om det här är första gången du använder Azure AD Application Proxy rekommenderar vi att du testar anslutningsverktyget genom att publicera en webbplats från ditt privata nätverk innan du publicerar program.

> [AZURE.NOTE] Application Proxy är en funktion som bara är tillgänglig om du har uppgraderat till Premium- eller Basic-versionen av Azure Active Directory. Mer information finns i [Azure Active Directory-versioner](active-directory-editions.md).

## Publicera en app med hjälp av guiden

1. Logga in som administratör på [den klassiska Azure-portalen](https://manage.windowsazure.com/).
2. Gå till Active Directory och välj den katalog där du aktiverade Application Proxy.

    ![Active Directory – ikon](./media/active-directory-application-proxy-publish/ad_icon.png)

3. Klicka på fliken **Program** och sedan på knappen **Lägg till** längst ned på skärmen

    ![Lägga till ett program](./media/active-directory-application-proxy-publish/aad_appproxy_selectdirectory.png)

4. Välj **Publicera ett program som ska vara tillgängligt utanför ditt nätverk**.

    ![Publicera ett program som ska vara tillgängligt utanför ditt nätverk](./media/active-directory-application-proxy-publish/aad_appproxy_addapp.png)

5. Ange följande information om ditt program:

    - **Namn**: Programmets användarvänliga namn. Det måste vara unikt i din katalog.
    - **Intern URL**: Adressen som Application Proxy Connector använder för att komma åt programmet inifrån ditt privata nätverk. Du kan ange en specifik sökväg på backend-servern som du vill publicera, medan resten av servern är opublicerad. På så sätt kan du publicera olika platser på samma server och ge varje plats sitt eget namn och sina egna åtkomstregler.
    - **Förautentiseringsmetod**: Hur Application Proxy ska verifiera användare innan de får åtkomst till ditt program. Välj något av alternativen i den nedrullningsbara menyn.

        - Azure Active Directory: Application Proxy dirigerar användarna till en inloggning med Azure AD, som autentiserar deras katalog- och programbehörigheter.
        - Genomströmning: Användarna behöver inte autentisera sig för att få åtkomst till programmet.

    ![Egenskaper för program](./media/active-directory-application-proxy-publish/aad_appproxy_appproperties.png)  

6. Slutför guiden genom att klicka på bockmarkeringen längst ned på skärmen. Nu har programmet definierats i Azure AD.


## Tilldela användare och grupper till programmet

För att dina användare ska få åtkomst till ditt publicerade program måste du tilldela dem antingen individuellt eller i grupper. För appar som kräver förautentisering ger detta behörighet att använda appen. För appar som inte kräver förautentisering behöver inte användarna behörighet men måste fortfarande tilldelas appen så att den visas i deras programlista.

1. När du har slutfört guiden Lägg till app visas sidan Snabbstart för ditt program. Du kan hantera vem som har tillgång till appen genom att välja **Användare och grupper**.

    ![Snabbstart för att tilldela användare för Application Proxy – skärmbild](./media/active-directory-application-proxy-publish/aad_appproxy_usersgroups.png)

2. Sök efter specifika grupper i katalogen, eller visa alla användare. Klicka på kryssmarkeringen för att visa resultatet.

    ![Söka efter grupper eller användare – skärmbild](./media/active-directory-application-proxy-publish/aad_appproxy_search.png)

2. Välj alla användare eller grupper som du vill tilldela den här appen och klicka på **Tilldela**. Du uppmanas att bekräfta åtgärden.

> [AZURE.NOTE] För appar med integrerad Windows-autentisering kan du endast tilldela användare och grupper som synkroniseras från din lokala Active Directory. Användare som loggar in med ett Microsoft-konto och gäster kan inte tilldelas appar som publiceras med Azure Active Directory Application Proxy. Se till att dina användare loggar in med autentiseringsuppgifter för samma domän som appen som du publicerar.


## Avancerad konfiguration

Du kan ändra publicerade appar eller konfigurera avancerade alternativ på sidan Konfigurera. På den här sidan kan du anpassa din app genom att ändra namnet eller genom att ladda upp en logotyp. Du kan också hantera åtkomstregler som förautentiseringsmetoden eller Multi-Factor Authentication.

![Avancerad konfiguration](./media/active-directory-application-proxy-publish/aad_appproxy_configure.png)


När du har publicerat program med hjälp av Azure Active Directory Application Proxy visas de i programlistan i Azure AD och du kan hantera dem där.

Om du inaktiverar Application Proxy-tjänster när du har publicerat program tas inte programmen bort, men de kan inte längre nås utanför ditt privata nätverk.

Om du vill visa ett program och förvissa dig om att det är tillgängligt dubbelklickar du på namnet på programmet. Om Application Proxy-tjänsten är inaktiverad och programmet inte är tillgängligt visas ett varningsmeddelande längst upp på skärmen.

Om du vill ta bort ett program markerar du programmet i listan och klickar sedan på **Ta bort**.

## Nästa steg

- [Publicera program med ditt domännamn](active-directory-application-proxy-custom-domains.md)
- [Aktivera enkel inloggning](active-directory-application-proxy-sso-using-kcd.md)
- [Aktivera villkorlig åtkomst](active-directory-application-proxy-conditional-access.md)
- [Arbeta med anspråksmedvetna program](active-directory-application-proxy-claims-aware-apps.md)

Läs mer om de senaste nyheterna och uppdateringarna i [bloggen om Application Proxy](http://blogs.technet.com/b/applicationproxyblog/)



<!--HONumber=Jun16_HO2-->


