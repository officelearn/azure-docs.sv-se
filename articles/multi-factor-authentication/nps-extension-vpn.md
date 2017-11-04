---
title: "VPN-integrering med Azure MFA med hjälp av NPS-tillägg | Microsoft Docs"
description: "Den här artikeln beskrivs integrera din VPN-infrastruktur med Azure MFA med hjälp av Server (NPS)-tillägget för Microsoft Azure."
services: active-directory
keywords: "Azure MFA integrera VPN, Azure Active Directory, NPS-tillägg"
documentationcenter: 
author: kgremban
manager: femila
ms.assetid: 
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: kgremban
ms.reviewer: jsnow
ms.custom: it-pro
ms.openlocfilehash: 8ac4c5d88e724febf21fe6bcc8ecf939283f361e
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/03/2017
---
# <a name="integrate-your-vpn-infrastructure-with-azure-multi-factor-authentication-mfa-using-the-network-policy-server-nps-extension-for-azure"></a>Integrera din VPN-infrastruktur med Azure Multi-Factor Authentication (MFA) med hjälp av Server (NPS)-tillägget för Azure

## <a name="overview"></a>Översikt

Server (NPS)-tillägget för Azure gör att organisationer kan skydda RADIUS Remote Authentication Dial-In User Service ()-klientautentisering med hjälp av molnbaserade [Azure Multi-Factor Authentication (MFA)](multi-factor-authentication-get-started-server-rdg.md), som innehåller tvåstegsverifiering.

Den här artikeln innehåller instruktioner för att integrera NPS-infrastrukturen med Azure MFA med hjälp av NPS-tillägget för Azure för att aktivera säker tvåstegsverifiering för användare som försöker ansluta till nätverket med hjälp av en VPN-anslutning. 

Nätverksprincip och Access Services (NPS) gör att organisationer följande möjligheter:

* Ange centrala platser för hantering och kontroll av begäranden för nätverk att ange vem som kan ansluta, vilka tidpunkter på dagen anslutningar tillåts varaktigheten för anslutningar och säkerhetsnivån som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje server för VPN- eller Gateway för fjärrskrivbord (RD), kan dessa principer anges en gång på en central plats. RADIUS-protokollet används för att ge centraliserad autentisering, auktorisering och redovisning. 
* Skapa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.
* Ger möjlighet att genomdriva autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.    

Mer information finns i [Server (NPS)](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-top). 

Om du vill förbättra säkerheten och tillhandahålla hög kompatibilitet, företag kan integrera NPS med Azure MFA för att säkerställa att användarna använder tvåstegsverifiering ska kunna ansluta till den virtuella porten på VPN-servern. De måste ange sina användarnamn/lösenord tillsammans med information som användaren har i deras kontroll för användare att få åtkomst. Den här informationen måste betrodda och inte enkelt dubbleras, till exempel ett mobiltelefonnummer, fast telefon nummer, program på en mobil enhet och så vidare.

Kunder som vill implementera tvåstegsverifiering för integrerad NPS och Azure MFA-miljöer hade konfigurerar och underhåller en separat MFA-Server i den lokala miljön enligt beskrivningen i fjärrskrivbordsgateway och Azure Multi-Factor Authentication-servern med RADIUS innan tillgängligheten för NPS-tillägget för Azure.

Tillgängligheten för NPS-tillägget för Azure nu ger organisationer möjlighet att distribuera en lokalt baserad MFA-lösning eller en molnbaserad lösning för MFA till säker autentisering för RADIUS-klienten.
 
## <a name="authentication-flow"></a>Autentiseringsflöde
När en användare ansluter till en virtuell port på en VPN-server, måste de först autentisera med olika protokoll som tillåter användning av en kombination av användarnamn/lösenord och certifikatbaserade autentiseringsmetoder. 

Utöver verifiering, och verifiera identitet har användare rätt behörigheter för. I enklare implementationer anges dessa behörighet som tillåter åtkomst direkt i Active Directory-användarobjekt. 

 ![Egenskaper för användare](./media/nps-extension-vpn/image1.png)

Enklare implementationer varje VPN-server som beviljar eller nekar åtkomst baserat på principer som definierats på varje lokala VPN-servern.

I större och mer skalbar implementeringar av principerna för att bevilja eller neka VPN-åtkomst är centraliserad på RADIUS-servrar. I det här fallet fungerar VPN-servern som en fjärråtkomstserver (RADIUS-klienten) som vidarebefordrar förfrågningar och kontot meddelanden till en RADIUS-server. Användare måste autentiseras för att ansluta till den virtuella porten på VPN-servern, och uppfyller de villkor som definieras centralt på RADIUS-servrar. 

När NPS-tillägget för Azure är integrerad med NPS, är flödet för autentiseringen följande:

1. VPN-servern tar emot en begäran om autentisering från en VPN-användare som innehåller användarnamn och lösenord för att ansluta till en resurs, till exempel en fjärrskrivbordssession. 
2. Fungerar som en RADIUS-klient, VPN-servern konverterar begäran till en RADIUS-åtkomstbegäran och skickar meddelandet (lösenord krypteras) till RADIUS-(NPS)-servern där NPS-tillägg har installerats. 
3. Kombinationen av användarnamn och lösenord har verifierats i Active Directory. Om användarnamnet / lösenord är felaktigt, RADIUS-servern skickar ett meddelande om nekad åtkomst. 
4. Om alla villkor som anges i NPS-anslutningsbegäran och nätverksprinciper uppfylls (till exempel tidpunkt eller grupp medlemskap begränsningar), NPS-tillägget utlöser en begäran om sekundära autentiseringen med Azure MFA. 
5. Azure MFA kommunicerar med Azure Active Directory, hämtar användarens information och utför sekundära autentiseringen genom att använda metoden konfigureras av användaren (textmeddelande, mobilapp och så vidare). 
6. Vid framgången för MFA-kontrollen har kommunicerar Azure MFA resultatet till NPS-tillägget.
7. När anslutningsförsöket både autentiseras och auktoriseras skickar NPS-servern där tillägget installeras en RADIUS-åtkomst-meddelande till VPN-server (RADIUS-klienten).
8. Användaren beviljas åtkomst till den virtuella porten på VPN-servern och upprättar en krypterad VPN-tunnel.

## <a name="prerequisites"></a>Krav
Det här avsnittet beskrivs förutsättningarna som krävs innan du integrera Azure MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats.

* VPN-infrastruktur
* Nätverksprincip och Access Services (NPS)
* Azure MFA-licens
* Windows Server-programvara
* Bibliotek
* Azure AD synkroniserade med lokala AD 
* Azure Active Directory-GUID-ID

### <a name="vpn-infrastructure"></a>VPN-infrastruktur
Den här artikeln förutsätter att du har en fungerande VPN-infrastruktur med hjälp av Microsoft Windows Server 2016 på plats och att VPN-servern för närvarande inte har konfigurerats att vidarebefordra anslutningsförfrågningar till en RADIUS-server. Du konfigurerar VPN-infrastruktur för att använda en central RADIUS-server i den här guiden.

Om du inte har en fungerande infrastruktur på plats kan du snabbt skapa infrastrukturen genom att följa riktlinjerna i flera självstudier för VPN-installationen hittar du i Microsoft och tredje parters webbplatser. 

### <a name="network-policy-and-access-services-nps-role"></a>Nätverksprincip och Access Services (NPS)

NPS-rolltjänsten tillhandahåller RADIUS-server och klient. Den här artikeln förutsätter att du har installerat NPS-rollen på en medlemsserver eller domänkontrollant i din miljö. För en VPN-konfiguration i den här guiden konfigurerar du RADIUS. Installera NPS-rollen på en server _andra_ än VPN-servern.

Information om hur du installerar NPS-rollen tjänst i Windows Server 2012 eller högre, se [installera en NAP-hälsoprincipserver](https://technet.microsoft.com/library/dd296890.aspx). Princip för NAP (Network Access) är föråldrad i Windows Server 2016. En beskrivning av bästa praxis för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant finns [bästa praxis för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Krävs är en licens för Azure MFA som finns tillgänglig via en Azure AD Premium, Enterprise Mobility plus Security (EMS) eller en MFA-prenumeration. Mer information finns i [hur du hämtar Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md). I testsyfte kan använda du en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i den här guiden utfördes med Windows Server 2016.

### <a name="libraries"></a>Bibliotek

Dessa bibliotek installeras automatiskt med tillägget.

-   [Visual C++ Redistributable-paket för Visual Studio 2013 (X64)](https://www.microsoft.com/download/details.aspx?id=40784)
-   [Microsoft Azure Active Directory-modulen för Windows PowerShell version 1.1.166.0](https://connect.microsoft.com/site1164/Downloads/DownloadDetails.aspx?DownloadID=59185)

Den Microsoft Azure Active Directory-modulen för Windows PowerShell installeras, om det inte redan finns, via ett konfigurationsskript som du körs som en del av installationen. Det finns inget behov av att installera den här modulen i förväg om den inte redan är installerad.

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory är synkroniserade med lokala Active Directory 

Om du vill använda NPS-tillägget lokala användare synkroniseras med Azure Active Directory och aktiverats för Multifaktorautentisering. Den här guiden förutsätter att lokala användare är synkroniserade med Azure Active Directory med AD Connect. Instruktioner för att aktivera användare för MFA finns nedan.
Information om Azure AD connect, se [integrera dina lokala kataloger med Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory-GUID-ID 
Om du vill installera NPS-tillägget som du behöver veta GUID för Azure Active Directory. Instruktioner för att hitta GUID för Azure Active Directory finns i nästa avsnitt.

## <a name="configure-radius-for-vpn-connections"></a>Konfigurera RADIUS för VPN-anslutningar

Om du har installerat rollen NPS-server på en medlemsserver, måste du konfigurera för att autentisera och auktorisera VPN-klienten att begäran VPN-anslutningar. 

Det här avsnittet förutsätter att du har installerat NPS-rollen men inte har konfigurerat den för användning i din infrastruktur.

>[!NOTE]
>Om du redan har en fungerande VPN-server som använder en centraliserad RADIUS-server för autentisering kan du hoppa över det här avsnittet.
>

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory
NPS-servern måste registreras i Active Directory för att fungera i det här scenariot.

1. Öppna Serverhanteraren.
2. I Serverhanteraren klickar du på **verktyg**, och klicka sedan på **Network Policy Server**. 
3. I NPS-konsolträdet högerklickar du på **NPS (lokal)**, och klicka sedan på **registrera servern i Active Directory**. Klicka på **OK** två gånger.

 ![Nätverksprincipserver](./media/nps-extension-vpn/image2.png)

4. Lämna konsolen öppen för nästa procedur.

### <a name="use-wizard-to-configure-radius-server"></a>Använd guiden för att konfigurera RADIUS-server
Du kan använda en standard (guidebaserad) eller avancerade konfigurationsalternativ för att konfigurera RADIUS-servern. Det här avsnittet förutsätter användningen av alternativet guidebaserad standardkonfiguration.

1. NPS-konsolen, klicka på **NPS (lokal)**.
2. Välj under standardkonfiguration **RADIUS-Server för fjärranslutningar eller VPN-anslutningar**, och klicka sedan på **konfigurera VPN eller fjärranslutning**.

 ![Konfigurera VPN](./media/nps-extension-vpn/image3.png)

3. På sidan Välj fjärranslutning eller virtuellt privat nätverk anslutningar typ väljer **VPN-anslutningar**, och klicka på **nästa**.

 ![Virtuellt privat nätverk](./media/nps-extension-vpn/image4.png)

4. På sidan Ange fjärranslutning eller VPN-servern **Lägg till**.
5. I den **ny RADIUS-klient** dialogrutan, ange ett eget namn, anger du namnet eller IP-adressen för VPN-servern och ange ett delade hemliga lösenord. Skapa den här delade hemliga lösenordet långa och komplexa. Anteckna det här lösenordet som du behöver för stegen i nästa avsnitt.

 ![Ny RADIUS-klient](./media/nps-extension-vpn/image5.png)

6. Klicka på **OK**, och sedan **nästa**.
7. På den **konfigurera autentiseringsmetoder** godkänner standardvalet (Microsoft krypterad autentisering version 2 (MS-CHAPv2) eller välj ett annat alternativ och klicka på **nästa**.

  >[!NOTE]
  >Om du konfigurerar Extensible Authentication Protocol (EAP), måste du använda MS-CHAPv2 eller PEAP. Inga andra EAP stöds.
 
8. På sidan Ange användargrupper **Lägg till** och välj en lämplig grupp, om sådan finns. Annars lämna alternativet tomt om du vill bevilja åtkomst till alla användare.

 ![Ange användargrupper](./media/nps-extension-vpn/image7.png)

9. Klicka på **Nästa**.
10. På sidan Ange IP-filter **nästa**.
11. På sidan Ange krypteringsinställningar acceptera standardinställningarna och klickar på **nästa**.

 ![Ange kryptering](./media/nps-extension-vpn/image8.png)

12. På Ange ett resursnamn är tomt, godkänner du standardinställningen och på **nästa**.

 ![Ange ett resursnamn](./media/nps-extension-vpn/image9.png)

13. Klicka på Slutför nya fjärr- eller VPN-anslutningar och RADIUS-klienter sidan **Slutför**.

 ![Slutföra anslutningar](./media/nps-extension-vpn/image10.png)

### <a name="verify-radius-configuration"></a>Kontrollera RADIUS-konfiguration
Det här avsnittet beskrivs den konfiguration som du skapat med hjälp av guiden.

1. Expandera RADIUS-klienter på NPS-servern i konsolen NPS (lokal) och välj **RADIUS-klienter**.
2. I informationsfönstret högerklickar du på RADIUS-klienten som du skapat med hjälp av guiden och klicka på **egenskaper**. Egenskaperna för RADIUS-klienten (VPN-server) ska vara samma som visas nedan.

 ![VPN-egenskaper](./media/nps-extension-vpn/image11.png)

3. Klicka på **Avbryt**.
4. NPS-servern i konsolen NPS (lokal) utöka **principer**, och välj **principer för anslutningsbegäran**. Du bör se den princip för VPN-anslutningar som liknar bilden nedan.

 ![Anslutningsbegäranden](./media/nps-extension-vpn/image12.png)

5. Enligt principer, Välj **nätverksprinciper**. Du bör en princip för virtuella privata nätverk (VPN) anslutningar som liknar bilden nedan.

 ![Egenskaper för nätverk](./media/nps-extension-vpn/image13.png)

## <a name="configure-vpn-server-to-use-radius-authentication"></a>Konfigurera VPN-servern för RADIUS-autentisering
I det här avsnittet kan du konfigurera VPN-servern för RADIUS-autentisering. Det här avsnittet förutsätter att du har en fungerande konfiguration för VPN-server men inte har konfigurerat VPN-servern för RADIUS-autentisering. När du konfigurerar VPN-servern måste bekräfta du att konfigurationen fungerar som förväntat.

>[!NOTE]
>Om du redan har en fungerande VPN-serverkonfiguration som använder RADIUS-autentisering kan du hoppa över det här avsnittet.
>

### <a name="configure-authentication-provider"></a>Konfigurera autentiseringsprovider
1. Öppna Serverhanteraren på VPN-servern.
2. I Serverhanteraren klickar du på **verktyg**, och sedan **Routning och fjärråtkomst**.
3. Högerklicka i konsolen Routning och fjärråtkomst  **\[servernamn\] (lokal)**, och klicka sedan på **egenskaper**.

 ![Routning och fjärråtkomst](./media/nps-extension-vpn/image14.png)
 
4. I den **[servernamn} (lokal) egenskaper** dialogrutan klickar du på den **säkerhet** fliken. 
5. På den **säkerhet** under autentiseringsprovider fliken klickar du på **RADIUS-autentisering**, och sedan **konfigurera**.

 ![RADIUS-autentisering](./media/nps-extension-vpn/image15.png)
 
6. I dialogrutan RADIUS-autentisering klickar du på **Lägg till**.
7. Lägg till namnet eller IP-adressen för RADIUS-server som du konfigurerade i föregående avsnitt i Lägg till RADIUS-servern i servernamn.
8. Klicka på delad hemlighet **ändra** och lägga till delade hemliga lösenordet du skapade och registrerats tidigare.
9. I timeout (sekunder), ändrar du värdet till ett värde mellan **30** och **60**. Detta är nödvändigt att tillräckligt med tid att slutföra ytterligare autentiseringsfaktor.
 
 ![Lägg till RADIUS-Server](./media/nps-extension-vpn/image16.png)
 
10. Klicka på **OK** tills du är klar stänger du alla dialogrutor.

### <a name="test-vpn-connectivity"></a>Testa en VPN-anslutning
I det här avsnittet kan bekräfta du att VPN-klienten autentiseras och auktoriseras RADIUS-servern när du försöker ansluta till VPN-virtuell port. Det här avsnittet förutsätter att du använder Windows 10 som en VPN-klient. 

>[!NOTE]
>Om du redan har konfigurerat ett VPN-klienten för att ansluta till VPN-servern och har sparat inställningarna kan du hoppa över de steg som rör konfigurera och spara ett objekt för VPN-anslutning.
>

1. Klicka på din VPN-klientdatorn **starta**, och sedan **inställningar** (kugghjulsikon).
2. I fönstret Inställningar, klickar du på **nätverk och Internet**.
3. Klicka på **VPN**.
4. Klicka på **lägga till en VPN-anslutning**.
5. I en VPN-anslutning, ange Windows (inbyggt) som VPN-provider och sedan Fyll i de återstående fälten efter behov, och sedan **spara**. 

 ![Lägg till VPN-anslutning](./media/nps-extension-vpn/image17.png)
 
6. Öppna den **nätverks- och delningscenter** på Kontrollpanelen.
7. Klicka på **ändra Kortinställningar**.

 ![Ändra inställningar för nätverkskort](./media/nps-extension-vpn/image18.png)

8. Högerklicka på VPN-nätverksanslutningen och klicka på Egenskaper. 

 ![Egenskaper för VPN-nätverk](./media/nps-extension-vpn/image19.png)

9. I egenskapsdialogrutan för VPN klickar du på den **säkerhet** fliken. 
10. På fliken säkerhet, kontrollera att endast **Microsoft CHAP Version 2 (MS-CHAP v2)** är markerad och klicka på OK.

 ![Tillåt protokoll](./media/nps-extension-vpn/image20.png)

11. Högerklicka på VPN-anslutningen och på **Anslut**.
12. På sidan Inställningar **Anslut**.

En lyckad anslutning visas i säkerhetsloggen på RADIUS-server som händelse-ID 6272 enligt nedan.

 ![Egenskaper för händelse](./media/nps-extension-vpn/image21.png)

## <a name="troubleshoot-guide"></a>Felsöka programguiden
Anta att VPN-konfiguration fungerade innan du har konfigurerat VPN-servern för att använda en centraliserad RADIUS-server för autentisering och auktorisering. I det här fallet är det troligt att problemet kan bero på en felaktig konfiguration av RADIUS-servern eller användning av ett ogiltigt användarnamn eller lösenord. Till exempel om du använder alternativt UPN-suffix i användarnamnet inloggningsförsöket misslyckas (du måste använda samma kontonamn för bästa resultat). 

Felsökning av problem med dessa är användbar om du vill starta för att granska säkerhetshändelser på RADIUS-servern. För att spara tid på att söka efter händelser, kan du använda den rollbaserade nätverksprincip och åtkomst till servern anpassad vy i Loggboken, som visas nedan. Händelse-ID 6273 anger händelser där nätverksprincipservern nekas åtkomst till en användare. 

 ![Loggboken](./media/nps-extension-vpn/image22.png)
 
## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication
Det här avsnittet innehåller instruktioner för att aktivera användare för MFA och konfigurera konton för tvåstegsverifiering. 

### <a name="enable-multi-factor-authentication"></a>Aktivera multifaktorautentisering
I det här avsnittet kan aktivera du Azure AD-konton för MFA. Använd den **klassiska portalen** användarna för MFA. 

1. Öppna en webbläsare och gå till [https://manage.windowsazure.com](https://manage.windowsazure.com). 
2. Logga in som administratör.
3. I portalen i det vänstra navigeringsfönstret klickar du på **ACTIVE DIRECTORY**.

 ![Standardkatalogen](./media/nps-extension-vpn/image23.png)

4. I kolumnen namn klickar du på **standardkatalog** (eller en annan katalog, vid behov).
5. På sidan Snabbstart **konfigurera**.

 ![Konfigurera standard](./media/nps-extension-vpn/image24.png)

6. Rulla ned på sidan Konfigurera och klicka på under multifaktorautentisering **hantera tjänstinställningar**.

 ![Hantera inställningar för MFA](./media/nps-extension-vpn/image25.png)
 
7. På sidan multifaktorautentisering granska standardinställningarna för tjänsten och klicka sedan på **användare**. 

 ![MFA-användare](./media/nps-extension-vpn/image26.png)
 
8. På sidan användare väljer du de användare som du vill aktivera för MFA och klicka sedan på **aktivera**.

 ![Egenskaper](./media/nps-extension-vpn/image27.png)
 
9. När du uppmanas, klickar du på **aktivera multifaktorautentisering**.

 ![Aktivera MFA](./media/nps-extension-vpn/image28.png)
 
10. Klicka på **Stäng**. 
11. Uppdatera sidan. MFA-statusen ändras till aktiverad.

Information om hur du aktiverar användare för Multifaktorautentisering finns i [komma igång med Azure Multi-Factor Authentication i molnet](multi-factor-authentication-get-started-cloud.md). 

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering
När ett konto har aktiverats för MFA, kan användare inte logga in till resurser som omfattas av principen MFA förrän de har konfigurerat en betrodd enhet ska användas för andra autentiseringsfaktor använt tvåstegsverifiering.

I det här avsnittet kan konfigurera du en betrodd enhet för användning med tvåstegsverifiering. Det finns flera alternativ att konfigurera dessa, inklusive följande:

* **Mobilapp**. Du installerar Microsoft Authenticator-appen på en Windows Phone, Android eller iOS-enhet. Beroende på din organisations principer kan du behöver använda appen i ett av två lägen: ta emot meddelanden för verifieringar (skickas ett meddelande till din enhet) eller Använd verifieringskoden (du måste ange en Verifieringskod som uppdateras var 30: e sekund). 
* **Mobiltelefon samtal eller textmeddelande**. Du kan antingen ta emot en automatisk telefonsamtal eller SMS. Med alternativet telefonsamtal besvara samtalet och tryck på #-tangenten för att autentisera. Med alternativet text kan du svara på textmeddelandet eller ange verifieringskoden i inloggning-gränssnittet.
* **Telefonsamtal till arbete**. Den här processen är densamma som beskrivs för automatisk telefonsamtal ovan.

Följ dessa instruktioner för att skapa en enhet som använder mobilappen för att ta emot push-meddelanden för verifiering.

1. Logga in på [https://aka.ms/mfasetup](https://aka.ms/mfasetup) eller en plats som [https://portal.azure.com](https://portal.azure.com), där du krävs för att autentisera med dina inloggningsuppgifter för MFA-aktiverade. 
2. När du loggar in med ditt användarnamn och lösenord kan visas du en skärm där du uppmanas att konfigurera kontot för ytterligare säkerhetsverifiering.

 ![Ökad säkerhet](./media/nps-extension-vpn/image29.png)

3. Klicka på **ställa in nu**.
4. Välj en Kontakta typ (telefon för autentisering, arbetstelefon eller mobilapp) på sidan ytterligare säkerhet verifiering. Sedan väljer ett land eller region och välj en metod. Metoden varierar beroende på typ av du väljer. Om du väljer mobilappar kan du till exempel väljer om du vill ta emot meddelanden för verifiering eller använda en Verifieringskod. De steg som följer förutsätter att du väljer **mobilappen** kontakta typen.

 ![Phone autentisering](./media/nps-extension-vpn/image30.png)

5. Välj mobilappar, klicka på **ta emot meddelanden för verifiering**, och sedan **konfigurera**. 

 ![Verifiering av Mobilappar](./media/nps-extension-vpn/image31.png)
 
6. Om du inte redan gjort det installerar du authenticator-mobilappen på din enhet. 
7. Följ instruktionerna i mobilappen för att skanna in streckkoden som presenterades eller ange informationen manuellt och klickar sedan på **klar**.

 ![Konfigurera Mobilappen](./media/nps-extension-vpn/image32.png)

8. På sidan för verifiering av ytterligare säkerhet **kontakta mig** och svara på meddelanden som skickas till din enhet.
9. På sidan för ytterligare säkerhet verifiering anger du ett kontaktnummer ifall du skulle förlora åtkomsten till mobilappen och klicka på **nästa**.

 ![Mobiltelefonnummer](./media/nps-extension-vpn/image33.png)
 
10. Klicka på ytterligare säkerhetsverifiering **klar**.

Enheten har nu konfigurerats för att tillhandahålla en andra metod för verifiering. Information om hur du konfigurerar konton för tvåstegsverifiering finns [Konfigurera mitt konto för tvåstegsverifiering](./end-user/multi-factor-authentication-end-user-first-time.md).

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller instruktioner för att konfigurera VPN för att använda Azure MFA för klientautentisering med VPN-servern.

När du installerar och konfigurerar tillägget NPS krävs alla RADIUS-baserad klientautentisering som bearbetas av den här servern att använda Azure MFA. Om inte alla VPN-användare är registrerade i Azure MFA, du kan ställa in en annan RADIUS-server för att autentisera användare som inte har konfigurerats för att använda Multifaktorautentisering. Eller skapa en post i registret som användarna handlingar kan tillhandahålla en andra autentiseringsfaktor endast om de har registrerats i MFA. 

Skapa ett nytt strängvärde med namnet _REQUIRE_USER_MATCH i HKLM\SOFTWARE\Microsoft\AzureMfa_, och ange värdet till TRUE eller FALSE. 

 ![Kräv Användarmatchning](./media/nps-extension-vpn/image34.png)
 
Om värdet är SANT eller inte har angetts, regleras alla autentiseringsbegäranden av MFA-kontrollen. Om värdet anges till FALSE, utfärdas MFA utmaningar enbart till användare som är registrerade i MFA. Endast använda FALSKT vid testning eller i produktionsmiljöer under en tid för onboarding.

### <a name="acquire-azure-active-directory-guid-id"></a>Hämta Azure Active Directory-GUID-ID

Som en del av konfigurationen av NPS-tillägget måste du ange administratörsautentiseringsuppgifter och Azure Active Directory-ID för Azure AD-klienten. Stegen nedan visar hur du kan hämta klient-ID.

1. Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com) som global administratör för Azure-klient.
2. I det vänstra navigeringsfönstret klickar du på den **Azure Active Directory** ikon.
3. Klicka på **Egenskaper**.
4. Katalog-ID till Urklipp och markera den **kopiera** ikon.
 
 ![Katalog-ID](./media/nps-extension-vpn/image35.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägg
NPS-tillägget måste vara installerad på en server som har serverrollen för nätverksprincip och Access Services (NPS) installerad och som fungerar som RADIUS-server i din design. Installera inte tillägget NPS på fjärrservern skrivbordet.

1. Hämta NPS-tillägget från [https://aka.ms/npsmfa](https://aka.ms/npsmfa). 
2. Kopiera den körbara filen för installationsprogrammet (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
3. Dubbelklicka på NPS-servern **NpsExtnForAzureMfaInstaller.exe**. Om du uppmanas, klickar du på **kör**.
4. Granska licensvillkoren för programvara i dialogrutan NPS-tillägget för Azure MFA installation Kontrollera **jag godkänner licensvillkoren och villkor**, och klicka på **installera**.

 ![NPS-tillägg](./media/nps-extension-vpn/image36.png)
 
5. Klicka på dialogrutan NPS-tillägget för Azure MFA installation **Stäng**.  

 ![Installationen lyckades](./media/nps-extension-vpn/image37.png) 
 
### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript
För att säkerställa säker kommunikation och säkerhet, måste du konfigurera certifikat för användning av NPS-tillägget. NPS-komponenter är ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS. 

Skriptet utförs följande åtgärder:

* Skapar ett självsignerat certifikat
* Associerar offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD
* Lagrar cert i lokala datorns Arkiv
* Beviljar åtkomst till certifikatets privata nyckel till nätverksanvändaren
* NPS-tjänsten har startats om

Om du vill använda dina egna certifikat måste du koppla den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD och så vidare.
Om du vill använda skriptet ger tillägget dina administrativa autentiseringsuppgifter för Azure Active Directory och Azure Active Directory-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerar NPS-tillägget.

1. Öppna en administrativ Windows PowerShell-Kommandotolken.
2. I PowerShell-Kommandotolken skriver _cd ”c:\Program Files\Microsoft\AzureMfa\Config'_, och tryck på **RETUR**.
3. Typen _.\AzureMfsNpsExtnConfigSetup.ps1_, och tryck på **RETUR**. 
 * Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om det inte är installerat installeras skriptet modulen.
 
 ![PowerShell](./media/nps-extension-vpn/image38.png)
 
4. När skriptet har kontrollerat installationen av PowerShell-modulen, visas dialogrutan för Azure Active Directory PowerShell-modulen. I dialogrutan, ange autentiseringsuppgifter för Azure AD-administratör och lösenord och klicka på **logga in**. 
 
 ![PowerShell-logga In](./media/nps-extension-vpn/image39.png)
 
5. När du uppmanas, klistra in klient-ID som du kopierade tidigare till Urklipp och tryck på **RETUR**. 

 ![Klient-ID:t](./media/nps-extension-vpn/image40.png)

6. Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Utdata är som på bilden nedan.

 ![Automatiskt signerat certifikat](./media/nps-extension-vpn/image41.png)

7. Starta om servern.
 
### <a name="verify-configuration"></a>Verifiera konfigurationen
Om du vill verifiera konfigurationen måste du upprätta en ny VPN-anslutning med VPN-servern. När du har angett dina autentiseringsuppgifter för primär autentisering, väntar VPN-anslutningen på den sekundära autentiseringen ska lyckas innan anslutningen har upprättats, enligt nedan. 

 ![Verifiera konfigurationen](./media/nps-extension-vpn/image42.png)

Om du har autentiseras med sekundära verifieringsmetod som du tidigare har konfigurerat i Azure MFA är du ansluten till resursen. Om den sekundära autentiseringen inte lyckas nekas åtkomst till resursen. 

I exemplet nedan används Authenticator-appen på en Windows phone för att tillhandahålla den sekundära autentiseringen.

 ![Verifiera konto](./media/nps-extension-vpn/image43.png)

När du har autentiserats med metoden sekundär, beviljas åtkomst till den virtuella porten på VPN-servern. Eftersom du inte behövs för att använda en sekundär autentiseringsmetod med en mobil app på en betrodd enhet loggen i processen är dock säkrare än att det skulle använda bara ett användarnamn / lösenord kombination.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa loggarna i Loggboken för lyckade inloggningshändelser
Om du vill visa lyckade inloggningshändelser loggas i Windows Loggboken, kan du utfärda följande Windows PowerShell-kommando för att fråga Windows säkerhetsloggen på NPS-servern.

Om du vill fråga lyckade inloggningshändelser i visningsprogrammet säkerhetshändelser, använder du följande kommando,
* _Get-WinEvent - loggnamn säkerhet_ | där {$_.ID - eq '6272'} | FL 

 ![Loggboken för säkerhet](./media/nps-extension-vpn/image44.png)
 
Du kan också visa säkerhetsloggen eller anpassad vy nätverksprincip och åtkomsttjänster enligt nedan:

 ![Princip för nätverksåtkomst](./media/nps-extension-vpn/image45.png)

På den server där du installerade NPS-tillägget för Azure MFA, hittar du loggarna i Loggboken program specifika tillägget på **program och tjänster Logs\Microsoft\AzureMfa**. 

* _Get-WinEvent - loggnamn säkerhet_ | där {$_.ID - eq '6272'} | FL

 ![Antal händelser](./media/nps-extension-vpn/image46.png)

## <a name="troubleshoot-guide"></a>Felsöka programguiden
Om konfigurationen inte fungerar som förväntat, är en bra plats att börja felsöka att kontrollera att användaren är konfigurerad för att använda Azure MFA. Behörighet att ansluta till [https://portal.azure.com](https://portal.azure.com). Om de tillfrågas om sekundära autentiseringen och kan autentisera, vet du att en felaktig konfiguration av Azure MFA.

Om Azure MFA fungerar för användare, bör du granska relevant händelseloggar. Dessa inkluderar säkerhetshändelse, Gateway fungerar och Azure MFA-loggar som beskrivs i föregående avsnitt. 

Nedan visas ett exempel på utdata för säkerhetsloggen visar misslyckade inloggningsförsök-händelser (händelse-ID 6273):

 ![Säkerhetsloggen](./media/nps-extension-vpn/image47.png)

Nedan visas en relaterad händelse från AzureMFA loggar:

 ![Azure MFA-loggar](./media/nps-extension-vpn/image48.png)

Att utföra avancerad felsökning av alternativ, kontakta NPS format loggfiler där NPS-tjänsten är installerad. Dessa loggfiler skapas i _%SystemRoot%\System32\Logs_ mapp som kommaavgränsad textfiler. En beskrivning av dessa loggfiler finns i avsnittet [tolka NPS loggfilerna](https://technet.microsoft.com/library/cc771748.aspx). 

Posterna i dessa loggfiler är svåra att tolka utan att importera dem till ett kalkylblad eller en databas. Du hittar ett antal IAS-Parser online som hjälper dig att tolka loggfilerna. Nedan visas utdata i en sådan nedladdningsbara [shareware-programmet](http://www.deepsoftware.com/iasviewer): 

 ![Shareware-programmet](./media/nps-extension-vpn/image49.png)

Slutligen för ytterligare felsökning av alternativ, kan du använda en protokollanalysator, till exempel Wireshark eller [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). Följande bild från Wireshark visar RADIUS-meddelanden mellan VPN-servern och NPS-servern.

 ![Microsoft Message Analyzer](./media/nps-extension-vpn/image50.png)

Mer information finns i [integrera din befintliga infrastruktur för NPS med Azure Multi-Factor Authentication](multi-factor-authentication-nps-extension.md).  

## <a name="next-steps"></a>Nästa steg
[Skaffa Azure Multi-Factor Authentication](multi-factor-authentication-versions-plans.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](multi-factor-authentication-get-started-server-rdg.md)

[Integrera dina lokala kataloger med Azure Active Directory](../active-directory/connect/active-directory-aadconnect.md)

