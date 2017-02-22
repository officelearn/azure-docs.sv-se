---
title: LDAP-autentisering och Azure MFA Server | Microsoft-dokument
description: "Det här är sidan om Azure Multi-Factor Authentication som hjälper dig att distribuera LDAP-autentisering och Azure Multi-Factor Authentication Server."
services: multi-factor-authentication
documentationcenter: 
author: kgremban
manager: femila
editor: yossib
ms.assetid: e1a68568-53d1-4365-9e41-50925ad00869
ms.service: multi-factor-authentication
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 01/03/2017
ms.author: kgremban
translationtype: Human Translation
ms.sourcegitcommit: 1c6386dda94a3e0ca6eb340f542d04cb336159c3
ms.openlocfilehash: 0de97050e385e3efb9e63bbf934712157ab0d0af


---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-autentisering och Azure Multi-Factor Authentication Server
Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När den ansluter till en katalog via LDAP, kan Azure Multi-Factor Authentication-servern konfigureras att fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare med IIS-autentisering, eller för primär autentisering i Azure MFA-användarportalen.

För att använda Azure Multi-Factor Authentication som en LDAP-proxy, placerar du Azure Multi-Factor Authentication-servern mellan LDAP-klienten (t.ex. en VPN-enhet eller ett program) och LDAP-katalogservern. Azure Multi-Factor Authentication-servern måste vara konfigurerad att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om svaret från LDAP-katalogen visar att de primära autentiseringsuppgifterna är giltiga, utför Azure Multi-Factor Authentication en andra identitetsverifiering och skickar tillbaka ett svar till LDAP-klienten. Hela autentisering fungerar endast om både LDAP-serverautentiseringen och det andra stegets verifiering lyckas.

## <a name="ldap-authentication-configuration"></a>Konfiguration av LDAP-autentisering
Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

### <a name="add-an-ldap-client"></a>Lägg till en LDAP-klient

1. I Azure Multi-Factor Authentication-server väljer du ikonen LDAP-autentisering i vänster meny.
2. Markera kryssrutan **Aktivera LDAP-autentisering**.

        ![LDAP Authentication](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. På fliken Klienter ändrar du TCP- och SSL-porten om Azure Multi-Factor Authentication LDAP-tjänsten ska binda till icke-standardportar för att lyssna efter LDAP-begäranden.
4. Om du planerar att använda LDAPS från klienten till Azure Multi-Factor Authentication Server måste ett SSL-certifikat vara installerat på servern som Server körs på. Klicka på **Bläddra…** bredvid rutan SSL-certifikat och välj det installerade certifikatet som ska användas för den säkra anslutningen.
5. Klicka på **Lägg till...**
6. I dialogrutan Lägg till LDAP-klient, anger du IP-adressen för enheten, servern eller programmet som ska autentisera mot servern samt programnamnet (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har importerats till servern än, eller om de ska undantas från tvåstegsverifiering, lämnar du rutan avmarkerad. Mer information om den här funktionen finns i hjälpfilen.

Upprepa de här stegen för att lägga till ytterligare LDAP-klienter.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurera LDAP-kataloganslutningen

När Azure Multi-Factor Authentication har konfigurerats att ta emot LDAP-autentiseringar måste dessa autentiseringar skickas via en proxyanslutning till LDAP-katalogen. Därför visar fliken Mål endast ett nedtonat alternativ för att använda ett LDAP-mål.

1. För att konfigurera anslutningen till LDAP-katalogen, klickar du på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativknappen **Använd specifik LDAP-konfiguration**.
3. Välj **Redigera...**
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till LDAP-katalogen. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.

    ![Katalogintegrering](./media/multi-factor-authentication-get-started-server-ldap/ldap.png)

5. Testa LDAP-anslutningen genom att klicka på knappen **Testa**.
6. Klicka på **OK** om LDAP-anslutningstestet lyckas.
7. Klicka på fliken **Filter**. Servern är förkonfigurerad att läsa in behållare, säkerhetsgrupper och användare från Active Directory. Om du binder till en annan LDAP-katalog, behöver du förmodligen redigera filtren som visas. Klicka på **Hjälp**-länken för mer information om filter.
8. Klicka på fliken **Attribut**. Servern är förkonfigurerad att mappa attribut från Active Directory.
9. Om du binder till en annan LDAP-katalog eller för att ändra de förkonfigurerade attributmappningarna, klickar du på **Redigera…**
10. I dialogrutan Redigera attribut ändrar du LDAP-attributmappningarna för din katalog. Attributnamn kan skrivas in eller väljas genom att klicka på **…** bredvid respektive fält. Klicka på **Hjälp**-länken för mer information om attribut.
11. Klicka på **OK**.
12. Klicka på ikonen **Företagsinställningar** och välj fliken **Matcha användarnamn**.
13. Om du ansluter till Active Directory från en domänansluten server, lämnar du alternativknappen **Använd Windows-säkerhetsidentifierare (SID:er) för matchning av användarnamn** markerad. Annars väljer du alternativknappen **Använd LDAP-unika identifierarattribut för matchning av användarnamn**. 

När alternativknappen **Använd LDAP-unikt identifierarattribut för matchning av användarnamn** markerats, försöker Azure Multi-Factor Authentication-servern matcha varje användarnamn till en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs i användarnamnattributen som definierats i fliken Katalogintegrering -> Attribut. När en användare autentiserar sig, matchas användarnamnet till den unika identifieraren i LDAP-katalogen och den unika identifieraren används för att matcha användaren i Azure Multi-Factor Authentication-datafilen. Det tillåter skiftlägesokänsliga jämförelser, såväl som långa och korta användarnamnsformat.

När du slutfört de här stegen, börjar MFA-servern lyssna på de konfigurerade portarna efter LDAP-åtkomstförfrågningar från de konfigurerade klienterna och är konfigurerat att skicka dessa förfrågningar via en proxy till LDAP-katalogen för autentisering.

## <a name="ldap-client-configuration"></a>LDAP-klientkonfiguration
Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera din enhet, server eller program för att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som ska vara samma som för Azure Multi-Factor Authentication-servern.
* Konfigurera timeout-gränsen för LDAP till 30-60 sekunder så att det finns tillräckligt med tid att validera användarens autentiseringsuppgifter med LDAP-katalogen, utföra det andra autentiseringssteget, ta emot svaret och sedan svara på LDAP-åtkomstförfrågan.
* Om du använder LDAPS måste enheten eller servern som kör LDAP-frågorna lita på SSL-certifikatet som är installerat på Azure Multi-Factor Authentication-servern.




<!--HONumber=Feb17_HO3-->


