---
title: IIS-autentisering och Azure MFA Server | Microsoft Docs
description: "Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera IIS-autentisering och Azure Multi-Factor Authentication Server."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 06/16/2017
ms.author: kgremban
ms.reviewer: yossib
ms.custom: H1Hack27Feb2017,it-pro
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: b1841aea07140a855a1978e7c3141f76aa2d8862
ms.contentlocale: sv-se
ms.lasthandoff: 09/14/2017

---
# <a name="configure-azure-multi-factor-authentication-server-for-iis-web-apps"></a>Konfigurera Azure Multi-Factor Authentication Server för IIS-webbappar

Via IIS-autentisering på Azure Multi-Factor Authentication-servern kan du aktivera och konfigurera IIS-autentisering för integrering med Microsoft IIS-webbprogram. Azure MFA Server installerar ett plugin-program som kan filtrera begäranden som görs till IIS-webbservern för att lägga till Azure Multi-Factor Authentication. IIS-plugin-programmet har stöd för formulärbaserad autentisering och integrerad Windows-HTTP-autentisering. Betrodda IP-adresser kan även konfigureras att undanta interna IP-adresser från tvåfaktorsautentisering.

![IIS-autentisering](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-form-based-iis-authentication-with-azure-multi-factor-authentication-server"></a>Använda formulärbaserad IIS-autentisering med Azure Multi-Factor Authentication Server
Om du ska skydda en IIS-webbapp som använder formulärbaserad autentisering installerar du Azure Multi-Factor Authentication Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan:

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **Formulärbaserad**.
3. Klicka på **Lägg till**.
4. Om du vill identifiera användarnamn, lösenord och domänvariabler automatiskt anger du inloggnings-URL:en (t.ex. https://localhost/contoso/auth/login.aspx) i dialogrutan Konfigurera formulärbaserad webbplats automatiskt och klickar på **OK**.
5. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på **Ange manuellt** i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en till inloggningssidan i fältet Skicka URL och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
8. Välj rätt format för begäran. Detta är inställt på **POST eller GET** för de flesta webbprogram.
9. Ange variabeln Användarnamn, Lösenord och Domän (om den visas på inloggningssidan). Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja **Visa källa** för att hitta namnen på inmatningsrutorna.
10. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
11. Klicka på **Avancerat** för att granska avancerade inställningar, inklusive:

  - Välj en anpassad nekandeväxlingsfil
  - Cachelagra lyckade autentiseringar på webbplatsen under en period med hjälp av cookies
  - Välj om du vill autentisera primära autentiseringsuppgifter mot en Windows-domän, LDAP-katalogen eller RADIUS-servern.

12. Klicka på **OK** så visas dialogrutan Lägg till formulärbaserad webbplats igen.
13. Klicka på **OK**.
14. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.

## <a name="using-integrated-windows-authentication-with-azure-multi-factor-authentication-server"></a>Använda integrerad Windows-autentisering med Azure Multi-Factor Authentication Server
Om du ska skydda en IIS-webbapp som använder integrerad Windows HTTP-autentisering installerar du Azure MFA Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan:

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **HTTP**.
3. Klicka på **Lägg till**.
4. I dialogrutan Lägg till grundläggande webbadress anger du URL:en för den webbplats där HTTP-autentiseringen utförs (t.ex. http://localhost/owa) och anger sedan ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Justera tiderna för Timeout vid inaktivitet och Längsta session om standardvärdet inte räcker.
6. Markera rutan om att **Multi-Factor Authentication-användarmatchning krävs** om alla användare har importerats eller ska importeras till servern och om multifaktorautentisering används. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
7. Markera kryssrutan för **cachelagring av cookies** om du vill det.
8. Klicka på **OK**.

## <a name="enable-iis-plug-ins-for-azure-multi-factor-authentication-server"></a>Aktivera IIS plugin-program för Azure Multi-Factor Authentication Server
När du har konfigurerat URL:erna och inställningarna för formulärbaserad autentisering eller HTTP-autentisering måste du välja de platser där Azure Multi-Factor Authentication IIS plugin-programmen ska läsas in och aktiveras i IIS. Följ dessa steg:

1. Om du kör på IIS 6 klickar du på fliken **ISAPI**. Välj den webbplats som webbappen körs under (t.ex. standardwebbplatsen) om du vill aktivera plugin-programmet för ISAPI-filter för Azure Multi-Factor Authentication för platsen i fråga.
2. Om den körs på IIS 7 eller senare, klickar du på fliken **Ursprunglig modul**. Välj den server, de webbplatser eller de program som ska användas för att aktivera IIS-plugin-programmet på de önskade nivåerna.
3. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen. Nu skyddar Azure Multi-Factor Authentication det valda IIS-programmet. Kontrollera att användarna har importerats till servern.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du kanske till exempel vill undanta användare från Azure Multi-Factor Authentication när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress. Följ dessa steg om du vill konfigurera tillförlitliga IP-adresser:

1. Klicka på fliken **Tillförlitliga IP-adresser** i avsnittet IIS-autentisering.
2. Klicka på **Lägg till**.
3. När dialogrutan för att lägga till tillförlitliga IP-adresser visas väljer du alternativknappen **Enkel IP**, **IP-intervall** eller **Undernät**.
4. Ange IP-adressen, IP-adressintervallet eller undernätet som ska vitlistas. Om du anger ett undernät väljer du lämplig nätmask och klickar på **OK**. Nu har vitlistan lagts till.

