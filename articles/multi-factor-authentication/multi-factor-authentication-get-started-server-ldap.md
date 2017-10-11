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
ms.topic: article
ms.date: 05/03/2017
ms.author: kgremban
ms.openlocfilehash: 8f4d5f9e84ad7bb4fff501370036e7f0da589bf3
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-autentisering och Azure Multi-Factor Authentication-servern
Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När du är ansluten till en katalog via LDAP kan Azure Multi-Factor Authentication-Server fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare med IIS-autentisering, eller för primär autentisering i Azure MFA-användarportalen.

Om du vill använda Azure Multi-Factor Authentication som en LDAP-proxy, infoga Azure Multi-Factor Authentication-servern mellan LDAP-klienten (t.ex, VPN-enhet, program) och LDAP-katalogserver. Azure Multi-Factor Authentication-servern måste vara konfigurerad att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om LDAP-katalogen validerar primära behörigheterna, utför en andra identitetsverifiering Azure Multi-Factor Authentication och skickar tillbaka ett svar till LDAP-klienten. Hela autentisering fungerar endast om både LDAP-serverautentiseringen och det andra stegets verifiering lyckas.

## <a name="configure-ldap-authentication"></a>Konfigurera LDAP-autentisering
Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

### <a name="add-an-ldap-client"></a>Lägg till en LDAP-klient

1. Välj ikonen LDAP-autentisering i den vänstra menyn i Azure Multi-Factor Authentication-servern.
2. Markera kryssrutan **Aktivera LDAP-autentisering**.

   ![LDAP-autentisering](./media/multi-factor-authentication-get-started-server-ldap/ldap2.png)

3. På fliken Klienter ändrar du TCP- och SSL-porten om Azure Multi-Factor Authentication LDAP-tjänsten ska binda till icke-standardportar för att lyssna efter LDAP-begäranden.
4. Om du tänker använda LDAPS från klienten till Azure Multi-Factor Authentication-servern installeras ett SSL-certifikat på samma server som MFA-servern. Klicka på **Bläddra** bredvid SSL-certifikatet kryssrutan och välj ett certifikat för säker anslutning.
5. Klicka på **Lägg till**.
6. I dialogrutan Lägg till LDAP-klienten anger du IP-adressen för installation, server eller program som autentiserar till servern och ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har ännu importerats till servern och/eller undantas från tvåstegsverifiering, lämnar du rutan avmarkerad. Se MFA-Server hjälpfilen för mer information om den här funktionen.

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

När alternativknappen **Använd LDAP-unikt identifierarattribut för matchning av användarnamn** markerats, försöker Azure Multi-Factor Authentication-servern matcha varje användarnamn till en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs i användarnamnattributen som definierats i fliken Katalogintegrering -> Attribut. När en användare autentiseras matchas användarnamnet Unik identifierare i LDAP-katalogen. Den unika identifieraren används för att matcha användaren i Azure Multi-Factor Authentication-datafilen. Detta möjliggör skiftlägeskänsliga jämförelser och långa och korta användarnamnformat.

När du har slutfört de här stegen MFA-servern lyssnar på de konfigurerade portarna för LDAP-förfrågningar från konfigurerade klienter och fungerar som proxy för dessa begäranden till LDAP-katalogen för autentisering.

## <a name="configure-ldap-client"></a>Konfigurera LDAP-klient
Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera din enhet, server eller program för att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som ska vara samma som för Azure Multi-Factor Authentication-servern.
* Konfigurera LDAP-timeout till 30 till 60 sekunder så att det är dags att verifiera användarens autentiseringsuppgifter med LDAP-katalogen, utföra andra steg verifieringen, deras svar och LDAP-begäran om åtkomst.
* Om du använder LDAPS måste enheten eller servern som kör LDAP-frågorna lita på SSL-certifikatet som är installerat på Azure Multi-Factor Authentication-servern.

