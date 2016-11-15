---
title: Skydda molnresurser med Azure MFA och AD FS
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS i molnet."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 0927fc67-8090-4fdd-913a-b3cfed3fbe77
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 20aefd2c94bca03653f11a91c5ade173cb6da9bf


---
# <a name="securing-cloud-resources-with-azure-multifactor-authentication-and-ad-fs"></a>Skydda molnresurser med Azure Multi-Factor Authentication och AD FS
Om din organisation är federerad med Azure Active Directory använder du Azure Multi-Factor Authentication eller Active Directory Federation Services för att skydda dessa resurser. Skydda dina resurser i Azure Active Directory med Azure Multi-Factor Authentication eller Active Directory Federation Services genom att använda följa steg.

## <a name="secure-azure-ad-resources-using-ad-fs"></a>Skydda Azure AD-resurser med hjälp av AD FS
För att skydda din molnresurs ska du först aktivera ett konto för användare och sedan skapa en anspråksregel. Följ dessa steg:

1. Följ anvisningarna i [Aktivera Multi-Factor-Authentication för användare](multi-factor-authentication-get-started-cloud.md#turn-on-multi-factor-authentication-for-users) för att aktivera ett konto.
2. Starta AD FS-hanteringskonsolen.
   ![Moln](./media/multi-factor-authentication-get-started-adfs-cloud/adfs1.png)
3. Gå till **Förlitande partsförtroenden** och högerklicka på Förlitande partsförtroenden. Välj **Redigera anspråksregler...**
4. Klicka på **Lägg till regel...**
5. Välj **Skicka anspråk med hjälp av en anpassad regel** i listrutan och klicka på **Nästa**.
6. Ange ett namn för anspråksregeln.
7. Lägg till följande text under Anpassad regel:
   
    ```
    => issue(Type = "http://schemas.microsoft.com/claims/authnmethodsreferences", Value = "http://schemas.microsoft.com/claims/multipleauthn");
    ```
   
    Motsvarande anspråk:
   
    ```
    <saml:Attribute AttributeName="authnmethodsreferences" AttributeNamespace="http://schemas.microsoft.com/claims">
    <saml:AttributeValue>http://schemas.microsoft.com/claims/multipleauthn</saml:AttributeValue>
    </saml:Attribute>
    ```
8. Klicka på **OK** sedan **Slutför**. Stäng AD FS-hanteringskonsolen.

Användarna kan slutföra inloggningen med hjälp av den lokala metoden (t.ex. smartkort).

## <a name="trusted-ips-for-federated-users"></a>Tillförlitliga IP-adresser för federerade användare
Tillförlitliga IP-adresser gör att administratörer kan kringgå tvåstegsverifiering för specifika IP-adresser eller federerade användare som har förfrågningar som kommer inifrån det egna intranätet. Följande avsnitt beskriver hur du konfigurerar tillförlitliga IP-adresser för Azure Multi-Factor Authentication med federerade användare och hur du kringgår tvåstegsverifiering när en begäran kommer inifrån en federerad användares intranät. Du gör detta genom att konfigurera AD FS att släppa igenom eller filtrera en mall för inkommande anspråk med anspråkstypen I företagsnätverket.

I det här exemplet används Office 365 för våra förlitande partsförtroenden.

### <a name="configure-the-ad-fs-claims-rules"></a>Konfigurera anspråksreglerna för AD FS
Det första vi måste göra är att konfigurera AD FS-anspråken. Vi ska skapa två anspråksregler, en för anspråkstypen inom företagsnätverket och ytterligare en som gör att våra användare förblir inloggade.

1. Öppna AD FS-hantering.
2. Välj **Förlitande partsförtroenden** till vänster.
3. Högerklicka på **Microsoft Office 365-identitetsplattform** och välj **Redigera anspråksregler...**
   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip1.png)
4. För Utfärdande av transformeringsregler klickar du på **Lägg till regel.**
   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip2.png)
5. I guiden Lägg till anspråksregel för transformering väljer du **Släpp igenom eller Filtrera ett inkommande anspråk** i listrutan och klickar sedan på **Nästa**.
   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip3.png)
6. I rutan bredvid Anspråksregelns namn ger du regeln ett namn. Exempel: InsideCorpNet.
7. Välj **Inom företagsnätverket** i listrutan bredvid Typ av inkommande anspråk.
   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip4.png)
8. Klicka på **Slutför**.
9. För Utfärdande av transformeringsregler klickar du på **Lägg till regel**.
10. I guiden Lägg till anspråksregel för transformering väljer du **Skicka anspråk med hjälp av en anpassad regel** i listrutan och klickar sedan på **Nästa**.
11. I rutan under Anspråksregelns namn skriver du *Håll användarna inloggade*.
12. I rutan Anpassad regel anger du:
    
        c:[Type == "http://schemas.microsoft.com/2014/03/psso"]
            => issue(claim = c);
    ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip5.png)
13. Klicka på **Slutför**.
14. Klicka på **Använd**.
15. Klicka på **OK**.
16. Stäng AD FS-hantering.

### <a name="configure-azure-multifactor-authentication-trusted-ips-with-federated-users"></a>Konfigurera tillförlitliga IP-adresser med federerade användare i Azure Multi-Factor Authentication
När nu anspråken är på plats kan vi konfigurera tillförlitliga IP-adresser.

1. Logga in på den [klassiska Azure-portalen](https://manage.windowsazure.com).
2. Klicka på **Active Directory** till vänster.
3. Under Katalog väljer du den katalog där du vill ställa in tillförlitliga IP-adresser.
4. Klicka på **Konfigurera** för den katalog som du har valt.
5. Klicka på **Hantera tjänstinställningar** i avsnittet Multi-Factor Authentication.
6. På sidan Tjänstinställningar väljer du **Hoppa över multi-factor authentication för förfrågningar från federerade användare som kommer från mitt intranät.**
   ![Molnet](./media/multi-factor-authentication-get-started-adfs-cloud/trustedip6.png)
7. Klicka på **Spara**.
8. Klicka på **Stäng**när uppdateringarna har tillämpats.

Klart! I det här läget behöver federerade Office 365-användare endast  använda MFA när ett anspråk kommer utifrån företagets intranät.




<!--HONumber=Nov16_HO2-->


