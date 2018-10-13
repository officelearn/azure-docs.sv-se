---
title: Vad är nytt i Enterprise-programhantering i Azure Active Directory | Microsoft Docs
description: Läs mer om nyheterna i Enterprise-programhantering i Azure Active Directory.
services: active-directory
documentationcenter: ''
author: ajamess
manager: mtillman
editor: ''
ms.assetid: 34ac4028-a5aa-40d9-a93b-0db4e0abd793
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/13/2017
ms.author: asteen
ms.reviewer: asteen
ms.openlocfilehash: fefc508679a309262d07a582fc8f5bdf9f67cfe5
ms.sourcegitcommit: 3a02e0e8759ab3835d7c58479a05d7907a719d9c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/13/2018
ms.locfileid: "49310128"
---
# <a name="whats-new-in-enterprise-application-management-in-azure-active-directory"></a>Vad är nytt i Enterprise-programhantering i Azure Active Directory 

Azure Active Directory (Azure AD) har förbättrad enterprise programhanteringsverktyg, med nya funktioner och möjligheter att hantera appar enklare och effektiv.

Nedan visas några av de förbättrade funktionerna för Azure AD i den [Azure-portalen](https://portal.azure.com).

- Galleriet för en förbättrad användarupplevelse med en förenklad programmet skapa en modell och stöd för alla programtyper som du är van att. 
- En helt ny quickstart-upplevelse som kan hjälpa dig att komma igång med en pilotdistribution av ditt program. 
- Konfigurera självbetjäning med bara några klick. 
- Förbättringar av programproxyn för enkel inloggningkonfigurationen och ta med din egen programanvändandet, så att du kan få mer gjort än innan.

## <a name="improvements-to-the-azure-active-directory-application-gallery"></a>Förbättringar av Azure Active Directory-Programgalleriet

Lägg till dina favoritprogram oavsett om de är från den [programgalleriet](manage-apps/what-is-single-sign-on.md#get-started-with-the-azure-ad-application-gallery), anpassade program som du utvidgar till molnet eller nya program som du utvecklar.  Du kan komma igång med denna nya upplevelse genom att klicka på **Lägg till** under **företagsprogram** eller **alla program**.
 
  ![Lägga till ett program](./media/active-directory-enterprise-apps-whats-new-azure-portal/01.png)

En gång i galleriet visas alla aktuella program som har stöd för användaretablering visas främre och center. Du kan bläddra bland alla typer av olika kategorier för att öka detaljnivån i de program som intresserar dig, du kan också använda sökupplevelsen att snabbt hitta de program som du vill integrera.

  ![Programgalleriet](./media/active-directory-enterprise-apps-whats-new-azure-portal/02.png)

## <a name="add-custom-applications-from-one-place"></a>Lägga till anpassade program från ett och samma ställe

Förutom att lägga till redan integrerade program från galleriet, är alla anpassade programkonfigurationen inträffar som du har använt till i den klassiska hanteringsportalen nu möjligt i den nya portalen. Om du utökar ett program från en lokal plats med hjälp av programproxyn integrera ditt eget lösenord eller en federerad SSO-program, eller skapa en helt ny App med hjälp av programregistret, kan du till den från den här en enda plats.

  ![Att lägga till ditt eget program](./media/active-directory-enterprise-apps-whats-new-azure-portal/03.png)

 
**Du kommer igång med att lägga till dina egna program**:

1. Klicka på **Lägg till en egen länk** överst i programgalleriet. 
1. Ser du två alternativ framför du: **distribuerar ett befintligt program** eller **utveckla ett nytt program**. Läs vidare för att lära dig skillnaden mellan de två alternativen och hur de används.

### <a name="deploying-existing-applications"></a>Distribuera befintliga program

1. Om du har ett program som körs redan och bara vill integrerar dem i Azure AD för enkel inloggning på eller etablering, Välj den **distribuerar ett befintligt program** alternativet. Välj ett namn för ditt program, klicka på **Lägg till**.
1. Klart! Istället för att kunna all information om ditt program direkt, kan du nu ställa in hur ditt nya program fungerar genom att navigera genom den vänstra menyn och konfigurera programmet enligt dina önskemål när som helst.

  ![Att lägga till ett befintligt program med ett klick](./media/active-directory-enterprise-apps-whats-new-azure-portal/04.png)
 
### <a name="developing-new-applications"></a>Utveckla nya program

1. Om du utvecklar ett nytt program är det enkelt att komma till programregistret rätt från galleriet:
1. Klicka på den **lägga till egna** alternativet från Appgalleriet, Välj **utveckla ett befintligt program**, så ser du en snabblänk till Lägg till programupplevelse.

  ![Lägga till ett nyligen utvecklade program med några få klick](./media/active-directory-enterprise-apps-whats-new-azure-portal/05.png)


>[!NOTE]
>När du lägger till ett program med hjälp av program-registret, visas den dyker upp i listan över företagsprogram där du kommer att kunna konfigurera enkel inloggning och hantera åtkomstprinciper för det nya programmet.

  ![Hantera åtkomst till det nya programmet under företagsprogram](./media/active-directory-enterprise-apps-whats-new-azure-portal/06.png)


## <a name="quickstart-get-going-with-your-new-application-right-away"></a>Snabbstart: Kom igång med det nya programmet direkt 

När du har lagt till ett program, om det vara förintegrerade eller din egen app, har vi skapat en anpassad upplevelse för Snabbstart för att få du förankrad i den nya upplevelsen program snabbt. Om du följer varje alternativ systematiskt vi vägleder dig genom Användargränssnittet och visar hur du kommer igång med en pilotdistribution av det nya programmet så snabbt som möjligt. 
 
  ![Den nya upplevelsen för program-Snabbstart](./media/active-directory-enterprise-apps-whats-new-azure-portal/07.png)

 Du kan gå till den här nya quickstart-upplevelsen när som helst och för alla program genom att klicka på **snabbstarten** från den vänstra navigeringsmenyn i programmet.


## <a name="updated-application-proxy-configuration"></a>Uppdaterade programmet proxykonfiguration

Nu ska vi say en av de nya program som du har lagt till körs i din lokala miljö och du vill integrera med Azure AD.  En av de nya häftiga grejer konfigurationsupplevelse för nya program i den nya Azure AD-portalen är att genom att dela upp programmets inloggningsläge från dess application proxy-konfiguration, kan nu enkelt visas enkel inloggning med lösenord eller federerade program som körs i din företagsnätverket direkt till molnet, utan att behöva skapa flera instanser av programmet.

Du kan också konfigurera någon av de nya program som du har lagt till för användning med Azure AD Application Proxy-direkt från den nya portalen, inklusive program som stöder intern Windows-autentiseringsupplevelse.

  ![Konfigurera ett program att använda integrerad Windows-autentisering inloggnings-alternativet](./media/active-directory-enterprise-apps-whats-new-azure-portal/08.png)
 
Kom igång med att konfigurera ett internt program för Windows-autentisering med Application Proxy:
1. Klicka på objektet för enkel inloggning för navigering och välj **integrerad Windows-autentisering** under inloggning inställningar och konfigurera inställningarna enligt dina önskemål.
1. Utöver stödet för dessa nya autentiseringslägen så att du kan också hämta certifikat från anpassade domäner till stöd för program som körs på säker slutpunkter i din organisation.  
 
   ![Laddar upp ett certifikat som ska användas med Application Proxy](./media/active-directory-enterprise-apps-whats-new-azure-portal/09.png)

1. Om du vill ladda upp ett nytt certifikat för ditt bland lokala program, klicka på den **programproxy** från den vänstra menyn, klickar på den **certifikat** väljare och ladda upp ett certifikat Vi kan använda för att kryptera filen begäranden från våra molnslutpunkt för ditt program.

## <a name="advanced-federated-single-sign-on-configuration"></a>Avancerad federerad enkel inloggning för konfiguration

För de som använder federerade program idag, att det finns många nya funktioner i SAML-baserad inloggning konfiguration. Börja med kan nu du helt anpassa, lägga till, ta bort och mappa de befintliga användarattribut utfärdas som anspråk i SAML-tokens.
 
  ![Anpassa de SAML-token användarattribut skickas till externa program](./media/active-directory-enterprise-apps-whats-new-azure-portal/10.png)

Så här kontrollerar att ut den nya federerad SSO-konfiguration:
1. Öppna en federerad programmet **enkel inloggning** från den vänstra navigeringsmenyn och kontrollera att den ”*SAML-baserad inloggning** läge har valts. 
1. När det, markerar kryssrutan under den **användarattribut** rubrik för att ändra alla attribut som ingår i SAML-token som skickas till programmet.

Du kan också skapa, förnyelse, och hantera certifikat för federerad enkel inloggning, samt redigera som hämtar ett meddelande visas när certifikatet upphör snart att gälla. Du ser dessa nya alternativ under den **certifikat** rubrik i samma enkel inloggning ruta.
 
  ![Skapa ett nytt certifikat, anpassa e-postmeddelande för förfallodatum och alternativ för Certifikatsignering](./media/active-directory-enterprise-apps-whats-new-azure-portal/11.png)

### <a name="relay-state-parameter"></a>Vidarebefordransstatus parameter
Slutligen har vi också utökade URL för SAML-parameteruppsättningen som vi har stöd för att inkludera den **Vidarebefordransstatus parametern**, vilket är den sida som användarna hamnar på inifrån en federerad programmet när inloggningen är klar. Det här är en användbar inställningen för att konfigurera om du vill skicka användarna till en specifik plats i programmet för att hämta dem går snabbt.

  ![Vidarebefordransstatus för SAML-parametern](./media/active-directory-enterprise-apps-whats-new-azure-portal/12.png)
 
**Ange parametern state relay**:

1. Aktivera den **visa avancerade URL-inställningar** kryssruta under den **domän och URL: er** rubriken på den enkel inloggning i fönstret konfiguration. 
1. En uppsättning nya URL: en ange rutor visas så att du kan ange den här parametern och andra inställningar för SAML-URL.

## <a name="bring-your-own-password-sso-applications"></a>Ta med ditt eget lösenord SSO-program

Vi vet att inte alla program har stöd för federation kompletta. Exempelvis kanske har en av de nya program som du har lagt till en anpassad inloggningsskärm som användarna använder ett användarnamn och lösenord för att logga in på. Du kan fortfarande integrera dessa typer av program med Azure AD via vår **Använd dina egna program** funktion, som nu är tillgänglig för dig att konfigurera i den nya portalen.
 
  ![Integrera anpassade lösenord vaulting program med Azure AD](./media/active-directory-enterprise-apps-whats-new-azure-portal/13.png)

**Ta del av funktionen ”Använd dina egna program'**:

1. När du ställer in enkel inloggning för läget för en ny anpassad App som du har lagt till **lösenordsbaserad inloggning**, ange URL: en där programmet återger dess inloggningsskärmen och 
1. Klicka på **Spara**.  
1. När du gör det vi kommer automatiskt att skrapa URL: en för ett användarnamn och lösenord Inmatningsruta så att du kan använda Azure AD att på ett säkert sätt överföra lösenord till programmet med hjälp av programåtkomstpanelens webbläsartillägg.

## <a name="configure-self-service-application-access"></a>Konfigurera självbetjäningsåtkomsten

När du har lagt till många nya program, kanske vill du tillåta användarna att söka och lägga till dessa program i sin egen åtkomstpaneler, utan att behöva bry dig du som administratör. Med den senaste versionen kan du nu konfigurera och hantera självbetjäningsåtkomsten direkt från den nya portalen.

  ![Konfigurera självbetjäningsåtkomsten ett lösenord för SSO-program](./media/active-directory-enterprise-apps-whats-new-azure-portal/14.png)
 
**Konfigurera och hantera självbetjäningsåtkomsten**:

1. För att komma igång, kan du välja den **självbetjäning** alternativet från programmet kvar navigeringsmenyn och ange den **Tillåt användare att begära åtkomst till det här programmet?** alternativet att ”**Ja**'. 
1. Detta aktiverar du kan konfigurera som har behörighet att bevilja åtkomst till det här programmet och vilken grupp Självbetjäningsanvändare läggs. Dessutom om programmet har konfigurerats för lösenord för enkel inloggning kan visas också ett annat alternativ som låter dig också dessa godkännare ha tillåtelse att hantera de lösenord som tilldelats programmet.

## <a name="feedback"></a>Feedback

Vi hoppas att du t.ex. använder den förbättrade Azure AD-upplevelse. Håll din feedback kommer! Publicera din feedback och förslag för förbättring i den **Admin Portal** delen av vår [Feedbackforum](https://feedback.azure.com/forums/169401-azure-active-directory/category/162510-admin-portal).  Vi är taggade om att skapa coola nya grejer varje dag, och Använd din vägledning för att forma och definiera vad vi bygga härnäst.

## <a name="next-steps"></a>Nästa steg

Mer information finns i [hantera program med Azure Active Directory](manage-apps/what-is-application-management.md).



