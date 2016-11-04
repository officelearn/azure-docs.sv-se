---
title: RADIUS-autentisering och Azure Multi-Factor Authentication Server
description: Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera RADIUS-autentisering och Azure Multi-Factor Authentication Server.
services: multi-factor-authentication
documentationcenter: ''
author: kgremban
manager: femila
editor: curtand

ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/15/2016
ms.author: kgremban

---
# RADIUS-autentisering och Azure Multi-Factor Authentication Server
I avsnittet RADIUS-autentisering kan du aktivera och konfigurera RADIUS-autentisering för Azure Multi-Factor Authentication Server. RADIUS är ett standardprotokoll för att acceptera autentiseringsförfrågningar och för att bearbeta dessa. Azure Multi-Factor Authentication Server fungerar som en RADIUS-server och placeras mellan RADIUS-klienten (t.ex. VPN-enhet) och autentiseringsmålet, som kan vara Active Directory (AD), en LDAP-katalog eller en annan RADIUS-server, för att lägga till Azure Multi-Factor Authentication. För att Azure Multi-Factor Authentication ska fungera måste du konfigurera Azure Multi-Factor Authentication Server så att den kan kommunicera med både klientservrarna och autentiseringsmålet. Azure Multi-Factor Authentication Server tar emot förfrågningar från en RADIUS-klient, validerar autentiseringsuppgifter mot autentiseringsmålet, lägger till Azure Multi-Factor Authentication och skickar tillbaka ett svar till RADIUS-klienten. Hela autentiseringen lyckas bara om både den primär autentiseringen och Azure Multi-Factor Authentication lyckas.

> [!NOTE]
> MFA-servern stöder endast RADIUS-protokollen PAP (Password Authentication Protocol) och MSCHAPv2 (Microsoft Challenge Handshake Authentication Protocol) när den fungerar som en RADIUS-server.  Andra protokoll, till exempel EAP (Extensible Authentication Protocol), kan användas när MFA-servern fungerar som en RADIUS-proxy till en annan RADIUS-server som har stöd för det protokollet, till exempel Microsoft NPS.
> </br>
> När du använder andra protokoll i den här konfigurationen fungerar inte enkelriktade SMS- och OATH-token eftersom MFA-servern inte kan initiera ett lyckat RADIUS-kontrollsvar med det protokollet.
> 
> 

![RADIUS-autentisering](./media/multi-factor-authentication-get-started-server-rdg/radius.png)

## Konfiguration av RADIUS-autentisering
Om du vill konfigurera RADIUS-autentisering installerar du Azure Multi-Factor Authentication Server på en Windows-server. Om du har en Active Directory-miljö ansluter du servern till domänen i nätverket. Konfigurera Azure Multi-Factor Authentication Server genom att följa dessa steg:

1. Klicka på ikonen för RADIUS-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Markera kryssrutan Aktivera RADIUS-autentisering.
3. Ändra autentiserings- och redovisningsportarna på fliken Klienter om RADIUS-tjänsten för Azure Multi-Factor Authentication ska bindas till andra portar än standardportarna för att lyssna efter RADIUS-förfrågningar från klienterna som ska konfigureras.
4. Klicka på Lägg till.
5. I dialogrutan Lägg till RADIUS-klient anger du IP-adressen för enheten/servern som ska autentisera mot Azure Multi-Factor Authentication-servern, ett programnamn (valfritt) och en delad hemlighet. Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och enheten/servern. Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
6. Markera rutan Kräv Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
7. Markera kryssrutan Aktivera OATH-reservtoken om användarna ska använda Azure Multi-Factor Authentication via en mobilapp och om du vill använda OATH-lösenord som reservautentisering för SMS, push-meddelanden eller telefonsamtal utanför IP-nätverket.
8. Klicka på OK.
9. Du kan upprepa steg 4 till och med 8 om du vill lägga till fler RADIUS-klienter.
10. Klicka på fliken Mål.
11. Om Azure Multi-Factor Authentication Server är installerad på en domänansluten server i en Active Directory-miljö väljer du Windows-domän.
12. Om användarna ska autentiseras mot en LDAP-katalog väljer du LDAP-bindning. När du använder LDAP-bindning måste du klicka på ikonen för Katalogintegrering och redigera LDAP-konfigurationen på fliken Inställningar så att servern kan bindas till katalogen. Anvisningar för hur du konfigurerar LDAP finns i LDAP-proxykonfigurationsguiden.
13. Om användarna ska verifieras mot en annan RADIUS-server väljer du RADIUS-server.
14. Konfigurera servern som RADIUS-förfrågningarna ska skickas till genom att klicka på Lägg till.
15. I dialogrutan Lägg till RADIUS-server anger du IP-adressen för RADIUS-servern och en delad hemlighet. Den delade hemligheten måste vara samma på både Azure Multi-Factor Authentication-servern och RADIUS-servern. Ändra autentiseringsporten och redovisningsporten om olika portar används av RADIUS-servern.
16. Klicka på OK.
17. Du måste lägga till Azure Multi-Factor Authentication Server som en RADIUS-klient på den andra RADIUS-servern så att den bearbetar åtkomstförfrågningar som skickats till den från Azure Multi-Factor Authentication Server. Du måste använda samma delade hemlighet som konfigurerats i Azure Multi-Factor Authentication Server.
18. Du kan upprepa det här steget om du vill lägga till fler RADIUS-servrar och konfigurera ordningen som servern ska anropa dem med knapparna Flytta upp och Flytta ned. Nu är Azure Multi-Factor Authentication Server-konfigurationen klar. Nu lyssnar servern på de konfigurerade portarna för RADIUS-åtkomstförfrågningar från de konfigurerade klienterna.   

## RADIUS-klientkonfiguration
Följ dessa riktlinjer när du konfigurerar RADIUS-klienten:

* Konfigurera enheten/servern att autentisera via RADIUS till Azure Multi-Factor Authentication-serverns IP-adress, som fungerar som RADIUS-servern.
* Använd samma delade hemlighet som du konfigurerade ovan.
* Konfigurera RADIUS-serverns timeout-värde till mellan 30 och 60 sekunder så att det finns tid att verifiera användarens autentiseringsuppgifter, utföra Multi-Factor Authentication och sedan svara på RADIUS-åtkomstbegäran.

<!--HONumber=Sep16_HO3-->


