---
title: Konfigurera en Azure Active Directory-hybridanslutning för federerade domäner | Microsoft Docs
description: Lär dig hur du konfigurerar Azure Active Directory-hybridanslutningar för federerade domäner.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a61c89199c89f09b5cc0e553dbbf48655ad1b6a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239100"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Självstudier: Konfigurera Hybrid Azure Active Directory-anslutningar för federerade domäner

Precis som en användare i organisationen är en enhet en kärnidentitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när som helst och från vilken plats som helst. Du kan uppnå det här målet genom att skapa enhetsidentiteter och hantera dem i Azure Active Directory (Azure AD) med hjälp av någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Genom att föra dina enheter till Azure AD maximeras användarproduktiviteten genom enkel inloggning (SSO) i molnet och lokala resurser. Du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) samtidigt.

En federerad miljö bör ha en identitetsprovider som stöder följande krav. Om du har en federerad miljö med Hjälp av Active Directory Federation Services (AD FS) stöds redan nedanstående krav.

- **WIAORMULTIAUTHN-anspråk:** Det här anspråket krävs för att göra hybrid Azure AD-koppling för Windows-enheter på lägre nivå.
- **WS-Trust-protokollet:** Det här protokollet krävs för att autentisera Windows nuvarande hybrid Azure AD-anslutna enheter med Azure AD.
  När du använder AD FS måste du aktivera följande WS-Trust-slutpunkter:`/adfs/services/trust/2005/windowstransport`
   `/adfs/services/trust/13/windowstransport`
   `/adfs/services/trust/2005/usernamemixed`
   `/adfs/services/trust/13/usernamemixed`
   `/adfs/services/trust/2005/certificatemixed`
   `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Både **adfs/services/trust/2005/windowstransport** och **adfs/services/trust/13/windowstransport** ska aktiveras som endast intranätvända slutpunkter och får INTE exponeras som extranätvända slutpunkter via webbprogramproxyn. Mer information om hur du inaktiverar WS-Trust Windows-slutpunkter finns i [Inaktivera WS-Trust Windows-slutpunkter i proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Du kan se vilka slutpunkter som är aktiverade via AD FS-hanteringskonsolen under > **Tjänstslutpunkter**. **Service**

I den här självstudien får du lära dig hur du konfigurerar hybrid Azure AD-anslutning för Active Directory-domänkopplingsdatorer i en federerad miljö med hjälp av AD FS.

Lär dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera Windows-enheter på nednivå
> * Verifiera registreringen
> * Felsöka

## <a name="prerequisites"></a>Krav

Den här självstudien förutsätter att du är bekant med följande artiklar:

- [Vad är en enhetsidentitet?](overview.md)
- [Så här planerar du implementering av hybrid-Azure AD-koppling](hybrid-azuread-join-plan.md)
- [Så här gör du kontrollerad validering av hybrid-Azure AD-koppling](hybrid-azuread-join-control.md)

För att kunna konfigurera scenariot i den här självstudien behöver du:

- Windows Server 2012 R2 med AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0 eller senare

Från och med version 1.1.819.0 innehåller Azure AD Connect en guide som du kan använda för att konfigurera hybrid Azure AD-koppling. Guiden förenklar konfigurationsprocessen avsevärt. Relaterad guide:

- Konfigurerar tjänstanslutningspunkterna (SCPs) för enhetsregistrering
- Säkerhetskopierar ditt befintliga förlitande part-förtroende för Azure AD
- Uppdaterar anspråksreglerna i ditt Azure AD-förtroende

Konfigurationsstegen i den här artikeln baseras på hur du använder Azure AD Connect-guiden. Om du har installerat en tidigare version av Azure AD Connect måste du uppgradera den till 1.1.819 eller senare för att kunna använda guiden. Om det inte är ett alternativ för dig att installera den senaste versionen av Azure AD Connect läser du hur du [konfigurerar hybrid-Azure AD-koppling manuellt](hybrid-azuread-join-manual.md).

Hybrid Azure AD-anslutning kräver att enheter har åtkomst till följande Microsoft-resurser inifrån organisationens nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Organisationens security token service (STS) (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com`(Om du använder eller planerar att använda sömlös SSO)

Från och med Windows 10 1803, om den momentana hybrid Azure AD-anslutningen för en federerad miljö med ad FS misslyckas, förlitar vi oss på Azure AD Connect för att synkronisera datorobjektet i Azure AD som sedan används för att slutföra enhetsregistreringen för hybrid Azure AD-anslutning. Kontrollera att Azure AD Connect har synkroniserat datorobjekten på de enheter som du vill ska vara hybrid Azure AD som är anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter måste du också konfigurera organisationsenheterna så att de synkroniseras i Azure AD Connect. Mer information om hur du synkroniserar datorobjekt med Hjälp av Azure AD Connect finns i [Konfigurera filtrering med hjälp av Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Om din organisation kräver åtkomst till internet via en utgående proxy rekommenderar Microsoft [att implementera WPAD (Web Proxy Auto-Discovery)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) för att aktivera Windows 10-datorer för enhetsregistrering med Azure AD. Om du stöter på problem med att konfigurera och hantera WPAD läser [du Felsöka automatisk identifiering](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och vill konfigurera proxyinställningar på datorn kan du göra det från och med Windows 10 1709. Mer information finns i [Konfigurera WinHTTP-inställningar med hjälp av ett grupprincipobjekt (GPO).](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/)

> [!NOTE]
> Om du konfigurerar proxyinställningar på datorn med winhttp-inställningar kan alla datorer som inte kan ansluta till den konfigurerade proxyn inte ansluta till internet.

Om din organisation kräver åtkomst till internet via en autentiserat utgående proxy måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer kör enhetsregistrering med hjälp av datorkontext måste du konfigurera utgående proxyautentisering med hjälp av datorkontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

Om du vill kontrollera om enheten kan komma åt ovanstående Microsoft-resurser under systemkontot kan du använda [skriptet för registrering av testenhet.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en hybrid-Azure AD-koppling med Hjälp av Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient  
- Företagsadministratörens autentiseringsuppgifter för var och en av skogarna
- Autentiseringsuppgifterna för AD FS-administratören

**Så här konfigurerar du en Azure AD-hybridkoppling med Hjälp av Azure AD Connect:**

1. Starta Azure AD Connect och välj sedan **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. På sidan **Ytterligare uppgifter** väljer du **Konfigurera enhetsalternativ**och väljer sedan **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-federated-domains/12.png)

1. På sidan **Översikt** väljer du **Nästa**.

   ![Översikt](./media/hybrid-azuread-join-federated-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation och väljer sedan **Nästa**.

   ![Anslut till Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. På sidan **Enhetsalternativ** väljer du **Konfigurera Hybrid Azure AD-koppling**och väljer sedan **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-federated-domains/15.png)

1. På **SCP-sidan** slutför du följande steg och väljer sedan **Nästa:**

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst. Du måste välja **AD FS-server** om inte din organisation har uteslutande Windows 10-klienter och du har konfigurerat synkronisering av datorer/enheter eller så använder din organisation sömlös SSO.
   1. Välj **Lägg till** om du vill ange autentiseringsuppgifter för företagsadministratören.

1. På sidan **Enhetsoperativsystem** väljer du de operativsystem som enheterna i Active Directory-miljön använder och väljer sedan **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. På **sidan Federationskonfiguration** anger du ad FS-administratörens autentiseringsuppgifter och väljer sedan **Nästa**.

   ![Federationskonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. På sidan **Klar att konfigurera** väljer du **Konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-federated-domains/19.png)

1. På sidan **Konfigurationens klara** väljer du **Avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivera Windows-enheter på nednivå

Om vissa av dina domänanslutna enheter är Windows-enheter på nednivå måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Installera Microsoft Workplace Join för Windows-datorer på nednivå

> [!NOTE]
> Windows 7-supporten upphörde den 14 januari 2020. Support för [Windows 7 har avslutats.](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

Om du vill slutföra hybrid-Azure AD-anslutning till dina Windows-enheter på nednivå och undvika certifikatuppmaningar när enheter autentiseras till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande url:er i den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`
- Organisationens STS (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com`(För sömlös SSO)

Du måste också aktivera **Tillåt uppdateringar av statusfältet via skript** i användarens lokala intranätzon.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installera Microsoft Workplace Join för Windows-datorer på nednivå

Om du vill registrera Windows-enheter på nednivå måste organisationer installera [Microsoft Workplace Join för datorer som inte är Windows 10.](https://www.microsoft.com/download/details.aspx?id=53554) Microsoft Workplace Join för datorer som inte är Windows 10 finns i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett programdistributionssystem som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder standardalternativen för `quiet` tyst installation med parametern. Den aktuella grenen av Configuration Manager erbjuder fördelar jämfört med tidigare versioner, till exempel möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet i systemet som körs i användarkontexten. Uppgiften utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst enheten till Azure AD med hjälp av användarautentiseringsuppgifterna när den har autentiserats med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

För verifiering av enhetsregistreringsstatus i din Azure-klientorganisation kan du använda cmdlet:en **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** i [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

När du använder **Cmdlet Get-MSolDevice** för att kontrollera serviceinformationen:

- Det måste finnas ett objekt med **enhets-ID:t** som matchar ID:t på Windows-klienten.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Den här inställningen motsvarar **tillståndet Hybrid Azure AD-anslutna** under **Enheter** i Azure AD-portalen.
- För enheter som används i villkorlig åtkomst måste värdet för **Aktiverad** vara **Sant** och **DeviceTrustLevel** måste **hanteras**.

**Så här kontrollerar du serviceinformationen:**

1. Öppna Windows PowerShell som administratör.
1. Ange `Connect-MsolService` för att ansluta till din Azure-klient.  
1. Ange `get-msoldevice -deviceId <deviceId>`.
1. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du får problem med att slutföra hybrid-Azure AD-anslutning för domänanslutna Windows-enheter läser du:

- [Felsöka hybrid Azure AD-anslutning för Windows-aktuella enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid-Azure AD-koppling för Windows-enheter på nivåer](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar enhetsidentiteter med hjälp av Azure-portalen](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
