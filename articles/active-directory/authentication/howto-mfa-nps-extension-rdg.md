---
title: Integrera RDG med Azure MFA NPS-tillägg - Azure Active Directory
description: Integrera infrastrukturen för fjärrskrivbordsgateway med Azure MFA med hjälp av servertillägget Network Policy för Microsoft Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: c61bea7f3ca1105edfec54501c5f0725a5a10225
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/03/2020
ms.locfileid: "80654101"
---
# <a name="integrate-your-remote-desktop-gateway-infrastructure-using-the-network-policy-server-nps-extension-and-azure-ad"></a>Integrera infrastrukturen för fjärrskrivbordsgateway med nps-tillägget (Network Policy Server) och Azure AD

Den här artikeln innehåller information om hur du integrerar infrastrukturen för fjärrskrivbordsgateway med Azure Multi-Factor Authentication (MFA) med hjälp av NPS-tillägget (Network Policy Server) för Microsoft Azure.

Nps-tillägget (Network Policy Server) för Azure gör det möjligt för kunder att skydda RADIUS-klientautentisering (Remote Authentication Dial-In User Service) med Azures molnbaserade [MFA -autentisering (Multi Factor Authentication).](multi-factor-authentication.md) Den här lösningen ger tvåstegsverifiering för att lägga till ett andra säkerhetslager i användarloggningar och transaktioner.

Den här artikeln innehåller steg-för-steg-instruktioner för att integrera NPS-infrastrukturen med Azure MFA med NPS-tillägget för Azure. Detta möjliggör säker verifiering för användare som försöker logga in på en fjärrskrivbordsgateway.

> [!NOTE]
> Den här artikeln ska inte användas med MFA Server-distributioner och bör endast användas med Azure MFA-distributioner (Molnbaserade).

Network Policy and Access Services (NPS) ger organisationer möjlighet att göra följande:

* Definiera centrala platser för hantering och kontroll av nätverksbegäranden genom att ange vem som kan ansluta, vilka tider på dagen anslutningar tillåts, varaktigheten av anslutningar och den säkerhetsnivå som klienter måste använda för att ansluta och så vidare. I stället för att ange dessa principer på varje VPN- eller Fjärrskrivbordsserver (RD) Gateway-server kan dessa principer anges en gång på en central plats. RADIUS-protokollet tillhandahåller den centraliserade autentisering, auktorisering och redovisning (AAA).
* Upprätta och tillämpa NAP-klientprinciper (Network Access Protection) som avgör om enheter beviljas obegränsad eller begränsad åtkomst till nätverksresurser.
* Tillhandahålla ett sätt att framtvinga autentisering och auktorisering för åtkomst till trådlösa åtkomstpunkter och Ethernet-switchar med 802,1 x kompatibla.

Vanligtvis använder organisationer NPS (RADIUS) för att förenkla och centralisera hanteringen av VPN-principer. Men många organisationer använder också NPS för att förenkla och centralisera hanteringen av rd desktop-anslutningsauktoriseringsprinciper (RD CAPs).

Organisationer kan också integrera NPS med Azure MFA för att öka säkerheten och tillhandahålla en hög efterlevnadsnivå. Detta säkerställer att användarna upprättar tvåstegsverifiering för att logga in på gatewayen för fjärrskrivbord. För att användare ska beviljas åtkomst måste de ange sin kombination av användarnamn/lösenord tillsammans med information som användaren har i sin kontroll. Denna information måste vara betrodd och inte lätt dupliceras, till exempel ett mobilnummer, fast nummer, applikation på en mobil enhet och så vidare. RDG stöder för närvarande telefonsamtal och push-meddelanden från Microsoft authenticator app metoder för 2FA. Mer information om autentiseringsmetoder som stöds finns i avsnittet [Ta reda på vilka autentiseringsmetoder användarna kan använda](howto-mfa-nps-extension.md#determine-which-authentication-methods-your-users-can-use).

Innan NPS-tillägget för Azure var tillgängliga måste kunder som ville implementera tvåstegsverifiering för integrerade NPS- och Azure MFA-miljöer konfigurera och underhålla en separat MFA-server i den lokala miljön som dokumenteras i [Remote Desktop Gateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md).

Tillgängligheten för NPS-tillägget för Azure ger nu organisationer möjlighet att distribuera antingen en lokalt baserad MFA-lösning eller en molnbaserad MFA-lösning för att skydda RADIUS-klientautentisering.

## <a name="authentication-flow"></a>Autentiseringsflöde

För att användare ska beviljas åtkomst till nätverksresurser via en fjärrskrivbordsgateway måste de uppfylla de villkor som anges i en princip för fjärrskrivbordsanslutningsauktorisering (RD CAP) och en princip för auktoriseringsprincipen för fjärrskrivbordsresurser (RD RAP). Certifikatutfärdare för fjärrskrivbord anger vem som har behörighet att ansluta till fjärrskrivbordsgateways. Rd RAPs ange nätverksresurser, till exempel fjärrskrivbord eller fjärrappar, som användaren får ansluta till via fjärrskrivbordsgatewayen.

En fjärrskrivbordsgateway kan konfigureras för att använda ett centralt principarkiv för certifikatutfärdare för fjärrskrivbord. Rd RAPs kan inte använda en central princip, eftersom de bearbetas på fjärrskrivbordsgatewayen. Ett exempel på en fjärrskrivbordsgateway som konfigurerats för att använda ett centralt principarkiv för klientåtkomstleverantörer för fjärrskrivbord är en RADIUS-klient till en annan NPS-server som fungerar som det centrala principarkivet.

När NPS-tillägget för Azure är integrerat med NPS- och Fjärrskrivbordsgatewayen är det lyckade autentiseringsflödet följande:

1. Servern för fjärrskrivbordsgateway tar emot en autentiseringsbegäran från en fjärrskrivbordsanvändare för att ansluta till en resurs, till exempel en fjärrskrivbordssession. När servern för fjärrskrivbordsgateway fungerar som en RADIUS-klient konverteras begäran till ett RADIUS Access-Request-meddelande och meddelandet skickas till RADIUS-servern (NPS) där NPS-tillägget är installerat.
1. Kombinationen av användarnamn och lösenord verifieras i Active Directory och användaren autentiseras.
1. Om alla villkor som anges i NPS-anslutningsbegäran och nätverksprinciperna är uppfyllda (till exempel tid på dygnet eller gruppmedlemskap), utlöser NPS-tillägget en begäran om sekundär autentisering med Azure MFA.
1. Azure MFA kommunicerar med Azure AD, hämtar användarens information och utför den sekundära autentiseringen med metoder som stöds.
1. När MFA-utmaningen är framgångsrik kommunicerar Azure MFA resultatet till NPS-tillägget.
1. NPS-servern, där tillägget är installerat, skickar ett RADIUS Access-Accept-meddelande för principen om fjärrskrivbordsbehörighet till servern för fjärrskrivbordsgateway.
1. Användaren beviljas åtkomst till den begärda nätverksresursen via fjärrskrivbordsgatewayen.

## <a name="prerequisites"></a>Krav

I det här avsnittet beskrivs de förutsättningar som krävs innan Azure MFA integreras med fjärrskrivbordsgatewayen. Innan du börjar måste du ha följande förutsättningar på plats.  

* Rds-infrastruktur (Remote Desktop Services)
* Azure MFA-licens
* Windows Server-programvara
* Nps-roll (Network Policy and Access Services)
* Azure Active Directory synkroniserat med lokal Active Directory
* AZURE Active Directory GUID-ID

### <a name="remote-desktop-services-rds-infrastructure"></a>Rds-infrastruktur (Remote Desktop Services)

Du måste ha en fungerande RDS-infrastruktur (Remote Desktop Services). Om du inte gör det kan du snabbt skapa den här infrastrukturen i Azure med hjälp av följande snabbstartsmall: [Skapa distribution av fjärrskrivbordssessionssamling](https://github.com/Azure/azure-quickstart-templates/tree/ad20c78b36d8e1246f96bb0e7a8741db481f957f/rds-deployment).

Om du vill skapa en lokal RDS-infrastruktur manuellt för testning följer du stegen för att distribuera en.
**Läs mer**: [Distribuera fjärrskrivbordstjänster med Azure-snabbstart](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-in-azure) och [grundläggande distribution av RDS-infrastruktur](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-deploy-infrastructure).

### <a name="azure-mfa-license"></a>Azure MFA-licens

Krävs är en licens för Azure MFA, som är tillgänglig via Azure AD Premium eller andra paket som innehåller den. Förbrukningsbaserade licenser för Azure MFA, till exempel per användare eller per autentiseringslicenser, är inte kompatibla med NPS-tillägget. Mer information finns i [Så här hämtar du Azure Multi-Factor Authentication](concept-mfa-licensing.md). I testsyfte kan du använda en utvärderingsprenumeration.

### <a name="windows-server-software"></a>Windows Server-programvara

NPS-tillägget kräver Windows Server 2008 R2 SP1 eller högre med nps-rolltjänsten installerad. Alla steg i det här avsnittet utfördes med Windows Server 2016.

### <a name="network-policy-and-access-services-nps-role"></a>Nps-roll (Network Policy and Access Services)

Nps-rolltjänsten tillhandahåller RADIUS-serverns och klientfunktionerna samt hälsotjänsten för network access-princip. Den här rollen måste installeras på minst två datorer i infrastrukturen: Fjärrskrivbordsgatewayen och en annan medlemsserver eller domänkontrollant. Som standard finns rollen redan på den dator som konfigurerats som fjärrskrivbordsgateway.  Du måste också installera NPS-rollen på åtminstone på en annan dator, till exempel en domänkontrollant eller medlemsserver.

Information om hur du installerar NPS-rolltjänsten Windows Server 2012 eller äldre finns i [Installera en NAP Health Policy Server](https://technet.microsoft.com/library/dd296890.aspx). En beskrivning av metodtips för NPS, inklusive rekommendationen att installera NPS på en domänkontrollant, finns [i Metodtips för NPS](https://technet.microsoft.com/library/cc771746).

### <a name="azure-active-directory-synched-with-on-premises-active-directory"></a>Azure Active Directory synkroniserat med lokal Active Directory

Om du vill använda NPS-tillägget måste lokala användare synkroniseras med Azure AD och aktiveras för MFA. Det här avsnittet förutsätter att lokala användare synkroniseras med Azure AD med AD Connect. Information om Azure AD connect finns i [Integrera dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md).

### <a name="azure-active-directory-guid-id"></a>AZURE Active Directory GUID-ID

Om du vill installera NPS-tillägget måste du känna till GUID för Azure AD. Instruktioner för att hitta GUID för Azure AD finns nedan.

## <a name="configure-multi-factor-authentication"></a>Konfigurera multifaktorautentisering

Det här avsnittet innehåller instruktioner för hur du integrerar Azure MFA med fjärrskrivbordsgatewayen. Som administratör måste du konfigurera Azure MFA-tjänsten innan användare själv kan registrera sina multifaktorenheter eller program.

Följ stegen i [Komma igång med Azure Multi-Factor Authentication i molnet](howto-mfa-getstarted.md) för att aktivera MFA för dina Azure AD-användare.

### <a name="configure-accounts-for-two-step-verification"></a>Konfigurera konton för tvåstegsverifiering

När ett konto har aktiverats för MFA kan du inte logga in på resurser som styrs av MFA-principen förrän du har konfigurerat en betrodd enhet som ska användas för den andra autentiseringsfaktorn och har autentiserats med tvåstegsverifiering.

Följ stegen i [Vad betyder Azure Multi-Factor Authentication för mig?](../user-help/multi-factor-authentication-end-user.md)

## <a name="install-and-configure-nps-extension"></a>Installera och konfigurera NPS-tillägg

Det här avsnittet innehåller instruktioner för hur du konfigurerar RDS-infrastruktur för att använda Azure MFA för klientautentisering med fjärrskrivbordsgatewayen.

### <a name="acquire-azure-active-directory-guid-id"></a>Skaffa Azure Active Directory GUID-ID

Som en del av konfigurationen av NPS-tillägget måste du ange administratörsautentiseringsuppgifter och Azure AD-ID för din Azure AD-klientorganisation. Följande steg visar hur du hämtar klient-ID.

1. Logga in på [Azure-portalen](https://portal.azure.com) som global administratör för Azure-klienten.
1. På Azure-portalmenyn väljer du **Azure Active Directory**eller söker efter och väljer Azure Active **Directory** på valfri sida.
1. Välj **egenskaper**.
1. Bredvid katalog-ID:t i egenskapsbladet klickar du på **ikonen Kopiera,** som visas nedan, för att kopiera ID:t till Urklipp.

   ![Hämta katalog-ID:et från Azure-portalen](./media/howto-mfa-nps-extension-rdg/azure-active-directory-id-in-azure-portal.png)

### <a name="install-the-nps-extension"></a>Installera NPS-tillägget

Installera NPS-tillägget på en server som har NPS-rollen (Network Policy and Access Services). Detta fungerar som RADIUS-server för din design.

> [!Important]
> Se till att du inte installerar NPS-tillägget på servern för fjärrskrivbordsgateway.
>

1. Ladda ner [NPS-tillägget](https://aka.ms/npsmfa).
1. Kopiera den körbara filen för inställningar (NpsExtnForAzureMfaInstaller.exe) till NPS-servern.
1. Dubbelklicka på **NpsExtnForAzureMfaInstaller.exe på NPS-servern**. Om du uppmanas till det klickar du på **Kör**.
1. I dialogrutan NPS-tillägg för Installationsprogrammet för Azure MFA läser du villkoren för programvarulicenser, **kontrollerar att jag godkänner licensvillkoren**och klickar på **Installera**.
1. Klicka på **Stäng**i dialogrutan INSTALLATIONSPROGRAM för NPS-tillägg för Azure MFA.

### <a name="configure-certificates-for-use-with-the-nps-extension-using-a-powershell-script"></a>Konfigurera certifikat för användning med NPS-tillägget med ett PowerShell-skript

Därefter måste du konfigurera certifikat för användning av NPS-tillägget för att säkerställa säker kommunikation och säkerhet. NPS-komponenterna innehåller ett Windows PowerShell-skript som konfigurerar ett självsignerat certifikat för användning med NPS.

Skriptet utför följande åtgärder:

* Skapar ett självsignerat certifikat
* Associerar offentlig nyckel för certifikat till tjänsthuvudnamn på Azure AD
* Lagrar certifikatet i den lokala maskinbutiken
* Ger åtkomst till certifikatets privata nyckel till nätverksanvändaren
* Startar om tjänsten Network Policy Server

Om du vill använda dina egna certifikat måste du associera den offentliga nyckeln för ditt certifikat till tjänstens huvudnamn på Azure AD och så vidare.

Om du vill använda skriptet anger du tillägget med dina Azure AD-administratörsbehörighet och Azure AD-klient-ID som du kopierade tidigare. Kör skriptet på varje NPS-server där du installerade NPS-tillägget. Gör något av följande:

1. Öppna en administrativ Windows PowerShell-prompt.
1. Skriv i `cd 'c:\Program Files\Microsoft\AzureMfa\Config'`PowerShell-prompten och tryck på **RETUR**.
1. Skriv `.\AzureMfaNpsExtnConfigSetup.ps1`och tryck på **RETUR**. Skriptet kontrollerar om Azure Active Directory PowerShell-modulen är installerad. Om det inte är installerat installeras modulen åt dig.

   ![Köra AzureMfaNpsExtnConfigSetup.ps1 i Azure AD PowerShell](./media/howto-mfa-nps-extension-rdg/image4.png)
  
1. När skriptet har verifierat installationen av PowerShell-modulen visas dialogrutan Azure Active Directory PowerShell-modul. Ange dina autentiseringsuppgifter och lösenord för Azure AD-administratör i dialogrutan och klicka på **Logga in**.

   ![Autentisera till Azure AD i PowerShell](./media/howto-mfa-nps-extension-rdg/image5.png)

1. När du uppmanas till det katalog-ID som du kopierade till Urklipp tidigare och tryck på **RETUR**.

   ![Mata in katalog-ID i PowerShell](./media/howto-mfa-nps-extension-rdg/image6.png)

1. Skriptet skapar ett självsignerat certifikat och utför andra konfigurationsändringar. Utdata ska vara som bilden som visas nedan.

   ![Utdata från PowerShell som visar självsignerat certifikat](./media/howto-mfa-nps-extension-rdg/image7.png)

## <a name="configure-nps-components-on-remote-desktop-gateway"></a>Konfigurera NPS-komponenter på gateway för fjärrskrivbord

I det här avsnittet konfigurerar du auktoriseringsprinciperna för anslutningsauktorisering för fjärrskrivbordsgateway och andra RADIUS-inställningar.

Autentiseringsflödet kräver att RADIUS-meddelanden utbyts mellan fjärrskrivbordsgatewayen och NPS-servern där NPS-tillägget är installerat. Det innebär att du måste konfigurera RADIUS-klientinställningarna för både Fjärrskrivbordsgateway och NPS-servern där NPS-tillägget är installerat.

### <a name="configure-remote-desktop-gateway-connection-authorization-policies-to-use-central-store"></a>Konfigurera auktoriseringsprinciper för anslutning till fjärrskrivbordsgateway för att använda centralt arkiv

Auktoriseringsprinciper för fjärrskrivbordsanslutning (RD CAPs) anger kraven för anslutning till en fjärrskrivbordsgatewayserver. Certifikatutfärdare för fjärrskrivbord kan lagras lokalt (standard) eller så kan de lagras i ett centralt cap-arkiv för fjärrskrivbord som kör NPS. Om du vill konfigurera integrering av Azure MFA med RDS måste du ange användningen av ett centralt arkiv.

1. Öppna **Serverhanteraren**på servern för fjärrskrivbordsgateway .
1. Klicka på **Verktyg**på menyn, peka på **Fjärrskrivbordstjänster**och klicka sedan på **Hanteraren för fjärrskrivbordsgateway**.
1. Högerklicka på ** \[Servernamn\] (Lokalt)** i hanteraren för fjärrskrivbordsgateway och klicka på **Egenskaper**.
1. Välj fliken CAP Store för **fjärrskrivbordslagring** i dialogrutan Egenskaper.
1. Välj Central server som **kör NPS**på fliken Cap Store för fjärrskrivbord. 
1. I fältet **Ange ett namn eller en IP-adress för servern som kör NPS-fältet** skriver du IP-adressen eller servernamnet för den server där du installerade NPS-tillägget.

   ![Ange namnet eller IP-adressen för NPS-servern](./media/howto-mfa-nps-extension-rdg/image10.png)
  
1. Klicka på **Lägg till**.
1. Ange en delad hemlighet i dialogrutan **Delad hemlighet** och klicka sedan på **OK**. Se till att du registrerar den här delade hemligheten och lagrar posten på ett säkert sätt.

   >[!NOTE]
   >Delad hemlighet används för att skapa förtroende mellan RADIUS-servrarna och klienterna. Skapa en lång och komplex hemlighet.
   >

   ![Skapa en delad hemlighet för att skapa förtroende](./media/howto-mfa-nps-extension-rdg/image11.png)

1. Stäng dialogrutan genom att klicka på **OK**.

### <a name="configure-radius-timeout-value-on-remote-desktop-gateway-nps"></a>Konfigurera RADIUS-timeout-värde på NPS för fjärrskrivbordsgateway

För att säkerställa att det finns tid att validera användarnas autentiseringsuppgifter, utföra tvåstegsverifiering, ta emot svar och svara på RADIUS-meddelanden, är det nödvändigt att justera RADIUS-timeout-värdet.

1. Öppna Serverhanteraren på servern för fjärrskrivbordsgateway. Klicka på **Verktyg**på menyn och sedan på **Nätverksprincipserver**.
1. Expandera **RADIUS-klienter och servrar** **i konsolen NPS (Local)** och välj **FjärrRADIE-server**.

   ![Hanteringskonsol för nätverksprincipserver som visar fjärr-RADIUS-server](./media/howto-mfa-nps-extension-rdg/image12.png)

1. Dubbelklicka på **TS GATEWAY SERVER GROUP**i informationsfönstret.

   >[!NOTE]
   >Den här RADIUS-servergruppen skapades när du konfigurerade den centrala servern för NPS-principer. Fjärrskrivbordsgatewayen vidarebefordrar RADIUS-meddelanden till den här servern eller servergruppen, om fler än en i gruppen.
   >

1. I dialogrutan Egenskaper för **TS GATEWAY SERVER GROUP** markerar du IP-adressen eller namnet på den NPS-server som du konfigurerade för att lagra certifikatutfärdare för fjärrskrivbord och klickar sedan på **Redigera**.

   ![Välj IP- eller namnet på DEN NPS-server som konfigurerats tidigare](./media/howto-mfa-nps-extension-rdg/image13.png)

1. Välj fliken **Belastningsutjämning i** dialogrutan **Redigera RADIUS-server.**
1. Ändra standardvärdet från 3 till ett värde mellan 30 och 60 sekunder i fältet **Antal sekunder utan svar innan begäran betraktas som borttappad** på fliken **Belastningsutjämning.**
1. I **antalet sekunder mellan begäranden när servern identifieras som otillgängligt** ändrar du standardvärdet på 30 sekunder till ett värde som är lika med eller större än det värde som du angav i föregående steg.

   ![Redigera timeout-inställningar för Radius Server på fliken Belastningsutjämning](./media/howto-mfa-nps-extension-rdg/image14.png)

1. Klicka på **OK** två gånger om du vill stänga dialogrutorna.

### <a name="verify-connection-request-policies"></a>Verifiera principer för anslutningsbegäran

När du konfigurerar fjärrskrivbordsgatewayen så att den använder ett centralt principarkiv för principer för anslutningsauktorisering är fjärrskrivbordsgatewayen konfigurerad för att vidarebefordra CAP-begäranden till NPS-servern. NPS-servern med Azure MFA-tillägget installerat bearbetar RADIUS-åtkomstbegäran. Följande steg visar hur du verifierar standardprincipen för anslutningsbegäran.  

1. Expandera **principer**i konsolen NPS (Local) på fjärrskrivbordsgatewayen och välj **principer för anslutningsbegäran**.
1. Dubbelklicka på **TS GATEWAY AUKTORISERINGSPRINCIPEN**.
1. Klicka på fliken Inställningar i dialogrutan Egenskaper **för** **AUKTORISERINGSPRINCIPEN för TS GATEWAY.**
1. Klicka på **Autentisering**under Vidarebefordra anslutningsbegäran på fliken **Inställningar.** RADIUS-klienten är konfigurerad för att vidarebefordra begäranden om autentisering.

   ![Konfigurera autentiseringsinställningar som anger servergruppen](./media/howto-mfa-nps-extension-rdg/image15.png)

1. Klicka på **Avbryt**.

>[!NOTE]
> Mer information om hur du skapar en princip för anslutningsbegäran finns i artikeln, Konfigurera dokumentation för [anslutningsbegäran för](https://docs.microsoft.com/windows-server/networking/technologies/nps/nps-crp-configure#add-a-connection-request-policy) samma. 

## <a name="configure-nps-on-the-server-where-the-nps-extension-is-installed"></a>Konfigurera NPS på servern där NPS-tillägget är installerat

DEN NPS-server där NPS-tillägget är installerat måste kunna utbyta RADIUS-meddelanden med NPS-servern på fjärrskrivbordsgatewayen. Om du vill aktivera det här meddelandeutbytet måste du konfigurera NPS-komponenterna på servern där TJÄNSTEN NPS-tillägg är installerad.

### <a name="register-server-in-active-directory"></a>Registrera server i Active Directory

Om NPS-servern ska fungera korrekt i det här scenariot måste den registreras i Active Directory.

1. Öppna **Serverhanteraren**på NPS-servern .
1. Klicka på **Verktyg**i Serverhanteraren och klicka sedan på **Nätverksprincipserver**.
1. Högerklicka på **NPS (Lokal)** i konsolen Nätverksprincipserver och klicka sedan på **Registrera server i Active Directory**.
1. Klicka på **OK** två gånger.

   ![Registrera NPS-servern i Active Directory](./media/howto-mfa-nps-extension-rdg/image16.png)

1. Lämna konsolen öppen för nästa procedur.

### <a name="create-and-configure-radius-client"></a>Skapa och konfigurera RADIUS-klient

Gateway för fjärrskrivbord måste konfigureras som RADIUS-klient till NPS-servern.

1. Högerklicka på **RADIUS-klienter** i **NPS-konsolen (Lokal)** på NPS-servern där NPS-tillägget är installerat och klicka på **Ny**.

   ![Skapa en ny RADIUS-klient i NPS-konsolen](./media/howto-mfa-nps-extension-rdg/image17.png)

1. Ange **New RADIUS Client** ett eget namn, till exempel _Gateway_och IP-adressen eller DNS-namnet på servern för fjärrskrivbordsgateway.
1. I fälten **Delad hemlighet** och Bekräfta **delade hemliga** anger du samma hemlighet som du använde tidigare.

   ![Konfigurera ett eget namn och IP- eller DNS-adressen](./media/howto-mfa-nps-extension-rdg/image18.png)

1. Stäng dialogrutan Ny RADIUS-klient genom att klicka **på OK.**

### <a name="configure-network-policy"></a>Konfigurera nätverksprincip

Kom ihåg att NPS-servern med Azure MFA-tillägget är det utsedda centrala principarkivet för cap -principen (Connection Authorization Policy). Därför måste du implementera en cap på NPS-servern för att auktorisera giltiga anslutningsbegäranden.  

1. Öppna KONSOLEN NPS (Local) på NPS-servern, expandera **principer**och klicka på **Nätverksprinciper**.
1. Högerklicka på **Anslutningar till andra åtkomstservrar**och klicka på **Dubblettprincip**.

   ![Duplicera anslutningen till andra åtkomstservrar](./media/howto-mfa-nps-extension-rdg/image19.png)

1. Högerklicka på **Kopiera anslutningar till andra åtkomstservrar**och klicka på **Egenskaper**.
1. Ange ett lämpligt namn i **principnamnet**i dialogrutan **Kopiera anslutningar till andra åtkomstservrar** _RDG_CAP._ Kontrollera **princip aktiverad**och välj **Bevilja åtkomst**. Välj **Fjärrskrivbordsgateway**i **Typ av nätverksåtkomstserver**eller lämna den som **ospecificerad**.

   ![Namnge principen, aktivera och bevilja åtkomst](./media/howto-mfa-nps-extension-rdg/image21.png)

1. Klicka på fliken **Begränsningar** och kontrollera **Tillåt klienter att ansluta utan att förhandla om en autentiseringsmetod**.

   ![Ändra autentiseringsmetoder så att klienter kan ansluta](./media/howto-mfa-nps-extension-rdg/image22.png)

1. Du kan också klicka på fliken **Villkor** och lägga till villkor som måste uppfyllas för att anslutningen ska kunna auktoriseras, till exempel medlemskap i en viss Windows-grupp.

   ![Ange även anslutningsvillkor](./media/howto-mfa-nps-extension-rdg/image23.png)

1. Klicka på **OK**. När du uppmanas att visa motsvarande hjälpavsnitt klickar du på **Nej**.
1. Kontrollera att den nya principen finns högst upp i listan, att principen är aktiverad och att den ger åtkomst.

   ![Flytta din princip till toppen av listan](./media/howto-mfa-nps-extension-rdg/image24.png)

## <a name="verify-configuration"></a>Verifiera konfiguration

För att verifiera konfigurationen måste du logga in på fjärrskrivbordsgatewayen med en lämplig RDP-klient. Var noga med att använda ett konto som tillåts av dina principer för anslutningsauktorisering och är aktiverat för Azure MFA.

Som visas i bilden nedan kan du använda webbsidan **För fjärrskrivbordswebbåtkomst.**

![Testa i webbåtkomst till fjärrskrivbord](./media/howto-mfa-nps-extension-rdg/image25.png)

När du har angett dina autentiseringsuppgifter för primär autentisering visar dialogrutan Anslutning till fjärrskrivbord en status för att initiera fjärranslutning, som visas nedan. 

Om du har autentiserat med den sekundära autentiseringsmetoden som du tidigare konfigurerat i Azure MFA är du ansluten till resursen. Men om den sekundära autentiseringen inte lyckas nekas du åtkomst till resursen. 

![Anslutning till fjärrskrivbord initierar en fjärranslutning](./media/howto-mfa-nps-extension-rdg/image26.png)

I exemplet nedan används Authenticator-appen på en Windows-telefon för att tillhandahålla sekundär autentisering.

![Exempel på Windows Phone Authenticator-app som visar verifiering](./media/howto-mfa-nps-extension-rdg/image27.png)

När du har autentiserats med den sekundära autentiseringsmetoden loggas du in i fjärrskrivbordsgatewayen som vanligt. Men eftersom du måste använda en sekundär autentiseringsmetod med hjälp av en mobilapp på en betrodd enhet är inloggningsprocessen säkrare än annars.

### <a name="view-event-viewer-logs-for-successful-logon-events"></a>Visa Loggboksloggar för lyckade inloggningshändelser

Om du vill visa de lyckade inloggningshändelserna i Loggboken i Windows kan du utfärda följande Windows PowerShell-kommando för att fråga windows terminaltjänst- och Windows-säkerhetsloggarna.

Om du vill fråga lyckade inloggningshändelser i gateway-driftloggarna _(Loggboken\Program- och tjänstloggar\Microsoft\Windows\TerminalServices-Gateway\Operational)_ använder du följande PowerShell-kommandon:

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '300'} | FL`
* Det här kommandot visar Windows-händelser som visar att användaren uppfyllde resursauktoriseringsprincipen (RD RAP) och har beviljats åtkomst.

![Visa händelser med PowerShell](./media/howto-mfa-nps-extension-rdg/image28.png)

* `Get-WinEvent -Logname Microsoft-Windows-TerminalServices-Gateway/Operational | where {$_.ID -eq '200'} | FL`
* Det här kommandot visar de händelser som visas när användaren uppfyllde kraven för anslutningsauktorisering.

![visa anslutningsauktoriseringsprincipen med PowerShell](./media/howto-mfa-nps-extension-rdg/image29.png)

Du kan också visa den här loggen och filtret på händelse-ID: er, 300 och 200. Om du vill fråga efter lyckade inloggningshändelser i loggboken Säkerhet använder du följande kommando:

* `Get-WinEvent -Logname Security | where {$_.ID -eq '6272'} | FL`
* Det här kommandot kan köras på antingen det centrala NPS- eller servern för fjärrskrivbordsgateway.

![Exempel på lyckade inloggningshändelser](./media/howto-mfa-nps-extension-rdg/image30.png)

Du kan också visa säkerhetsloggen eller den anpassade vyn Nätverksprincip och Åtkomsttjänster, som visas nedan:

![Loggboken för nätverksprincip och åtkomsttjänster](./media/howto-mfa-nps-extension-rdg/image31.png)

På servern där du installerade NPS-tillägget för Azure MFA kan du hitta Loggboken programloggar som är specifika för tillägget på _Application and Services Logs\Microsoft\AzureMfa_.

![Loggboken AuthZ programloggar](./media/howto-mfa-nps-extension-rdg/image32.png)

## <a name="troubleshoot-guide"></a>Felsökningsguide

Om konfigurationen inte fungerar som förväntat är det första stället att börja felsöka att verifiera att användaren är konfigurerad för att använda Azure MFA. Låt användaren ansluta till [Azure-portalen](https://portal.azure.com). Om användare uppmanas att verifiera sekundär verifiering och autentisera kan du eliminera en felaktig konfiguration av Azure MFA.

Om Azure MFA fungerar för användaren/användarna bör du granska relevanta händelseloggar. Dessa inkluderar säkerhetshändelse, gateway-drift och Azure MFA-loggar som beskrivs i föregående avsnitt.

Nedan visas ett exempel på säkerhetsloggen som visar en misslyckad inloggningshändelse (händelse-ID 6273).

![Exempel på en misslyckad inloggningshändelse](./media/howto-mfa-nps-extension-rdg/image33.png)

Nedan visas en relaterad händelse från AzureMFA-loggarna:

![Exempel på Azure MFA-logg i Loggboken](./media/howto-mfa-nps-extension-rdg/image34.png)

Om du vill utföra avancerade felsökningsalternativ läser du loggfilerna för NPS-databasformat där NPS-tjänsten är installerad. Dessa loggfiler skapas i mappen _%SystemRoot%\System32\Loggar_ som kommaavgränsade textfiler.

En beskrivning av dessa loggfiler finns i [Tolka LOGGFILER för NPS-databasformat](https://technet.microsoft.com/library/cc771748.aspx). Posterna i dessa loggfiler kan vara svåra att tolka utan att importera dem till ett kalkylblad eller en databas. Du kan hitta flera IAS-tolkare online för att hjälpa dig att tolka loggfilerna.

Bilden nedan visar resultatet av en sådan nedladdningsbar [shareware ansökan](https://www.deepsoftware.com/iasviewer).

![Exempel på IAS-tolkar för Shareware-appen](./media/howto-mfa-nps-extension-rdg/image35.png)

Slutligen, för ytterligare felsökningsalternativ, kan du använda en protokollanalysator, till exempel [Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Bilden nedan från Microsoft Message Analyzer visar nätverkstrafik filtrerad på RADIUS-protokollet som innehåller användarnamnet **CONTOSO\AliceC**.

![Microsoft Message Analyzer visar filtrerad trafik](./media/howto-mfa-nps-extension-rdg/image36.png)

## <a name="next-steps"></a>Nästa steg

[Skaffa Azure Multi-Factor Authentication](concept-mfa-licensing.md)

[Fjärrskrivbordsgateway och Azure Multi-Factor Authentication Server med RADIUS](howto-mfaserver-nps-rdg.md)

[Integrerar dina lokala kataloger med Azure Active Directory](../hybrid/whatis-hybrid-identity.md)
