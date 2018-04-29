---
title: Remote Desktop Gateway-integrering med Azure MFA NPS-tillägg | Microsoft Docs
description: Den här artikeln beskrivs integrera din infrastruktur för Remote Desktop Gateway med Azure MFA med hjälp av Server (NPS)-tillägget för Microsoft Azure.
services: active-directory
keywords: Azure MFA integrera servern för fjärrskrivbordsgateway, Azure Active Directory, NPS-tillägg
documentationcenter: ''
author: MicrosoftGuyJFlo
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: joflore
ms.reviewer: richagi
ms.custom: it-pro
ms.openlocfilehash: 0c050ee237650be7d43be2454a2bc3c07f096b8c
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
#  <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera din infrastruktur för Remote Desktop Gateway med tillägget Server (NPS) och Azure AD

Den här artikeln innehåller information för att integrera din infrastruktur för Remote Desktop Gateway med Azure Multi-Factor Authentication (MFA) med hjälp av Server (NPS)-tillägget för Microsoft Azure. 

Server (NPS)-tillägg för Azure kan kunderna att skydda RADIUS Remote Authentication Dial-In User Service ()-klientautentisering med hjälp av Azure är molnbaserad [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Den här lösningen ger tvåstegsverifiering för att lägga till ett andra säkerhetslager till användarinloggningar och transaktioner.

Den här artikeln innehåller stegvisa instruktioner för integrering av NPS-infrastrukturen med Azure MFA med hjälp av NPS-tillägget för Azure. Detta gör att säkra verifiering för användare som försöker logga in på en server för fjärrskrivbordsgateway. 

Nätverksprincip och Access Services (NPS) gör att organisationer kan göra följande:
* Definiera centrala platser för hantering och kontroll av begäranden för nätverk genom att ange vem som kan ansluta vilka tider på dagen anslutningar är tillåtna, längden av anslutningar och säkerhetsnivån som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje server för VPN- eller Gateway för fjärrskrivbord (RD) kan dessa principer anges en gång på en central plats. RADIUS-protokollet ger centraliserad autentisering, auktorisering och redovisning. 
* Skapa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.
* Ger möjlighet att genomdriva autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.    

Normalt principer organisationer Använd NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN. Men använda många organisationer också NPS för att förenkla och centralisera hanteringen av Anslutningsutjämning för Fjärrskrivbord anslutning auktoriseringsprinciper (RD cap). 

Organisationer kan också integrera NPS med Azure MFA för att förbättra säkerheten och ger en hög nivå av kompatibilitet. Detta säkerställer att användare upprättar tvåstegsverifiering att logga in på servern för fjärrskrivbordsgateway. De måste ange sina kombinationen av användarnamn/lösenord tillsammans med information som användaren har i sin kontroll för användare att få åtkomst. Den här informationen måste betrodda och inte enkelt dubbleras, till exempel ett mobiltelefonnummer, fast telefon nummer, program på en mobil enhet och så vidare.

Tidigare tillgänglighet för NPS-tillägget för Azure tvungen kunder som vill implementera tvåstegsverifiering för integrerad NPS och Azure MFA-miljöer att konfigurera och underhålla en separat MFA-Server i den lokala miljön enligt beskrivningen i [ Remote Desktop Gateway och Azure Multi-Factor Authentication-servern med hjälp av RADIUS](howto-mfaserver-nps-rdg.md).

Tillgängligheten för NPS-tillägget för Azure nu ger organisationer möjlighet att distribuera en lokalt baserad MFA-lösning eller en molnbaserad lösning för MFA till säker autentisering för RADIUS-klienten.

## <a name="authentication-flow"></a>Autentiseringsflöde

För användare att få åtkomst till nätverksresurser via en Gateway för fjärrskrivbord måste de uppfylla de krav som anges i en RD auktoriseringsprincip för klientanslutning (RD CAP) och en värdserver för resursen auktoriseringsprincip (Värddator FJÄRRSKRIVBORDSANSLUTNING). Auktoriseringsprinciper för fjärrskrivbordsanslutning ange vem som har behörighet att ansluta till RD Gateway. Auktoriseringsprinciper för fjärrskrivbordsresurser ange nätverksresurser, till exempel fjärrskrivbord eller Fjärrprogram som användaren tillåts att ansluta till via fjärrskrivbordsgateway. 

En fjärrskrivbordsgateway kan konfigureras för att använda en central principarkiv för auktoriseringsprinciper för fjärrskrivbordsanslutning. Auktoriseringsprinciper för fjärrskrivbordsresurser kan inte använda en central princip när de bearbetas på RD Gateway. Ett exempel på en RD Gateway som konfigurerats för att använda en central principarkiv för auktoriseringsprinciper för fjärrskrivbordsanslutning är en RADIUS-klient till en annan NPS-server som fungerar som centrala principarkivet.

När NPS-tillägget för Azure är integrerad med NPS och fjärrskrivbordsgateway är flödet för autentiseringen följande:

1. Remote Desktop Gateway-servern tar emot en autentiseringsbegäran från en användare av fjärrskrivbord kan ansluta till en resurs, till exempel en fjärrskrivbordssession. Fungerar som en RADIUS-klient, server för fjärrskrivbordsgateway konverterar begäran till en RADIUS-åtkomstbegäran och skickar meddelandet till RADIUS (NPS)-servern där NPS-tillägg har installerats. 
2. Kombinationen av användarnamn och lösenord har verifierats i Active Directory och användaren har autentiserats.
3. Om alla villkor som anges i NPS-anslutningsbegäran och de nätverksprinciper uppfylls (till exempel tidpunkt eller grupp medlemskap begränsningar), NPS-tillägget utlöser en begäran om sekundära autentiseringen med Azure MFA. 
4. Azure MFA kommunicerar med Azure AD, hämtar användarens information och utför sekundära autentiseringen genom att använda metoden konfigureras av användaren (textmeddelande, mobilapp och så vidare). 
5. Vid framgången för MFA-kontrollen har kommunicerar Azure MFA resultatet till NPS-tillägget.
6. NPS-servern där tillägget installeras, skickar meddelandet RADIUS nekad för auktoriseringsprincip för Fjärrskrivbordsanslutning principen till servern för fjärrskrivbordsgateway.
7. Användaren beviljas åtkomst till den begärda nätverksresursen via RD Gateway.

## <a name="prerequisites"></a>Förutsättningar
Det här avsnittet beskrivs förutsättningarna som krävs innan du integrera Azure MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats.  

* Remote Desktop Services (RDS)-infrastruktur
* Azure MFA-licens
* Windows Server-programvara
* Nätverksprincip och Access Services (NPS)
* Azure Active Directory är synkroniserade med lokala Active Directory
* Azure Active Directory-GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Remote Desktop Services (RDS)-infrastruktur
Du måste ha en fungerande Remote Desktop Services (RDS)-infrastruktur på plats. Om du inte gör det kommer du snabbt skapa infrastrukturen i Azure med hjälp av följande Snabbstart mall: [skapa Remote Desktop Sessionssamling distribution](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Om du vill skapa en lokal RDS infrastruktur snabbt testsyfte manuellt, följer du stegen för att distribuera en. 
**Lär dig mer**: [distribuera RDS med Azure Snabbstart](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) och [grundläggande RDS-distribution för infrastruktur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Azure MFA-licens
Krävs är en licens för Azure MFA som finns tillgänglig via en Azure AD Premium, Enterprise Mobility plus Security (EMS) eller en MFA-prenumeration. Förbrukningsbaserad licenser för Azure MFA, enligt användare eller per autentisering licenser, är inte kompatibla med NPS-tillägget. Mer information finns i [hur du hämtar Azure Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan använda du en utvärderingsprenumeration. 

### <a name="windows-server-software"></a>Windows Server-programvara
NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i det här avsnittet utfördes med Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Nätverksprincip och Access Services (NPS)
NPS-rolltjänsten tillhandahåller RADIUS-servern och klienten funktioner samt nätverksprincip Access-tjänsten för hälsotillstånd. Den här rollen måste installeras på minst två datorer i din infrastruktur: The Remote Desktop Gateway och en annan medlemsserver eller domänkontrollant. Som standard är rollen som redan finns på den dator som har konfigurerats som server för fjärrskrivbordsgateway.  Du måste sedan också installera NPS-rollen på minst på en annan dator, till exempel en domänkontrollant eller medlemsserver.

Information om hur du installerar NPS-rollen service i Windows Server 2012 eller tidigare, se [installera en NAP-hälsoprincipserver](https://technet.microsoft.com/library/dd296890.aspx). En beskrivning av bästa praxis för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant finns [bästa praxis för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory är synkroniserade med lokala Active Directory
Om du vill använda NPS-tillägget lokala användare synkroniseras med Azure AD och aktiverats för MFA. Det här avsnittet förutsätter att lokala användare är synkroniserade med Azure AD med hjälp av AD Connect. Information om Azure AD connect, se [integrera dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md). 

### <a name="azure-active-directory-guid-id"></a>Azure Active Directory-GUID-ID
Om du vill installera NPS-tillägget som du behöver veta GUID för Azure AD. Instruktioner för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication 
Det här avsnittet innehåller instruktioner för att integrera Azure MFA med fjärrskrivbordsgateway. Som administratör kan konfigurera du Azure MFA-tjänsten innan användare kan registrera sina enheter på flera plan eller program.

Följ stegen i [komma igång med Azure Multi-Factor Authentication i molnet](howto-mfa-getstarted.md) du aktiverar Multifaktorautentisering för dina Azure AD-användare. 

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering
När ett konto har aktiverats för Multifaktorautentisering, kan du logga in till resurser som omfattas av principen MFA förrän du har konfigurerat en betrodd enhet för den andra faktorn autentisering och har autentiseras med tvåstegsverifiering.

Följ stegen i [vad Azure Multi-Factor Authentication innebär för mig?](./../../multi-factor-authentication/end-user/multi-factor-authentication-end-user.md) att förstå och konfigurera dina enheter för MFA med ditt användarkonto.

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägget
Det här avsnittet innehåller instruktioner för att konfigurera RDS infrastruktur att använda Azure MFA för klientautentisering med fjärrskrivbordsgateway.

### <a name="acquire-azure-active-directory-guid-id"></a>Hämta Azure Active Directory-GUID-ID

Som en del av konfigurationen av filnamnstillägget NPS måste ange autentiseringsuppgifter som administratör och Azure AD-ID för Azure AD-klienten. Följande steg visar hur du registrerar klient-ID.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klient.
2. I det vänstra navigeringsfönstret, Välj den **Azure Active Directory** ikon.
3. Välj **egenskaper**.
4. I bladet egenskaper bredvid katalog-ID, klickar du på den **kopiera** ikonen som visas nedan, för att kopiera det ID: T till Urklipp.

 ![Egenskaper](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägg
Installera NPS-tillägg på en server som har nätverksprinciper och Access Services (NPS)-rollen installerad. Detta fungerar som RADIUS-server för din design. 

>[!Important]
> Var noga med att du inte installerar NPS-tillägget på servern för fjärrskrivbordsgateway.
> 

1. Hämta den [NPS tillägget](https://aka.ms/npsmfa). 
2. Kopiera den körbara filen för installationsprogrammet (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
3. Dubbelklicka på NPS-servern **NpsExtnForAzureMfaInstaller.exe**. Om du uppmanas, klickar du på **kör**.
4. Granska licensvillkoren för programvara i dialogrutan NPS-tillägget för Azure MFA installation Kontrollera **jag godkänner licensvillkoren och villkor**, och klicka på **installera**.
 
  ![Azure MFA-inställningar](./media/howto-mfa-nps-extension-rdg/image2.png)

5. Klicka på dialogrutan NPS-tillägget för Azure MFA installation **Stäng**. 

  ![NPS-tillägget för Azure MFA](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript
Därefter måste du konfigurera certifikat för användning av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenter är ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS. 

Skriptet utförs följande åtgärder:

* Skapar ett självsignerat certifikat
* Associerar offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD
* Lagrar cert i lokala datorns Arkiv
* Beviljar åtkomst till certifikatets privata nyckel till nätverksanvändaren
* NPS-tjänsten har startats om

Om du vill använda dina egna certifikat måste du koppla den offentliga nyckeln för certifikatet till tjänstens huvudnamn på Azure AD och så vidare.

Om du vill använda skriptet, ger du tillägget med dina administratörsautentiseringsuppgifter för Azure AD och Azure AD-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du har installerat tillägget NPS. Gör något av följande:

1. Öppna en administrativ Windows PowerShell-Kommandotolken.
2. I PowerShell-Kommandotolken skriver **cd ”c:\Program Files\Microsoft\AzureMfa\Config'**, och tryck på **RETUR**.
3. Typen _.\AzureMfsNpsExtnConfigSetup.ps1_, och tryck på **RETUR**. Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om inte har installerats, installeras modulen av skriptet.

  ![Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
4. När skriptet har kontrollerat installationen av PowerShell-modulen, visas dialogrutan för Azure Active Directory PowerShell-modulen. I dialogrutan, ange autentiseringsuppgifter för Azure AD-administratör och lösenord och klicka på **logga In**.

  ![Öppna Powershell-konto](./media/howto-mfa-nps-extension-rdg/image5.png)

5. När du uppmanas, klistra in klient-ID som du kopierade tidigare till Urklipp och tryck på **RETUR**.

  ![Ange klient-ID](./media/howto-mfa-nps-extension-rdg/image6.png)

6. Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Utdata ska vara som på bilden nedan.

  ![Självsignerat certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på servern för fjärrskrivbordsgateway
I det här avsnittet konfigurerar du auktoriseringsprinciper för Remote Desktop Gateway-anslutningen och andra inställningar för RADIUS.

Autentiseringsflödet kräver att RADIUS-meddelanden utväxlas mellan fjärrskrivbordsgateway och NPS-servern där NPS-servern är installerad. Det innebär att måste du konfigurera RADIUS-klientinställningarna på både servern för fjärrskrivbordsgateway och NPS-servern där NPS-tillägg har installerats. 

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera servern för fjärrskrivbordsgateway anslutning auktoriseringsprinciper för att använda centrala store
Auktoriseringsprinciper för fjärrskrivbordsanslutning (RD cap) Ange krav för att ansluta till en server för fjärrskrivbordsgateway. Auktoriseringsprinciper för fjärrskrivbordsanslutning lagras lokalt (standard) eller de kan lagras i en central auktoriseringsprincip för fjärrskrivbordsanslutning som kör NPS. Du måste ange användning av en central lagring för att konfigurera Azure MFA-integrering med Fjärrskrivbordstjänster.

1. Öppna på servern för fjärrskrivbordsgateway **Serverhanteraren**. 
2. Klicka på menyn **verktyg**, peka på **Fjärrskrivbordstjänster**, och klicka sedan på **hanteraren för fjärrskrivbordsgateway**.

  ![Fjärrskrivbordstjänster](./media/howto-mfa-nps-extension-rdg/image8.png)

3. RD Gateway Manager, högerklicka på  **\[servernamn\] (lokal)**, och klicka på **egenskaper**.

  ![Servernamn](./media/howto-mfa-nps-extension-rdg/image9.png)

4. I dialogrutan Egenskaper väljer du den **auktoriseringsprincip för fjärrskrivbordsanslutning** fliken.
5. Välj fliken auktoriseringsprincip för fjärrskrivbordsanslutning **Central Server som kör NPS**. 
6. I den **ange namn eller IP-adress för servern som kör NPS** skriver du IP-adress eller namnet på den server där du installerade NPS-tillägget.

  ![Ange namn eller IP-adress](./media/howto-mfa-nps-extension-rdg/image10.png)
  
7. Klicka på **Lägg till**.
8. I den **delad hemlighet** dialogrutan, ange en delad hemlighet och klicka sedan på **OK**. Se till att du registrerar den här delad hemlighet och lagra posten på ett säkert sätt.

 >[!NOTE]
 >Delad hemlighet används för att upprätta förtroende mellan RADIUS-servrar och klienter. Skapa en långa och komplexa hemlighet.
 >

 ![Delad hemlighet](./media/howto-mfa-nps-extension-rdg/image11.png)

9. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS-timeout-värde på Remote Desktop Gateway NPS
Om du vill se till att det är dags att verifiera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, svar och svara på RADIUS-meddelanden är det nödvändigt att justera RADIUS timeout-värdet.

1. Öppna Serverhanteraren på servern för fjärrskrivbordsgateway. Klicka på menyn **verktyg**, och klicka sedan på **Network Policy Server**. 
2. I den **NPS (lokal)** konsolen, expandera **RADIUS-klienter och servrar**, och välj **fjärranslutna RADIUS-Server**.

 ![Fjärr-RADIUS-Server](./media/howto-mfa-nps-extension-rdg/image12.png)

3. I informationsfönstret dubbelklickar du på **TS GATEWAY-SERVERGRUPP**.

 >[!NOTE]
 >RADIUS-Server gruppen skapades när du har konfigurerat den centrala servern för NPS-policys. RD Gateway vidarebefordrar RADIUS till denna server eller en grupp av servrar, om mer än en i gruppen.
 >

4. I den **gruppegenskaper för TS GATEWAY-SERVER** dialogrutan Välj IP-adressen eller namnet på den NPS-server som du har konfigurerat för att lagra auktoriseringsprinciper för fjärrskrivbordsanslutning och klicka sedan på **redigera**. 

 ![TS Gateway-servergrupp](./media/howto-mfa-nps-extension-rdg/image13.png)

5. I den **redigera RADIUS-Server** dialogrutan markerar du den **belastningsutjämning** fliken.
6. I den **belastningsutjämning** fliken den **antal sekunder utan svar innan begäran anses som utelämnats** fältet, ändrar du standardvärdet 3 till ett värde mellan 30 och 60 sekunder.
7. I den **antalet sekunder mellan begäranden när servern anses vara otillgänglig** fältet, ändrar du standardvärdet på 30 sekunder till ett värde som är lika med eller större än värdet du angav i föregående steg.

 ![Redigera RADIUS-Server](./media/howto-mfa-nps-extension-rdg/image14.png)

8.  Klicka på **OK** två gånger för att stänga dialogrutorna.

### <a name="verify-connection-request-policies"></a>Kontrollera principer för anslutningsbegäran 
När du konfigurerar Fjärrskrivbordsgatewayen att använda en central princip store för anslutningen auktoriseringsprinciper konfigureras RD Gateway för att vidarebefordra CAP-begäranden till NPS-servern. NPS-servern med Azure MFA-tillägget installerat, bearbetar RADIUS-förfrågan. Följande steg visar hur du verifierar standardprincipen. 

1. Expandera på RD Gateway i konsolen NPS (lokal) **principer**, och välj **principer för anslutningsbegäran**.
2. Högerklicka på **ansluta principer för anslutningsbegäran**, och dubbelklicka på **AUKTORISERINGSPRINCIP för TS GATEWAY**.
3. I den **AUKTORISERINGSPRINCIP för TS GATEWAY egenskaper** dialogrutan klickar du på den **inställningar** fliken.
4. På **inställningar** klickar du på fliken under Vidarebefordra anslutningsbegäran **autentisering**. RADIUS-klienten är konfigurerad för att vidarebefordra begäranden för autentisering.

 ![Autentiseringsinställningar](./media/howto-mfa-nps-extension-rdg/image15.png)
 
5. Klicka på **Avbryt**. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på den server där tillägget NPS är installerad
NPS-servern där NPS-tillägget installeras måste vara kan utbyta RADIUS-meddelanden med NPS-server på servern för fjärrskrivbordsgateway. Om du vill aktivera den här exchange-meddelande måste du konfigurera NPS-komponenter på den server där tillägget NPS-tjänsten är installerad. 

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory
NPS-servern måste registreras i Active Directory för att fungera i det här scenariot.

1. Öppna på NPS-servern **Serverhanteraren**.
2. I Serverhanteraren klickar du på **verktyg**, och klicka sedan på **Network Policy Server**. 
3. I NPS-konsolträdet högerklickar du på **NPS (lokal)**, och klicka sedan på **registrera servern i Active Directory**. 
4. Klicka på **OK** två gånger.

 ![Registrera servern i AD](./media/howto-mfa-nps-extension-rdg/image16.png)

5. Lämna konsolen öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klient 
Remote Desktop Gateway måste konfigureras som en RADIUS-klient till NPS-servern. 

1. På NPS-servern där tillägget NPS installeras i den **NPS (lokal)** konsolen och högerklicka på **RADIUS-klienter** och på **ny**.

 ![Ny RADIUS-klienter](./media/howto-mfa-nps-extension-rdg/image17.png)

2. I den **ny RADIUS-klient** dialogrutan Ange ett eget namn som _Gateway_, och IP-adress eller DNS-namnet på servern för fjärrskrivbordsgateway. 
3. I den **delad hemlighet** och **Bekräfta delad hemlighet** ange samma hemlighet som du använde tidigare.

 ![Namn och adress](./media/howto-mfa-nps-extension-rdg/image18.png)

4. Klicka på **OK** att stänga dialogrutan Ny RADIUS-klient.

### <a name="configure-network-policy"></a>Konfigurera principen för nätverk
Kom ihåg att NPS-servern med Azure MFA-tillägget är avsedda centrala principarkivet för anslutningen auktorisering princip (CAP). Du måste därför att implementera ett tak för NPS-servern att godkänna giltiga anslutningar begäranden.  

1. Expandera i konsolen NPS (lokal) **principer**, och klicka på **nätverksprinciper**.
2. Högerklicka på **anslutningar till andra servrar**, och klicka på **duplicera princip**. 

 ![Duplicera princip](./media/howto-mfa-nps-extension-rdg/image19.png)

3. Högerklicka på **kopia av anslutningar till andra servrar**, och klicka på **egenskaper**.

 ![Egenskaper för nätverk](./media/howto-mfa-nps-extension-rdg/image20.png)

4. I den **kopia av anslutningar till andra servrar** i dialogrutan **principnamn**, ange ett lämpligt namn som _RDG_CAP_. Kontrollera **principen är aktiverad**, och välj **bevilja åtkomst**. Valfritt: i **typ av nätverksåtkomstserver**väljer **fjärrskrivbordsgateway**, eller så kan du lämna den som **Ospecificerad**.

 ![Kopia av anslutningar](./media/howto-mfa-nps-extension-rdg/image21.png)

5.  Klicka på den **begränsningar** och kontrollera **tillåta klienter att ansluta utan att förhandla om autentiseringsmetod**.

 ![Tillåt klienter att ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

6. Du kan också klicka på den **villkor** fliken och Lägg till villkor som måste uppfyllas för anslutning till behöriga, till exempel medlemskap i en specifik Windows-grupp.

 ![Villkor](./media/howto-mfa-nps-extension-rdg/image23.png)

7. Klicka på **OK**. När du uppmanas att visa det aktuella hjälpavsnittet, klickar du på **nr**.
8. Kontrollera att den nya principen är överst i listan att principen är aktiverad, och att den ger åtkomst.

 ![Nätverksprinciper](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfigurationen
Du måste logga in på servern för fjärrskrivbordsgateway med en lämplig RDP-klient för att kontrollera konfigurationen. Se till att använda ett konto som tillåts av din anslutning auktoriseringsprinciper och är aktiverat för Azure MFA. 

Som visas i bilden nedan kan du använda den **webbåtkomst för fjärrskrivbord** sidan.

![Webbåtkomst till fjärrskrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering, visar dialogrutan Remote Desktop ansluta statusen initierar anslutning till enligt nedan. 

Om du lyckas autentisera med den sekundära autentiseringsmetoden som du tidigare har konfigurerat i Azure MFA är du ansluten till resursen. Om den sekundära autentiseringen inte lyckas nekas åtkomst till resursen. 

![Initiera anslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows phone för att tillhandahålla den sekundära autentiseringen.

![Konton](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserats med hjälp av den sekundära autentiseringsmetoden, är du inloggad på servern för fjärrskrivbordsgateway som vanligt. Men eftersom du måste använda en sekundär autentiseringsmetod med en mobil app på en betrodd enhet, logga in på processen är säkrare än den annars skulle vara.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa loggarna i Loggboken för lyckade inloggningshändelser
Om du vill visa händelser för lyckad inloggning i Windows Loggboken, kan du utfärda följande Windows PowerShell-kommando för att fråga loggar Windows Terminal Services och Windows-säkerhet.

Fråga lyckad inloggning-händelser i Gateway-arbetsloggarna _(loggboken\program och tjänster Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, Använd följande PowerShell-kommandon:

* _Get-WinEvent - loggnamn Microsoft-Windows-TerminalServices-Gateway/Operational_ | där {$_.ID - eq '300'} | FL 
* Detta kommando visar Windows-händelser som visar användaren uppfyllda resurskraven auktorisering princip (RD RAP) och har beviljats åtkomst.

![Visa Loggboken](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent - loggnamn Microsoft-Windows-TerminalServices-Gateway/Operational_ | där {$_.ID - eq ”200”} | FL
* Detta kommando visar de händelser som visas när användaren uppfyller kraven för anslutningen auktorisering princip.

![Anslutningsverifiering](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa den här loggen och filter på händelse-ID, 300 och 200. Om du vill fråga lyckade inloggningshändelser i visningsprogrammet säkerhetshändelser, använder du följande kommando:

* _Get-WinEvent - loggnamn säkerhet_ | där {$_.ID - eq '6272'} | FL 
* Det här kommandot kan köras på den centrala NPS eller RD Gateway-servern. 

![Lyckade inloggningshändelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också visa säkerhetsloggen eller anpassad vy nätverksprincip och åtkomsttjänster enligt nedan:

![Nätverksprincip och åtkomsttjänster](./media/howto-mfa-nps-extension-rdg/image31.png)

På den server där du installerade NPS-tillägget för Azure MFA, hittar du loggarna i Loggboken program specifika tillägget på _program och tjänster Logs\Microsoft\AzureMfa_. 

![Loggar i Loggboken program](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Felsöka programguiden

Om konfigurationen inte fungerar som förväntat, är den första platsen att börja felsöka att kontrollera att användaren är konfigurerad för att använda Azure MFA. Behörighet att ansluta till den [Azure-portalen](https://portal.azure.com). Om användaren uppmanas att sekundära kontroll och kan autentisera, vet du att en felaktig konfiguration av Azure MFA.

Om Azure MFA fungerar för användare, bör du granska relevant händelseloggar. Dessa inkluderar säkerhetshändelse, Gateway fungerar och Azure MFA-loggar som beskrivs i föregående avsnitt. 

Nedan visas ett exempel på utdata för säkerhetsloggen visar misslyckade inloggningsförsök-händelser (händelse-ID 6273).

![Misslyckade inloggningsförsök händelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterad händelse från AzureMFA loggar:

![Azure MFA-logg](./media/howto-mfa-nps-extension-rdg/image34.png)

Att utföra avancerad felsökning av alternativ, kontakta NPS format loggfiler där NPS-tjänsten är installerad. Dessa loggfiler skapas i _%SystemRoot%\System32\Logs_ mapp som kommaavgränsad textfiler. 

En beskrivning av dessa loggfiler finns i avsnittet [tolka NPS loggfilerna](https://technet.microsoft.com/library/cc771748.aspx). Posterna i dessa loggfiler kan vara svårt att tolka utan att importera dem till ett kalkylblad eller en databas. Du hittar flera IAS Parser online som hjälper dig att tolka loggfilerna. 

Bilden nedan visar utdata i en sådan nedladdningsbara [shareware-programmet](http://www.deepsoftware.com/iasviewer). 

![Shareware-app](./media/howto-mfa-nps-extension-rdg/image35.png)

Slutligen för ytterligare felsökning av alternativ, kan du använda en protokollanalysator sådana [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

Bilden nedan från Microsoft Message Analyzer visar nätverkstrafik filtreras i RADIUS-protokollet som innehåller användarnamnet **Contoso\AliceC**.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg
[Skaffa Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../connect/active-directory-aadconnect.md)
