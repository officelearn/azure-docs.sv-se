---
title: Integrera VPN med Azure MFA med hjälp av NPS-tillägg - Azure Active Directory
description: Integrera din VPN-infrastruktur med Azure MFA med hjälp av NPS-tillägget för Microsoft Azure.
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
ms.openlocfilehash: 2674e5ca12269d44e111f140fce77bd8bc0c9ae7
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60361955"
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrera din VPN-infrastruktur med Azure MFA med hjälp av NPS-tillägget för Azure

## <a name="overview"></a>Översikt

Nätverksprincipserver (NPS)-tillägget för Azure gör att organisationer kan skydda Remote Authentication Dial-In User Service (RADIUS)-klientautentisering med hjälp av Microsofts molnbaserade [Azure Multi-Factor Authentication (MFA)](howto-mfaserver-nps-rdg.md), som tillhandahåller tvåstegsverifiering.

Den här artikeln innehåller instruktioner för att integrera NPS-infrastruktur med MFA med hjälp av NPS-tillägget för Azure. Den här processen gör det möjligt för säker tvåstegsverifiering för användare som försöker ansluta till nätverket med hjälp av en VPN-anslutning.

Nätverksprincip och åtkomsttjänster ger organisationer möjlighet att:

* Tilldela en central plats för hantering och kontroll över nätverksbegäranden ange:

  * Vem som kan ansluta

  * Vilka tider på dagen anslutningar tillåts

  * Varaktigheten för anslutningar

  * Vilken säkerhetsnivå som klienter måste använda för att ansluta

    I stället för att ange principer på varje server för VPN eller fjärrskrivbordsgateway du göra det när de är på en central plats. RADIUS-protokollet används för att ge centraliserad autentisering, auktorisering och redovisning.

* Fastställa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.

* Ett sätt att användning av autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.
  Mer information finns i [Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top).

För att förbättra säkerheten och ge en hög nivå av efterlevnad, kan organisationer integrera NPS med Azure Multi-Factor Authentication så att användarna ska använda tvåstegsverifiering för att ansluta till den virtuella porten på VPN-servern. För användare som ska få åtkomstbehörighet måste de ange sitt användarnamn och lösenord kombination och annan information som de styr. Den här informationen måste vara betrodd och inte enkelt dupliceras. Den kan innehålla ett mobiltelefonnummer, en fast telefon siffra eller ett program på en mobil enhet.

Innan den är tillgänglig för NPS-tillägget för Azure-kunder som ville implementera tvåstegsverifiering för integrerad NPS och MFA miljöer var tvungen att konfigurera och underhålla en separat MFA-server i en lokal miljö. Den här typen av autentisering erbjuds av Remote Desktop Gateway och Azure Multi-Factor Authentication Server med RADIUS.

Med NPS-tillägget för Azure kan organisationer skydda RADIUS-klientautentisering genom att distribuera en lokal baserat MFA-lösning eller en molnbaserad MFA-lösning.

## <a name="authentication-flow"></a>Autentiseringsflödet

När användarna ansluter till en virtuell port på en VPN-server, måste de först autentisera med hjälp av en mängd olika protokoll. Protokollen Tillåt användning av en kombination av användarnamn och lösenord och certifikatbaserade autentiseringsmetoder.

Förutom att autentisera och verifiera sin identitet kan ha användare lämplig fjärråtkomstbehörigheter. I enklare implementationer anges fjärråtkomstbehörigheter som tillåter åtkomst direkt på Active Directory-objekt.

![Fliken i Active Directory-användare och datorer användaregenskaper](./media/howto-mfa-nps-extension-vpn/image1.png)

I enklare implementationer varje VPN-server som beviljar eller nekar åtkomst baserat på principer som har definierats på varje lokala VPN-servern.

I större och mer skalbart-implementeringar kan är de principer som bevilja eller neka VPN-åtkomst centraliserade på RADIUS-servrar. I dessa fall kan fungerar VPN-servern som en fjärråtkomstserver (RADIUS-klienten) som vidarebefordrar anslutningsbegäranden och konto-meddelanden till en RADIUS-server. För att ansluta till den virtuella porten på VPN-servern, användare måste autentiseras och uppfyller de villkor som definieras centralt på RADIUS-servrar.

När NPS-tillägget för Azure är integrerad med NPS, resulterar ett flöde för lyckad autentisering, enligt följande:

1. VPN-servern tar emot en begäran om autentisering från en VPN-användare som innehåller användarnamn och lösenord för att ansluta till en resurs, till exempel en fjärrskrivbordssession.
2. Fungerar som en RADIUS-klient, VPN-servern konverterar begäran till en RADIUS *åtkomstbegäran* meddelandet och skickar dem (med ett krypterat lösenord) till RADIUS-servern där NPS-tillägget installeras.
3. Kombinationen av användarnamn och lösenord har verifierats i Active Directory. Om antingen användarnamnet eller lösenordet är felaktigt, RADIUS-servern skickar en *nekad åtkomst* meddelande.
4. Om alla villkor som anges i NPS-anslutningsbegäran och principer för nätverk är uppfyllda (till exempel tid på dagen eller grupp begränsningar av medlemskap), NPS-tillägget utlöser en begäran för sekundär autentisering med Azure Multi-Factor Authentication.
5. Azure Multi-Factor Authentication kommunicerar med Azure Active Directory, hämtar användarens information och utför den sekundära autentiseringen med den metod som har konfigurerats av användaren (cell via telefonsamtal, textmeddelande eller mobilapp).
6. När MFA-kontrollen lyckas kommunicerar resultatet till NPS-tillägget i Azure Multi-Factor Authentication.
7. När anslutningsförsöket både autentiseras och auktoriseras NPS där tillägget installeras skickar en RADIE *nekad* meddelandet till VPN-server (RADIUS-klient).
8. Användaren beviljas åtkomst till den virtuella porten på VPN-servern och upprättar en krypterad VPN-tunnel.

## <a name="prerequisites"></a>Nödvändiga komponenter

Det här avsnittet beskrivs de krav som måste slutföras innan du kan integrera MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats:

* VPN-infrastruktur
* Rollen Nätverksprincip och åtkomsttjänster
* Azure Multi-Factor Authentication-licens
* Windows Server-programvara
* Bibliotek
* Azure Active Directory (Azure AD) som är synkroniserad med den lokala Active Directory
* ID för Azure Active Directory-GUID

### <a name="vpn-infrastructure"></a>VPN-infrastruktur

Den här artikeln förutsätter att du har en fungerande VPN-infrastruktur som använder Microsoft Windows Server 2016 och att VPN-servern för närvarande inte har konfigurerats att vidarebefordra anslutningsförfrågningar till en RADIUS-server. I artikeln får konfigurera du VPN-infrastruktur för att använda en central RADIUS-server.

Om du inte har en fungerande VPN-infrastruktur på plats, kan du snabbt skapa en genom att följa riktlinjerna i ett stort antal VPN-installationen självstudier som du hittar på Microsoft och tredje parter.

### <a name="the-network-policy-and-access-services-role"></a>Rollen Nätverksprincip och åtkomsttjänster

Nätverksprincip och åtkomsttjänster tillhandahåller funktioner för RADIUS-servern och klienten. Den här artikeln förutsätter att du har installerat rollen Nätverksprincip och åtkomsttjänster på en medlemsserver eller domänkontrollant i din miljö. I den här guiden konfigurerar du RADIUS för en VPN-konfiguration. Installera rollen Nätverksprincip och åtkomsttjänster på en server *än* VPN-servern.

Information om hur du installerar rollen Nätverksprincip och åtkomsttjänster tjänsten Windows Server 2012 eller senare finns [installera en NAP-hälsoprincipserver](https://technet.microsoft.com/library/dd296890.aspx). NAP är föråldrad i Windows Server 2016. En beskrivning av bästa praxis för NPS, inklusive rekommendationen för att installera NPS på en domänkontrollant finns i [bästa praxis för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Det krävs en licens för Azure Multi-Factor Authentication och är tillgänglig via en Azure AD Premium, Enterprise Mobility + Security eller en fristående licens för Multifaktorautentisering. Förbrukningsbaserad licenser för Azure MFA som per användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. Mer information finns i [så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med rollen Nätverksprincip och åtkomsttjänster har installerats. Alla steg i den här guiden genomfördes med Windows Server 2016.

### <a name="libraries"></a>Bibliotek

Följande bibliotek installeras automatiskt med NPS-tillägget:

-   [Visual C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory-modulen för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Om Microsoft Azure Active Directory PowerShell-modulen inte finns installeras den med ett konfigurationsskript som du kör som en del av installationen. Det finns inget behov att installera modulen förbereds i förväg om den inte redan är installerad.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory som har synkroniserats med den lokala Active Directory

Om du vill använda NPS-tillägget, lokala användare synkroniseras med Azure Active Directory och aktiverade för MFA. Den här handboken förutsätts att en lokal användare synkroniseras med Azure Active Directory via Azure AD Connect. Anvisningar för att ge användare för MFA finns nedan.

Information om Azure AD Connect finns i [integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID för Azure Active Directory-GUID

Om du vill installera NPS-tillägget som du behöver veta GUID för Azure Active Directory. Anvisningar för att hitta GUID för Azure Active Directory finns i nästa avsnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurera RADIUS för VPN-anslutningar

Om du har installerat NPS-rollen på en medlemsserver, måste du konfigurera den för att autentisera och auktorisera de begäranden VPN-anslutningarna för VPN-klienten. 

Det här avsnittet förutsätter att du har installerat rollen Nätverksprincip och åtkomsttjänster men inte har konfigurerat den för användning i din infrastruktur.

> [!NOTE]
> Om du redan har en fungerande VPN-server som använder en centraliserad RADIUS-server för autentisering kan du hoppa över det här avsnittet.
>

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory

För att fungera korrekt i det här scenariot, måste NPS-servern registreras i Active Directory.

1. Öppna Serverhanteraren.

2. I Serverhanteraren väljer **verktyg**, och välj sedan **Network Policy Server**.

3. I NPS-konsolträdet högerklickar du på **NPS (lokal)**, och välj sedan **registrera servern i Active Directory**. Välj **OK** två gånger.

    ![Registrera servern i Active Directory-menyalternativ](./media/howto-mfa-nps-extension-vpn/image2.png)

4. Lämna konsolen öppen för nästa procedur.

### <a name="use-wizard-to-configure-the-radius-server"></a>Använd guiden för att konfigurera RADIUS-server

Du kan använda en standard (guidebaserad) eller avancerade konfigurationsalternativ för att konfigurera RADIUS-servern. Det här avsnittet förutsätter att du använder alternativet guidebaserad standardkonfiguration.

1. I NPS-konsolträdet väljer **NPS (lokal)**.

2. Under **standardkonfiguration**väljer **RADIUS-Server för fjärranslutningar eller VPN-anslutningar**, och välj sedan **konfigurera VPN eller fjärranslutna**.

    ![Konfigurera RADIUS-Server för fjärranslutningar eller VPN-anslutningar](./media/howto-mfa-nps-extension-vpn/image3.png)

3. I den **välja fjärranslutning eller virtuellt privat nätverk anslutningar typ** väljer **VPN-anslutningar**, och välj sedan **nästa**.

    ![Konfigurera anslutningar för virtuella privata nätverk](./media/howto-mfa-nps-extension-vpn/image4.png)

4. I den **ange fjärranslutning eller VPN-servern** väljer **Lägg till**.

5. I den **ny RADIUS-klient** fönstret, ange ett eget namn, anger du namnet eller IP-adressen för VPN-servern och ange sedan lösenordet för en delad hemlighet. Se det delade hemliga lösenordet långa och komplexa. Spela in det, eftersom du behöver det i nästa avsnitt.

    ![Skapa ett fönster för ny RADIUS-klient](./media/howto-mfa-nps-extension-vpn/image5.png)

6. Välj **OK**, och välj sedan **nästa**.

7. I den **konfigurera autentiseringsmetoder** fönstret acceptera standardvärdet (**Microsoft Encrypted Authentication version 2 [MS-CHAPv2])** eller välj ett annat alternativ och välj **nästa** .

    > [!NOTE]
    > Om du konfigurerar Extensible Authentication Protocol (EAP), måste du använda Microsoft Challenge Handshake Authentication Protocol (CHAPv2) eller PEAP Protected Extensible Authentication Protocol (). Inga andra EAP stöds.

8. I den **ange användargrupper** väljer **Lägg till**, och välj sedan en lämplig grupp. Lämna alternativet tomt om du vill bevilja åtkomst till alla användare om det finns ingen grupp.

    ![Ange användargrupper fönster för att tillåta eller neka åtkomst](./media/howto-mfa-nps-extension-vpn/image7.png)

9. Välj **Nästa**.

10. I den **ange IP-filter** väljer **nästa**.

11. I den **ange krypteringsinställningar** , accepterar du standardinställningarna och välj sedan **nästa**.

    ![Fönstret Ange krypteringsinställningar](./media/howto-mfa-nps-extension-vpn/image8.png)

12. I den **ange ett resursnamn** fönstret lämna sfärnamnet tomt, accepterar du standardinställningen och välj sedan **nästa**.

    ![Ange ett sfärnamnet fönster](./media/howto-mfa-nps-extension-vpn/image9.png)

13. I den **slutföra nya fjärr- eller VPN-anslutningar och RADIUS-klienter** väljer **Slutför**.

    ![Slutförda konfigurationsfönstret](./media/howto-mfa-nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Kontrollera att RADIUS-konfigurationen

Det här avsnittet beskrivs den konfiguration som du skapade med hjälp av guiden.

1. Nätverksprincipservern i NPS (lokal)-konsolen, expandera **RADIUS-klienter**, och välj sedan **RADIUS-klienter**.

2. I informationsfönstret högerklickar du på RADIUS-klienten som du skapade och välj sedan **egenskaper**. Egenskaperna för RADIUS-klienten (VPN-server) ska se ut som de visas här:

    ![Kontrollera egenskaper för VPN och konfiguration](./media/howto-mfa-nps-extension-vpn/image11.png)

3. Välj **Avbryt**.

4. Nätverksprincipservern i NPS (lokal)-konsolen, expandera **principer**, och välj sedan **principer för anslutningsbegäran**. Principen för VPN-anslutningar visas enligt följande bild:

    ![Princip för anslutningsbegäran som visar VPN-anslutningsprincip](./media/howto-mfa-nps-extension-vpn/image12.png)

5. Under **principer**väljer **nätverksprinciper**. Du bör se en princip för virtuella privata nätverk (VPN) anslutningar som liknar den princip som visas i följande bild:

    ![Nätverksprinciper som visar princip för VPN-anslutningar](./media/howto-mfa-nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurera VPN-servern för RADIUS-autentisering

I det här avsnittet konfigurerar du VPN-servern för RADIUS-autentisering. Anvisningarna förutsätter att du har en fungerande konfiguration av en VPN-server men inte har konfigurerat den att använda RADIUS-autentisering. När du konfigurerar VPN-servern, kontrollerar du att konfigurationen fungerar som förväntat.

> [!NOTE]
> Om du redan har en fungerande VPN-serverkonfiguration som använder RADIUS-autentisering kan du hoppa över det här avsnittet.
>

### <a name="configure-authentication-provider"></a>Konfigurera autentiseringsprovider

1. Öppna Serverhanteraren på VPN-servern.

2. I Serverhanteraren väljer **verktyg**, och välj sedan **Routing and Remote Access**.

3. I den **Routing and Remote Access** fönstret högerklickar du på  **\<servernamn > (lokal)**, och välj sedan **egenskaper**.

4. I den  **\<servernamn > (lokalt) egenskaper** väljer den **Security** fliken.

5. På den **Security** fliken, under **autentiseringsprovider**väljer **RADIUS-autentisering**, och välj sedan **konfigurera**.

    ![Konfigurera RADIUS-autentisering-providern](./media/howto-mfa-nps-extension-vpn/image15.png)

6. I den **RADIUS-autentisering** väljer **Lägg till**.

7. I den **Lägg till RADIUS-Server** fönstret gör du följande:

    a. I den **servernamn** anger du namn eller IP-adressen för RADIUS-servern som du konfigurerade i föregående avsnitt.

    b. För den **delad hemlighet**väljer **ändra**, och ange sedan det delade hemliga lösenordet som du skapade och antecknade tidigare.

    c. I den **timeout (sekunder)** väljer du ett värde från **30** via **60**.  
    Timeout-värdet är nödvändigt för att tillräckligt med tid att slutföra den andra faktorn för autentisering.

    ![Lägg till RADIUS-Server fönstret konfigurera timeout-värde](./media/howto-mfa-nps-extension-vpn/image16.png)

8. Välj **OK**.

### <a name="test-vpn-connectivity"></a>Testa VPN-anslutningen

I det här avsnittet ska bekräfta du att VPN-klienten autentiseras och auktoriseras av RADIUS-servern när du försöker ansluta till den virtuella VPN-porten. Anvisningarna förutsätter att du använder Windows 10 som en VPN-klient.

> [!NOTE]
> Om du redan har konfigurerat en VPN-klient för att ansluta till VPN-servern och har sparat inställningarna kan du hoppa över åtgärder som rör konfigurera och spara ett objekt för VPN-anslutning.
>

1. På din VPN-klientdator, Välj den **starta** och välj sedan den **inställningar** knappen.

2. I den **Windows-inställningar** väljer **nätverk och Internet**.

3. Välj **VPN**.

4. Välj **lägga till en VPN-anslutning**.

5. I den **lägga till en VPN-anslutning** fönstret i den **VPN-leverantören** väljer **Windows (inbyggt)**, Fyll i fälten Återstående efter behov, och välj sedan **Spara**.

    ![Fönstret ”Lägg till en VPN-anslutning”](./media/howto-mfa-nps-extension-vpn/image17.png)

6. Gå till **Kontrollpanelen**, och välj sedan **nätverks- och delningscenter**.

7. Välj **ändra inställningarna för nätverkskortet**.

    ![Nätverks- och delningscenter – ändra inställningar för nätverkskort](./media/howto-mfa-nps-extension-vpn/image18.png)

8. Högerklicka på nätverksanslutningen VPN och välj sedan **egenskaper**.

9. I egenskapsfönstret VPN väljer du den **Security** fliken.

10. På den **Security** fliken, kontrollera att endast **Microsoft CHAP Version 2 (MS-CHAP v2)** är markerat och välj sedan **OK**.

    ![Alternativet ”Tillåt dessa protokoll”](./media/howto-mfa-nps-extension-vpn/image20.png)

11. Högerklicka på VPN-anslutningen och välj sedan **Connect**.

12. I den **inställningar** väljer **Connect**.  
    En lyckad anslutning ut i säkerhetsloggen på RADIUS-server som händelse-ID 6272 ser så här:

    ![Händelsen egenskapsfönstret som visar en lyckad anslutning](./media/howto-mfa-nps-extension-vpn/image21.png)

## <a name="troubleshooting-radius"></a>Felsöka RADIUS

Anta att din VPN-konfiguration fungerade innan du konfigurerade VPN-servern om du vill använda en centraliserad RADIUS-server för autentisering och auktorisering. Om konfigurationen fungerar, är det troligt att problemet orsakas av en felaktig konfiguration av RADIUS-servern eller användning av ett ogiltigt användarnamn eller lösenord. Inloggningsförsök kan exempelvis misslyckas om du använder alternativt UPN-suffix i användarnamnet. Använd samma kontonamn för bästa resultat.

Om du vill felsöka problemen är en perfekt ställe att börja att undersöka säkerhetshändelser på RADIUS-servern. För att spara tid på att leta efter händelser, kan du använda den rollbaserade nätverksprincip och åtkomst till servern anpassad vy i Loggboken, som visas här. ”Händelse-ID 6273” anger händelser där NPS nekas åtkomst till en användare.

![Event Viewer som visar NPAS händelser](./media/howto-mfa-nps-extension-vpn/image22.png)

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication

För att få hjälp att konfigurera användare för Multifaktorautentisering finns i artiklar [kräva tvåstegsverifiering för en användare eller grupp](howto-mfa-userstates.md) och [konfigurerar mitt konto för tvåstegsverifiering](../user-help/multi-factor-authentication-end-user-first-time.md)

## <a name="install-and-configure-the-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller instruktioner för att konfigurera VPN för att använda MFA för klientautentisering med VPN-servern.

När du installerar och konfigurerar NPS-tillägget, måste all autentisering för RADIUS-klient som bearbetas av den här servern använda MFA. Om alla VPN-användare inte har registrerats i Azure Multi-Factor Authentication, kan du göra något av följande:

* Konfigurera en annan RADIUS-server för att autentisera användare som inte har konfigurerats för att använda MFA.

* Skapa en registerpost som gör att handlingar användarna måste ange en andra autentiseringsfaktor om de är registrerade i Azure Multi-Factor Authentication.

Skapa ett nytt strängvärde med namnet _REQUIRE_USER_MATCH i HKLM\SOFTWARE\Microsoft\AzureMfa_, och ange värdet *SANT* eller *FALSKT*.

![Inställningen ”Kräv Användarmatchning”](./media/howto-mfa-nps-extension-vpn/image34.png)

Om värdet anges till *SANT* eller är tomt används alla autentiseringsbegäranden omfattas av en MFA-kontrollen. Om värdet anges till *FALSKT*, MFA utmaningar utfärdas bara för användare som är registrerade i Azure Multi-Factor Authentication. Använd den *FALSKT* konfigurera under testning eller i produktionsmiljöer under en period för onboarding.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Hämta ID för Azure Active Directory-GUID

Som en del av konfigurationen av NPS-tillägget, måste du ange autentiseringsuppgifter och ID för Azure AD-klienten. Hämta ID: T genom att göra följande:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klient.

2. I den vänstra rutan väljer du den **Azure Active Directory** knappen.

3. Välj **egenskaper**.

4. Om du vill kopiera en Azure AD-ID, Välj den **kopia** knappen.

    ![Azure AD-katalog-ID i Azure portal](./media/howto-mfa-nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

NPS-tillägget måste installeras på en server som har nätverksprinciper och Access Services-rollen installerad och som fungerar som RADIUS-server i din design. Gör *inte* Installera NPS-tillägget på Remote Desktop-servern.

1. Hämta NPS-tillägget från [Microsoft Download Center](https://aka.ms/npsmfa).

2. Kopiera den körbara filen för installationsprogrammet (*NpsExtnForAzureMfaInstaller.exe*) till NPS-servern.

3. Dubbelklicka på NPS-servern **NpsExtnForAzureMfaInstaller.exe** och, om du uppmanas, väljer **kör**.

4. I den **NPS-tillägget för Azure MFA installation** fönster, granska licensvillkoren för programvaran, Välj den **jag godkänner licensvillkoren och villkor** kryssrutan och välj sedan **installera**.

    ![Fönstret ”NPS-tillägget för Azure MFA för”](./media/howto-mfa-nps-extension-vpn/image36.png)

5. I den **NPS-tillägget för Azure MFA installation** väljer **Stäng**.  

    ![”Installationen lyckades” bekräftelsefönstret](./media/howto-mfa-nps-extension-vpn/image37.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Om du vill se till att skydda kommunikationen och kontroll, konfigurerar du certifikat för användning av NPS-tillägget. NPS-komponenterna är ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat.
* Associerar den offentliga nyckeln för certifikatet till tjänstens huvudnamn i Azure AD.
* Lagrar certifikatet i lokala datorns Arkiv.
* Tilldelar nätverk användaråtkomst till certifikatets privata nyckel.
* Startar om NPS-tjänsten.

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för certifikatet med tjänstens huvudnamn i Azure AD och så vidare.

Om du vill använda skriptet, ger du tillägget med dina administrativa autentiseringsuppgifter för Azure Active Directory och Azure Active Directory klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerar NPS-tillägget.

1. Kör Windows PowerShell som administratör.

2. Ange i PowerShell-kommandotolken **cd ”c:\Program Files\Microsoft\AzureMfa\Config”**, och tryck sedan på RETUR.

3. I nästa kommandotolk, ange **.\AzureMfaNpsExtnConfigSetup.ps1**, och tryck sedan på RETUR. Skriptet kontrollerar om Azure AD PowerShell-modulen är installerad. Om det inte är installerat installerar skriptet modulen åt dig.

    ![Kör konfigurationsskriptet AzureMfsNpsExtnConfigSetup.ps1](./media/howto-mfa-nps-extension-vpn/image38.png)

    När skriptet har kontrollerat installationen av PowerShell-modulen, visar Azure Active Directory PowerShell-inloggningen modulfönstret.

4. Ange dina autentiseringsuppgifter för Azure AD-administratör och lösenord och välj sedan **logga in**.

    ![Autentisera till Azure AD PowerShell](./media/howto-mfa-nps-extension-vpn/image39.png)

5. Klistra in klient-ID som du kopierade tidigare vid kommandotolken och tryck sedan på RETUR.

    ![Infoga Azure AD-katalog-ID som kopieras innan](./media/howto-mfa-nps-extension-vpn/image40.png)

    Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Utdata är precis som i följande bild:

    ![PowerShell-fönstret som visar självsignerat certifikat](./media/howto-mfa-nps-extension-vpn/image41.png)

6. Starta om servern.

### <a name="verify-the-configuration"></a>Kontrollera konfigurationen

Du kan verifiera konfigurationen, måste du upprätta en ny VPN-anslutning med VPN-servern. När du har angett dina autentiseringsuppgifter för primär autentisering, väntar VPN-anslutningen på den sekundära autentiseringen ska lyckas innan anslutningen har upprättats, enligt nedan.

![Fönstret VPN för Windows-inställningar](./media/howto-mfa-nps-extension-vpn/image42.png)

Om du autentisera med den sekundära verifieringsmetod som du tidigare har konfigurerat i Azure MFA, är du ansluten till resursen. Om den sekundära autentiseringen misslyckas, nekas åtkomst till resursen.

I följande exempel ger Microsoft Authenticator-appen på en Windows Phone-sekundär autentisering:

![Exempel MFA-kommandotolk på Windows Phone](./media/howto-mfa-nps-extension-vpn/image43.png)

När du har autentiserats med hjälp av metoden sekundära, beviljas åtkomst till den virtuella porten på VPN-servern. Eftersom du tvungen att använda en metod för sekundär autentisering med hjälp av en mobilapp på en betrodd enhet, är logga in igen säkrare än om det använder bara en användarnamn och lösenord kombination.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Visa loggarna i Loggboken för lyckade inloggningshändelser

Om du vill visa fråga lyckade inloggningshändelser i Windows Loggboken Windows säkerhetsloggen på NPS-servern genom att ange följande PowerShell-kommando:

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![PowerShell security Loggboken](./media/howto-mfa-nps-extension-vpn/image44.png)

Du kan också visa säkerhetsloggen eller anpassad vy nätverkspolicy och åtkomsttjänster som visas här:

![Exempel NPS-logg](./media/howto-mfa-nps-extension-vpn/image45.png)

På den server där du installerade NPS-tillägget för Azure Multi-Factor Authentication, finns loggarna i Loggboken program som är specifika för tillägget på *program och tjänster Logs\Microsoft\AzureMfa*.

    `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`

![Exempel Event Viewer AuthZ loggar fönstret](./media/howto-mfa-nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Felsökningsguide

Om konfigurationen inte fungerar som förväntat, kan du börja felsöka genom att verifiera att användaren är konfigurerad för att använda MFA. Få användaren att ansluta till den [Azure-portalen](https://portal.azure.com). Om användaren uppmanas att ange sekundära autentiseringen och kan autentisera, vet du att en felaktig konfiguration av MFA som ett problem.

Om MFA fungerar för användaren, granska relevant loggarna i Loggboken. Loggarna är säkerhetshändelse, drift-Gateway och Azure Multi-Factor Authentication-loggar som beskrivs i föregående avsnitt.

Ett exempel på en säkerhetslogg som visar en misslyckad inloggning händelse (händelse-ID 6273) visas här:

![Säkerhetsloggen som visar en händelse för Misslyckad inloggning](./media/howto-mfa-nps-extension-vpn/image47.png)

En relaterad händelse från Azure Multi-Factor Authentication-loggen visas här:

![Azure Multi-Factor Authentication loggar](./media/howto-mfa-nps-extension-vpn/image48.png)

Om du vill göra avancerad felsökning, loggfilerna NPS databasen format där NPS-tjänsten är installerad. Loggfilerna skapas i den _%SystemRoot%\System32\Logs_ mapp som kommaavgränsad textfiler. En beskrivning av loggfilerna finns i [tolka NPS loggfilerna](https://technet.microsoft.com/library/cc771748.aspx).

Posterna i dessa loggfiler är svåra att tolka om du inte exporterar dem till ett kalkylblad eller en databas. Du hittar många tjänsten IAS (Internet Authentication) online verktyg för analys som hjälper dig att tolka loggfilerna. Utdata i en sådan nedladdningsbara [shareware programmet](https://www.deepsoftware.com/iasviewer) visas här:

![Exempel på Shareware app IAS-parsern](./media/howto-mfa-nps-extension-vpn/image49.png)

Om du vill göra ytterligare felsökning kan du använda en protokollanalysator, till exempel Wireshark eller [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Följande bild från Wireshark visar RADIUS-meddelanden mellan VPN-servern och NPS.

![Microsoft Message Analyzer som visar filtrerade trafik](./media/howto-mfa-nps-extension-vpn/image50.png)

Mer information finns i [integrera din befintliga NPS-infrastruktur med Azure Multi-Factor Authentication](howto-mfa-nps-extension.md).

## <a name="next-steps"></a>Nästa steg

[Hämta Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
