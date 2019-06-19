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
ms.openlocfilehash: 738b4f47054081f0fb1b1a530bdf21cbf07a7726
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204700"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Självstudier: Konfigurera Azure Active Directory-hybridanslutningar för federerade domäner

Som en användare i din organisation är en enhet en core-identitet som du vill skydda. Du kan använda en enhetsidentitet för att skydda dina resurser när som helst och var som helst. Du kan göra det här målet genom att föra enhetsidentiteter och hantera dem i Azure Active Directory (Azure AD) med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Få ut dina enheter till Azure AD maximerar användarproduktivitet via enkel inloggning (SSO) mellan molnet och lokala resurser. Du kan skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) på samma gång.

Lär dig hur du konfigurerar hybrid Azure AD-anslutning för enheter för Active Directory-domänanslutna datorer i en federerad miljö med hjälp av Active Directory Federation Services (AD FS) i den här självstudien.

> [!NOTE]
> Om din federerad miljö använder en identitetsprovider än AD FS, måste du kontrollera att din identitetsprovider har stöd för WS-Trust-protokollet. WS-Trust krävs att autentisera din aktuella Windows-hybrid Azure AD-anslutna enheter med Azure AD. Om du har Windows äldre enheter som du vill skapa en hybrid Azure AD-anslutning, måste din identitetsprovider stöd för WIAORMULTIAUTHN kravet. 

Lär dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera Windows äldre enheter
> * Verifiera registreringen
> * Felsöka

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att you'e bekant med de här artiklarna:

- [Vad är en enhetsidentitet?](overview.md)
- [Hur du planerar din hybrid Azure AD join-implementering](hybrid-azuread-join-plan.md)
- [Hur du gör kontrollerad validering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

För att kunna konfigurera scenariot i den här självstudien behöver du:

- Windows Server 2012 R2 med AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0 eller senare

Från och med version 1.1.819.0, innehåller Azure AD Connect en guide som du kan använda för att konfigurera hybrid Azure AD-anslutning. Guiden förenklar avsevärt konfigurationsprocessen. Relaterad guide:

- Konfigurerar tjänstanslutningspunkter (SCP) för enhetsregistrering
- Säkerhetskopierar ditt befintliga förlitande part-förtroende för Azure AD
- Uppdaterar anspråksreglerna i ditt Azure AD-förtroende

Konfigurationsstegen i den här artikeln baseras på med hjälp av Azure AD Connect-guiden. Om du har en tidigare version av Azure AD Connect är installerat, måste du uppgradera den till 1.1.819 eller senare för att använda guiden. Om du installerar den senaste versionen av Azure AD Connect inte är ett alternativ för dig, se [manuellt konfigurera hybrid Azure AD-anslutning](hybrid-azuread-join-manual.md).

Hybrid Azure AD-anslutning kräver att enheter ska ha åtkomst till följande Microsoft-resurser från i din organisations nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Din organisations säkerhet säkerhetstokentjänst (STS) (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda sömlös enkel inloggning)

Från och med Windows 10-1803 om omedelbara hybrid Azure AD-anslutning för en federerad miljö med hjälp av AD FS misslyckas kan vi förlitar sig på Azure AD Connect att synkronisera datorobjektet i Azure AD som har därefter används för att slutföra enhetsregistrering för hybrid Azure AD-anslutning. Kontrollera att Azure AD Connect har synkroniserats datorobjekt av de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU), måste du också konfigurera organisationsenheter ska synkroniseras i Azure AD Connect. Mer information om hur du synkroniserar datorobjekt med hjälp av Azure AD Connect finns [konfigurera filtrering med hjälp av Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Om din organisation kräver åtkomst till internet via en utgående proxy, Microsoft rekommenderar [implementera Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) att aktivera Windows 10-datorer för enhetsregistrering med Azure AD. Om det uppstår problem konfigurera och hantera WPAD Se [Felsöka automatisk identifiering](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och vill konfigurera proxyinställningarna på datorn, kan du göra det från och med Windows 10 1709. Mer information finns i [konfigurera WinHTTP-inställningar med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningarna på datorn med hjälp av WinHTTP-inställningarna, att alla datorer som inte kan ansluta till den konfigurerade proxyn kunna ansluta till internet.

Om din organisation kräver åtkomst till internet via en autentiserad proxyserver för utgående, måste du se till att din Windows 10-datorer kan autentisera till utgående proxy. Eftersom Windows 10-datorer kör enhetsregistrering genom att använda datorns kontext, måste du konfigurera utgående proxy-autentisering med hjälp av datorns kontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect, behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient  
- Administratörsautentiseringsuppgifterna för var och en av skogarna
- Autentiseringsuppgifterna för AD FS-administratören

**Konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect**:

1. Starta Azure AD Connect och välj sedan **konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. På den **ytterligare uppgifter** väljer **konfigurera Enhetsalternativ**, och välj sedan **nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-federated-domains/12.png)

1. På den **översikt** väljer **nästa**.

   ![Översikt](./media/hybrid-azuread-join-federated-domains/13.png)

1. På den **Anslut till Azure AD** , anger du autentiseringsuppgifterna för en global administratör för Azure AD-klienten och välj sedan **nästa**.

   ![Anslut till Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. På den **Enhetsalternativ** väljer **konfigurera Hybrid Azure AD-anslutning**, och välj sedan **nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-federated-domains/15.png)

1. På den **SCP** , utför följande steg och välj sedan **nästa**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst. Du måste välja **AD FS-servern** såvida inte organisationen har endast Windows 10-klienter och du har konfigurerat datorn/enheten synkronisering eller din organisation använder sömlös enkel inloggning.
   1. Välj **Lägg till** att ange autentiseringsuppgifterna som företagsadministratör.

1. På den **enhetsoperativsystem** väljer du de operativsystem som använder enheterna i Active Directory-miljön, och välj sedan **nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. På den **fedarationskonfiguration** , ange autentiseringsuppgifterna för AD FS-administratören och välj sedan **nästa**.

   ![Federationskonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. På den **redo att konfigurera** väljer **konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-federated-domains/19.png)

1. På den **Configuration slutföra** väljer **avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivera Windows äldre enheter

Om några av dina domänanslutna enheter är Windows äldre enheter, måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Installera Microsoft Workplace Join för Windows äldre datorer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

Du slutför hybrid Azure AD join för dina äldre Windows-enheter och för att undvika certifikat när enheter ska autentiseras mot Azure AD kan du skicka en princip till dina domänanslutna enheter att lägga till följande webbadresser i zonen Lokalt intranät i Internet Explorer:

- `https://device.login.microsoftonline.com`
- Din organisations STS (för federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (För sömlös enkel inloggning)

Du måste också aktivera **tillåta uppdateringar till statusfältet via skript** i användarens lokala intranätzonen.

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installera Microsoft Workplace Join för Windows äldre datorer

Om du vill registrera Windows äldre enheter, organisationer måste installera [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join för Windows 10-datorer är tillgängligt i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med den `quiet` parametern. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarkontexten. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften tyst kopplar ihop enheten med Azure AD med hjälp av autentiseringsuppgifter för användare när den autentiserar med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

Du kan använda för att verifiera enheten registreringstillståndet i din Azure-klient, den **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** cmdlet i den [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

När du använder den **Get-MSolDevice** cmdlet för att kontrollera service:

- Ett objekt med den **enhets-ID** som matchar ID på Windows klient måste finnas.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Den här inställningen motsvarar den **Hybrid Azure AD-ansluten** tillstånd under **enheter** i Azure AD-portalen.
- För enheter som används i villkorlig åtkomst måste värdet för **aktiverad** måste vara **SANT** och **DeviceTrustLevel** måste vara **hanterade**.

**Att kontrollera service**:

1. Öppna Windows PowerShell som administratör.
1. Ange `Connect-MsolService` att ansluta till din Azure-klient.  
1. Ange `get-msoldevice -deviceId <deviceId>`.
1. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du får problem med att slutföra hybrid Azure AD-anslutning för domänanslutna Windows-enheter, se:

- [Felsöka hybrid Azure AD-anslutning för befintliga Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure AD-anslutning för Windows äldre enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hantera enhetsidentiteter med hjälp av Azure portal](device-management-azure-portal.md).

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
