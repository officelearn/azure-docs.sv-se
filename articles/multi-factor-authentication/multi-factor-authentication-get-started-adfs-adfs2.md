---
title: Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0
description: Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS 2.0.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtland

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban

---
# Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0
Om din organisation är federerad med Azure Active Directory och du har resurser som är lokala eller i molnet som du vill skydda, kan du göra det med hjälp av Azure Multi-Factor Authentication Server och konfigurera tjänsten för AD FS så att multifaktorautentisering används för värdefulla slutpunkter.

Den här dokumentationen beskriver hur du använder Azure Multi-Factor Authentication Server med AD FS 2.0.  Information om hur du använder Azure Multi-Factor Authentication med Windows Server 2012 R2 AD FS finns i [Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).

## AD FS 2.0-proxyserver
Om du vill skydda AD FS 2.0 med en proxy installerar du Azure Multi-Factor Authentication Server på ADFS-proxyservern och konfigurerar servern genom att följa stegen nedan.

### Så här skyddar du AD FS 2.0 med en proxy
1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken Formulärbaserad.
3. Klicka på Lägg till.
   <center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Om du vill identifiera användarnamn, lösenord och domänvariabler automatiskt anger du inloggnings-URL:en (t.ex. https://sso.contoso.com/adfs/ls) i dialogrutan Konfigurera formulärbaserad webbplats automatiskt och klickar på OK.
5. Markera rutan Kräv Azure Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på knappen Ange manuellt i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en för AD FS-inloggningssidan i fältet Överföringswebbadress (t.ex. https://sso.contoso.com/adfs/ls) och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar. Mer information om Överföringswebbadress finns i hjälpfilen.
8. Ange ”POST eller GET” för Format för förfrågan.
9. Ange användarnamnsvariabeln (ctl00$ContentPlaceHolder1$UsernameTextBox) och lösenordsvariabeln (ctl00$ContentPlaceHolder1$PasswordTextBox). Om en textruta för domänen visas på den formulärbaserade inloggningssidan anger du även domänvariabeln. Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja Visa källa för att hitta namnen på inmatningsrutorna på inloggningssidan.
10. Markera rutan Kräv Azure Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad.
    <center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Klicka på knappen Avancerat om du vill granska de avancerade inställningarna, t.ex. om du vill välja en anpassad fil för en ”Nekad åtkomst”-sida, cachelagra lyckade autentiseringar till webbplatsen under en viss tidsperiod med hjälp av cookies och välja hur du vill autentisera primära autentiseringsuppgifter.
12. Eftersom AD FS-proxyservern troligen inte är ansluten till domänen använder du sannolikt LDAP för att ansluta till domänkontrollanten för användarimport och förautentisering. Klicka på fliken Primär autentisering i dialogrutan Avancerad formulärbaserad webbplats och väljer ”LDAP-bindning” för autentiseringstypen Förautentisering.
13. När du är klar klickar du på knappen OK för att gå tillbaka till dialogrutan Lägg till formulärbaserad webbplats. Mer information om avancerade inställningar finns i hjälpfilen.
14. Stäng dialogrutan genom att klicka på OK.
15. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.
16. Klicka på fliken Ursprunglig modul och välj servern, webbplatsen som ADFS-proxyn körs under (t.ex. ”Standardwebbplats”) eller ADFS-proxyprogrammet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-plugin-programmet på önskad nivå.
17. Klicka på rutan Aktivera IIS-autentisering överst på skärmen.
18. Nu är IIS-autentisering aktiverat. För att kunna utföra förautentisering till din Active Directory (AD) via LDAP måste du dock konfigurera LDAP-anslutningen till domänkontrollanten. Det gör du genom att klicka på ikonen Katalogintegrering.
19. På fliken Inställningar väljer du alternativknappen Använd specifik LDAP-konfiguration.
    <center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
20. Klicka på knappen Redigera.
21. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till AD-domänkontrollanten. Beskrivningar av fälten finns i tabellen nedan. Obs! Den här informationen finns också i hjälpfilen för Azure Multi-Factor Authentication Server.
22. Testa LDAP-anslutningen genom att klicka på knappen Testa.
    <center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
23. Klicka på OK om LDAP-anslutningstestet lyckas.
24. Klicka på ikonen Företagsinställningar och välj fliken Matcha användarnamn.
25. Välj alternativknappen Matcha användarnamn med unikt identifierarattribut för LDAP.
26. Om användare ska ange sina användarnamn i inloggingsformuläret för ADFS-proxyn i formatet ”domän\användarnamn” måste servern kunna extrahera domänen från användarnamnet när LDAP-frågan skapas. Detta kan göras via en registerinställning.
27. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor på en 64-bitarsserver. Om du arbetar på en 32-bitarsserver utelämnar du ”Wow6432Node” i sökvägen. Skapa en ny DWORD-registernyckel med namnet ”UsernameCxz_stripPrefixDomain” och ange värdet till 1. Nu skyddas ADFS-proxyn av Azure Multi-Factor Authentication. Se till att användarna har importerats från Active Directory till servern. Se avsnittet Tillförlitliga IP-adresser nedan om du vill vitlista interna IP-adresser så att tvåfaktorsautentisering inte krävs vid inloggning till webbplatsen från dessa platser.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## AD FS 2.0 direkt utan någon proxy
Om du vill skydda AD FS när AD FS-proxyn inte används installerar du Azure Multi-Factor Authentication Server på AD FS-servern och konfigurera servern genom att följa stegen nedan.

### Så här skyddar du AD FS 2.0 utan en proxy
1. Klicka på ikonen för IIS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken HTTP.
3. Klicka på Lägg till.
4. I dialogrutan Lägg till grundläggande webbadress anger du URL:en för ADFS-webbplatsen där HTTP-autentiseringen utförs (t.ex. https://sso.domain.com/adfs/ls/auth/integrated) i fältet Bas-URL och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Om du vill justerar du värdena för timeout vid inaktivitet och längsta sessionstid.
6. Markera rutan Kräv Azure Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
7. Markera kryssrutan för cachelagring av cookies om du vill.
   <center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Klicka på OK.
9. Klicka på fliken Ursprunglig modul och välj servern, webbplatsen som ADFS körs under (t.ex. ”Standardwebbplats”) eller ADFS-programmet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-plugin-programmet på önskad nivå.
10. Klicka på rutan Aktivera IIS-autentisering överst på skärmen. Nu skyddas ADFS av Azure Multi-Factor Authentication. Se till att användarna har importerats från Active Directory till servern. Se avsnittet Tillförlitliga IP-adresser nedan om du vill vitlista interna IP-adresser så att tvåfaktorsautentisering inte krävs vid inloggning till webbplatsen från dessa platser.

## Tillförlitliga IP-adresser
Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du kanske till exempel vill undanta användare från Azure Multi-Factor Authentication när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.

### Så här konfigurerar du tillförlitliga IP-adresser
1. Klicka på fliken Tillförlitliga IP-adresser i avsnittet IIS-autentisering.
2. Klicka på Lägg till.
3. När dialogrutan Lägg till tillförlitlig IP-adress visas väljer du alternativknappen Enkel IP, IP-intervall eller Undernät.
4. Ange IP-adressen, IP-adressintervallet eller undernätet som ska vitlistas. Om du anger ett undernät väljer du lämplig nätmask och klickar på OK. Nu har den tillförlitliga IP-adressen lagts till.

<center>![Konfiguration](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Sep16_HO3-->


