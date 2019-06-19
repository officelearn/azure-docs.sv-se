---
title: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner | Microsoft Docs
description: Konfigurera Azure Active Directory-hybridanslutning för hanterade domäner.
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
ms.openlocfilehash: b24888934d7e89a13b1b07b7138be476575fc306
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204622"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudier: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner

Som en användare i din organisation är en enhet en core-identitet som du vill skydda. Du kan använda en enhetsidentitet för att skydda dina resurser när som helst och var som helst. Du kan göra det här målet genom att föra enhetsidentiteter och hantera dem i Azure Active Directory (Azure AD) med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Få ut dina enheter till Azure AD maximerar användarproduktivitet via enkel inloggning (SSO) mellan molnet och lokala resurser. Du kan skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) på samma gång.

I den här självstudien får lära du att konfigurera hybrid Azure AD-anslutning för enheter för Active Directory-domänanslutna datorer i en hanterad miljö. 

En hanterad miljö kan vara distribueras antingen via [lösenordshashsynkronisering (PHS)](../hybrid/whatis-phs.md) eller [direktautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md). Dessa scenarier måste inte du konfigurera en federationsserver för autentisering.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien förutsätter att du är bekant med de här artiklarna:

- [Vad är en enhetsidentitet?](overview.md)
- [Hur du planerar din hybrid Azure AD join-implementering](hybrid-azuread-join-plan.md)
- [Hur du gör kontrollerad validering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Om du vill konfigurera scenariot i den här artikeln, måste den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller senare) installerat.

Kontrollera att Azure AD Connect har synkroniserats datorobjekt av de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU), måste du också konfigurera organisationsenheter ska synkroniseras i Azure AD Connect. Mer information om hur du synkroniserar datorobjekt med hjälp av Azure AD Connect finns [konfigurera filtrering med hjälp av Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Från och med version 1.1.819.0, innehåller Azure AD Connect en guide som du kan använda för att konfigurera hybrid Azure AD-anslutning. Guiden förenklar avsevärt konfigurationsprocessen. Guiden konfigurerar tjänstanslutningspunkter (SCP) för registrering av enheten.

Konfigurationsstegen i den här artikeln baseras på med hjälp av guiden i Azure AD Connect.

Hybrid Azure AD-anslutning kräver att enheter ska ha åtkomst till följande Microsoft-resurser från i din organisations nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda sömlös enkel inloggning)

Om din organisation kräver åtkomst till internet via en utgående proxy, Microsoft rekommenderar [implementera Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) att aktivera Windows 10-datorer för enhetsregistrering med Azure AD. Om det uppstår problem konfigurera och hantera WPAD Se [Felsöka automatisk identifiering](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och du behöver för att konfigurera proxyinställningarna på datorn, kan du göra det från och med Windows 10 1709. Mer information finns i [konfigurera WinHTTP-inställningar med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningarna på datorn med hjälp av WinHTTP-inställningarna, att alla datorer som inte kan ansluta till den konfigurerade proxyn kunna ansluta till internet.

Om din organisation kräver åtkomst till internet via en autentiserad proxyserver för utgående, måste du se till att din Windows 10-datorer kan autentisera till utgående proxy. Eftersom Windows 10-datorer kör enhetsregistrering genom att använda datorns kontext, måste du konfigurera utgående proxy-autentisering med hjälp av datorns kontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en Hybrid Azure AD-anslutning med Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient
- Administratörsautentiseringsuppgifterna för var och en av skogarna

**Konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect:**

1. Starta Azure AD Connect och välj sedan **konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-managed-domains/11.png)

1. På den **ytterligare uppgifter** väljer **konfigurera Enhetsalternativ**, och välj sedan **nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/12.png)

1. På den **översikt** väljer **nästa**.

   ![Översikt](./media/hybrid-azuread-join-managed-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klient.  

   ![Anslut till Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. På den **Enhetsalternativ** väljer **konfigurera Hybrid Azure AD-anslutning**, och välj sedan **nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/15.png)

1. På den **SCP** för varje skog där du vill att Azure AD Connect för att konfigurera SCP: N, utför följande steg och välj sedan **nästa**:

   ![SCP](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst.
   1. Välj **Lägg till** att ange autentiseringsuppgifterna som företagsadministratör.

1. På den **enhetsoperativsystem** , markera operativsystem som enheterna med din Active Directory-miljö och välj sedan **nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-managed-domains/17.png)

1. På den **redo att konfigurera** väljer **konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-managed-domains/19.png)

1. På den **Configuration slutföra** väljer **avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivera Windows äldre enheter

Om några av dina domänanslutna enheter är Windows äldre enheter, måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös SSO
- Installera Microsoft Workplace Join för Windows äldre datorer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

o Slutför hybrid Azure AD join för dina äldre Windows-enheter och för att undvika certifikat när enheter ska autentiseras mot Azure AD kan du skicka en princip till dina domänanslutna enheter att lägga till följande webbadresser i zonen Lokalt intranät i Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Du måste också aktivera **tillåta uppdateringar till statusfältet via skript** i användarens lokala intranätzonen.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös SSO

För att slutföra hybrid Azure AD join för dina Windows äldre enheter i en hanterad domän använder som [PHS]... /hybrid/whatis-phs.MD) eller [PTA](../hybrid/how-to-connect-pta.md) som autentiseringsmetod din Azure AD-molnet måste också [konfigurerar sömlös SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installera Microsoft Workplace Join för Windows äldre datorer

Om du vill registrera Windows äldre enheter, organisationer måste installera [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join för Windows 10-datorer är tillgängligt i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med den `quiet` parametern. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarkontexten. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften tyst kopplar ihop enheten med Azure AD med hjälp av autentiseringsuppgifter för användare när den autentiserar med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

Du kan använda för att verifiera enheten registreringstillståndet i din Azure-klient, den **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** cmdlet i den [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

När du använder den **Get-MSolDevice** cmdlet för att kontrollera service:

- Ett objekt med den **enhets-ID** som matchar ID på Windows klient måste finnas.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Den här inställningen motsvarar den **Hybrid Azure AD-ansluten** tillstånd på den **enheter** i Azure AD-portalen.
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
