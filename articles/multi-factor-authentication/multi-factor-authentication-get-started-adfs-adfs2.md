---
title: "Använda Azure MFA Server med AD FS 2.0 | Microsoft Docs"
description: "Det här är sidan om Azure Multi-Factor Authentication som beskriver hur du kommer igång med Azure MFA och AD FS 2.0."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: 96168849-241a-4499-a224-d829913caa7e
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/14/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: dcda8b30adde930ab373a087d6955b900365c4cc
ms.openlocfilehash: 7ad06e9086d6532b3de2ba060b37424051f022c2


---
# <a name="secure-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-20"></a>Skydda molnet och lokala resurser med Azure Multi-Factor Authentication Server med AD FS 2.0
Den här artikeln är för organisationer som federerade med Azure Active Directory och som vill skydda resurser som finns lokalt eller i molnet. Skydda dina resurser genom att använda Azure Multi-Factor Authentication-servern och konfigurera den för att fungera med AD FS så att tvåstegsverifiering utlöses för slutpunkter med högt värde.

Den här dokumentationen beskriver hur du använder Azure Multi-Factor Authentication Server med AD FS 2.0.  Mer information finns i [Skydda molnresurser och lokala resurser med Azure Multi-Factor Authentication Server med Windows Server 2012 R2 AD FS](multi-factor-authentication-get-started-adfs-w2k12.md).

## <a name="secure-ad-fs-20-with-a-proxy"></a>Skydda AD FS 2.0 med en proxy
Om du vill skydda AD FS 2.0 med en proxy installerar du Azure Multi-Factor Authentication Server på ADFS-proxyservern och konfigurerar servern.

### <a name="configure-iis-authentication"></a>Konfigurera IIS-autentisering
1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **Formulärbaserad**.
3. Klicka på **Lägg …** till.
   <center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/setup1.png)</center>
4. Om du vill identifiera användarnamn, lösenord och domänvariabler automatiskt anger du inloggnings-URL:en (som t.ex. https://sso.contoso.com/adfs/ls) i dialogrutan Konfigurera formulärbaserad webbplats automatiskt och klickar på OK.
5. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
6. Om sidvariablerna inte kan identifieras automatiskt klickar du på knappen **Ange manuellt** i dialogrutan Konfigurera formulärbaserad webbplats automatiskt.
7. I dialogrutan Lägg till formulärbaserad webbplats anger du URL:en för AD FS-inloggningssidan i fältet Överföringswebbadress ( som t.ex. https://sso.contoso.com/adfs/ls) och anger ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar. Mer information om Överföringswebbadress finns i hjälpfilen.
8. Ange ”POST eller GET” för Format för förfrågan.
9. Ange användarnamnsvariabeln (ctl00$ContentPlaceHolder1$UsernameTextBox) och lösenordsvariabeln (ctl00$ContentPlaceHolder1$PasswordTextBox). Om en textruta för domänen visas på den formulärbaserade inloggningssidan anger du även domänvariabeln. Du kan behöva gå till inloggningssidan i en webbläsare, högerklicka på sidan och välja **Visa källa** för att hitta namnen på inmatningsrutorna på inloggningssidan.
10. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad.
    <center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/manual.png)</center>
11. Klicka på knappen **Avancerat** för att granska avancerade inställningar. Du kan konfigurera inställningarna, t.ex. om du vill välja en anpassad fil för en ”Nekad åtkomst”-sida, cachelagra lyckade autentiseringar till webbplatsen hjälp av cookies och välja hur du vill autentisera primära autentiseringsuppgifter.
12. Eftersom AD FS-proxyservern troligen inte är ansluten till domänen kan du använda LDAP för att ansluta till domänkontrollanten för användarimport och förautentisering. Klicka på fliken **Primär autentisering** i dialogrutan Avancerad formulärbaserad webbplats och välj **LDAP-bindning** för autentiseringstypen Förautentisering.
13. När du är klar klickar du på knappen **OK** för att gå tillbaka till dialogrutan Lägg till formulärbaserad webbplats. Mer information om avancerade inställningar finns i hjälpfilen.
14. Stäng dialogrutan genom att klicka på **OK**.
15. När URL- och sidvariablerna har identifierats eller angetts visas webbplatsdata på panelen Formulärbaserad.
16. Klicka på fliken **Ursprunglig modul** och välj servern, webbplatsen som ADFS-proxyn körs under (t.ex. ”Standardwebbplats”) eller ADFS-proxyprogrammet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-pluginprogrammet på önskad nivå.
17. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen.
18. Nu är IIS-autentisering aktiverat.

### <a name="configure-directory-integration"></a>Konfigurera katalogintegrering
Du har aktiverat IIS-autentisering, men för att kunna utföra förautentisering till din Active Directory (AD) via LDAP måste du konfigurera LDAP-anslutningen till domänkontrollanten.

1. Det gör du genom att klicka på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativknappen **Använd specifik LDAP-konfiguration**.
   <center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap1.png)</center>
3. Klicka på **Redigera** till.
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till AD-domänkontrollanten. Beskrivningar av fälten finns i tabellen nedan. Den här informationen finns också i hjälpfilen för Azure Multi-Factor Authentication Server.
5. Testa LDAP-anslutningen genom att klicka på knappen **Testa**.
   <center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/ldap2.png)</center>
6. Klicka på **OK** om LDAP-anslutningstestet lyckas.

### <a name="configure-company-settings"></a>Konfigurera företagsinställningar
1. Klicka därefter på ikonen **Företagsinställningar** och välj fliken **Matcha användarnamn**.
2. Välj alternativknappen **Matcha användarnamn med unikt identifierarattribut för LDAP**.
3. Om användare anger sina användarnamn i ”domän\användarnamn”-format måste servern kunna extrahera domänen från användarnamnet när LDAP-frågan skapas. Detta kan göras via en registerinställning.
4. Öppna Registereditorn och gå till HKEY_LOCAL_MACHINE/SOFTWARE/Wow6432Node/Positive Networks/PhoneFactor på en 64-bitarsserver. Om du arbetar på en 32-bitarsserver utelämnar du ”Wow6432Node” i sökvägen. Skapa en DWORD-registernyckel med namnet ”UsernameCxz_stripPrefixDomain” och ange värdet till 1. Nu skyddas ADFS-proxyn av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se avsnittet [Tillförlitliga IP-adresser](#trusted-ips) om du vill vitlista interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

<center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/reg.png)</center>

## <a name="ad-fs-20-direct-without-a-proxy"></a>AD FS 2.0 direkt utan någon proxy
Du kan skydda AD FS när AD FS-proxy inte används. Installera Azure Multi-Factor Authentication Server på AD FS-servern och konfigurera servern genom att följa stegen nedan:

1. Klicka på ikonen för **IIS-autentisering** på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Klicka på fliken **HTTP**.
3. Klicka på **Lägg …** till.
4. I dialogrutan Lägg till grundläggande webbadress anger du URL:en för ADFS-webbplatsen där HTTP-autentiseringen utförs (som t.ex. https://sso.domain.com/adfs/ls/auth/integrated) i fältet Bas-URL. Ange sedan ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
5. Om du vill justerar du värdena för timeout vid inaktivitet och längsta sessionstid.
6. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
7. Markera kryssrutan för cachelagring av cookies om du vill.
   <center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/noproxy.png)</center>
8. Klicka på **OK**.
9. Klicka på fliken **Ursprunglig modul** och välj servern, webbplatsen (som t.ex. ”Standardwebbplats”) eller ADFS-programmet (t.ex. ”Is” under ”adfs”) för att aktivera IIS-pluginprogrammet på önskad nivå.
10. Klicka på rutan **Aktivera IIS-autentisering** överst på skärmen. Nu skyddas ADFS av Azure Multi-Factor Authentication.

Se till att användarna har importerats från Active Directory till servern. Se avsnittet Tillförlitliga IP-adresser om du vill vitlista interna IP-adresser så att tvåstegsverifiering inte krävs vid inloggning till webbplatsen från dessa platser.

## <a name="trusted-ips"></a>Tillförlitliga IP-adresser
Tillförlitliga IP-adresser låter användarna kringgå Azure Multi-Factor Authentication för webbplatsförfrågningar som kommer från specifika IP-adresser eller undernät. Du vill kanske till exempel undanta användare från tvåstegsverifiering när de loggar in från kontoret. För att göra det anger du kontorets undernät som en tillförlitlig IP-adress.

### <a name="to-configure-trusted-ips"></a>Så här konfigurerar du tillförlitliga IP-adresser
1. Klicka på fliken **Tillförlitliga IP-adresser** i avsnittet IIS-autentisering.
2. Klicka på **Lägg …** till.
3. När dialogrutan Lägg till tillförlitlig IP-adress visas väljer du en av alternativknapparna **Enkel IP**, **IP-intervall** eller **Undernät**.
4. Ange IP-adressen, IP-adressintervallet eller undernätet som ska vitlistas. Om du anger ett undernät väljer du lämplig nätmask och klickar på **OK**. Nu har den tillförlitliga IP-adressen lagts till.

<center>![Installation](./media/multi-factor-authentication-get-started-adfs-adfs2/trusted.png)</center>



<!--HONumber=Dec16_HO1-->


