---
title: Integrera RDG med Azure MFA NPS-tillägg – Azure Active Directory
description: Integrera din infrastruktur för Remote Desktop Gateway med Azure MFA med hjälp av NPS-tillägget för Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6ec402cf2c741d88d230e5734485bf9eb0dd1b03
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74381819"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera fjärrskrivbordsgateway infrastruktur med Network Policy Server (NPS)-tillägget och Azure AD

Den här artikeln innehåller information för att integrera din infrastruktur för Remote Desktop Gateway med Azure Multi-Factor Authentication (MFA) med hjälp av nätverksprincipserver (NPS)-tillägget för Microsoft Azure.

Nätverks princip Server (NPS)-tillägget för Azure gör det möjligt för kunder att skydda Remote Authentication Dial-In User Service (RADIUS) klientautentisering med hjälp av Azures molnbaserade [Multi-Factor Authentication (MFA)](multi-factor-authentication.md). Den här lösningen tillhandahåller tvåstegsverifiering för att lägga till ett andra säkerhetslager till användarinloggningar och transaktioner.

Den här artikeln innehåller stegvisa instruktioner för att integrera NPS-infrastruktur med Azure MFA med NPS-tillägget för Azure. På så sätt kan säker verifiering för användare som försöker logga in på en fjärrskrivbordsgateway.

> [!NOTE]
> Den här artikeln bör inte användas med MFA Server-distributioner och bör endast användas med Azure MFA (molnbaserad) distributioner.

Nätverksprinciper och Access Services (NPS) gör att organisationer kan du göra följande:

* Definiera centrala platser för hantering och kontroll över nätverksbegäranden genom att ange vem som kan ansluta, vilka tider på dagen anslutningar tillåts, varaktigheten för anslutningar och säkerhetsnivån som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje server för VPN eller Remote Desktop (Rdg) kan anges dessa principer en gång på en central plats. RADIUS-protokollet ger centraliserad autentisering, auktorisering och redovisning.
* Fastställa och tillämpa hälsopolicys för Network Access Protection (NAP) klienten som avgör om enheter har beviljats obegränsad eller begränsad åtkomst till nätverksresurser.
* Skapar du ett sätt att tillämpa autentisering och auktorisering för åtkomst till 802. 1 x-kompatibla trådlösa åtkomstpunkter och Ethernet-växlar.

Organisationer använder vanligt vis NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN-principer. Men använda många organisationer även NPS för att förenkla och centralisera hanteringen av auktoriseringsprinciper för RD Desktop Connection (RD cap).

Organisationer kan även integrera NPS med Azure MFA för att förbättra säkerheten och ge en hög nivå av efterlevnad. Detta säkerställer att användare upprättar tvåstegsverifiering att logga in på den Remote Desktop Gateway. De måste ange sina användarnamn och lösenordet tillsammans med information som användaren har i deras kontroll för användare som ska få åtkomstbehörighet. Den här informationen måste vara betrodd och inte enkelt dupliceras, till exempel ett mobiltelefonnummer, både fasta telefoner tal, program på en mobil enhet och så vidare. RDG stöder för närvarande telefonsamtal och push-meddelanden från Microsoft Authenticator app-metoder för 2FA. Mer information om autentiseringsmetoder som stöds finns i avsnittet [avgöra vilka autentiseringsmetoder som användarna kan använda](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Innan åtkomsten till NPS-tillägget för Azure har gjorts måste kunder som vill implementera tvåstegsverifiering för integrerad NPS-och Azure MFA-miljö konfigurera och underhålla en separat MFA-server i den lokala miljön som dokumenterad i [Fjärrskrivbordsgateway och azure Multi-Factor Authentication-Server med RADIUS](howto-mfaserver-nps-rdg.md).

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

## <a name="prerequisites"></a>Krav

Det här avsnittet beskriver förutsättningarna som krävs innan du integrera Azure MFA med fjärrskrivbordsgateway. Innan du börjar måste du ha följande krav på plats.  

* Remote Desktop Services (RDS)-infrastruktur
* Azure MFA-licens
* Windows Server-programvara
* Nätverksprinciper och Access Services (NPS)-rollen
* Azure Active Directory är synkroniserad med den lokala Active Directory
* ID för Azure Active Directory-GUID

### <a name="remote-desktop-services-rds-infrastructure"></a>Remote Desktop Services (RDS)-infrastruktur

Du måste ha en fungerande Remote Desktop Services (RDS)-infrastruktur på plats. Om du inte gör det kan du snabbt skapa den här infrastrukturen i Azure med hjälp av följande snabb starts mall: [skapa en samling distribution](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment)av fjärrskrivbordssessioner.

Om du vill skapa en lokal RDS infrastruktur snabbt i testsyfte manuellt följer du stegen för att distribuera en.
**Läs mer**: [distribuera fjärr skrivbords tjänster med Azure snabb start](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) och [grundläggande distribution av fjärr skrivbords infrastruktur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Krävs är en licens för Azure MFA som är tillgängliga via Azure AD Premium eller andra paket som innehåller den. Förbrukningsbaserad licenser för Azure MFA, till exempel enligt användare eller per autentisering licenser är inte kompatibla med NPS-tillägget. Mer information finns i [så här skaffar du Azure-Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller senare med NPS-rolltjänsten installerad. Alla steg i det här avsnittet har utförts med Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Nätverksprinciper och Access Services (NPS)-rollen

NPS-rolltjänsten tillhandahåller RADIUS-servern och klienten funktioner samt Nätverksåtkomstpolicy-tjänsten för hälsotillstånd. Den här rollen måste installeras på minst två datorer i infrastrukturen: The Remote Desktop Gateway och en annan medlemsserver eller domänkontrollant. Rollen är redan finns på datorn som konfigurerats som server för fjärrskrivbordsgateway som standard.  Du måste sedan även installera NPS-rollen på minst på en annan dator, till exempel en domänkontrollant eller medlemsserver.

Information om hur du installerar NPS-rolltjänsten Windows Server 2012 eller äldre finns i [installera en NAP-hälsopolicy Server](https://technet.microsoft.com/library/dd296890.aspx). En beskrivning av metod tips för NPS, inklusive rekommendationer för att installera NPS på en domänkontrollant finns i [metod tips för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory är synkroniserad med den lokala Active Directory

Om du vill använda NPS-tillägget, lokala användare synkroniseras med Azure AD och aktiverade för MFA. Det här avsnittet förutsätter att en lokal användare är synkroniserad med Azure AD med hjälp av AD Connect. Information om Azure AD Connect finns i [integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>ID för Azure Active Directory-GUID

Om du vill installera NPS-tillägget som du behöver veta GUID för Azure AD. Anvisningar för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera Multi-Factor Authentication

Det här avsnittet innehåller anvisningar för att integrera Azure MFA och fjärrskrivbordsgateway. Som administratör kan konfigurera du Azure MFA-tjänsten innan användare kan registrera sina enheter med flera faktorer eller program.

Följ stegen i [komma igång med azure Multi-Factor Authentication i molnet](howto-mfa-getstarted.md) för att aktivera MFA för dina Azure AD-användare.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering

När ett konto har aktiverats för MFA, kan du logga in till resurser som omfattas av principen MFA förrän du har konfigurerat en betrodd enhet om du vill använda för den andra faktorn för autentisering och har autentiseras med tvåstegsverifiering.

Följ stegen i [Vad innebär Azure Multi-Factor Authentication för mig?](../user-help/multi-factor-authentication-end-user.md) för att förstå och konfigurera dina enheter korrekt för MFA med ditt användar konto.

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägget

Det här avsnittet innehåller anvisningar för att konfigurera RDS-infrastruktur att använda Azure MFA för klientautentisering med fjärrskrivbordsgateway.

### <a name="acquire-azure-active-directory-guid-id"></a>Hämta ID för Azure Active Directory-GUID

Som en del av konfigurationen av NPS-tillägget måste du ange autentiseringsuppgifter som administratör och Azure AD-ID för Azure AD-klienten. Följande steg visar hur du hämtar klient-ID.

1. Logga in på [Azure Portal](https://portal.azure.com) som global administratör för Azure-klienten.
1. I det vänstra navigerings fönstret väljer du ikonen **Azure Active Directory** .
1. Välj **Egenskaper**.
1. På bladet egenskaper, bredvid katalog-ID, klickar du på **kopierings** ikonen, som visas nedan, för att kopiera ID: t till Urklipp.

   ![Hämtar katalog-ID: t från Azure Portal](./media/howto-mfa-nps-extension-rdg/image1.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

Installera NPS-tillägget på en server som har rollen nätverksprinciper och Access Services (NPS) installerat. Detta fungerar som RADIUS-server för din design.

> [!Important]
> Var noga med att du inte installerar NPS-tillägget på Remote Desktop Gateway-servern.
>

1. Ladda ned [NPS-tillägget](https://aka.ms/npsmfa).
1. Kopiera den körbara filen för installationsprogrammet (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
1. Dubbelklicka på **NpsExtnForAzureMfaInstaller. exe**på NPS-servern. Om du uppmanas till det klickar du på **Kör**.
1. I dialog rutan NPS-tillägg för Azure MFA-installationen granskar du licens villkoren för program vara, kontrollerar **att jag godkänner licens villkoren**och klickar på **Installera**.
1. I dialog rutan NPS-tillägg för Azure MFA-installationen klickar du på **Stäng**.

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
1. Skriv `cd ‘c:\Program Files\Microsoft\AzureMfa\Config’`i PowerShell-prompten och tryck på **RETUR**.
1. Skriv `.\AzureMfaNpsExtnConfigSetup.ps1`och tryck på **RETUR**. Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om inte har installerats installerar skriptet modulen åt dig.

   ![Köra AzureMfaNpsExtnConfigSetup. ps1 i Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. När skriptet har kontrollerat installationen av PowerShell-modulen, visas dialogrutan för Azure Active Directory PowerShell-modulen. Ange dina autentiseringsuppgifter och lösen ord för Azure AD-administratören i dialog rutan och klicka på **Logga**in.

   ![Autentisera till Azure AD i PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. När du uppmanas klistrar du in det katalog-ID som du kopierade till Urklipp tidigare och trycker på **RETUR**.

   ![Placera katalog-ID i PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skriptet skapar ett självsignerat certifikat och utför andra ändringar i konfigurationen. Resultatet ska se ut som på bilden nedan.

   ![Utdata från PowerShell som visar självsignerade certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på fjärrskrivbordsgateway

I det här avsnittet ska konfigurera du auktoriseringsprinciper för Remote Desktop Gateway-anslutning och andra RADIUS-inställningar.

Autentiserings flödet kräver att RADIUS-meddelanden utbyts mellan Fjärrskrivbordsgateway och NPS-servern där NPS-tillägget är installerat. Det innebär att du måste konfigurera RADIUS-klientinställningarna på både fjärrskrivbordsgateway och NPS-servern där NPS-tillägget installeras.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera fjärrskrivbordsgateway anslutning auktoriseringsprinciper för att använda centrala store

Auktoriseringsprinciper för fjärrskrivbordsanslutning (RD cap) ange kraven för att ansluta till en server för fjärrskrivbordsgateway. Auktoriseringsprinciper för fjärrskrivbordsanslutning kan lagras lokalt (standard) eller de kan lagras i en central auktoriseringsprincip för Fjärrskrivbordsanslutning butik som kör NPS. Om du vill konfigurera integrering av Azure MFA med Fjärrskrivbordstjänster, måste du ange användning av en central store.

1. Öppna **Serverhanteraren**på RD Gateway-servern.
1. Klicka på **verktyg**på menyn, peka på **Fjärrskrivbordstjänster**och klicka sedan på **hanteraren för fjärrskrivbordsgateway**.
1. I Hanteraren för fjärrskrivbordsgateway högerklickar du på **\[Server namn\] (lokal)** och klickar på **Egenskaper**.
1. I dialog rutan Egenskaper väljer du fliken **lagring av Auktoriseringsprinciper för fjärr SKRIVBORDS anslutning** .
1. På fliken Arkiv för Auktoriseringsprinciper för fjärr skrivbords anslutning väljer du **Central Server som kör NPS**. 
1. I fältet **Ange ett namn eller en IP-adress för den server som kör NPS** anger du IP-adressen eller Server namnet för den server där du installerade NPS-tillägget.

   ![Ange namn eller IP-adress för din NPS-server](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicka på **Lägg till**.
1. I dialog rutan **delad hemlighet** anger du en delad hemlighet och klickar sedan på **OK**. Se till att du registrerar den här delade hemligheten och lagra posten på ett säkert sätt.

   >[!NOTE]
   >Delad hemlighet används för att upprätta förtroende mellan RADIUS-servrar och klienter. Skapa en hemlighet med långa och komplexa.
   >

   ![Skapa en delad hemlighet för att upprätta förtroende](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS-timeout-värde på Remote Desktop Gateway NPS

För att säkerställa att det finns tid att verifiera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, få svar och svara på RADIUS-meddelanden, det är nödvändigt att justera RADIUS timeout-värdet.

1. Öppna Serverhanteraren på servern för fjärrskrivbordsgateway. På menyn klickar du på **verktyg**och sedan på **nätverks princip Server**.
1. I **NPS-konsolen (lokal)** expanderar du **RADIUS-klienter och-servrar**och väljer **fjärr-RADIUS-server**.

   ![Hanterings konsol för nätverks princip server som visar fjärr-RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. I informations fönstret dubbelklickar du på **TS Gateway Server grupp**.

   >[!NOTE]
   >Den här gruppen för RADIUS-Server skapas när du har konfigurerat den centrala servern för NPS-policys. RD Gateway vidarebefordrar RADIUS-meddelanden till den här servern eller en grupp av servrar, om mer än en i gruppen.
   >

1. I dialog rutan **Egenskaper för TS Gateway-servergrupp** väljer du IP-adressen eller namnet på den NPS-server som du konfigurerade för att lagra Auktoriseringsprinciper för fjärr skrivbords anslutning och klickar sedan på **Redigera**.

   ![Välj IP-adressen eller namnet på den tidigare konfigurerade NPS-servern](./media/howto-mfa-nps-extension-rdg/image13.png)

1. I dialog rutan **Redigera RADIUS-server** väljer du fliken **belastnings utjämning** .
1. På fliken **belastnings utjämning** i **antal sekunder utan svar innan begäran anses vara släppt** , ändra standardvärdet från 3 till ett värde mellan 30 och 60 sekunder.
1. I **antal sekunder mellan begär anden när servern identifieras som otillgänglig** , ändra standardvärdet 30 sekunder till ett värde som är lika med eller större än värdet som du angav i föregående steg.

   ![Redigera inställningar för tids gräns för RADIUS-server på fliken belastnings utjämning](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicka på **OK** två gånger för att stänga dialog rutorna.

### <a name="verify-connection-request-policies"></a>Kontrollera principer för anslutningsbegäran

Som standard när du konfigurerar Fjärrskrivbordsgatewayen att använda en central principarkiv för auktoriseringsprinciper för anslutning, har RD Gateway konfigurerats för att vidarebefordra CAP-begäranden till NPS-servern. NPS-server med Azure MFA-tillägget installerat, bearbetar RADIUS-åtkomstbegäran. Följande steg visar hur du verifierar standardprincipen.

1. På RD Gateway, i NPS-konsolen (lokal), expanderar du **principer**och väljer **principer för anslutningsbegäran**.
1. Dubbelklicka på **TS Gateway-auktoriseringsprincip**.
1. I dialog rutan **Egenskaper för TS Gateway-auktoriseringsprincip** klickar du på fliken **Inställningar** .
1. På fliken **Inställningar** , under vidarebefordra anslutningsbegäran, klickar du på **autentisering**. RADIUS-klienten har konfigurerats att vidarebefordra begäranden för autentisering.

   ![Konfigurera autentiseringsinställningar som anger Server gruppen](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicka på **Avbryt**.

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på den server där NPS-tillägget har installerats

NPS-servern där NPS-tillägget installeras måste vara kan utbyta meddelanden för RADIUS med NPS-server på den Remote Desktop Gateway. Om du vill aktivera den här meddelandeutbyte, måste du konfigurera NPS-komponenter på den server där NPS-tillägg-tjänsten är installerad.

### <a name="register-server-in-active-directory"></a>Registrera servern i Active Directory

NPS-servern måste registreras i Active Directory för att fungera korrekt i det här scenariot.

1. Öppna **Serverhanteraren**på NPS-servern.
1. I Serverhanteraren klickar du på **verktyg**och sedan på **nätverks princip Server**.
1. I konsolen nätverks princip Server högerklickar du på **NPS (lokal)** och klickar sedan på **registrera server i Active Directory**.
1. Klicka på **OK** två gånger.

   ![Registrera NPS-servern i Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Lämna konsolen öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klient

Fjärrskrivbordsgateway måste konfigureras som en RADIUS-klient till NPS-servern.

1. På NPS-servern där NPS-tillägget är installerat går du till **NPS-konsolen (lokal)** , högerklickar på **RADIUS-klienter** och klickar på **ny**.

   ![Skapa en ny RADIUS-klient i NPS-konsolen](./media/howto-mfa-nps-extension-rdg/image17.png)

1. I dialog rutan **ny RADIUS-klient** anger du ett eget namn, till exempel _Gateway_och IP-adressen eller DNS-namnet på servern för fjärrskrivbordsgateway.
1. I fälten **delad hemlighet** och **Bekräfta delad hemlighet** anger du samma hemlighet som du använde tidigare.

   ![Konfigurera ett eget namn och IP-eller DNS-adressen](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Klicka på **OK** för att stänga dialog rutan ny RADIUS-klient.

### <a name="configure-network-policy"></a>Konfigurera principen för nätverk

Kom ihåg att NPS-server med Azure MFA-tillägget är avsedda centrala principarkivet för anslutning auktorisering princip (CAP). Därför måste du implementera ett tak på NPS-servern att godkänna giltiga anslutningar begäranden.  

1. Öppna NPS-konsolen (lokal) på NPS-servern, expandera **principer**och klicka på **nätverks principer**.
1. Högerklicka på **anslutningar till andra åtkomst servrar**och klicka på **duplicera princip**.

   ![Duplicera anslutningen till andra åtkomst server principer](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Högerklicka på **kopia av anslutningar till andra åtkomst servrar**och klicka på **Egenskaper**.
1. I dialog rutan **kopia av anslutningar till andra åtkomst servrar** , under **princip namn**, anger du ett lämpligt namn, till exempel _RDG_CAP_. Kontrol lera att **principen är aktive rad**och välj **bevilja åtkomst**. Alternativt, i **typ av nätverks åtkomst Server**, väljer du **Fjärrskrivbordsgateway**, eller så kan du lämna den som **ospecificerad**.

   ![Namnge principen, aktivera och bevilja åtkomst](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicka på fliken **begränsningar** och markera **Tillåt att klienter ansluter utan att förhandla om en autentiseringsmetod**.

   ![Ändra autentiseringsmetoder för att tillåta klienter att ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Du kan också klicka på fliken **villkor** och lägga till villkor som måste uppfyllas för att anslutningen ska kunna godkännas, till exempel medlemskap i en speciell Windows-grupp.

   ![Alternativt kan du ange anslutnings villkor](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicka på **OK**. När du uppmanas att visa motsvarande hjälp avsnitt klickar du på **Nej**.
1. Kontrollera att den nya principen är överst i listan att principen är aktiverad, och att den ger åtkomst.

   ![Flytta principen överst i listan](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfigurationen

Du måste logga in på den Remote Desktop Gateway med en lämplig RDP-klient för att kontrollera konfigurationen. Glöm inte att använda ett konto som tillåts av din anslutning auktoriseringsprinciper och är aktiverad för Azure MFA.

Som du ser i bilden nedan kan du använda **webb åtkomst sidan för fjärr skrivbord** .

![Testa i webb åtkomst till fjärr skrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering, visar dialogrutan Remote Desktop ansluta statusen initierar fjärranslutning, enligt nedan. 

Om du autentisera med den sekundära autentiseringsmetoden som du tidigare har konfigurerat i Azure MFA, är du ansluten till resursen. Om det inte går att genomföra den sekundära autentiseringen, nekas åtkomst till resursen. 

![Anslutning till fjärrskrivbord att initiera en fjärr anslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows-telefon för sekundär autentisering.

![Exempel på Windows Phone Authenticator-app som visar verifiering](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserats med hjälp av den sekundära autentiseringsmetoden, är du inloggad på fjärrskrivbordsgateway som vanligt. Men eftersom du måste använda en sekundär autentiseringsmetod med en mobilapp på en betrodd enhet är inloggnings processen säkrare än vad som annars skulle vara.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa loggarna i Loggboken för lyckade inloggningshändelser

Om du vill visa händelser för lyckad inloggning i Windows Loggboken, kan du utfärda följande Windows PowerShell-kommando för att fråga Windows Terminal Services och Windows-Security-loggar.

Använd följande PowerShell-kommandon för att fråga lyckade inloggnings händelser i de operativa loggarna för gateway _(Event loggboken\program-och Services Logs\Microsoft\Windows\TerminalServices-Gateway\Operational)_ :

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Detta kommando visar Windows-händelser som visar användaren uppfyller resurskraven auktorisering princip (värddator FJÄRRSKRIVBORDSANSLUTNING) och har beviljats åtkomst.

![Visa händelser med PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Detta kommando visar de händelser som visas när användaren uppfyllda anslutning behörighetskraven för principen.

![Visa principen för anslutningsauktorisering med PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa den här loggen och filter på händelse-ID, 300 och 200. Om du vill fråga lyckade inloggningshändelser i event viewer-loggfilerna för säkerhet, använder du följande kommando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Det här kommandot kan köras på central NPS-server eller RD Gateway-servern.

![Exempel på lyckade inloggnings händelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också visa säkerhetsloggen eller anpassad vy nätverkspolicy och åtkomsttjänster enligt nedan:

![Nätverks policy och åtkomst tjänster Loggboken](./media/howto-mfa-nps-extension-rdg/image31.png)

På den server där du installerade NPS-tillägget för Azure MFA kan du hitta Loggboken program loggar som är specifika för tillägget på _program-och tjänst Logs\Microsoft\AzureMfa_.

![Loggboken AuthZ program loggar](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Felsöka Guide

Om konfigurationen inte fungerar som förväntat, är den första platsen för att börja felsöka att kontrollera att användaren är konfigurerad för att använda Azure MFA. Be användaren att ansluta till [Azure Portal](https://portal.azure.com). Om användare ombeds sekundära verifiering och kan autentisera, vet du att en felaktig konfiguration av Azure MFA.

Om Azure MFA fungerar för användare, bör du granska relevant händelseloggarna. Dessa inkluderar säkerhetshändelse, gatewayen fungerar och Azure MFA-loggar som beskrivs i föregående avsnitt.

Nedan visas ett exempel på utdata för säkerhetsloggen som visar misslyckade-händelse (Event ID 6273).

![Exempel på en misslyckad inloggnings händelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterade händelse från AzureMFA-loggar:

![Exempel på Azure MFA-inloggning Loggboken](./media/howto-mfa-nps-extension-rdg/image34.png)

Utföra avancerade felsöka alternativ genom att granska loggfilerna NPS databasen format där NPS-tjänsten är installerad. Loggfilerna skapas i mappen _%systemroot%\System32\Logs_ som kommaavgränsade textfiler.

En beskrivning av dessa loggfiler finns i [tolka loggfiler för NPS-databasfiler](https://technet.microsoft.com/library/cc771748.aspx). Posterna i loggfilerna kan vara svårt att tolka utan att importera dem till ett kalkylblad eller en databas. Du hittar flera IAS Parser online som hjälper dig att tolka loggfilerna.

Bilden nedan visar utdata från ett sådant nedladdnings Bart [shareware-program](https://www.deepsoftware.com/iasviewer).

![Exempel på shareware-app IAS-parser](./media/howto-mfa-nps-extension-rdg/image35.png)

Slutligen kan du använda en protokoll analys, till exempel [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx), för ytterligare fel söknings alternativ.

Bilden nedan från Microsoft Message Analyzer visar nätverks trafik som filtrerats på RADIUS-protokoll som innehåller användar namnet **CONTOSO\AliceC**.

![Microsoft Message Analyzer visar filtrerad trafik](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg

[Skaffa Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
