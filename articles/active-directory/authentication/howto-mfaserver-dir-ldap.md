---
title: LDAP-autentisering och Azure MFA Server – Azure Active Directory
description: Distribuera LDAP-autentisering och Azure Multi-Factor Authentication Server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 093849e10e9776327a54ea3a9ae22b863a528d37
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/22/2019
ms.locfileid: "58367872"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-autentisering och Azure Multi-Factor Authentication Server

Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När du är ansluten till en katalog via LDAP, kan Azure Multi-Factor Authentication Server fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare med IIS-autentisering, eller för primär autentisering i Azure MFA-användarportalen.

Infoga Azure Multi-Factor Authentication Server mellan LDAP-klient (till exempel VPN-enhet, program) och LDAP-katalogservern för att använda Azure Multi-Factor Authentication som en LDAP-proxy. Azure Multi-Factor Authentication-servern måste vara konfigurerad att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om LDAP-katalogen valideras de primära autentiseringsuppgifterna Azure Multi-Factor Authentication utför en andra identitetsverifiering och skickar tillbaka ett svar till LDAP-klienten. Hela autentisering fungerar endast om både LDAP-serverautentiseringen och det andra stegets verifiering lyckas.

## <a name="configure-ldap-authentication"></a>Konfigurera LDAP-autentisering

Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

### <a name="add-an-ldap-client"></a>Lägg till en LDAP-klient

1. Välj ikonen LDAP-autentisering på den vänstra menyn i Azure Multi-Factor Authentication-servern.
2. Markera kryssrutan **Aktivera LDAP-autentisering**.

   ![LDAP-autentisering i MFA Server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. På fliken Klienter ändrar du TCP- och SSL-porten om Azure Multi-Factor Authentication LDAP-tjänsten ska binda till icke-standardportar för att lyssna efter LDAP-begäranden.
4. Om du planerar att använda LDAPS från klienten till Azure Multi-Factor Authentication Server, måste ett SSL-certifikat installeras på samma server som MFA-servern. Klicka på **Bläddra** bredvid SSL-certifikatet rutan och välj ett certifikat för säker anslutning.
5. Klicka på **Lägg till**.
6. I dialogrutan Lägg till LDAP-klient anger du IP-adressen för enheten, servern eller programmet som autentiserar mot servern samt programnamnet (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett stort antal användare inte har ännu importerats till servern eller undantas från tvåstegsverifiering, lämnar du rutan avmarkerad. Se MFA-servern hjälpa för ytterligare information om den här funktionen.

Upprepa de här stegen för att lägga till ytterligare LDAP-klienter.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurera LDAP-kataloganslutningen

När Azure Multi-Factor Authentication har konfigurerats att ta emot LDAP-autentiseringar måste dessa autentiseringar skickas via en proxyanslutning till LDAP-katalogen. Därför visar fliken Mål endast ett nedtonat alternativ för att använda ett LDAP-mål.

1. För att konfigurera anslutningen till LDAP-katalogen, klickar du på ikonen **Katalogintegrering**.
2. På fliken Inställningar väljer du alternativknappen **Använd specifik LDAP-konfiguration**.
3. Välj **Redigera...**
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till LDAP-katalogen. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.

    ![Directory-integrering LDAP-konfiguration](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Testa LDAP-anslutningen genom att klicka på knappen **Testa**.
6. Klicka på **OK** om LDAP-anslutningstestet lyckas.
7. Klicka på fliken **Filter**. Servern är förkonfigurerad att läsa in containrar, säkerhetsgrupper och användare från Active Directory. Om du binder till en annan LDAP-katalog, behöver du förmodligen redigera filtren som visas. Klicka på **Hjälp**-länken för mer information om filter.
8. Klicka på fliken **Attribut**. Servern är förkonfigurerad att mappa attribut från Active Directory.
9. Om du binder till en annan LDAP-katalog eller för att ändra de förkonfigurerade attributmappningarna, klickar du på **Redigera…**
10. I dialogrutan Redigera attribut ändrar du LDAP-attributmappningarna för din katalog. Attributnamn kan skrivas in eller väljas genom att klicka på **…** bredvid respektive fält. Klicka på **Hjälp**-länken för mer information om attribut.
11. Klicka på **OK**.
12. Klicka på ikonen **Företagsinställningar** och välj fliken **Matcha användarnamn**.
13. Om du ansluter till Active Directory från en domänansluten server, lämnar du alternativknappen **Använd Windows-säkerhetsidentifierare (SID:er) för matchning av användarnamn** markerad. Annars väljer du alternativknappen **Använd LDAP-unika identifierarattribut för matchning av användarnamn**.

När alternativknappen **Använd LDAP-unikt identifierarattribut för matchning av användarnamn** markerats, försöker Azure Multi-Factor Authentication-servern matcha varje användarnamn till en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs i användarnamnattributen som definierats i fliken Katalogintegrering -> Attribut. När en användare autentiseras matchas användarnamnet med den unika identifieraren i LDAP-katalogen. Den unika identifieraren används för att matcha användaren i Azure Multi-Factor Authentication-datafilen. Det möjliggör skiftlägeskänsliga jämförelser och långa och korta användarnamnsformat.

När du har slutfört de här stegen MFA-servern lyssnar på de konfigurerade portarna efter LDAP-åtkomstförfrågningar från de konfigurerade klienterna och fungerar som proxy för dessa begäranden till LDAP-katalogen för autentisering.

## <a name="configure-ldap-client"></a>Konfigurera LDAP-klient

Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera din enhet, server eller program för att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som ska vara samma som för Azure Multi-Factor Authentication-servern.
* Konfigurera timeout-värde för LDAP till 30 och 60 sekunder så att det finns tid att validera användarens autentiseringsuppgifter med LDAP-katalogen, utföra andra autentiseringssteget, ta emot svaret och svara på LDAP-åtkomstförfrågan.
* Om du använder LDAPS måste enheten eller servern som kör LDAP-frågorna lita på SSL-certifikatet som är installerat på Azure Multi-Factor Authentication-servern.
