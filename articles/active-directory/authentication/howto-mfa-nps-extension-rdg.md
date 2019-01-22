---
title: Remote Desktop Gateway-integrering med Azure MFA NPS-tillägget | Microsoft Docs
description: Integrera din infrastruktur för Remote Desktop Gateway med Azure MFA med hjälp av NPS-tillägget för Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.component: authentication
ms.topic: conceptual
ms.date: 12/03/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.openlocfilehash: 853f8a499bfed461f75a79ff18f878f37d109e81
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/22/2019
ms.locfileid: "54425364"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera fjärrskrivbordsgateway infrastruktur med Network Policy Server (NPS)-tillägget och Azure AD

Den här artikeln innehåller information för att integrera din infrastruktur för Remote Desktop Gateway med Azure Multi-Factor Authentication (MFA) med hjälp av nätverksprincipserver (NPS)-tillägget för Microsoft Azure.

Nätverksprincipserver (NPS)-tillägget för Azure kan kunderna att skydda Remote Authentication Dial-In User Service (RADIUS) klientautentisering med hjälp av Azure är Microsofts molnbaserade [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Den här lösningen tillhandahåller tvåstegsverifiering för att lägga till ett andra säkerhetslager till användarinloggningar och transaktioner.

Den här artikeln innehåller stegvisa instruktioner för att integrera NPS-infrastruktur med Azure MFA med NPS-tillägget för Azure. På så sätt kan säker verifiering för användare som försöker logga in på en fjärrskrivbordsgateway.

> [!NOTE]
> Den här artikeln bör inte användas tillsammans med MFA Server-distributioner och bör endast användas med Azure MFA (molnbaserade) distributioner.

Nätverksprinciper och Access Services (NPS) gör att organisationer kan du göra följande:

* Definiera centrala platser för hantering och kontroll över nätverksbegäranden genom att ange vem som kan ansluta, vilka tider på dagen anslutningar tillåts, varaktigheten för anslutningar och säkerhetsnivån som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje server för VPN eller Remote Desktop (Rdg) kan anges dessa principer en gång på en central plats. RADIUS-protokollet ger centraliserad autentisering, auktorisering och redovisning. 
* Fastställa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.
* Skapar du ett sätt att tillämpa autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.

Normalt principer organisationer Använd NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN. Men använda många organisationer även NPS för att förenkla och centralisera hanteringen av auktoriseringsprinciper för RD Desktop Connection (RD cap).

Organisationer kan även integrera NPS med Azure MFA för att förbättra säkerheten och ge en hög nivå av efterlevnad. Detta säkerställer att användare upprättar tvåstegsverifiering att logga in på den Remote Desktop Gateway. De måste ange sina användarnamn och lösenordet tillsammans med information som användaren har i deras kontroll för användare som ska få åtkomstbehörighet. Den här informationen måste vara betrodd och inte enkelt dupliceras, till exempel ett mobiltelefonnummer, både fasta telefoner tal, program på en mobil enhet och så vidare. Mer information om autentiseringsmetoder som stöds finns i avsnittet [ta reda på vilka autentiseringsmetoder som användarna kan använda](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Före tillgängligheten för NPS-tillägget för Azure behövde-kunder som vill implementera tvåstegsverifiering för integrerad NPS och Azure MFA-miljöer konfigurerar och underhåller en separat MFA-Server i en lokal miljö enligt beskrivningen i [ Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md).

Tillgängligheten för NPS-tillägget för Azure erbjuder organisationer nu möjlighet att distribuera en lokal baserat MFA-lösning eller en molnbaserad MFA-lösning till säker autentisering för RADIUS-klienten.

## <a name="authentication-flow"></a>Autentiseringsflödet

För att användarna ska ha åtkomst till nätverksresurser via en fjärrskrivbordsgateway måste de uppfyller de villkor som anges i en RD auktoriseringsprincip för klientanslutning (RD CAP) och en RD Resource auktoriseringsprincip (värddator FJÄRRSKRIVBORDSANSLUTNING). Auktoriseringsprinciper för fjärrskrivbordsanslutning ange vem som har behörighet att ansluta till RD Gateway. Auktoriseringsprinciper för fjärrskrivbordsresurser ange nätverksresurser, till exempel fjärrskrivbord eller fjärr-appar som användaren kan ansluta till via RD Gateway. 

En RD Gateway kan konfigureras för att använda en central principarkiv för auktoriseringsprinciper för fjärrskrivbordsanslutning. Auktoriseringsprinciper för fjärrskrivbordsresurser kan inte använda en central princip när de bearbetas på RD Gateway. Ett exempel på en RD Gateway konfigurerad för att använda en central principarkiv för auktoriseringsprinciper för fjärrskrivbordsanslutning är en RADIUS-klient till en annan NPS-server som fungerar som det centrala principarkivet.

När NPS-tillägget för Azure är integrerad med NPS och Remote Desktop Gateway, är lyckad autentisering-flödet följande:

1. Remote Desktop Gateway-servern tar emot en begäran om autentisering från fjärrskrivbordsanvändare att ansluta till en resurs, till exempel en fjärrskrivbordssession. Fungerar som en RADIUS-klient, Remote Desktop Gateway-servern konverterar begäran till en RADIUS-åtkomstbegäran och skickar meddelandet till RADIUS (NPS)-servern där NPS-tillägget installeras.
1. Kombinationen av användarnamn och lösenord har verifierats i Active Directory och användaren har autentiserats.
1. Om alla villkor som anges i NPS-anslutningsbegäran och principer för nätverk är uppfyllda (till exempel tid på dagen eller grupp begränsningar av medlemskap), NPS-tillägget utlöser en begäran för sekundär autentisering med Azure MFA.
1. Azure MFA kommunicerar med Azure AD, hämtar användarens information och utför sekundära autentiseringen med metoder som stöds.
1. Vid en lyckad distribution av MFA-kontrollen kommunicerar Azure MFA resultatet till NPS-tillägget.
1. NPS-servern där tillägget installeras, skickar meddelandet RADIUS Access-Accept för auktoriseringsprincip för Fjärrskrivbordsanslutning principen till Remote Desktop Gateway-servern.
1. Användaren beviljas åtkomst till den begärda nätverksresursen via RD Gateway.

## <a name="prerequisites"></a>Förutsättningar

Det här avsnittet beskriver förutsättningarna som krävs innan du integrera Azure MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats.  

* Remote Desktop Services (RDS)-infrastruktur
* Azure MFA-licens
* Windows Server-programvara
* Nätverksprinciper och Access Services (NPS)-rollen
* Azure Active Directory är synkroniserad med den lokala Active Directory
* ID för Azure Active Directory-GUID

### <a name="remote-desktop-services-rds-infrastructure"></a>Remote Desktop Services (RDS)-infrastruktur

Du måste ha en fungerande Remote Desktop Services (RDS)-infrastruktur på plats. Om du inte gör det, kan du snabbt skapa den här infrastrukturen i Azure med hjälp av följande Snabbstart mall: [Skapa Sessionssamling för Remote Desktop distribution](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment). 

Om du vill skapa en lokal RDS infrastruktur snabbt i testsyfte manuellt följer du stegen för att distribuera en. 
**Läs mer**: [Distribuera RDS med Azure Snabbstart](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) och [grundläggande RDS infrastrukturdistribution](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure). 

### <a name="azure-mfa-license"></a>Azure MFA-licens

Krävs är en licens för Azure MFA som är tillgängliga via Azure AD Premium eller andra paket som innehåller den. Förbrukningsbaserad licenser för Azure MFA, till exempel enligt användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. Mer information finns i [så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i det här avsnittet har utförts med Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Nätverksprinciper och Access Services (NPS)-rollen

NPS-rolltjänsten tillhandahåller RADIUS-servern och klienten funktioner samt Nätverksåtkomstpolicy-tjänsten för hälsotillstånd. Den här rollen måste installeras på minst två datorer i din infrastruktur: Fjärrskrivbordsgateway och en annan medlemsserver eller domänkontrollant. Rollen är redan finns på datorn som konfigurerats som server för fjärrskrivbordsgateway som standard.  Du måste sedan även installera NPS-rollen på minst på en annan dator, till exempel en domänkontrollant eller medlemsserver.

Information om hur du installerar NPS-rollen självbetjäning i Windows Server 2012 eller äldre, se [installera en NAP-hälsoprincipserver](https://technet.microsoft.com/library/dd296890.aspx). En beskrivning av bästa praxis för NPS, inklusive rekommendationen för att installera NPS på en domänkontrollant finns i [Metodtips för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory är synkroniserad med den lokala Active Directory

Om du vill använda NPS-tillägget, lokala användare synkroniseras med Azure AD och aktiverade för MFA. Det här avsnittet förutsätter att en lokal användare är synkroniserad med Azure AD med hjälp av AD Connect. Information om Azure AD connect, se [integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID för Azure Active Directory-GUID

Om du vill installera NPS-tillägget som du behöver veta GUID för Azure AD. Anvisningar för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication 

Det här avsnittet innehåller anvisningar för att integrera Azure MFA och fjärrskrivbordsgateway. Som administratör kan konfigurera du Azure MFA-tjänsten innan användare kan registrera sina enheter med flera faktorer eller program.

Följ stegen i [komma igång med Azure Multi-Factor Authentication i molnet](howto-mfa-getstarted.md) att aktivera MFA för din Azure AD-användare.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering

När ett konto har aktiverats för MFA, kan du logga in till resurser som omfattas av principen MFA förrän du har konfigurerat en betrodd enhet om du vill använda för den andra faktorn för autentisering och har autentiseras med tvåstegsverifiering.

Följ stegen i [vad Azure Multi-Factor Authentication innebär för mig?](../user-help/multi-factor-authentication-end-user.md) att förstå och konfigurera korrekt dina enheter för MFA med ditt användarkonto.

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller anvisningar för att konfigurera RDS-infrastruktur att använda Azure MFA för klientautentisering med fjärrskrivbordsgateway.

### <a name="acquire-azure-active-directory-guid-id"></a>Hämta ID för Azure Active Directory-GUID

Som en del av konfigurationen av NPS-tillägget måste du ange autentiseringsuppgifter som administratör och Azure AD-ID för Azure AD-klienten. Följande steg visar hur du hämtar klient-ID.

1. Logga in på den [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klient.
1. I det vänstra navigeringsfönstret väljer du den **Azure Active Directory** ikon.
1. Välj **egenskaper**.
1. I bladet egenskaper bredvid katalog-ID klickar du på den **kopia** ikonen som visas nedan, för att kopiera ID: T till Urklipp.

 ![Egenskaper](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

Installera NPS-tillägget på en server som har rollen nätverksprinciper och Access Services (NPS) installerat. Detta fungerar som RADIUS-server för din design.

>[!Important]
> Var noga med att du inte installerar NPS-tillägget på Remote Desktop Gateway-servern.
>

1. Ladda ned den [NPS-tillägget](https://aka.ms/npsmfa). 
1. Kopiera den körbara filen för installationsprogrammet (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
1. Dubbelklicka på NPS-servern **NpsExtnForAzureMfaInstaller.exe**. Om du uppmanas till detta klickar du på **kör**.
1. I dialogrutan NPS-tillägget för Azure MFA installation granska licensvillkoren för programvaran, kontrollera **jag godkänner licensvillkoren och villkor**, och klicka på **installera**.

  ![Azure MFA-installationen](./media/howto-mfa-nps-extension-rdg/image2.png)

1. I dialogrutan NPS-tillägget för Azure MFA installationen klickar du på **Stäng**.

  ![NPS-tillägget för Azure MFA](./media/howto-mfa-nps-extension-rdg/image3.png)

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med hjälp av ett PowerShell-skript

Därefter måste du konfigurera certifikat för användning av NPS-tillägget för att se till att skydda kommunikationen och kontroll. NPS-komponenterna är ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat
* Associerar offentliga nyckeln för certifikatet till tjänstens huvudnamn i Azure AD
* Lagrar certifikatet i lokala datorns Arkiv
* Beviljar åtkomst till certifikatets privata nyckel för användaren i nätverket
* Startar om Network Policy Server-tjänsten

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för certifikatet till tjänstens huvudnamn i Azure AD och så vidare.

Om du vill använda skriptet, ger du tillägget med dina administratörsautentiseringsuppgifter för Azure AD och Azure AD-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerade NPS-tillägget. Gör något av följande:

1. Öppna en administrativ Windows PowerShell-prompt.
1. I PowerShell-Kommandotolken skriver `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`, och tryck på **RETUR**.
1. Typ `.\AzureMfaNpsExtnConfigSetup.ps1`, och tryck på **RETUR**. Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om inte har installerats installerar skriptet modulen åt dig.

  ![Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. När skriptet har kontrollerat installationen av PowerShell-modulen, visas dialogrutan för Azure Active Directory PowerShell-modulen. I dialogrutan anger du dina autentiseringsuppgifter för Azure AD-administratör och lösenord och klicka på **logga In**.

  ![Öppna Powershell-konto](./media/howto-mfa-nps-extension-rdg/image5.png)

1. När du uppmanas, klistra in det klient-ID som du tidigare kopierade till Urklipp och tryck på **RETUR**.

  ![Ange klient-ID](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Resultatet ska se ut som på bilden nedan.

  ![Självsignerat certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på fjärrskrivbordsgateway

I det här avsnittet ska konfigurera du auktoriseringsprinciper för Remote Desktop Gateway-anslutning och andra RADIUS-inställningar.

Autentiseringsflödet kräver att RADIUS-meddelanden utbytas mellan fjärrskrivbordsgateway och NPS-servern där NPS-servern är installerad. Det innebär att du måste konfigurera RADIUS-klientinställningarna på både fjärrskrivbordsgateway och NPS-servern där NPS-tillägget installeras.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera fjärrskrivbordsgateway anslutning auktoriseringsprinciper för att använda centrala store

Auktoriseringsprinciper för fjärrskrivbordsanslutning (RD cap) ange kraven för att ansluta till en server för fjärrskrivbordsgateway. Auktoriseringsprinciper för fjärrskrivbordsanslutning kan lagras lokalt (standard) eller de kan lagras i en central auktoriseringsprincip för Fjärrskrivbordsanslutning butik som kör NPS. Om du vill konfigurera integrering av Azure MFA med Fjärrskrivbordstjänster, måste du ange användning av en central store.

1. Öppna på servern för fjärrskrivbordsgateway **Serverhanteraren**.
1. På menyn klickar du på **verktyg**, peka på **Remote Desktop Services**, och klicka sedan på **hanteraren för fjärrskrivbordsgateway**.

  ![Fjärrskrivbordstjänster](./media/howto-mfa-nps-extension-rdg/image8.png)

1. I den hanteraren för Fjärrskrivbordsgateway, högerklicka på  **\[servernamn\] (lokal)**, och klicka på **egenskaper**.

  ![Servernamn](./media/howto-mfa-nps-extension-rdg/image9.png)

1. I dialogrutan Egenskaper väljer du den **RD CAP Store** fliken.
1. På fliken RD CAP Store väljer **Central server som kör NPS**. 
1. I den **ange namn eller IP-adressen för servern som kör NPS** skriver IP-adress eller namnet på den server där du installerade NPS-tillägget.

  ![Ange namn eller IP-adress](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicka på **Lägg till**.
1. I den **delad hemlighet** dialogrutan, ange en delad hemlighet och klicka sedan på **OK**. Se till att du registrerar den här delade hemligheten och lagra posten på ett säkert sätt.

 >[!NOTE]
 >Delad hemlighet används för att upprätta förtroende mellan RADIUS-servrar och klienter. Skapa en hemlighet med långa och komplexa.
 >

 ![Delad hemlighet](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS-timeout-värde på Remote Desktop Gateway NPS

För att säkerställa att det finns tid att verifiera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, få svar och svara på RADIUS-meddelanden, det är nödvändigt att justera RADIUS timeout-värdet.

1. Öppna Serverhanteraren på servern för fjärrskrivbordsgateway. På menyn klickar du på **verktyg**, och klicka sedan på **Network Policy Server**. 
1. I den **NPS (lokal)** konsolen, expandera **RADIUS-klienter och servrar**, och välj **fjärranslutna RADIUS-Server**.

 ![Fjärr-RADIUS-Server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. I informationsfönstret dubbelklickar du på **TS GATEWAY SERVER GROUP**.

 >[!NOTE]
 >Den här gruppen för RADIUS-Server skapas när du har konfigurerat den centrala servern för NPS-policys. RD Gateway vidarebefordrar RADIUS-meddelanden till den här servern eller en grupp av servrar, om mer än en i gruppen.
 >

1. I den **gruppegenskaper för TS GATEWAY SERVER** dialogrutan Välj IP-adressen eller namnet på den NPS-server som du har konfigurerat för att lagra auktoriseringsprinciper för fjärrskrivbordsanslutning och klicka sedan på **redigera**. 

 ![TS Gateway-servergrupp](./media/howto-mfa-nps-extension-rdg/image13.png)

1. I den **redigera RADIUS-Server** dialogrutan den **Utjämning av nätverksbelastning** fliken.
1. I den **Utjämning av nätverksbelastning** fliken den **antal sekunder utan svar innan begäran anses vara förlorad** fältet, ändrar du standardvärdet från 3 till ett värde mellan 30 och 60 sekunder.
1. I den **antal sekunder mellan begäranden när servern anses vara otillgänglig** fältet, ändrar du standardvärdet på 30 sekunder till ett värde som är lika med eller större än värdet du angav i föregående steg.

 ![Redigera Radius-Server](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicka på **OK** två gånger för att stänga dialogrutorna.

### <a name="verify-connection-request-policies"></a>Kontrollera principer för anslutningsbegäran

Som standard när du konfigurerar Fjärrskrivbordsgatewayen att använda en central principarkiv för auktoriseringsprinciper för anslutning, har RD Gateway konfigurerats för att vidarebefordra CAP-begäranden till NPS-servern. NPS-server med Azure MFA-tillägget installerat, bearbetar RADIUS-åtkomstbegäran. Följande steg visar hur du verifierar standardprincipen.

1. Fjärrskrivbordsgateway i NPS (lokal)-konsolen, expandera **principer**, och välj **principer för anslutningsbegäran**.
1. Dubbelklicka på **TS GATEWAY AUTHORIZATION POLICY**.
1. I den **TS GATEWAY AUTHORIZATION POLICY egenskaper** dialogrutan klickar du på den **inställningar** fliken.
1. På **inställningar** klickar du på fliken, under Vidarebefordra anslutningsbegäran **autentisering**. RADIUS-klienten har konfigurerats att vidarebefordra begäranden för autentisering.

 ![Autentiseringsinställningar](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicka på **Avbryt**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på den server där NPS-tillägget har installerats

NPS-servern där NPS-tillägget installeras måste vara kan utbyta meddelanden för RADIUS med NPS-server på den Remote Desktop Gateway. Om du vill aktivera den här meddelandeutbyte, måste du konfigurera NPS-komponenter på den server där NPS-tillägg-tjänsten är installerad.

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory

NPS-servern måste registreras i Active Directory för att fungera korrekt i det här scenariot.

1. Öppna på NPS-servern **Serverhanteraren**.
1. I Serverhanteraren klickar du på **verktyg**, och klicka sedan på **Network Policy Server**.
1. I NPS-konsolträdet högerklickar du på **NPS (lokal)**, och klicka sedan på **registrera servern i Active Directory**.
1. Klicka på **OK** två gånger.

 ![Registrera servern i AD](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Lämna konsolen öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klient

Fjärrskrivbordsgateway måste konfigureras som en RADIUS-klient till NPS-servern.

1. På NPS-servern där NPS-tillägget installeras i den **NPS (lokal)** konsolen, högerklicka på **RADIUS-klienter** och klicka på **New**.

 ![Ny RADIUS-klienter](./media/howto-mfa-nps-extension-rdg/image17.png)

1. I den **ny RADIUS-klient** dialogrutan Ange ett eget namn, till exempel _Gateway_, och IP-adressen eller DNS-namnet på den Remote Desktop Gateway-servern. 
1. I den **delad hemlighet** och **Bekräfta delad hemlighet** fält, anger du samma hemlighet som du använt tidigare.

 ![Namn och adress](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klicka på **OK** att stänga dialogrutan Ny RADIUS-klient.

### <a name="configure-network-policy"></a>Konfigurera principen för nätverk

Kom ihåg att NPS-server med Azure MFA-tillägget är avsedda centrala principarkivet för anslutning auktorisering princip (CAP). Därför måste du implementera ett tak på NPS-servern att godkänna giltiga anslutningar begäranden.  

1. Öppna konsolen NPS (lokal) på NPS-servern, expandera **principer**, och klicka på **nätverksprinciper**.
1. Högerklicka på **anslutningar till andra klientåtkomstservrar**, och klicka på **duplicera princip**.

 ![Dubbla](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Högerklicka på **kopia av anslutningar till andra klientåtkomstservrar**, och klicka på **egenskaper**.

 ![Nätverksegenskaper](./media/howto-mfa-nps-extension-rdg/image20.png)

1. I den **kopia av anslutningar till andra klientåtkomstservrar** i dialogrutan **principnamn**, ange ett lämpligt namn, till exempel _RDG_CAP_. Kontrollera **principen är aktiverad**, och välj **bevilja åtkomst**. Valfritt: i **typ av nätverksåtkomstserver**väljer **fjärrskrivbordsgateway**, eller lämna den som **Ospecificerad**.

 ![Kopia av anslutningar](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicka på den **begränsningar** fliken och markera **tillåter klienter att ansluta utan att förhandla om autentiseringsmetod**.

 ![Tillåt klienter att ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Du kan också klicka på den **villkor** fliken och Lägg till villkor som måste uppfyllas för anslutningen ska ha befogenhet, till exempel medlemskap i en specifik Windows-grupp.

 ![Villkor](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicka på **OK**. När du uppmanas att visa motsvarande hjälpavsnitt, klickar du på **nr**.
1. Kontrollera att den nya principen är överst i listan att principen är aktiverad, och att den ger åtkomst.

 ![Nätverksprinciper](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfigurationen

Du måste logga in på den Remote Desktop Gateway med en lämplig RDP-klient för att kontrollera konfigurationen. Glöm inte att använda ett konto som tillåts av din anslutning auktoriseringsprinciper och är aktiverad för Azure MFA.

Som visas i bilden nedan kan du använda den **webbåtkomst för fjärrskrivbord** sidan.

![Webbåtkomst till fjärrskrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering, visar dialogrutan Remote Desktop ansluta statusen initierar fjärranslutning, enligt nedan. 

Om du autentisera med den sekundära autentiseringsmetoden som du tidigare har konfigurerat i Azure MFA, är du ansluten till resursen. Om det inte går att genomföra den sekundära autentiseringen, nekas åtkomst till resursen. 

![Initiera fjärranslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows-telefon för sekundär autentisering.

![Konton](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserats med hjälp av den sekundära autentiseringsmetoden, är du inloggad på fjärrskrivbordsgateway som vanligt. Men eftersom du måste använda en metod för sekundär autentisering med hjälp av en mobilapp på en betrodd enhet, logga in processen är säkrare än det annars skulle vara.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa loggarna i Loggboken för lyckade inloggningshändelser

Om du vill visa händelser för lyckad inloggning i Windows Loggboken, kan du utfärda följande Windows PowerShell-kommando för att fråga Windows Terminal Services och Windows-Security-loggar.

Att fråga efter lyckade inloggningshändelser i de operativa loggarna Gateway _(loggboken\program och tjänster Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_, Använd följande PowerShell-kommandon:

* _Get-WinEvent - loggnamn Microsoft-Windows-TerminalServices-Gateway/Operational_ | där {$_.ID - eq '300'} | FL 
* Detta kommando visar Windows-händelser som visar användaren uppfyller resurskraven auktorisering princip (värddator FJÄRRSKRIVBORDSANSLUTNING) och har beviljats åtkomst.

![Visa Loggboken](./media/howto-mfa-nps-extension-rdg/image28.png)

* _Get-WinEvent - loggnamn Microsoft-Windows-TerminalServices-Gateway/Operational_ | där {$_.ID - eq ”200”} | FL
* Detta kommando visar de händelser som visas när användaren uppfyllda anslutning behörighetskraven för principen.

![Anslutningsauktorisering](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa den här loggen och filter på händelse-ID, 300 och 200. Om du vill fråga lyckade inloggningshändelser i event viewer-loggfilerna för säkerhet, använder du följande kommando:

* _Get-WinEvent - loggnamn Security_ | där {$_.ID - eq '6272'} | FL 
* Det här kommandot kan köras på central NPS-server eller RD Gateway-servern. 

![Lyckade inloggningshändelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också visa säkerhetsloggen eller anpassad vy nätverkspolicy och åtkomsttjänster enligt nedan:

![Nätverksprincip och åtkomsttjänster](./media/howto-mfa-nps-extension-rdg/image31.png)

På den server där du installerade NPS-tillägget för Azure MFA, finns loggarna i Loggboken program specifikt för tillägget på _program och tjänster Logs\Microsoft\AzureMfa_. 

![Loggar i Loggboken program](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Felsöka Guide

Om konfigurationen inte fungerar som förväntat, är den första platsen för att börja felsöka att kontrollera att användaren är konfigurerad för att använda Azure MFA. Få användaren att ansluta till den [Azure-portalen](https://portal.azure.com). Om användare ombeds sekundära verifiering och kan autentisera, vet du att en felaktig konfiguration av Azure MFA.

Om Azure MFA fungerar för användare, bör du granska relevant händelseloggarna. Dessa inkluderar säkerhetshändelse, gatewayen fungerar och Azure MFA-loggar som beskrivs i föregående avsnitt. 

Nedan visas ett exempel på utdata för säkerhetsloggen som visar misslyckade-händelse (Event ID 6273).

![Misslyckade händelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterade händelse från AzureMFA-loggar:

![Azure MFA-logg](./media/howto-mfa-nps-extension-rdg/image34.png)

Utföra avancerade felsöka alternativ genom att granska loggfilerna NPS databasen format där NPS-tjänsten är installerad. Dessa loggfiler skapas i _%SystemRoot%\System32\Logs_ mapp som kommaavgränsad textfiler. 

En beskrivning av dessa loggfiler, se [tolka NPS loggfilerna](https://technet.microsoft.com/library/cc771748.aspx). Posterna i loggfilerna kan vara svårt att tolka utan att importera dem till ett kalkylblad eller en databas. Du hittar flera IAS Parser online som hjälper dig att tolka loggfilerna. 

Bilden nedan visar utdata i en sådan nedladdningsbara [shareware programmet](https://www.deepsoftware.com/iasviewer). 

![Shareware-app](./media/howto-mfa-nps-extension-rdg/image35.png)

Slutligen ytterligare felsöka alternativ kan du använda en protokollanalysator sådana [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx). 

Bilden nedan från Microsoft Message Analyzer visar nätverkstrafik filtrerad efter RADIUS-protokollet som innehåller användarnamnet **CONTOSO\AliceC**.

![Microsoft Message Analyzer](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg

[Skaffa Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
