---
title: "Vad är nytt i Enterprise programhantering i Azure Active Directory | Microsoft Docs"
description: "Lär dig vad är nytt i Enterprise programhantering i Azure Active Directory."
services: active-directory
documentationcenter: 
author: ajamess
manager: mtillman
editor: 
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: 949eae51c469205489153c271a7c20fcc843ad46
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2018
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Vad är nytt i Enterprise programhantering i Azure Active Directory 

Azure Active Directory (AD Azure) har förbättrats enterprise hantera program, med nya funktioner och möjligheter att hantera appar enklare och effektivare.

Här följer några av förbättringarna för Azure AD i den [Azure-portalen](https://portal.azure.com).

- En förbättrad programgalleriet användarupplevelse med en förenklad tillämpning skapa modell och stöd för alla programtyper som du ofta. 
- En helt ny Snabbstart-upplevelse som kan hjälpa dig att komma igång med en pilot för programmet. 
- Konfigurera självbetjäning principer med bara några klickningar. 
- Förbättringar av application proxy enkel inloggning konfiguration och sätta egna program-upplevelser, så att du kan få mer gjort än innan.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Förbättringar av Azure Active Directory-Programgalleriet

Lägg till program, oavsett om de är från den [programgalleriet](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery), anpassade program som du utöka till molnet eller nya program som du utvecklar.  Du kan komma igång med den nya upplevelsen genom att klicka på **Lägg till** på den **företagsprogram** översikt eller **alla program** blad.
 
  ![Lägga till ett program](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

En gång i galleriet ser du våra aktuella program som stöder användaretablering visas främre och center.  Du kan bläddra i alla typer av olika kategorier till de program som du är intresserad mer detaljerat eller du kan använda sökupplevelsen för att snabbt hitta de program som du vill integrera.

  ![Programgalleriet](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Lägga till anpassade program från en plats

Förutom att lägga till redan integrerade program från galleriet är alla anpassade programkonfigurationen inträffar som du har använt till i den klassiska hanteringsportalen nu möjligt i den nya portalen. Om du vill utvidga ett program från lokalt med programproxyn integrera ditt eget lösenord eller en extern SSO-program, eller skapar en helt ny program använda registret för programmet, kan du till den från den här en enda plats.

  ![Lägga till egna program](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Börja lägga till egna program**:

1. Klicka på den **lägga till en egen länk** överst i galleriet för programmet. 
2. Du ser två alternativ framför du: **distribuera ett befintligt program** eller **utveckla ett nytt program**. Läs vidare för att lära dig skillnaden mellan de två alternativen och hur de används.

### <a name="deploying-existing-applications"></a>Distribuera befintliga program

1. Om du har ett program som redan kör och bara vill integrera den med Azure AD för enkel inloggning på eller etablering, Välj den **distribuera ett befintligt program** alternativet. Välj ett namn för programmet, klickar du på **Lägg till**.
2. Klart! I stället för att behöva känna till hur detaljerna om ditt program direkt, kan du nu ställa in hur programmet fungerar genom att navigera genom den vänstra menyn och konfigurera programmet så att dina önskemål när som helst.

  ![Lägga till ett befintligt program med ett enda klick](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Utveckla nya program

1. Om du utvecklar ett nytt program är det enkelt för dig att få till registret för programmet från galleriet:
2. Klicka på den **lägga till egna** alternativet från galleriet program, Välj den **utveckla ett befintligt program** val och du ser en snabb länk till Lägg till programupplevelse.

  ![Lägga till en nyligen utvecklat program i ett par klick](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>När du lägger till ett program använda registret för programmet, visas den visas i listan över företagsprogram där du kommer att kunna konfigurera enkel inloggning och hantera principer för åtkomst för det nya programmet.

  ![Hantera åtkomst till det nya programmet under företagsprogram](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quick-start-get-going-with-your-new-application-right-away"></a>Snabbstartsguide: komma igång med ditt nya program direkt 

När du har lagt till ett program, om det vara förintegrerade eller egna app har vi skapat en skräddarsydd upplevelse Snabbkurs att få med dig snabbt grundat i den nya upplevelsen för program. Om du följer varje alternativ systematiskt vi vägleder dig genom Användargränssnittet och hur du kan komma igång med en pilot för det nya programmet så snabbt som möjligt. 
 
  ![De nya program snabb start upplevelse](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Du kan besöka den nya Snabbstart upplevelsen när som helst och för alla program genom att klicka på **Snabbstart** från den vänstra navigeringsmenyn i programmet.


## <a name="updated-application-proxy-configuration"></a>Uppdaterade program proxykonfiguration
Nu ska vi Säg något av de nya program som du har lagt till körs i din lokala miljö och du vill integrera med Azure AD.  En kall Nyheter på den nya konfigurationen upplevelsen i den nya Azure AD-portalen är att dela upp programmets inloggning-läge från dess proxykonfiguration för programmet, du kan nu enkelt exponera lösenord SSO eller federerade program som körs i företagsnätverket direkt till molnet, utan att behöva skapa flera instanser av programmet.

Utöver detta, kan du nu också konfigurera något nytt program du har lagt till för användning med Azure AD Application Proxy direkt från den nya portalen, inklusive program som stöder intern upplevelser för Windows-autentisering.

  ![Konfigurera ett program att använda integrerad Windows-autentisering inloggning alternativet](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 

Du kommer igång med att konfigurera en intern Windows-autentisering program med Application Proxy:
1. Klicka på enkel inloggning navigeringsobjektet och välj **integrerad Windows-autentisering** i bladet inställningar inloggning och konfigurera inställningarna enligt dina önskemål.
2. Utöver att stödja dessa nya autentiseringslägen, kan du även överföra certifikat från anpassade domäner att stödja program som körs på säkra slutpunkter inom din organisation.  
 
   ![Överföra ett certifikat som ska användas med Application Proxy](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

3. Om du vill överföra ett nytt certifikat för din favorit lokalt program klickar du på den **programproxy** från den vänstra navigeringsmenyn, klickar du på den **certifikat** selector och ladda upp en certifikatfil som vi kan använda för att kryptera förfrågningar från våra molnslutpunkt för ditt program.

## <a name="advanced-federated-single-sign-on-configuration"></a>Avancerad federerad enkel inloggning konfiguration

För de som du använder idag federerade program, finns det många nya funktioner i bladet SAML-baserad inloggning konfiguration. Börja med, kan nu du helt anpassa, lägga till, ta bort och mappa befintliga användarattribut som anspråk i SAML-token.
 
  ![Anpassa attribut för SAML-token användaren skickades till externa program](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)


Kontrollera att ut den nya federerad enkel inloggning konfiguration:
1. Öppna en federerade program **enkel inloggning** bladet från den vänstra navigeringsmenyn och kontrollera att den '*SAML-baserade inloggning** läge är markerad. 
2. En gång, aktivera kryssrutan under den **användarattribut** rubrik att ändra alla attribut som ingår i en SAML-token som skickades till programmet.

Du kan också skapa, förnyelse, och hantera certifikat för federerad enkel inloggning, samt redigera som hämtar ett meddelande när certifikatet upphör snart att gälla. Du ser dessa nya alternativ under den **certifikat** rubrik i samma enkel inloggning bladet.
 
  ![Skapa ett nytt certifikat, anpassa e-postmeddelanden upphör att gälla och alternativ för Certifikatsignering](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-paramenter"></a>Relay tillstånd paramenter
Slutligen har vi också utökade uppsättning URL för SAML-parametrar som vi stöder för att inkludera den **Relay tillstånd parametern**, vilket är den sida som användarna hamnar på inuti en externa program när inloggningen har slutförts. Detta är mycket användbar inställningen för att konfigurera om du vill skicka dina användare till en specifik plats i programmet för att få dem går snabbt.

  ![Parametern SAML Relay tillstånd](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Ange parametern state relay**:

1. Aktivera den **visa avancerade inställningar för URL: en** kryssrutan under den **domän och URL: er** rubriken på den enkel inloggning på bladet för konfigurationen. 
2. När du gör det visas en uppsättning nya URL: en inkommande rutor visas som gör att du kan ange den här och andra, SAML-URL-inställningar.

## <a name="bring-your-own-password-sso-applications"></a>Ta med ditt eget lösenord SSO-program

Vi vet att inte alla program har stöd för identitetsfederation direkt ur lådan. Till exempel kanske har en av de nya program som du har lagt till en anpassad inloggningsskärm som användarna använda ett användarnamn och lösenord för att logga in på. Du kan fortfarande integrera dessa typer av program med Azure AD med hjälp av vår **sätta egna program** funktionen som är nu tillgänglig för dig att konfigurera i den nya portalen.
 
  ![Integrera anpassade lösenord vaulting program med Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Checka ut funktionen Ta med egna program**:

1. När du ställer in enkel inloggning läge för ett nytt anpassat program som du har lagt till **lösenordsbaserade inloggning**, ange URL: en där programmet återgivningar dess inloggningssidan och på **spara**.  
2. När du gör det kan vi ska automatiskt skrapa URL: en för ett användarnamn och lösenord Inmatningsruta och du kan använda Azure AD för att säkert överföra lösenord för det aktuella programmet med hjälp av webbläsartillägget för åtkomst-panelen.

## <a name="configure-self-service-application-access"></a>Konfigurera självbetjäning programåtkomst

När du har lagt till många nya program, kanske vill du tillåta användarna att bläddra och lägga till dessa program till sina egna åtkomst paneler, utan att behöva bry dig du som administratör. Med den senaste versionen kan du nu konfigurera och hantera självbetjäning programåtkomst direkt från den nya portalen.

  ![Konfigurera självbetjäning programåtkomst lösenord SSO-program](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Konfigurera och hantera självbetjäning programåtkomst**:

1. Om du vill komma igång, kan du välja den **självbetjäning** alternativet från programmet har lämnat navigeringsmenyn och ange den **Tillåt användare att begära åtkomst till det här programmet?** att '**Ja**'. 
2. Detta gör att du konfigurerar som har tillåtelse att godkänna åtkomst till det här programmet och vilken grupp Självbetjäningsanvändare läggs. Dessutom, om programmet har konfigurerats för lösenord enkel inloggning på, får du också se ett annat alternativ som gör att du kan låta de godkännarna för att hantera de lösenord som tilldelats programmet.

##<a name="feedback"></a>Feedback

Vi hoppas att du vill med hjälp av den förbättrade Azure AD-upplevelse. Skriv ned feedback kommer! Publicera din feedback och förslag på förbättringar i den **administrationsportalen** avsnitt i vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Vi är glada om hur du skapar nya nya produkter varje dag, och använder din information i form och definiera vad vi bygga härnäst.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [hantera program med Azure Active Directory](active-directory-enable-sso-scenario.md).



