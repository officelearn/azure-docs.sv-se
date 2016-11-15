---
title: IIS-autentisering och Azure Multi-Factor Authentication Server
description: "Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera IIS-autentisering och Azure Multi-Factor Authentication Server."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: d1bf1c8a-2c10-4ae6-9f4b-75f0c3df43eb
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 138e23a0964c0e540c6b2ddf8891aa9af6782233


---
# <a name="iis-authentication"></a>IIS-autentisering
I avsnittet IIS-autentisering för Azure Multi-Factor Authentication Server kan du aktivera och konfigurera IIS-autentisering för integrering med Microsoft IIS-webbprogram. Azure Multi-Factor Authentication Server installerar ett plugin-program som kan filtrera begäranden som görs till IIS-webbservern för att lägga till Azure Multi-Factor Authentication. IIS-plugin-programmet har stöd för formulärbaserad autentisering och integrerad Windows-HTTP-autentisering. Betrodda IP-adresser kan även konfigureras att undanta interna IP-adresser från tvåfaktorsautentisering.

![IIS-autentisering](./media/multi-factor-authentication-get-started-server-iis/iis.png)

## <a name="using-formbased-iis-authentication-with-azure-multifactor-authentication-server"></a>Använda formulärbaserad IIS-autentisering med Azure Multi-Factor Authentication Server
Om du ska skydda en IIS-webbapp som använder formulärbaserad autentisering installerar du Azure Multi-Factor Authentication Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan.

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken Formulärbaserad.
3. Klicka på Lägg till.
4. Om du vill identifiera användarnamn, lösenord och domänvariabler automatiskt anger du inloggnings-URL:en (t.ex. https://localhost/contoso/auth/login.aspx) i dialogrutan Konfigurera formulärbaserad webbplats automatiskt och klickar på OK.
5. Markera rutan Kräv Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på knappen Ange manuellt i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en till inloggningssidan i fältet Skicka URL och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar. Mer information om Överföringswebbadress finns i hjälpfilen.
8. Välj rätt format för begäran. Detta är ”POST eller GET” för de flesta webbprogram.
9. Ange variabeln Användarnamn, Lösenord och Domän (om den visas på inloggningssidan). Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja Visa källa för att hitta namnen på inmatningsrutorna på sidan.
10. Markera rutan Kräv Azure Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
11. Klicka på knappen Avancerat om du vill visa avancerade inställningar. Du kan till exempel välja en anpassad fil för sidan för nekad åtkomst, cachelagra lyckade autentiseringar till webbplatsen under en viss tid med hjälp av cookies och välja om de primära autentiseringsuppgifterna ska autentisera mot Windows-domänen, en LDAP-katalog eller en RADIUS-server. När du är klar klickar du på knappen OK för att gå tillbaka till dialogrutan Lägg till formulärbaserad webbplats. Mer information om avancerade inställningar finns i hjälpfilen.
12. Klicka på OK.
13. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.
14. Avsnittet Aktivera IIS-plugin-program för Azure Multi-Factor Authentication Server nedan innehåller anvisningar för hur du slutför IIS-autentiseringskonfigurationen.

## <a name="using-integrated-windows-authentication-with-azure-multifactor-authentication-server"></a>Använda integrerad Windows-autentisering med Azure Multi-Factor Authentication Server
Om du ska skydda en IIS-webbapp som använder integrerad Windows HTTP-autentisering installerar du Azure Multi-Factor Authentication Server på IIS-webbservern och konfigurerar servern genom att följa stegen nedan.

1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken HTTP. Klicka på fliken Formulärbaserad.
3. Klicka på Lägg till.
4. I fältet Bas-URL i dialogrutan Lägg till grundläggande webbadress anger du URL:en för den webbplats där HTTP-autentiseringen utförs (t.ex. http://localhost/owa) och anger sedan ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Justera tiderna för Timeout vid inaktivitet och Längsta session om standardvärdet inte räcker.
6. Markera rutan Kräv Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
7. Markera kryssrutan för cachelagring av cookies om du vill.
8. Klicka på OK.
9. Avsnittet [Aktivera IIS-plugin-program för Azure Multi-Factor Authentication Server](#enable-iis-plug-ins-for-azure-multi-factor-authentication-server) nedan  innehåller anvisningar för hur du slutför IIS-autentiseringskonfigurationen.

## <a name="enable-iis-plugins-for-azure-multifactor-authentication-server"></a>Aktivera IIS plugin-program för Azure Multi-Factor Authentication Server
När du har konfigurerat URL:erna och inställningarna för formulärbaserad autentisering eller HTTP-autentisering måste du välja de platser där Azure Multi-Factor Authentication IIS plugin-programmen ska läsas in och aktiveras i IIS. Följ dessa steg:

1. Om du har IIS 6 klickar du på fliken ISAPI och väljer den webbplats som webbappen körs under (t.ex. standardwebbplatsen) för att aktivera plugin-programmet för ISAPI-filter för Azure Multi-Factor Authentication för platsen.
2. Om du har IIS 7 eller senare klickar du på fliken Ursprunglig modul och väljer servern, webbplatserna eller programmen för att aktivera IIS-plugin-programmet på önskade nivåer.
3. Klicka på rutan Aktivera IIS-autentisering överst på skärmen. Nu skyddar Azure Multi-Factor Authentication det valda IIS-programmet. Kontrollera att användarna har importerats till servern. Se avsnittet Tillförlitliga IP-adresser nedan om du vill vitlista interna IP-adresser så att tvåfaktorsautentisering inte krävs vid inloggning till webbplatsen från dessa platser.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du kanske till exempel vill undanta användare från Azure Multi-Factor Authentication när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress. Följ dessa steg om du vill konfigurera tillförlitliga IP-adresser:

1. Klicka på fliken Tillförlitliga IP-adresser i avsnittet IIS-autentisering.
2. Klicka på Lägg till.
3. När dialogrutan Lägg till tillförlitlig IP-adress visas väljer du alternativknappen Enkel IP, IP-intervall eller Undernät.
4. Ange IP-adressen, IP-adressintervallet eller undernätet som ska vitlistas. Om du anger ett undernät väljer du lämplig nätmask och klickar på OK. Nu har vitlistan lagts till.




<!--HONumber=Nov16_HO2-->


