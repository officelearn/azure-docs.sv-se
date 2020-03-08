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
ms.openlocfilehash: 3407214d332cbd333fe019948d254e01d71197fb
ms.sourcegitcommit: bc792d0525d83f00d2329bea054ac45b2495315d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78672286"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudie: Konfigurera Azure Active Directory Join-hybrid för hanterade domäner

Som en användare i din organisation är en enhet en kärn identitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när du vill och var som helst. Du kan uppnå det här målet genom att ta med enhets identiteter och hantera dem i Azure Active Directory (Azure AD) med hjälp av någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Genom att överföra dina enheter till Azure AD kan du maximera användar produktiviteten genom enkel inloggning (SSO) i molnet och lokala resurser. Du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) på samma gång.

I den här självstudien får du lära dig hur du konfigurerar hybrid Azure AD-anslutning för Active Directory domänanslutna datorer enheter i en hanterad miljö. 

En hanterad miljö kan distribueras antingen via [PHS (Password hash Sync)](../hybrid/whatis-phs.md) eller [direktautentisering (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md). De här scenarierna kräver inte att du konfigurerar en Federations Server för autentisering.

I den här guiden får du lära dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure AD-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Förutsättningar

Den här självstudien förutsätter att du är bekant med de här artiklarna:

- [Vad är en enhets identitet?](overview.md)
- [Planera din hybrid Azure AD Join-implementering](hybrid-azuread-join-plan.md)
- [Så här gör du en kontrollerad verifiering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Om du vill konfigurera scenariot i den här artikeln behöver du den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller senare) installerat.

Kontrol lera att Azure AD Connect har synkroniserat dator objekt för de enheter som du vill ska vara hybrid Azure AD-anslutna till Azure AD. Om datorns objekt tillhör vissa organisationsenheter (OU) måste du även konfigurera organisationsenheterna för synkronisering i Azure AD Connect. Mer information om hur du synkroniserar dator objekt med hjälp av Azure AD Connect finns i [Konfigurera filtrering med hjälp av Azure AD Connect](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Från och med version 1.1.819.0 innehåller Azure AD Connect en guide som du kan använda för att konfigurera hybrid Azure AD-anslutning. Guiden fören klar konfigurations processen avsevärt. Guiden konfigurerar tjänst anslutnings punkter (SCP: r) för enhets registrering.

Konfigurations stegen i den här artikeln baseras på hur du använder guiden i Azure AD Connect.

Hybrid Azure AD-anslutning kräver att enheter har åtkomst till följande Microsoft-resurser inifrån din organisations nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (om du använder eller planerar att använda sömlös SSO)

Om din organisation kräver åtkomst till Internet via en utgående proxy rekommenderar Microsoft att [implementera WPAD (Web Proxy Auto-Discovery)](/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) för att aktivera Windows 10-datorer för enhets registrering med Azure AD. Om du stöter på problem med att konfigurera och hantera WPAD, se [Felsöka automatisk identifiering](/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och behöver konfigurera proxyinställningar på datorn kan du göra det från och med Windows 10 1709. Mer information finns i [Konfigurera WinHTTP-inställningar med ett grup princip objekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningar på datorn med hjälp av WinHTTP-inställningar kommer alla datorer som inte kan ansluta till den konfigurerade proxyn inte att kunna ansluta till Internet.

Om din organisation kräver åtkomst till Internet via en autentiserad utgående proxy, måste du se till att dina Windows 10-datorer kan autentiseras mot den utgående proxyn. Eftersom Windows 10-datorer kör enhets registrering med hjälp av dator kontext måste du konfigurera utgående proxyautentisering med hjälp av dator kontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

För att kontrol lera om enheten har åtkomst till ovanstående Microsoft-resurser under system kontot kan du använda anslutnings skriptet för [test av enhets registrering](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0) .

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure AD-anslutning

Om du vill konfigurera en Hybrid Azure AD-anslutning med Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient
- Autentiseringsuppgifter för företags administratör för varje skog

**Konfigurera en hybrid Azure AD-anslutning med hjälp av Azure AD Connect:**

1. Starta Azure AD Connect och välj sedan **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-managed-domains/11.png)

1. På sidan **Ytterligare aktiviteter** väljer du **Konfigurera enhets alternativ**och väljer sedan **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/12.png)

1. På sidan **Översikt** väljer du **Nästa**.

   ![Översikt](./media/hybrid-azuread-join-managed-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klient.  

   ![Anslut till Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. På sidan **enhets alternativ** väljer du **Konfigurera hybrid Azure AD-anslutning**och väljer sedan **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/15.png)

1. På sidan **SCP** , för varje skog där du vill Azure AD Connect konfigurera SCP, slutför du följande steg och väljer sedan **Nästa**:

   ![Tjänstanslutningspunkt](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst.
   1. Välj **Lägg till** för att ange autentiseringsuppgifter för företags administratören.

1. På sidan **enhets operativ system** väljer du de operativ system som enheterna i din Active Directory miljö använder och väljer sedan **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-managed-domains/17.png)

1. På sidan **klar att konfigurera** väljer du **Konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-managed-domains/19.png)

1. På sidan **konfigurationen har slutförts** väljer du **Avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-downlevel-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är Windows-enheter med äldre enheter måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös SSO
- Installera Microsoft Workplace Join för äldre Windows-datorer

> [!NOTE]
> Support för Windows 7 upphörde 14 januari 2020. För mer information [har support för Windows 7 upphört](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra en fullständig Azure AD-anslutning av dina Windows-enheter med äldre versioner och för att undvika certifikat meddelanden när enheter autentiserar till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande URL: er till zonen Lokalt intranät i Internet Utforskarvyn

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Du måste också aktivera **Tillåt uppdateringar av statusfältet via skript** i användarens lokala intranät zon.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös SSO

För att kunna slutföra hybrid Azure AD-anslutning av dina Windows-enheter med äldre versioner i en hanterad domän som använder [PHS](../hybrid/whatis-phs.md) eller [PTA](../hybrid/how-to-connect-pta.md) som din Azure AD-molnbaserad autentiseringsmetod, måste du också [Konfigurera sömlös SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-downlevel-computers"></a>Installera Microsoft Workplace Join för äldre Windows-datorer

För att registrera Windows-enheter som är äldre måste organisationer installera [Microsoft Workplace Join för icke-Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554). Microsoft Workplace Join för datorer som inte är Windows 10-datorer finns i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett program distributions system som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder vanliga obevakade installations alternativ med parametern `quiet`. Den aktuella grenen av Configuration Manager erbjuder förmåner jämfört med tidigare versioner, t. ex. möjligheten att spåra slutförda registreringar.

Installations programmet skapar en schemalagd aktivitet på det system som körs i användar kontexten. Aktiviteten utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst till enheten med Azure AD med hjälp av användarautentiseringsuppgifterna när den har autentiserats med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

För att verifiera enhetens registrerings tillstånd i din Azure-klient kan du använda cmdleten **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)** i [modulen Azure Active Directory PowerShell](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

När du använder cmdleten **Get-MSolDevice** för att kontrol lera tjänst informationen:

- Det måste finnas ett objekt med det **enhets-ID** som matchar ID: t för Windows-klienten.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Den här inställningen motsvarar **hybrid Azure AD-anslutna** tillstånd på sidan **enheter** i Azure AD-portalen.
- För enheter som används i villkorlig åtkomst måste värdet för **Enabled** vara **True** och **DeviceTrustLevel** måste **hanteras**.

**Så här kontrollerar du tjänst informationen**:

1. Öppna Windows PowerShell som administratör.
1. Ange `Connect-MsolService` för att ansluta till din Azure-klient.  
1. Ange `get-msoldevice -deviceId <deviceId>`.
1. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du får problem med att slutföra hybrid Azure AD-anslutning för domänanslutna Windows-enheter, se:

- [Felsöka hybrid Azure AD-anslutning för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid Azure AD-anslutning för tidigare Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Lär dig hur du [hanterar enhets identiteter med hjälp av Azure Portal](device-management-azure-portal.md).
