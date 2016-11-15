---
title: LDAP-autentisering och Azure Multi-Factor Authentication Server
description: "Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera LDAP-autentisering och Azure Multi-Factor Authentication Server."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: curtand
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/04/2016
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 1c57047f9a7eefcc0c4dbf08e705c3193e89f9a7


---
# <a name="ldap-authentication-and-azure-multifactor-authentication-server"></a>LDAP-autentisering och Azure Multi-Factor Authentication Server
Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När den konfigureras att ansluta till en katalog via LDAP kan Azure Multi-Factor Authentication Server konfigureras att fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare när IIS-autentisering används eller primär autentisering på Azure Multi-Factor Authentication-användarportalen.

När du använder Azure Multi-Factor Authentication som en LDAP-proxy placeras Azure Multi-Factor Authentication Server mellan LDAP-klienten (t.ex. en VPN-enhet eller ett program) och LDAP-katalogservern för att lägga till multifaktorautentisering. För att Azure Multi-Factor Authentication ska fungera måste Multi-Factor Authentication Server konfigureras att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om svaret från LDAP-katalogen visar att de primära autentiseringsuppgifterna är giltiga utför Azure Multi-Factor Authentication en autentisering med en andra faktor och skickar tillbaka ett svar till LDAP-klienten. Autentiseringen lyckas bara om både autentiseringen till LDAP-servern och multifaktorautentiseringen lyckas.

## <a name="ldap-authentication-configuration"></a>Konfiguration av LDAP-autentisering
Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

1. Klicka på ikonen för LDAP-autentisering på den vänstra menyn i Azure Multi-Factor Authentication Server.
2. Markera kryssrutan Aktivera LDAP-autentisering.![LDAP-autentisering](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)
3. På fliken Klienter ändrar du TCP- och SSL-porten om LDAP-tjänsten för Azure Multi-Factor Authentication ska binda till andra portar än standardportarna för att lyssna efter LDAP-förfrågningar från klienterna som ska konfigureras.
4. Om du planerar att använda LDAPS från klienten till Azure Multi-Factor Authentication Server måste ett SSL-certifikat vara installerat på servern som Server körs på. Klicka på Bläddra bredvid rutan SSL-certifikat och välj det installerade certifikatet som ska användas för den säkra anslutningen.
5. Klicka på Lägg till.
6. I dialogrutan Lägg till LDAP-klient anger du IP-adressen för enheten, servern eller programmet som ska autentisera mot servern samt programnamnet (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan Kräv Azure Multi-Factor Authentication-användarmatchning om alla användare har importerats eller ska importeras till servern och använda multifaktorautentisering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från multifaktorautentisering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.
8. Du kan upprepa steg 5 till 7 om du vill lägga till fler LDAP-klienter.
9. När Azure Multi-Factor Authentication har konfigurerats att ta emot LDAP-autentiseringar måste dessa autentiseringar skickas via en proxyanslutning till LDAP-katalogen. Därför visar fliken Mål endast ett nedtonat alternativ för att använda ett LDAP-mål. Klicka på ikonen Katalogintegrering för att konfigurera LDAP-kataloganslutningen.
10. På fliken Inställningar väljer du alternativknappen Använd specifik LDAP-konfiguration.
11. Klicka på knappen till.
12. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till LDAP-katalogen. Beskrivningar av fälten finns i tabellen nedan. Obs! Den här informationen finns också i hjälpfilen för Azure Multi-Factor Authentication Server.![Katalogintegrering](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)
13. Testa LDAP-anslutningen genom att klicka på knappen Testa.
14. Klicka på OK om LDAP-anslutningstestet lyckas.
15. Klicka på fliken Filter. Servern är förkonfigurerad att läsa in behållare, säkerhetsgrupper och användare från Active Directory. Om du binder till en annan LDAP-katalog måste du förmodligen redigera filtren som visas. Klicka på länken Hjälp om du vill ha mer information om filter.
16. Klicka på fliken Attribut. Servern är förkonfigurerad att mappa attribut från Active Directory.
17. Om du binder till en annan LDAP-katalog eller om du vill ändra de förkonfigurerade attributmappningarna klickar du på Redigera.
18. I dialogrutan Redigera attribut ändrar du LDAP-attributmappningarna för din katalog. Du kan skriva attributnamnen eller välja dem genom att klicka på knappen ”...” bredvid respektive fält.
19. Klicka på länken Hjälp om du vill ha mer information om attribut.
20. Klicka på OK.
21. Klicka på ikonen Företagsinställningar och välj fliken Matcha användarnamn.
    22. Om du ansluter till Active Directory från en domänansluten server bör du kunna lämna alternativknappen Matcha användarnamn med Windows säkerhetsidentifierare (SID) markerad. Annars väljer du alternativknappen Matcha användarnamn med unikt identifierarattribut för LDAP. Om det här alternativet är valt försöker Azure Multi-Factor Authentication Server matcha varje användarnamn med en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs i attributen för användarnamn som definierats under Katalogintegrering > fliken Attribut. När en användare autentiseras matchas användarnamnet med den unika identifieraren i LDAP-katalogen och den unika identifieraren används för att matcha användaren i Azure Multi-Factor Authentication-datafilen. Detta ger stöd för skiftlägeskänsliga jämförelser, samt långa och korta format för användarnamn. Det här är det sista steget i Azure Multi-Factor Authentication Server-konfigurationen. Nu lyssnar servern på de konfigurerade portarna efter LDAP-åtkomstförfrågningar från de konfigurerade klienterna och är konfigurerad att skicka dessa förfrågningar via en proxyanslutning till LDAP-katalogen för autentisering.

## <a name="ldap-client-configuration"></a>LDAP-klientkonfiguration
Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera enheten, servern eller programmet att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som är de som används för Azure Multi-Factor Authentication Server.
* Konfigurera timeout-gränsen för LDAP till 30 till 60 sekunder så att det finns tillräckligt med tid att validera användarens autentiseringsuppgifter med LDAP-katalogen, utföra en autentisering med en andra faktor, ta emot svaret och sedan svara på LDAP-åtkomstförfrågan.
* Om du använder LDAPS måste enheten eller servern som kör LDAP-frågorna lita på SSL-certifikatet som är installerat på Azure Multi-Factor Authentication-servern.




<!--HONumber=Nov16_HO2-->


