<properties 
    pageTitle="Skydda molnresurser med Azure Multi-Factor Authentication och AD FS" 
    description="Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS i molnet." 
    services="multi-factor-authentication" 
    documentationCenter="" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtland"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="05/12/2016" 
    ms.author="billmath"/>

# Skydda molnresurser med Azure Multi-Factor Authentication och AD FS

Om din organisation är federerad med Azure Active Directory och du har resurser som används av Azure AD kan du använda Azure Multi-Factor Authentication eller Active Directory Federation Services för att skydda dessa resurser. Skydda dina resurser i Azure Active Directory med Azure Multi-Factor Authentication eller Active Directory Federation Services genom att följa stegen nedan.

## Om du vill skydda Azure AD gör med AD FS gör du så här: 



1. Följ anvisningarna i [Aktivera Multi-Factor-Authentication](active-directory/multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) för användare för att aktivera ett konto.
2. Konfigurera en anspråksregel genom att följa stegen nedan:

![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)

-   Starta AD FS-hanteringskonsolen.
-   Gå till Förlitande partsförtroenden och högerklicka på Förlitande partsförtroenden. Välj Redigera anspråksregler...
-   Klicka på Lägg till regel...
-   Välj Skicka anspråk med hjälp av en anpassad regel i listrutan och klicka på Nästa.
-   Ange ett namn för anspråksregeln.
-   Lägg till följande under Anpassad regel:


        => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");

    Motsvarande anspråk:

        <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
        <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
        </saml:Attribute>
- Klicka på OK. Klicka på Slutför. Stäng AD FS-hanteringskonsolen.

Användarna kan slutföra inloggningen med hjälp av den lokala metoden (t.ex. smartkort).

## Tillförlitliga IP-adresser för federerade användare
Tillförlitliga IP-adresser gör att administratörer kan kringgå multifaktorautentisering för specifika IP-adresser eller federerade användare som har förfrågningar som kommer inifrån det egna intranätet. Följande avsnitt beskriver hur du konfigurerar tillförlitliga IP-adresser för Azure Multi-Factor Authentication med federerade användare och hur du kringgår multifaktorautentisering när en begäran kommer inifrån en federerad användares intranät.  Du gör detta genom att konfigurera AD FS att släppa igenom eller filtrera en mall för inkommande anspråk med anspråkstypen I företagsnätverket.  I det här exemplet används Office 365 för våra förlitande partsförtroenden.

### Konfigurera anspråksreglerna för AD FS

Det första vi måste göra är att konfigurera AD FS-anspråken. Vi ska skapa två anspråksregler, en för anspråkstypen I företagsnätverket och ytterligare en som gör att våra användare förblir inloggade.

1. Öppna AD FS-hantering.
2. Välj Förlitande partsförtroenden till vänster.
3. Högerklicka på Microsoft Office 365-identitetsplattform i mitten och välj **Redigera anspråksregler**
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. För Utfärdande av transformeringsregler klickar du på **Lägg till regel.**
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. I guiden Lägg till anspråksregel för transformering väljer du Släpp igenom eller Filtrera ett inkommande anspråk i listrutan och klickar sedan på Nästa.
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. I rutan bredvid Anspråksregelns namn ger du regeln ett namn. Exempel: InsideCorpNet.
7. Välj I företagsnätverket i listrutan bredvid Typ av inkommande anspråk.
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Klicka på Slutför.
9. För Utfärdande av transformeringsregler klickar du på **Lägg till regel**
10. I guiden Lägg till anspråksregel för transformering väljer du Skicka anspråk med hjälp av en anpassad regel i listrutan och klickar sedan på Nästa.
11. I rutan under Anspråksregelns namn skriver du Håll användarna inloggade.
12. I rutan Anpassad regel anger du:
        
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Klicka på **Slutför**.
14. Klicka på **Använd**.
15. Klicka på **OK**.
16. Stäng AD FS-hantering.



### Konfigurera tillförlitliga IP-adresser med federerade användare i Azure Multi-Factor Authentication
Nu när anspråken är på plats kan vi konfigurera tillförlitliga IP-adresser.

1. Logga in på Azure-hanteringsportalen.
2. Klicka på Active Directory till vänster.
3. Under Katalog klickar du på den katalog som du vill konfigurera tillförlitliga IP-adresser för.
4. Klicka på Konfigurera för den katalog som du har valt.
5. Klicka på Hantera tjänstinställningar i avsnittet Multi-Factor Authentication.
6. På sidan Tjänstinställningar väljer du **För förfrågningar från externa användare som kommer från mitt intranät.**
![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Klicka på Spara.
8. Klicka på Stäng när uppdateringarna har tillämpats.


Klart! I det här läget behöver federerade Office 365-användare endast  använda MFA när ett anspråk kommer utifrån företagets intranät.









<!--HONumber=jun16_HO2-->


