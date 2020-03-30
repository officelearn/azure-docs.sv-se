---
title: VPN med Azure MFA med NPS-tillägget - Azure Active Directory
description: Integrera din VPN-infrastruktur med Azure MFA med hjälp av tillägget Network Policy Server för Microsoft Azure.
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: f446f1549b3efcd5f27752fac972dfd80c8650d6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75425410"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrera din VPN-infrastruktur med Azure MFA med hjälp av tillägget Network Policy Server för Azure

Nps-tillägget (Network Policy Server) för Azure gör det möjligt för organisationer att skydda RADIUS-klientautentisering (Remote Authentication Dial-In User Service) med hjälp av molnbaserad [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), som ger tvåstegsverifiering.

Den här artikeln innehåller instruktioner för hur du integrerar NPS-infrastruktur med MFA med hjälp av NPS-tillägget för Azure. Den här processen möjliggör säker tvåstegsverifiering för användare som försöker ansluta till nätverket med hjälp av en VPN.

Nätverksprincip- och åtkomsttjänster ger organisationer möjlighet att:

* Tilldela en central plats för hantering och kontroll av nätverksbegäranden för att ange:

  * Vem kan ansluta

  * Vilka tider på dygnet anslutningar är tillåtna

  * Varaktigheten av anslutningar

  * Den säkerhetsnivå som klienter måste använda för att ansluta

    I stället för att ange principer för varje VPN- eller Fjärrskrivbordsgatewayserver gör du det när de har en central plats. RADIUS-protokollet används för att tillhandahålla centraliserad autentisering, auktorisering och redovisning (AAA).

* Upprätta och tillämpa NAP-klientprinciper (Network Access Protection) som avgör om enheter beviljas obegränsad eller begränsad åtkomst till nätverksresurser.

* Skapa ett sätt att framtvinga autentisering och auktorisering för åtkomst till trådlösa åtkomstpunkter och Ethernet-switchar med 802,1 x x kompatibla.
  Mer information finns i [Nätverksprincipserver](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

För att öka säkerheten och tillhandahålla en hög efterlevnadsnivå kan organisationer integrera NPS med Azure Multi-Factor Authentication för att säkerställa att användare använder tvåstegsverifiering för att ansluta till den virtuella porten på VPN-servern. För att användare ska beviljas åtkomst måste de ange sin kombination av användarnamn och lösenord och annan information som de kontrollerar. Denna information måste vara betrodd och inte lätt dupliceras. Det kan innehålla ett mobilnummer, ett fast nummer eller ett program på en mobil enhet.

Innan NPS-tillägget för Azure var tillgängliga måste kunder som ville implementera tvåstegsverifiering för integrerade NPS- och MFA-miljöer konfigurera och underhålla en separat MFA-server i en lokal miljö. Den här typen av autentisering erbjuds av Remote Desktop Gateway och Azure Multi-Factor Authentication Server med RADIUS.

Med NPS-tillägget för Azure kan organisationer skydda RADIUS-klientautentisering genom att distribuera antingen en lokalt baserad MFA-lösning eller en molnbaserad MFA-lösning.

## <a name="authentication-flow"></a>Autentiseringsflöde

När användare ansluter till en virtuell port på en VPN-server måste de först autentisera med hjälp av en mängd olika protokoll. Protokollen tillåter användning av en kombination av användarnamn och lösenord och certifikatbaserade autentiseringsmetoder.

Förutom att autentisera och verifiera sin identitet måste användarna ha rätt behörighet att ringa upp. I enkla implementeringar anges uppringningsbehörigheter som tillåter åtkomst direkt på Active Directory-användarobjekten.

![På fliken Uppringning i användaregenskaper för Användare och datorer i Active Directory](./media/howto-mfa-nps-extension-vpn/image1.png)

I enkla implementeringar beviljar eller nekar varje VPN-server åtkomst baserat på principer som har definierats på varje lokal VPN-server.

I större och mer skalbara implementeringar centraliseras principerna som beviljar eller nekar VPN-åtkomst på RADIUS-servrar. I dessa fall fungerar VPN-servern som en ACCESS-server (RADIUS-klient) som vidarebefordrar anslutningsbegäranden och kontomeddelanden till en RADIUS-server. För att ansluta till den virtuella porten på VPN-servern måste användarna autentiseras och uppfylla de villkor som definieras centralt på RADIUS-servrar.

När NPS-tillägget för Azure är integrerat med NPS, ett framgångsrikt autentiseringsflöde resulterar, enligt följande:

1. VPN-servern tar emot en autentiseringsbegäran från en VPN-användare som innehåller användarnamnet och lösenordet för anslutning till en resurs, till exempel en fjärrskrivbordssession.
2. Vpn-servern fungerar som en RADIUS-klient och konverterar begäran till ett RADIUS *Access-Request-meddelande* och skickar den (med ett krypterat lösenord) till RADIUS-servern där NPS-tillägget är installerat.
3. Kombinationen av användarnamn och lösenord verifieras i Active Directory. Om användarnamnet eller lösenordet är felaktigt skickar *RADIUS-servern ett Access-Reject-meddelande.*
4. Om alla villkor, som anges i NPS-anslutningsbegäran och nätverksprinciper, är uppfyllda (till exempel tid på dygnet eller gruppmedlemskap), utlöser NPS-tillägget en begäran om sekundär autentisering med Azure Multi-Factor Authentication.
5. Azure Multi-Factor Authentication kommunicerar med Azure Active Directory, hämtar användarens information och utför den sekundära autentiseringen med hjälp av den metod som konfigureras av användaren (mobiltelefonsamtal, sms eller mobilapp).
6. När MFA-utmaningen lyckas kommunicerar Azure Multi-Factor Authentication resultatet till NPS-tillägget.
7. När anslutningsförsöket har autentiserats och auktoriserats skickar NPS där tillägget är installerat ett RADIUS *Access-Accept-meddelande till VPN-servern* (RADIUS-klienten).
8. Användaren beviljas åtkomst till den virtuella porten på VPN-servern och upprättar en krypterad VPN-tunnel.

## <a name="prerequisites"></a>Krav

I det här avsnittet beskrivs de förutsättningar som måste slutföras innan du kan integrera MFA med VPN. Innan du börjar måste du ha följande förutsättningar på plats:

* VPN-infrastruktur
* Roll för nätverksprincip och åtkomsttjänster
* Licens för Azure Multi Factor-autentisering
* Windows Server-programvara
* Bibliotek
* Azure Active Directory (Azure AD) synkroniserad med lokal Active Directory
* AZURE Active Directory GUID-ID

### <a name="vpn-infrastructure"></a>VPN-infrastruktur

Den här artikeln förutsätter att du har en fungerande VPN-infrastruktur som använder Microsoft Windows Server 2016 och att VPN-servern för närvarande inte är konfigurerad för att vidarebefordra anslutningsbegäranden till en RADIUS-server. I artikeln konfigurerar du VPN-infrastrukturen så att den använder en central RADIUS-server.

Om du inte har en fungerande VPN-infrastruktur på plats kan du snabbt skapa en genom att följa vägledningen i många VPN-installationshandledningar som du kan hitta på Microsoft- och tredjepartswebbplatser.

### <a name="the-network-policy-and-access-services-role"></a>Rollen Nätverksprincip och åtkomsttjänster

Network Policy and Access Services tillhandahåller RADIUS-servern och klientfunktionerna. Den här artikeln förutsätter att du har installerat rollen Nätverksprincip och Åtkomsttjänster på en medlemsserver eller domänkontrollant i din miljö. I den här guiden konfigurerar du RADIUS för en VPN-konfiguration. Installera rollen Nätverksprincip och Åtkomsttjänster på en annan server *än* VPN-servern.

Information om hur du installerar rolltjänsten För nätverksprincip och Åtkomsttjänster Windows Server 2012 eller senare finns i [Installera en NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx). NAP är inaktuell i Windows Server 2016. En beskrivning av metodtips för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant, finns [i Metodtips för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA-licens

En licens krävs för Azure Multi-Factor Authentication och den är tillgänglig via en Azure AD Premium, Enterprise Mobility + Security eller en fristående multifaktorautentiseringslicens. Förbrukningsbaserade licenser för Azure MFA, till exempel per användare eller per autentiseringslicenser, är inte kompatibla med NPS-tillägget. Mer information finns i [Så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare, med rollen Nätverksprincip och Åtkomsttjänster installerad. Alla steg i den här guiden utfördes med Windows Server 2016.

### <a name="libraries"></a>Bibliotek

Följande bibliotek installeras automatiskt med NPS-tillägget:

-   [Visuella C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory-modul för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Om Microsoft Azure Active Directory PowerShell-modulen inte redan finns installeras den med ett konfigurationsskript som du kör som en del av installationsprocessen. Det finns ingen anledning att installera modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory synkroniserat med lokal Active Directory

Om du vill använda NPS-tillägget måste lokala användare synkroniseras med Azure Active Directory och aktiveras för MFA. Den här guiden förutsätter att lokala användare synkroniseras med Azure Active Directory via Azure AD Connect. Instruktioner för att aktivera användare för MFA finns nedan.

Information om Azure AD Connect finns i [Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>AZURE Active Directory GUID-ID

Om du vill installera NPS-tillägget måste du känna till GUID för Azure Active Directory. Instruktioner för att hitta GUID för Azure Active Directory finns i nästa avsnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurera RADIUS för VPN-anslutningar

Om du har installerat NPS-rollen på en medlemsserver måste du konfigurera den för att autentisera och auktorisera VPN-klienten som begär VPN-anslutningar. 

Det här avsnittet förutsätter att du har installerat rollen Nätverksprincip och Åtkomsttjänster men inte har konfigurerat den för användning i infrastrukturen.

> [!NOTE]
> Om du redan har en fungerande VPN-server som använder en centraliserad RADIUS-server för autentisering kan du hoppa över det här avsnittet.
>

### <a name="register-server-in-active-directory"></a>Registrera server i Active Directory

Om NPS-servern ska fungera korrekt i det här fallet måste den vara registrerad i Active Directory.

1. Öppna Serverhanteraren.

2. Välj **Verktyg**i Serverhanteraren och välj sedan **Nätverksprincipserver**.

3. Högerklicka på **NPS (Lokal)** i konsolen Nätverksprincipserver och välj sedan **Registrera server i Active Directory**. Välj **OK** två gånger.

    ![Registrera server i Active Directory-menyalternativet](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Lämna konsolen öppen för nästa procedur.

### <a name="use-wizard-to-configure-the-radius-server"></a>Använda guiden för att konfigurera RADIUS-servern

Du kan använda ett standardalternativ (guidebaserad) eller avancerat konfigurationsalternativ för att konfigurera RADIUS-servern. Det här avsnittet förutsätter att du använder det guidebaserade standardkonfigurationsalternativet.

1. Välj **NPS (Local)** i konsolen Nätverksprincipserver.

2. Under **Standardkonfiguration**väljer du **RADIUS-server för fjärranslutningar eller VPN-anslutningar**och väljer sedan **Konfigurera VPN eller Fjärranslutning**.

    ![Konfigurera RADIUS-server för fjärranslutningar eller VPN-anslutningar](./media/howto-mfa-nps-extension-vpn/image3.png)

3. I fönstret **Välj fjärranslutning eller virtuella privata nätverksanslutningar väljer** du **Virtuella privata nätverksanslutningar**och väljer sedan **Nästa**.

    ![Konfigurera virtuella privata nätverksanslutningar](./media/howto-mfa-nps-extension-vpn/image4.png)

4. Välj **Lägg till**i fönstret **Ange fjärranslutning eller VPN-server.**

5. Ange ett eget namn i **klientfönstret ny RADIUS,** ange VPN-serverns adress eller IP-adress och ange sedan ett delat hemligt lösenord. Gör det delade hemliga lösenordet långt och komplext. Spela in det, eftersom du behöver det i nästa avsnitt.

    ![Skapa ett nytt RADIUS-klientfönster](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Välj **OK**och välj sedan **Nästa**.

7. I fönstret **Konfigurera autentiseringsmetoder** godkänner du standardvalet **(Microsoft Encrypted Authentication version 2 [MS-CHAPv2])** eller väljer ett annat alternativ och väljer **Nästa**.

    > [!NOTE]
    > Om du konfigurerar EAP (Extensible Authentication Protocol) måste du använda antingen MICROSOFT Challenge-Handshake Authentication Protocol (CHAPv2) eller PEAP (Protected Extensible Authentication Protocol). Ingen annan EAP stöds.

8. I fönstret **Ange användargrupper** väljer du **Lägg till**och väljer sedan en lämplig grupp. Om det inte finns någon grupp lämnar du markeringen tom för att ge åtkomst till alla användare.

    ![Fönstret Ange användargrupper om du vill tillåta eller neka åtkomst](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Välj **Nästa**.

10. Välj **Nästa**i fönstret **Ange IP-filter** .

11. I fönstret **Ange krypteringsinställningar** godkänner du standardinställningarna och väljer sedan **Nästa**.

    ![Fönstret Ange krypteringsinställningar](./media/howto-mfa-nps-extension-vpn/image8.png)

12. I fönstret **Ange ett sfärnamn** lämnar du sfärnamnet tomt, accepterar standardinställningen och väljer sedan **Nästa**.

    ![Fönstret Ange ett sfärnamn](./media/howto-mfa-nps-extension-vpn/image9.png)

13. Välj **Slutför** **nya fjärranslutningar eller virtuella privata nätverksanslutningar och RADIUS-klienter.**

    ![Fönstret Slutförd konfiguration](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Verifiera RADIUS-konfigurationen

I det här avsnittet beskrivs konfigurationen som du skapade med hjälp av guiden.

1. Expandera **RADIUS-klienter**i konsolen NPS (local) på nätverksprincipservern och välj sedan **RADIUS-klienter**.

2. Högerklicka på den RADIUS-klient som du skapade i informationsfönstret och välj sedan **Egenskaper**. Egenskaperna för DIN RADIUS-klient (VPN-servern) ska vara som de som visas här:

    ![Verifiera VPN-egenskaper och KONFIGURATION](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Välj **Avbryt**.

4. Expandera **principer**i konsolen NPS (lokal) på nätverksprincipservern och välj sedan **Principer för anslutningsbegäran**. PRINCIPEN VPN-anslutningar visas enligt följande bild:

    ![Princip för anslutningsbegäran som visar VPN-anslutningsprincip](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Under **Principer**väljer du **Nätverksprinciper**. Du bör se en vpn-anslutningsprincip (Virtual Private Network) som liknar principen som visas i följande bild:

    ![Nätverksprinciper som visar principen virtuella privata nätverksanslutningar](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurera VPN-servern så att RADIUS-autentisering

I det här avsnittet konfigurerar du VPN-servern så att radius-autentisering används. Instruktionerna förutsätter att du har en fungerande konfiguration av en VPN-server men inte har konfigurerat den för att använda RADIUS-autentisering. När du har konfigurerat VPN-servern bekräftar du att konfigurationen fungerar som förväntat.

> [!NOTE]
> Om du redan har en fungerande VPN-serverkonfiguration som använder RADIUS-autentisering kan du hoppa över det här avsnittet.
>

### <a name="configure-authentication-provider"></a>Konfigurera autentiseringsprovider

1. Öppna Serverhanteraren på VPN-servern.

2. I Serverhanteraren väljer du **Verktyg**och väljer sedan **Routning och Fjärråtkomst**.

3. Högerklicka på ** \<servernamnet> (lokalt)** i fönstret **Routning och fjärråtkomst** och välj sedan **Egenskaper**.

4. Välj fliken **Säkerhet** i ** \<** fönstret Egenskaper för servernamn> (lokal).

5. Välj **RADIUS-autentisering**under **Autentiseringsprovider**på fliken **Säkerhet** och välj sedan **Konfigurera**.

    ![Konfigurera RADIUS-autentiseringsprovider](./media/howto-mfa-nps-extension-vpn/image15.png)

6. Välj **Lägg till**i fönstret **RADIUS-autentisering** .

7. Gör följande i fönstret **Lägg till RADIUS-server:**

    a. I rutan **Servernamn** anger du namnet eller IP-adressen för den RADIUS-server som du konfigurerade i föregående avsnitt.

    b. För den **delade hemligheten**väljer du **Ändra**och anger sedan det delade hemliga lösenordet som du skapade och spelade in tidigare.

    c. Ange värdet **30**i rutan **Time-out (sekunder).**  
    Tidsgränsen är nödvändig för att ge tillräckligt med tid för att slutföra den andra autentiseringsfaktorn.

    ![Lägga till RADIUS-serverfönster som konfigurerar time-out](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Välj **OK**.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutning

I det här avsnittet bekräftar du att VPN-klienten autentiseras och auktoriseras av RADIUS-servern när du försöker ansluta till vpn-virtuella porten. Instruktionerna förutsätter att du använder Windows 10 som VPN-klient.

> [!NOTE]
> Om du redan har konfigurerat en VPN-klient för att ansluta till VPN-servern och har sparat inställningarna kan du hoppa över stegen för att konfigurera och spara ett VPN-anslutningsobjekt.
>

1. På VPN-klientdatorn väljer du **Start-knappen** och väljer sedan knappen **Inställningar.**

2. Välj **Nätverks- & Internet i**fönstret **Windows-inställningar** .

3. Välj **VPN**.

4. Välj **Lägg till en VPN-anslutning**.

5. I rutan Lägg till en VPN-anslutning väljer du **Windows (inbyggt) i**rutan **Lägg till en VPN-anslutning,** slutför de återstående fälten, beroende på vad som är lämpligt, och väljer sedan **Spara**. **VPN provider**

    ![Fönstret "Lägg till en VPN-anslutning"](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Gå till **Kontrollpanelen**och välj sedan **Nätverks- och delningscenter**.

7. Välj **Ändra kortinställningar**.

    ![Nätverks- och delningscenter – ändra kortinställningar](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Högerklicka på VPN-nätverksanslutningen och välj sedan **Egenskaper**.

9. Välj fliken **Säkerhet** i fönstret VPN-egenskaper.

10. Kontrollera att endast Microsoft **CHAP Version 2 (MS-CHAP v2)** är markerat på fliken **Säkerhet** och välj sedan **OK**.

    ![Alternativet "Tillåt dessa protokoll"](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Högerklicka på VPN-anslutningen och välj sedan **Anslut**.

12. Välj **Anslut**i fönstret **Inställningar** .  
    En lyckad anslutning visas i säkerhetsloggen på RADIUS-servern, som händelse-ID 6272, som visas här:

    ![Fönstret Händelseegenskaper som visar en lyckad anslutning](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Felsöka RADIUS

Anta att VPN-konfigurationen fungerade innan du konfigurerade VPN-servern för att använda en centraliserad RADIUS-server för autentisering och auktorisering. Om konfigurationen fungerade är det troligt att problemet orsakas av en felaktig konfiguration av RADIUS-servern eller användning av ett ogiltigt användarnamn eller lösenord. Om du till exempel använder det alternativa UPN-suffixet i användarnamnet kan inloggningsförsöket misslyckas. Använd samma kontonamn för bästa resultat.

För att felsöka dessa problem är en idealisk plats att börja att undersöka säkerhetshändelseloggarna på RADIUS-servern. Om du vill spara tid på att söka efter händelser kan du använda den anpassade vyn Nätverksprincip och Åtkomstserver i Loggboken, som visas här. "Händelse-ID 6273" anger händelser där NPS nekas åtkomst till en användare.

![Loggboken som visar NPAS-händelser](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurera multifaktorautentisering

För hjälp med att konfigurera användare för multifaktorautentisering se artiklarna [Planera en molnbaserad Azure Multi-Factor Authentication-distribution](howto-mfa-getstarted.md#create-conditional-access-policy) och [Konfigurera mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller instruktioner för hur du konfigurerar VPN för att använda MFA för klientautentisering med VPN-servern.

När du har installerat och konfigurerat NPS-tillägget krävs all RADIUS-baserad klientautentisering som bearbetas av den här servern för att använda MFA. Om alla DINA VPN-användare inte är registrerade i Azure Multi-Factor Authentication kan du göra något av följande:

* Konfigurera en annan RADIUS-server för att autentisera användare som inte är konfigurerade för att använda MFA.

* Skapa en registerpost som gör att ifrågasatta användare kan tillhandahålla en andra autentiseringsfaktor om de är registrerade i Azure Multi-Factor Authentication.

Skapa ett nytt strängvärde med namnet _REQUIRE_USER_MATCH i HKLM\SOFTWARE\Microsoft\AzureMfa_och ange värdet till *Sant* eller *Falskt*.

![Inställningen "Kräv användarmatchning"](./media/howto-mfa-nps-extension-vpn/image34.png)

Om värdet är inställt på *Sant* eller är tomt, är alla autentiseringsbegäranden föremål för en MFA-utmaning. Om värdet är inställt på *False*utfärdas MFA-utmaningar endast till användare som är registrerade i Azure Multi-Factor Authentication. Använd inställningen *Falskt* endast i test- eller produktionsmiljöer under en introduktionsperiod.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Skaffa Azure Active Directory GUID-ID

Som en del av konfigurationen av NPS-tillägget måste du ange administratörsbehörighet och ID för din Azure AD-klientorganisation. Skaffa ID genom att göra följande:

1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klienten.

2. På Azure-portalmenyn väljer du **Azure Active Directory**eller söker efter och väljer Azure Active **Directory** på valfri sida.

3. Välj **egenskaper**.

4. Om du vill kopiera ditt Azure AD-ID väljer du knappen **Kopiera.**

    ![Azure AD-katalog-ID i Azure-portalen](./media/howto-mfa-nps-extension-vpn/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

NPS-tillägget måste installeras på en server som har rollen Nätverksprincip och Åtkomsttjänster installerat och som fungerar som RADIUS-servern i designen. Installera *inte* NPS-tillägget på VPN-servern.

1. Hämta NPS-tillägget från [Microsoft Download Center](https://aka.ms/npsmfa).

2. Kopiera den körbara filen för inställningar *(NpsExtnForAzureMfaInstaller.exe)* till NPS-servern.

3. Dubbelklicka på **NpsExtnForAzureMfaInstaller.exe** på NPS-servern och välj **Kör**om du uppmanas att göra det.

4. I **fönstret NPS-tillägg för Installationsprogrammet för Azure MFA** granskar du licensvillkoren för programvara, väljer **kryssrutan Jag godkänner licensvillkoren** och väljer sedan **Installera**.

    ![Fönstret "NPS-tillägg för Installationsprogrammet för Azure MFA"](./media/howto-mfa-nps-extension-vpn/image36.png)

5. Välj **Stäng**i fönstret **NPS-tillägg för installationsprogrammet för Azure MFA** .  

    ![Bekräftelsefönstret "Lyckad installation"](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Konfigurera certifikat som ska användas av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenterna innehåller ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat.
* Associerar den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD.
* Lagrar certifikatet i det lokala datorarkivet.
* Ger nätverksanvändaren åtkomst till certifikatets privata nyckel.
* Startar om NPS-tjänsten.

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för ditt certifikat med tjänstens huvudnamn på Azure AD och så vidare.

Om du vill använda skriptet anger du tillägget med dina Administrativa Azure Active Directory-autentiseringsuppgifter och Azure Active Directory-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerar NPS-tillägget.

1. Kör Windows PowerShell som administratör.

2. I Kommandotolken i PowerShell anger du **cd "c:\Program\Microsoft\AzureMfa\Config"** och väljer sedan Retur.

3. I nästa kommandotolk anger du **.\AzureMfaNpsExtnConfigSetup.ps1**och väljer sedan Retur. Skriptet kontrollerar om Azure AD PowerShell-modulen är installerad. Om det inte är installerat installeras modulen åt dig.

    ![Köra konfigurationsskriptet AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    När skriptet har verifierat installationen av PowerShell-modulen visas inloggningsfönstret för Azure Active Directory PowerShell-modul.

4. Ange dina Azure AD-administratörsuppgifter och lösenord och välj sedan **Logga in**.

    ![Autentisera till Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Klistra in klient-ID:et som du kopierade tidigare i kommandotolken och välj sedan Retur.

    ![Ange Azure AD-katalog-ID som kopierats tidigare](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skriptet skapar ett självsignerat certifikat och utför andra konfigurationsändringar. Utdata är sådär i följande bild:

    ![PowerShell-fönster med självsignerat certifikat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Starta om servern.

### <a name="verify-the-configuration"></a>Kontrollera konfigurationen

För att verifiera konfigurationen måste du upprätta en ny VPN-anslutning till VPN-servern. När du har angett dina autentiseringsuppgifter för primär autentisering väntar VPN-anslutningen på att den sekundära autentiseringen ska lyckas innan anslutningen upprättas, som visas nedan.

![VPN-fönstret Windows-inställningar](./media/howto-mfa-nps-extension-vpn/image42.png)

Om du har autentiserat med den sekundära verifieringsmetoden som du tidigare konfigurerat i Azure MFA är du ansluten till resursen. Men om den sekundära autentiseringen misslyckas nekas du åtkomst till resursen.

I följande exempel innehåller Microsoft Authenticator-appen på en Windows Phone den sekundära autentiseringen:

![Exempel på MFA-prompt på Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

När du har autentiserats med den sekundära metoden får du åtkomst till den virtuella porten på VPN-servern. Eftersom du var tvungen att använda en sekundär autentiseringsmetod med hjälp av en mobilapp på en betrodd enhet är inloggningsprocessen säkrare än om den bara använde en kombination av användarnamn och lösenord.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Visa Loggboksloggar för lyckade inloggningshändelser

Om du vill visa lyckade inloggningshändelser i Loggboken i Windows loggar frågar du windowssäkerhetsloggen på NPS-servern genom att ange följande PowerShell-kommando:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell-säkerhet Loggboken](./media/howto-mfa-nps-extension-vpn/image44.png)

Du kan också visa säkerhetsloggen eller den anpassade vyn Nätverksprincip och Åtkomsttjänster, som du ser här:

![Exempel på serverlogg för nätverksprincip](./media/howto-mfa-nps-extension-vpn/image45.png)

På servern där du installerade NPS-tillägget för Azure Multi-Factor Authentication kan du hitta loggböckerprogramloggar som är specifika för tillägget vid *program- och tjänstloggar\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Fönstret AuthZ-loggar för Loggbokens exempel](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Felsökningsguide

Om konfigurationen inte fungerar som förväntat börjar du felsöka genom att kontrollera att användaren är konfigurerad för att använda MFA. Låt användaren ansluta till [Azure-portalen](https://portal.azure.com). Om användaren uppmanas att ange sekundär autentisering och kan autentisera kan du eliminera en felaktig konfiguration av MFA som ett problem.

Om MFA fungerar för användaren läser du relevanta Loggboksloggar. Loggarna innehåller säkerhetshändelsen, gateway-drift och Azure Multi-Factor Authentication loggar som beskrivs i föregående avsnitt.

Ett exempel på en säkerhetslogg som visar en misslyckad inloggningshändelse (händelse-ID 6273) visas här:

![Säkerhetslogg som visar en misslyckad inloggningshändelse](./media/howto-mfa-nps-extension-vpn/image47.png)

En relaterad händelse från Azure Multi-Factor Authentication Log visas här:

![Azure MultiFaktorautentiseringsloggar](./media/howto-mfa-nps-extension-vpn/image48.png)

Om du vill göra avancerad felsökning läser du loggfilerna för NPS-databasformat där NPS-tjänsten är installerad. Loggfilerna skapas i mappen _%SystemRoot%\System32\Loggar_ som kommaavgränsade textfiler. En beskrivning av loggfilerna finns i [Tolka LOGGFILER för NPS-databasformat](https://technet.microsoft.com/library/cc771748.aspx).

Posterna i dessa loggfiler är svåra att tolka om du inte exporterar dem till ett kalkylblad eller en databas. Du hittar många IAS-tolkningsverktyg (Internet Authentication Service) online för att hjälpa dig att tolka loggfilerna. Resultatet av en sådan nedladdningsbar [shareware ansökan](https://www.deepsoftware.com/iasviewer) visas här:

![Exempel på IAS-tolkar för Shareware-appen](./media/howto-mfa-nps-extension-vpn/image49.png)

Om du vill utföra ytterligare felsökning kan du använda en protokollanalysator som Wireshark eller [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Följande bild från Wireshark visar RADIUS-meddelanden mellan VPN-servern och NPS.

![Microsoft Message Analyzer visar filtrerad trafik](./media/howto-mfa-nps-extension-vpn/image50.png)

Mer information finns i [Integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Nästa steg

[Hämta Azure-multifaktorautentisering](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
