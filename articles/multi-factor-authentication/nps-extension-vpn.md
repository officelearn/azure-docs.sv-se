---
title: "Integrera VPN med Azure MFA med hjälp av NPS-tillägget | Microsoft Docs"
description: "Den här artikeln beskrivs integrera din VPN-infrastruktur med Azure MFA med hjälp av NPS-tillägget för Microsoft Azure."
services: active-directory
keywords: "Azure MFA integrera VPN, Azure Active Directory, NPS-tillägg"
documentationcenter: 
author: MicrosoftGuyJFlo
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: c058f1e747849cde0f15a039779665c8718da947
ms.sourcegitcommit: 8aa014454fc7947f1ed54d380c63423500123b4a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/23/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-mfa-by-using-the-network-policy-server-extension-for-azure"></a>Integrera din VPN-infrastruktur med Azure MFA med hjälp av NPS-tillägget för Azure

## <a name="overview"></a>Översikt

Server (NPS)-tillägget för Azure gör att organisationer kan skydda RADIUS Remote Authentication Dial-In User Service ()-klientautentisering med hjälp av molnbaserade [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), som innehåller tvåstegsverifiering.

Den här artikeln innehåller instruktioner för att integrera NPS-infrastruktur med MFA med hjälp av NPS-tillägget för Azure. Den här processen kan säkra tvåstegsverifiering för användare som försöker ansluta till nätverket med hjälp av en VPN-anslutning. 

Nätverksprincip och åtkomsttjänster ger organisationer möjlighet att:

* Tilldela en central plats för hantering och kontroll av begäranden för nätverk att ange:

    * Vem som kan ansluta 
    
    * Vilka tider på dagen anslutningar tillåts 
    
    * Varaktighet för anslutningar
    
    * Säkerhetsnivån som klienter måste använda för att ansluta

    I stället för att ange principer på varje VPN eller Remote Desktop Gateway-server du göra det när de är på en central plats. RADIUS-protokollet används för att ge centraliserad autentisering, auktorisering och redovisning. 

* Skapa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.

* Ange ett sätt att tillse autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.   
Mer information finns i [Network Policy Server](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

För att förbättra säkerheten och ger en hög nivå av kompatibilitet, kan organisationer integrera NPS med Azure Multi-Factor Authentication så att användarna ska använda tvåstegsverifiering för att ansluta till den virtuella porten på VPN-servern. De måste ange sina användarnamn och kombinationen av lösenord och annan information som de styr för användare att få åtkomst. Den här informationen måste betrodda och inte enkelt dubbletter. Den kan innehålla ett mobiltelefonnummer, en fast telefon tal eller ett program på en mobil enhet.

Kunder som vill implementera tvåstegsverifiering för integrerade NPS innan tillgängligheten för NPS-tillägget för Azure och MFA miljöer hade konfigurerar och underhåller en separat MFA-server i en lokal miljö. Den här typen av autentisering som erbjuds av servern för fjärrskrivbordsgateway och Azure Multi-Factor Authentication-servern med hjälp av RADIUS.

Organisationer kan skydda RADIUS-klientautentisering genom att distribuera en lokalt baserad MFA-lösning eller en molnbaserad lösning för MFA med NPS-tillägget för Azure.
 
## <a name="authentication-flow"></a>Autentiseringsflöde
När användarna ansluter till en virtuell port på en VPN-server, måste de först autentisera med hjälp av en mängd olika protokoll. Protokoll som tillåter användning av en kombination av användarnamn och lösenord och certifikatbaserade autentiseringsmetoder. 

Användare måste ha rätt behörigheter för förutom verifiering, och verifiera sin identitet. I enklare implementationer anges behörighet som tillåter åtkomst direkt i Active Directory-användarobjekt. 

![Användaregenskaper](./media/nps-extension-vpn/image1.png)

I enklare implementationer varje VPN-server som beviljar eller nekar åtkomst baserat på principer som är definierade på varje lokala VPN-servern.

I större och mer skalbar implementeringar centraliserad av principer som beviljar eller nekar VPN-åtkomst på RADIUS-servrar. I dessa fall fungerar VPN-servern som en fjärråtkomstserver (RADIUS-klienten) som vidarebefordrar förfrågningar och kontot meddelanden till en RADIUS-server. Användare måste autentiseras för att ansluta till den virtuella porten på VPN-servern, och uppfyller de villkor som definieras centralt på RADIUS-servrar. 

När NPS-tillägget för Azure är integrerad med NPS, resultatet en lyckad autentiseringsflöde, enligt följande:

1. VPN-servern tar emot en begäran om autentisering från en VPN-användare som innehåller användarnamn och lösenord för att ansluta till en resurs, till exempel en fjärrskrivbordssession. 

2. Fungerar som en RADIUS-klient, VPN-servern konverterar begäran till en RADIUS *åtkomstbegäran* meddelande och skickar den (med ett krypterat lösenord) till RADIUS-servern där NPS-tillägg har installerats. 

3. Kombinationen av användarnamn och lösenord har verifierats i Active Directory. Om antingen användarnamnet eller lösenordet är felaktigt RADIUS-servern skickar en *nekad åtkomst* meddelande. 

4. Om alla villkor som anges i NPS-anslutningsbegäran och principer för nätverk är uppfyllda (till exempel tidpunkt eller grupp medlemskap begränsningar), NPS-tillägget utlöser en begäran om sekundära autentiseringen med Azure Multi-Factor Authentication. 

5. Azure Multi-Factor Authentication kommunicerar med Azure Active Directory, hämtar användarens information och utför den sekundära autentiseringen med hjälp av metoden som har konfigurerats av användaren (cell telefonsamtal, textmeddelande eller mobilapp). 

6. När MFA-kontrollen lyckas kommunicerar Azure Multi-Factor Authentication resultatet till NPS-tillägget.

7. När anslutningsförsöket både autentiseras och auktoriseras NPS där tillägget installeras skickar ett RADIUS *nekad* meddelandet till VPN-server (RADIUS-klienten).

8. Användaren beviljas åtkomst till den virtuella porten på VPN-servern och upprättar en krypterad VPN-tunnel.

## <a name="prerequisites"></a>Krav
Det här avsnittet beskrivs de förutsättningar som måste slutföras innan du kan integrera MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats:

* VPN-infrastruktur
* Nätverksprincip och åtkomsttjänster roll
* Azure Multi-Factor Authentication-licens
* Windows Server-programvara
* Bibliotek
* Azure Active Directory (AD Azure) synkroniseras med lokala Active Directory 
* Azure Active Directory-GUID-ID

### <a name="vpn-infrastructure"></a>VPN-infrastruktur
Den här artikeln förutsätter att du har en fungerande VPN-infrastruktur som använder Microsoft Windows Server 2016 och att VPN-servern för närvarande inte har konfigurerats att vidarebefordra anslutningsförfrågningar till en RADIUS-server. I artikeln får konfigurera du VPN-infrastruktur för att använda en central RADIUS-server.

Om du inte har en fungerande VPN-infrastruktur på plats kan du snabbt skapa en genom att följa riktlinjerna i flera VPN-installationen självstudier som du hittar på Microsoft och tredje parters webbplatser. 
            
### <a name="the-network-policy-and-access-services-role"></a>Rollen Nätverksprincip och åtkomsttjänster

Nätverksprincip och åtkomsttjänster innehåller funktioner för RADIUS-server och klient. Den här artikeln förutsätter att du har installerat rollen Nätverksprincip och åtkomsttjänster på en medlemsserver eller domänkontrollant i din miljö. I den här guiden konfigurerar du RADIUS för en VPN-konfiguration. Installera rollen Nätverksprincip och åtkomsttjänster på en server *än* VPN-servern.

Information om hur du installerar rollen Nätverksprincip och åtkomsttjänster tjänsten Windows Server 2012 eller senare finns [installera en NAP-hälsoprincipserver](https://technet.microsoft.com/library/dd296890.aspx). NAP är föråldrad i Windows Server 2016. En beskrivning av bästa praxis för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant finns [bästa praxis för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Det krävs en licens för Azure Multi-Factor Authentication och är tillgänglig via en Azure AD Premium, Enterprise Mobility + Security eller en fristående Multi-Factor Authentication-licens. Förbrukningsbaserad licenser för Azure MFA som per användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. Mer information finns i [hur du hämtar Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). I testsyfte kan använda du en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med nätverksprinciper och Access Services-rollen installerad. Alla steg i den här guiden utfördes med Windows Server 2016.

### <a name="libraries"></a>Bibliotek

Följande bibliotek installeras automatiskt med NPS-tillägg:

-   [Visual C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory-modulen för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Om Microsoft Azure Active Directory PowerShell-modulen inte finns installeras den med ett konfigurationsskript som du körs som en del av installationen. Det finns inget behov av att installera modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory-synced-with-on-premises-active-directory"></a>Azure Active Directory som har synkroniserats med lokala Active Directory 

Om du vill använda NPS-tillägget lokala användare synkroniseras med Azure Active Directory och aktiverats för MFA. Den här guiden förutsätter att lokala användare synkroniseras med Azure Active Directory via Azure AD Connect. Instruktioner för att aktivera användare för MFA finns nedan.

Information om Azure AD Connect finns [integrera dina lokala kataloger med Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory-GUID-ID 

Om du vill installera NPS-tillägget som du behöver veta GUID för Azure Active Directory. Instruktioner för att hitta GUID för Azure Active Directory finns i nästa avsnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurera RADIUS för VPN-anslutningar

Om du har installerat NPS-rollen på en medlemsserver, måste du konfigurera den för att autentisera och auktorisera VPN-klienten att begäranden VPN-anslutningar. 

Det här avsnittet förutsätter att du har installerat rollen Nätverksprincip och åtkomsttjänster men inte har konfigurerat den för användning i din infrastruktur.

> [!NOTE]
> Om du redan har en fungerande VPN-server som använder en centraliserad RADIUS-server för autentisering kan du hoppa över det här avsnittet.
>

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory
NPS-servern måste registreras i Active Directory för att fungera korrekt i det här scenariot.

1. Öppna Serverhanteraren.

2. I Serverhanteraren väljer **verktyg**, och välj sedan **Network Policy Server**. 

3. I NPS-konsolträdet högerklickar du på **NPS (lokal)**, och välj sedan **registrera servern i Active Directory**. Välj **OK** två gånger.

    ![Nätverksprincipserver](./media/nps-extension-vpn/image2.png)

4. Lämna konsolen öppen för nästa procedur.

### <a name="use-wizard-to-configure-the-radius-server"></a>Använd guiden för att konfigurera RADIUS-server
Du kan använda en standard (guidebaserad) eller avancerade konfigurationsalternativ för att konfigurera RADIUS-servern. Det här avsnittet förutsätter att du använder alternativet guidebaserad standardkonfiguration.

1. I NPS-konsolträdet väljer **NPS (lokal)**.

2. Under **standardkonfiguration**väljer **RADIUS-Server för fjärranslutningar eller VPN-anslutningar**, och välj sedan **konfigurera VPN eller fjärranslutning**.

    ![Konfigurera VPN](./media/nps-extension-vpn/image3.png)

3. I den **välja fjärranslutning eller virtuellt privat nätverk anslutningar typen** väljer **VPN-anslutningar**, och välj sedan **nästa**.

    ![Virtuellt privat nätverk](./media/nps-extension-vpn/image4.png)

4. I den **ange fjärranslutning eller VPN-servern** väljer **Lägg till**.

5. I den **ny RADIUS-klient** och ange ett eget namn, anger du namnet eller IP-adressen för VPN-servern och ange en delade hemliga lösenordet.  
    Skapa delade hemliga lösenordet långa och komplexa. Posten, eftersom du behöver det i nästa avsnitt.

    ![Ny RADIUS-klient](./media/nps-extension-vpn/image5.png)

6. Välj **OK**, och välj sedan **nästa**.

7. I den **konfigurera autentiseringsmetoder** fönstret acceptera standardvärdet (**Microsoft krypterad autentisering version 2 [MS-CHAPv2])** eller välj ett annat alternativ och välj **nästa** .

    > [!NOTE]
    > Om du konfigurerar Extensible Authentication Protocol (EAP), måste du använda Microsoft Challenge Handshake Authentication Protocol (CHAPv2) eller PEAP Protected Extensible Authentication Protocol (). Inga andra EAP stöds.
 
8. I den **ange användargrupper** väljer **Lägg till**, och välj sedan ett lämpligt gruppen.  
    Om det finns ingen grupp lämna det tomt om du vill bevilja åtkomst till alla användare.

    ![Fönstret Ange användargrupper](./media/nps-extension-vpn/image7.png)

9. Välj **nästa**.

10. I den **ange IP-filter** väljer **nästa**.

11. I den **ange krypteringsinställningar** fönstret acceptera standardinställningarna och välj sedan **nästa**.

    ![Fönstret Ange krypteringsinställningar](./media/nps-extension-vpn/image8.png)

12. I den **anger ett resursnamn** fönstret är tomt sfär, godkänner du standardinställningen och välj sedan **nästa**.

    ![Ange ett resursnamn fönster](./media/nps-extension-vpn/image9.png)

13. I den **Slutför nya fjärranslutningar eller VPN-anslutningar och RADIUS-klienter** väljer **Slutför**.

    ![Fönstret ”Slutför nya fjärr- eller virtuella privata nätverksanslutningar och RADIUS-klienter”](./media/nps-extension-vpn/image10.png)

### <a name="verify-the-radius-configuration"></a>Kontrollera RADIUS-konfiguration
Det här avsnittet beskrivs den konfiguration som du skapat med hjälp av guiden.

1. Expandera på nätverksprincipservern i konsolen NPS (lokal) **RADIUS-klienter**, och välj sedan **RADIUS-klienter**.

2. I informationsfönstret högerklickar du på RADIUS-klienten som du skapade och välj sedan **egenskaper**.  
    Egenskaper för RADIUS-klienten (VPN-server) bör vara som de som visas här:

    ![VPN-egenskaper](./media/nps-extension-vpn/image11.png)

3. Välj **Avbryt**.

4. Expandera på nätverksprincipservern i konsolen NPS (lokal) **principer**, och välj sedan **principer för anslutningsbegäran**.  
    VPN-anslutningar principen visas som visas i följande bild:

    ![Anslutningsbegäranden](./media/nps-extension-vpn/image12.png)

5. Under **principer**väljer **nätverksprinciper**.  
    Du bör se en princip för virtuella privata nätverk (VPN) anslutningar som liknar den princip som visas i följande bild:

    ![Nätverksprinciper](./media/nps-extension-vpn/image13.png)

## <a name="configure-your-vpn-server-to-use-radius-authentication"></a>Konfigurera VPN-servern för RADIUS-autentisering
I det här avsnittet kan du konfigurera VPN-servern för RADIUS-autentisering. Anvisningarna förutsätter att du har en fungerande konfiguration för VPN-server men inte har konfigurerat den att använda RADIUS-autentisering. När du konfigurerar VPN-servern, kontrollerar du att konfigurationen fungerar som förväntat.

> [!NOTE]
> Om du redan har en fungerande VPN-serverkonfiguration som använder RADIUS-autentisering kan du hoppa över det här avsnittet.
>

### <a name="configure-authentication-provider"></a>Konfigurera autentiseringsprovider
1. Öppna Serverhanteraren på VPN-servern.

2. I Serverhanteraren väljer **verktyg**, och välj sedan **Routning och fjärråtkomst**.

3. I den **Routning och fjärråtkomst** fönstret, högerklicka på  **\<servernamn > (lokal)**, och välj sedan **egenskaper**.

    ![Fönstret Routning och fjärråtkomst](./media/nps-extension-vpn/image14.png)
 
4. I den  **\<servernamn > (lokal) egenskaper** väljer den **säkerhet** fliken. 

5. På den **säkerhet** fliken, under **autentiseringsprovider**väljer **RADIUS-autentisering**, och välj sedan **konfigurera**.

    ![RADIUS-autentisering](./media/nps-extension-vpn/image15.png)
 
6. I den **RADIUS-autentisering** väljer **Lägg till**.

7. I den **Lägg till RADIUS-Server** fönster, gör du följande:

    a. I den **servernamn** ange namn eller IP-adress för RADIUS-server som du konfigurerade i föregående avsnitt.

    b. För den **delad hemlighet**väljer **ändra**, och ange sedan delade hemliga lösenordet som du skapade och registrerats tidigare.

    c. I den **tidsgräns (sekunder)** väljer du ett värde från **30** via **60**.  
    Timeout-värdet är nödvändigt att ge tillräckligt med tid att slutföra ytterligare autentiseringsfaktor.
 
    ![Fönstret Lägg till RADIUS-Server](./media/nps-extension-vpn/image16.png)
 
8. Välj **OK**.

### <a name="test-vpn-connectivity"></a>Testa en VPN-anslutning
I det här avsnittet kan bekräfta du att VPN-klienten autentiseras och auktoriseras RADIUS-servern när du försöker ansluta till den virtuella porten VPN. Anvisningarna förutsätter att du använder Windows 10 som en VPN-klient. 

> [!NOTE]
> Om du redan har konfigurerat ett VPN-klienten för att ansluta till VPN-servern och har sparat inställningarna kan du hoppa över de steg som rör konfigurera och spara ett objekt för VPN-anslutning.
>

1. På klientdatorn VPN väljer du den **starta** och välj sedan den **inställningar** knappen.

2. I den **Windowsinställningar** väljer **nätverk och Internet**.

3. Välj **VPN**.

4. Välj **lägga till en VPN-anslutning**.

5. I den **lägga till en VPN-anslutning** fönster i den **tillverkare av VPN** väljer **Windows (inbyggt)**, Fyll i de återstående fälten efter behov, och välj sedan **Spara**. 

    ![Fönstret ”Lägg till en VPN-anslutning”](./media/nps-extension-vpn/image17.png)
 
6. Gå till **Kontrollpanelen**, och välj sedan **nätverks- och delningscenter**.

7. Välj **ändra Kortinställningar**.

    ![Ändra inställningar för nätverkskort](./media/nps-extension-vpn/image18.png)

8. Högerklicka på nätverksanslutningen för VPN- och välj sedan **egenskaper**. 

    ![Egenskaper för VPN-nätverk](./media/nps-extension-vpn/image19.png)

9. I egenskapsfönstret för VPN-, Välj den **säkerhet** fliken. 

10. På den **säkerhet** kontrollerar att bara **Microsoft CHAP Version 2 (MS-CHAP v2)** är markerad och välj sedan **OK**.

    ![Alternativet ”Tillåt dessa protokoll”](./media/nps-extension-vpn/image20.png)

11. Högerklicka på VPN-anslutningen och välj sedan **Anslut**.

12. I den **inställningar** väljer **Anslut**.  
    En lyckad anslutning visas i säkerhetsloggen på RADIUS-server som händelse-ID 6272 som visas här:

    ![Fönstret Egenskaper för händelse](./media/nps-extension-vpn/image21.png)

## <a name="troubleshooting-guide"></a>Felsökningsguide
Anta att VPN-konfiguration fungerade innan du har konfigurerat VPN-servern för att använda en centraliserad RADIUS-server för autentisering och auktorisering. Om konfigurationen fungerar är det troligt att problemet orsakas av en felaktig konfiguration av RADIUS-servern eller användning av ett ogiltigt användarnamn eller lösenord. Till exempel om du använder alternativt UPN-suffix i användarnamnet, kan försök logga in misslyckas. Använd samma kontonamn för bästa resultat. 

Felsökning av problem med dessa är användbar om du vill starta för att granska säkerhetshändelser på RADIUS-servern. Om du vill spara tid på att söka efter händelser, kan du använda rollbaserad nätverksprincip och åtkomst till servern anpassad vy i Loggboken, som visas här. ”Händelse-ID 6273” anger händelser där NPS nekas åtkomst till en användare. 

![Loggboken](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor authentication
Det här avsnittet innehåller instruktioner för att aktivera användare för MFA och konfigurera konton för tvåstegsverifiering. 

### <a name="enable-multi-factor-authentication"></a>Aktivera multifaktorautentisering
I det här avsnittet kan aktivera du Azure AD-konton för MFA. Använd den klassiska Azure-portalen så att användarna för MFA. 

1. Gå till den [Microsoft Azure](https://manage.windowsazure.com) webbplats. 

2. Logga in som administratör.

3. I den vänstra rutan, Välj **Active Directory**.

    ![Standardkatalog](./media/nps-extension-vpn/image23.png)

4. I den **namn** väljer **standardkatalog** (eller en annan katalog, vid behov).

5. I den **standardkatalogen** väljer **konfigurera**.

    ![Konfigurera standardkatalogen](./media/nps-extension-vpn/image24.png)

6. I den **konfigurera** fönstret under **multifaktorautentisering**väljer **hantera tjänstinställningar**.

    ![Hantera inställningar för multifaktorautentisering](./media/nps-extension-vpn/image25.png)
 
7. I den **multifaktorautentisering** fönster, granska standard tjänstinställningar och välj sedan den **användare** fliken. 

    ![Fliken Multi-Factor authentication-användare](./media/nps-extension-vpn/image26.png)
 
8. På den **användare** väljer du de användare som du vill aktivera för MFA och välj sedan **aktivera**.

    ![Egenskaper](./media/nps-extension-vpn/image27.png)
 
9. När du uppmanas, Välj **aktivera multifaktorautentisering**.

    ![Aktivera multifaktorautentisering](./media/nps-extension-vpn/image28.png)
 
10. Välj **Stäng**. 

11. Uppdatera sidan.  
    Multifaktorautentisering statusen ändras till *aktiverad*.

Information om hur användarna för MFA finns [Kom igång med Azure Multi-Factor Authentication i molnet](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering
Användare kan inte logga in till resurser som styrs av principen MFA förrän de har konfigurerat en betrodd enhet ska användas för andra autentiseringsfaktor när ett konto har aktiverats för Multifaktorautentisering.

I det här avsnittet kan konfigurera du en betrodd enhet för användning med tvåstegsverifiering. Du har flera Enhetsalternativ för, inklusive följande:

* **Mobilapp**: installation av Microsoft Authenticator-appen på en Windows Phone, Android eller iOS-enhet. Beroende på din organisations principer kan krävs du för att använda appen i ett av två lägen: 
    * Ta emot meddelanden för verifieringar (skickas ett meddelande till din enhet).
    * Använd verifieringskoden (du måste ange en Verifieringskod som uppdateras var 30: e sekund). 

* **Mobiltelefon samtal eller textmeddelande**: du kan ta emot en automatisk telefonsamtal eller ett textmeddelande. Med alternativet telefonsamtal besvara samtalet och välj nummertecken (#) för autentisering. Med alternativet text kan du svara på textmeddelandet eller ange verifieringskoden i gränssnittet för inloggning.

* **Telefonsamtal till arbete**: den här processen är densamma som den process som beskrivs tidigare för automatisk telefonsamtal.

Om du vill konfigurera en enhet för att använda den mobila appen tar emot push-meddelanden för verifiering, gör du följande:

1. Logga in på [Microsoft Azure](https://aka.ms/mfasetup) eller en plats som den [Azure-portalen](https://portal.azure.com), där du måste autentisera med dina inloggningsuppgifter för MFA-aktiverade.  
    Du uppmanas att konfigurera kontot för ytterligare säkerhetsverifiering som visas här:

    ![Ökad säkerhet](./media/nps-extension-vpn/image29.png)

2. Välj **ställa in nu**.

3. I den **ytterligare säkerhetsverifiering** skriver du väljer en kontakt (**telefon för autentisering**, **Arbetstelefon**, eller **mobilappen** ), Välj ett land eller region och välj sedan en metod. Välj inte **kontakta mig** ännu.  
    Metoden varierar beroende på typ av. Om du exempelvis **mobilappen**, kan du välja om du vill ta emot meddelanden för verifiering eller använda en Verifieringskod. 

    ![Fönstret ”ytterligare säkerhetsverifiering”](./media/nps-extension-vpn/image30.png)

    De steg som följer förutsätter att du har valt **mobilappen** kontakta typen.

4. Välj **mobilappen**väljer **ta emot meddelanden för verifiering**, och välj sedan **konfigurera**. 

    ![Fönstret ”ytterligare säkerhetsverifiering”](./media/nps-extension-vpn/image31.png)
 
5. Om du inte redan gjort det installerar du Microsoft Authenticator-mobilappen på din enhet. 

6. Skanna in streckkoden som visas i mobilappen, eller ange informationen manuellt och välj sedan **klar**.

    ![Konfigurera Microsoft Authenticator-mobilappen](./media/nps-extension-vpn/image32.png)

7. I den **ytterligare säkerhetsverifiering** väljer **kontakta mig**, och sedan svara på meddelandet som skickas till din enhet.

8. I den **ytterligare säkerhetsverifiering** fönstret under **steg3: Om du skulle förlora åtkomsten till mobilappen**, anger ett kontaktnummer att anropa om du skulle förlora åtkomsten till mobilappen och välj sedan  **Nästa**.

    ![Fönstret ”ytterligare säkerhetsverifiering”](./media/nps-extension-vpn/image33.png)
 
9. I den **ytterligare säkerhetsverifiering** väljer **klar**.

Enheten har nu konfigurerats för att tillhandahålla en andra metod för verifiering. Information om hur du konfigurerar konton för tvåstegsverifiering finns [Konfigurera mitt konto för tvåstegsverifiering](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-the-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller instruktioner för att konfigurera VPN för att använda MFA för klientautentisering med VPN-servern.

När du installerar och konfigurerar tillägget NPS krävs alla RADIUS-baserad klientautentisering som bearbetas av den här servern att använda MFA. Om VPN-användare inte har registrerats i Azure Multi-Factor Authentication kan du göra något av följande:

* Konfigurera en annan RADIUS-server för att autentisera användare som inte har konfigurerats för att använda Multifaktorautentisering. 

* Skapa en post i registret som användarna handlingar kan tillhandahålla en andra autentiseringsfaktor om de har registrerats i Azure Multi-Factor Authentication. 

Skapa ett nytt strängvärde med namnet _REQUIRE_USER_MATCH i HKLM\SOFTWARE\Microsoft\AzureMfa_, och ange värdet *SANT* eller *FALSKT*. 

![Inställningen ”Kräv Användarmatchning”](./media/nps-extension-vpn/image34.png)
 
Om värdet anges till *SANT* eller är tomt, alla autentiseringsbegäranden regleras av MFA-kontrollen. Om värdet anges till *FALSKT*, MFA utmaningar utfärdas enbart till användare som är registrerade i Azure Multi-Factor Authentication. Använd den *FALSKT* anger under testning eller i produktionsmiljöer under en tid för onboarding.

### <a name="obtain-the-azure-active-directory-guid-id"></a>Hämta Azure Active Directory-GUID-ID

Som en del av konfigurationen av filnamnstillägget NPS, måste du ange autentiseringsuppgifter och ID för Azure AD-klienten. Hämta ID: T genom att göra följande:

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klient.

2. I den vänstra rutan, Välj den **Azure Active Directory** knappen.

3. Välj **egenskaper**.

4. Din Azure AD-ID och markera den **kopiera** knappen.
 
    ![Azure AD-ID](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägg
NPS-tillägget måste installeras på en server som har serverrollen för nätverksprincip och åtkomsttjänster installerad och som fungerar som RADIUS-server i din design. Gör *inte* Installera NPS-tillägget på servern för fjärrskrivbord.

1. Hämta NPS-tillägget från [Microsoft Download Center](https://aka.ms/npsmfa). 

2. Kopiera den körbara filen för installationsprogrammet (*NpsExtnForAzureMfaInstaller.exe*) till NPS-servern.

3. Dubbelklicka på NPS-servern **NpsExtnForAzureMfaInstaller.exe** och välj om du uppmanas, **kör**.

4. I den **NPS-tillägget för Azure MFA installation** fönster, granska licensvillkoren för programvara, Välj den **jag godkänner licensvillkoren och villkor** kryssrutan och välj sedan **installera**.

    ![Fönstret ”NPS-tillägget för Azure MFA för”](./media/nps-extension-vpn/image36.png)
 
5. I den **NPS-tillägget för Azure MFA installation** väljer **Stäng**.  

    ![”Installationen lyckades” bekräftelsefönstret](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-by-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript
Konfigurera certifikat för användning av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenter är ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS. 

Skriptet utförs följande åtgärder:

* Skapar ett självsignerat certifikat.
* Associerar den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD.
* Lagrar certifikatet i lokala datorns Arkiv.
* Ger användaren nätverksåtkomst till certifikatets privata nyckel.
* NPS-tjänsten har startats om.

Om du vill använda dina egna certifikat måste du koppla den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD och så vidare.

Om du vill använda skriptet ger tillägget dina administrativa autentiseringsuppgifter för Azure Active Directory och Azure Active Directory klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerar NPS-tillägget.

1. Kör Windows PowerShell som administratör.

2. I Kommandotolken PowerShell ange **cd c:\Program Files\Microsoft\AzureMfa\Config**, och välj sedan RETUR.

3. I nästa kommandotolk, ange **.\AzureMfsNpsExtnConfigSetup.ps1**, och välj sedan RETUR.  
    Skriptet kontrollerar om Azure AD PowerShell-modulen är installerad. Om det inte är installerat installeras skriptet modulen.
 
    ![PowerShell](./media/nps-extension-vpn/image38.png)
 
    När skriptet har kontrollerat installationen av PowerShell-modulen, visar Azure Active Directory PowerShell-inloggning modulfönstret. 

4. Ange autentiseringsuppgifter för Azure AD-administratör och lösenord och välj sedan **logga in**. 
 
    ![Fönstret för PowerShell](./media/nps-extension-vpn/image39.png)
 
5. Klistra in klient-ID som du kopierade tidigare i Kommandotolken, och välj sedan RETUR. 

    ![Klient-ID:t](./media/nps-extension-vpn/image40.png)

    Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Utdata är precis som i följande bild:

    ![Självsignerat certifikat](./media/nps-extension-vpn/image41.png)

6. Starta om servern.

### <a name="verify-the-configuration"></a>Kontrollera att konfigurationen
Om du vill verifiera konfigurationen måste du upprätta en ny VPN-anslutning med VPN-servern. När du har angett dina autentiseringsuppgifter för primär autentisering, väntar VPN-anslutningen på den sekundära autentiseringen ska lyckas innan anslutningen har upprättats, enligt nedan. 

![Fönstret VPN för Windows-inställningar](./media/nps-extension-vpn/image42.png)

Om du har autentiseras med sekundära verifieringsmetod som du tidigare har konfigurerat i Azure MFA är du ansluten till resursen. Om den sekundära autentiseringen misslyckas nekas åtkomst till resursen. 

I följande exempel innehåller Microsoft Authenticator-appen på en Windows Phone sekundära autentiseringen:

![Verifiera konto](./media/nps-extension-vpn/image43.png)

När du har autentiserats med hjälp av metoden sekundär, beviljas åtkomst till den virtuella porten på VPN-servern. Eftersom var du tvungen att använda en sekundär autentiseringsmetod med hjälp av en mobil app på en betrodd enhet, är logga in-processen säkrare än om den kombinera endast användarnamn och lösenord.

### <a name="view-event-viewer-logs-for-successful-sign-in-events"></a>Visa loggarna i Loggboken för händelser för lyckad inloggning
Om du vill visa händelser för lyckad inloggning i Windows Loggboken, logga frågan Windows-säkerhet på NPS-servern genom att ange följande PowerShell-kommando:

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL 

![PowerShell säkerhet Loggboken](./media/nps-extension-vpn/image44.png)
 
Du kan också visa säkerhetsloggen eller anpassad vy nätverksprincip och åtkomsttjänster som visas här:

![Network Policy Server logg](./media/nps-extension-vpn/image45.png)

På den server där du installerade NPS-tillägget för Azure Multi-Factor Authentication, hittar du loggarna i Loggboken program som är specifika för tillägget på *program och tjänster Logs\Microsoft\AzureMfa*. 

    _Get-WinEvent -Logname Security_ | where {$_.ID -eq '6272'} | FL

![Loggboken ”antal händelser” fönstret](./media/nps-extension-vpn/image46.png)

## <a name="troubleshooting-guide"></a>Felsökningsguide
Om konfigurationen inte fungerar som förväntat, kan du börja felsöka genom att verifiera att användaren är konfigurerad för att använda Multifaktorautentisering. Behörighet att ansluta till den [Azure-portalen](https://portal.azure.com). Om användaren uppmanas att ange sekundära autentiseringen och kan autentisera, vet du att en felaktig konfiguration av MFA som ett problem.

Om MFA arbetar för användaren, granska relevant loggarna i Loggboken. Loggarna innehålla säkerhetshändelse, Gateway fungerar och Azure Multi-Factor Authentication loggar som beskrivs i föregående avsnitt. 

Ett exempel på en säkerhetslogg som visar en misslyckad inloggning händelse (event ID 6273) visas här:

![Säkerhetsloggen visar en misslyckad inloggning-händelse](./media/nps-extension-vpn/image47.png)

En relaterad händelse från Azure Multi-Factor Authentication-loggen visas här:

![Azure Multi-Factor Authentication loggar](./media/nps-extension-vpn/image48.png)

Gör Avancerad felsökning finns i NPS format loggfiler där NPS-tjänsten är installerad. Loggfilerna skapas i den _%SystemRoot%\System32\Logs_ mapp som kommaavgränsad textfiler. En beskrivning av loggfilerna finns [tolka NPS loggfilerna](https://technet.microsoft.com/library/cc771748.aspx). 

Posterna i dessa loggfiler är svåra att tolka såvida inte du exportera dem till ett kalkylblad eller en databas. Du hittar många tjänsten IAS (Internet Authentication) parsning online verktyg som hjälper dig att tolka loggfilerna. Utdata i en sådan nedladdningsbara [shareware-programmet](http://www.deepsoftware.com/iasviewer) visas här: 

![Shareware-programmet](./media/nps-extension-vpn/image49.png)

Om du vill göra ytterligare felsökning kan du använda en protokollanalysator, till exempel Wireshark eller [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Följande bild från Wireshark visar RADIUS-meddelanden mellan VPN-servern och NPS.

![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

Mer information finns i [integrera din befintliga infrastruktur för NPS med Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md). 

## <a name="next-steps"></a>Nästa steg
[Hämta Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrera dina lokala kataloger med Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

