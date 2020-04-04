---
title: LDAP-autentisering och Azure MFA Server – Azure Active Directory
description: Distribuera LDAP-autentisering och Azure Multi Factor Authentication Server.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/11/2018
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2df89a764bc9b92a830b13e4216412694bb95523
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80653023"
---
# <a name="ldap-authentication-and-azure-multi-factor-authentication-server"></a>LDAP-autentisering och Azure Multi Factor Authentication Server

Som standard konfigureras Azure Multi-Factor Authentication Server att importera eller synkronisera användare från Active Directory. Tjänsten kan dock konfigureras att binda till olika LDAP-kataloger, till exempel en ADAM-katalog, eller en särskild Active Directory-domänkontrollant. När azure Multi-Factor Authentication Server är ansluten till en katalog via LDAP kan den fungera som en LDAP-proxy för att utföra autentiseringar. Den stöder också användningen av LDAP-bindningar som ett RADIUS-mål, förautentisering av användare med IIS-autentisering, eller för primär autentisering i Azure MFA-användarportalen.

Om du vill använda Azure Multi Factor Authentication som en LDAP-proxy infogar du Azure Multi-Factor Authentication Server mellan LDAP-klienten (till exempel VPN-apparat, program) och LDAP-katalogservern. Azure Multi-Factor Authentication-servern måste vara konfigurerad att kommunicera med både klientservrarna och LDAP-katalogen. I den här konfigurationen accepterar Azure Multi-Factor Authentication Server LDAP-förfrågningar från klientservrar och klientprogram och vidarebefordrar dem till mål-LDAP-katalogservern för verifiering av de primära autentiseringsuppgifterna. Om LDAP-katalogen validerar de primära autentiseringsuppgifterna utför Azure Multi-Factor Authentication en andra identitetsverifiering och skickar ett svar tillbaka till LDAP-klienten. Hela autentisering fungerar endast om både LDAP-serverautentiseringen och det andra stegets verifiering lyckas.

> [!IMPORTANT]
> Från och med den 1 juli 2019 kommer Microsoft inte längre att erbjuda MFA Server för nya distributioner. Nya kunder som vill kräva multifaktorautentisering från sina användare bör använda molnbaserad Azure Multi-Factor-autentisering. Befintliga kunder som har aktiverat MFA Server före den 1 juli kommer att kunna ladda ner den senaste versionen, framtida uppdateringar och generera aktiveringsautentiseringsuppgifter som vanligt.

## <a name="configure-ldap-authentication"></a>Konfigurera LDAP-autentisering

Om du vill konfigurera LDAP-autentisering installerar du Azure Multi-Factor Authentication-servern på en Windows-server. Följ dessa steg:

### <a name="add-an-ldap-client"></a>Lägg till en LDAP-klient

1. I Azure Multi Factor Authentication Server väljer du ikonen LDAP-autentisering på den vänstra menyn.
2. Markera kryssrutan **Aktivera LDAP-autentisering.**

   ![LDAP-autentisering i MFA-server](./media/howto-mfaserver-dir-ldap/ldap2.png)

3. På fliken Klienter ändrar du TCP-port och SSL-port (TLS) om Azure Multi-Factor Authentication LDAP-tjänsten ska binda till icke-standardportar för att lyssna efter LDAP-begäranden.
4. Om du planerar att använda LDAPS från klienten till Azure Multi-Factor Authentication Server måste ett TLS/SSL-certifikat installeras på samma server som MFA Server. Klicka på **Bläddra** bredvid rutan SSL(TLS) och välj ett certifikat som ska användas för den säkra anslutningen.
5. Klicka på **Lägg till**.
6. I dialogrutan Lägg till LDAP-klient anger du IP-adressen för den installation, server eller det program som autentiserar till servern och ett programnamn (valfritt). Programnamnet visas i Azure Multi-Factor Authentication-rapporter och kan visas i autentiseringsmeddelanden i SMS- eller mobilappar.
7. Markera rutan **Kräv Azure Multi-Factor Authentication-användarmatchning** om alla användare har importerats eller ska importeras till servern och använda tvåstegsverifiering. Om ett betydande antal användare ännu inte har importerats till servern och/eller är undantagna från tvåstegsverifiering lämnar du rutan omarkerad. Mer information om den här funktionen finns i hjälpfilen för MFA Server.

Upprepa de här stegen för att lägga till ytterligare LDAP-klienter.

### <a name="configure-the-ldap-directory-connection"></a>Konfigurera LDAP-kataloganslutningen

När Azure Multi-Factor Authentication har konfigurerats att ta emot LDAP-autentiseringar måste dessa autentiseringar skickas via en proxyanslutning till LDAP-katalogen. Därför visar fliken Mål endast ett nedtonat alternativ för att använda ett LDAP-mål.

> [!NOTE]
> Katalogintegration garanteras inte att fungera med andra kataloger än Active Directory Domain Services.

1. Om du vill konfigurera LDAP-kataloganslutningen klickar du på ikonen **Katalogintegration.**
2. Välj alternativknappen Använd **specifik LDAP-konfiguration** på fliken Inställningar.
3. Välj **Redigera...**
4. I dialogrutan Redigera LDAP-konfiguration fyller du i fälten med den information som krävs för att ansluta till LDAP-katalogen. Fältbeskrivningar finns inkluderade i hjälpfilen för Azure Multi-Factor Authentication-server.

    ![LDAP-konfiguration för katalogintegration](./media/howto-mfaserver-dir-ldap/ldap.png)

5. Testa LDAP-anslutningen genom att klicka på **knappen Testa.**
6. Om LDAP-anslutningstestet lyckades klickar du på **OK** OK-knappen.
7. Klicka på fliken **Filter.** Servern är förkonfigurerad för att läsa in behållare, säkerhetsgrupper och användare från Active Directory. Om du binder till en annan LDAP-katalog, behöver du förmodligen redigera filtren som visas. Klicka på **hjälplänken** om du vill ha mer information om filter.
8. Klicka på fliken **Attribut.** Servern är förkonfigurerad för att mappa attribut från Active Directory.
9. Om du binder till en annan LDAP-katalog eller för att ändra de förkonfigurerade attributmappningarna, klickar du på **Redigera…**
10. I dialogrutan Redigera attribut ändrar du LDAP-attributmappningarna för din katalog. Attributnamn kan skrivas in eller markeras genom att klicka på **...** bredvid respektive fält. Klicka på **hjälplänken** om du vill ha mer information om attribut.
11. Klicka på **OK**.
12. Klicka på ikonen **Företagsinställningar** och välj fliken **Användarnamnsupplösning.**
13. Om du ansluter till Active Directory från en domänansluten server, lämnar du alternativknappen **Använd Windows-säkerhetsidentifierare (SID:er) för matchning av användarnamn** markerad. Annars väljer du **attributet Använd LDAP-unik identifierare för matchande användarnamn.**

När alternativknappen **Använd LDAP-unikt identifierarattribut för matchning av användarnamn** markerats, försöker Azure Multi-Factor Authentication-servern matcha varje användarnamn till en unik identifierare i LDAP-katalogen. En LDAP-sökning utförs på de användarnamnsattribut som definierats på fliken Katalogintegration -> attribut. När en användare autentiserar matchas användarnamnet till den unika identifieraren i LDAP-katalogen. Den unika identifieraren används för att matcha användaren i datafilen för multifaktorautentisering i Azure. Detta möjliggör skiftlägesokänsliga jämförelser och långa och korta användarnamnsformat.

När du har slutfört de här stegen lyssnar MFA-servern på de konfigurerade portarna för LDAP-åtkomstbegäranden från de konfigurerade klienterna och fungerar som en proxy för dessa begäranden till LDAP-katalogen för autentisering.

## <a name="configure-ldap-client"></a>Konfigurera LDAP-klient

Följ dessa riktlinjer när du konfigurerar LDAP-klienten:

* Konfigurera din enhet, server eller program för att autentisera via LDAP till Azure Multi-Factor Authentication-servern som om det vore din LDAP-katalog. Använd samma inställningar som du använder normalt för att ansluta direkt till LDAP-katalogen, förutom servernamnet eller IP-adressen som ska vara samma som för Azure Multi-Factor Authentication-servern.
* Konfigurera timeouten för LDAP till 30-60 sekunder så att det finns tid att validera användarens autentiseringsuppgifter med LDAP-katalogen, utföra verifiering i andra steget, ta emot deras svar och svara på LDAP-åtkomstbegäran.
* Om du använder LDAPS måste den installation eller server som gör LDAP-frågorna lita på TLS/SSL-certifikatet som är installerat på Azure Multi-Factor Authentication Server.
