---
title: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner | Microsoft Docs
description: Konfigurera Azure Active Directory-hybridanslutning för hanterade domäner.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: tutorial
ms.date: 03/06/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcd00972c2da0d3d5dafe76a8619e0f0ccaedc19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "79239114"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudie: Konfigurera Azure Active Directory Join-hybrid för hanterade domäner

I den här självstudien får du lära dig hur du konfigurerar hybrid-Azure Active Directory (Azure AD) gå med för Active Directory domän-anslutna enheter. Den här metoden stöder en hanterad miljö som innehåller både lokala Active Directory och Azure AD.

Precis som en användare i organisationen är en enhet en kärnidentitet som du vill skydda. Du kan använda en enhets identitet för att skydda dina resurser när som helst och från vilken plats som helst. Du kan uppnå det här målet genom att hantera enhetsidentiteter i Azure AD. Använd någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

Den här artikeln fokuserar på hybrid Azure AD-koppling.

Genom att föra dina enheter till Azure AD maximeras användarproduktiviteten genom enkel inloggning (SSO) i molnet och lokala resurser. Du kan skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md) samtidigt.

Du kan distribuera en hanterad miljö med hjälp av [PHS (Password Hash Sync)](../hybrid/whatis-phs.md) eller [pass-through authentication (PTA)](../hybrid/how-to-connect-pta.md) med [sömlös enkel inloggning](../hybrid/how-to-connect-sso.md). Dessa scenarier kräver inte att du konfigurerar en federationsserver för autentisering.

I den här självstudiekursen får du lära du dig att:

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Krav

- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller senare)
- Autentiseringsuppgifterna för en global administratör för din Azure AD-klient
- Företagsadministratörens autentiseringsuppgifter för var och en av skogarna

Bekanta dig med dessa artiklar:

- [Vad är en enhetsidentitet?](overview.md)
- [Så här planerar du implementering av hybrid-Azure Active Directory-anslutning](hybrid-azuread-join-plan.md)
- [Kontrollerad verifiering av Azure AD-anslutningshybrid](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Kontrollera att Azure AD Connect har synkroniserat datorobjekten på de enheter som du vill ska vara hybrid Azure AD som är anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter konfigurerar du organisationsenheterna så att de synkroniseras i Azure AD Connect. Mer information om hur du synkroniserar datorobjekt med Hjälp av Azure AD Connect finns i [Organisationsenhetsbaserad filtrering](../hybrid/how-to-connect-sync-configure-filtering.md#organizational-unitbased-filtering).

Från och med version 1.1.819.0 innehåller Azure AD Connect en guide för att konfigurera hybrid Azure AD-koppling. Guiden förenklar konfigurationsprocessen avsevärt. Guiden konfigurerar tjänstanslutningspunkterna (SCPs) för enhetsregistrering.

Konfigurationsstegen i den här artikeln baseras på att använda guiden i Azure AD Connect.

Hybrid Azure AD-anslutning kräver att enheter har åtkomst till följande Microsoft-resurser inifrån organisationens nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`(Om du använder eller planerar att använda sömlös SSO)

Om din organisation kräver åtkomst till internet via en utgående proxy rekommenderar vi [att du implementerar WPAD (Web Proxy Auto-Discovery)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) för att aktivera Windows 10-datorer för enhetsregistrering med Azure AD. Mer om du vill åtgärda problem med att konfigurera och hantera WPAD finns i [Felsöka automatisk identifiering](/previous-versions/tn-archive/cc302643(v=technet.10)).

Om du inte använder WPAD kan du konfigurera proxyinställningar på datorn som börjar med Windows 10 1709. Mer information finns i [WinHTTP Proxy Settings deployed by GPO](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningar på datorn med winhttp-inställningar kan alla datorer som inte kan ansluta till den konfigurerade proxyn inte ansluta till internet.

Om din organisation kräver åtkomst till internet via en autentiserat utgående proxy kontrollerar du att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer kör enhetsregistrering med hjälp av datorkontext konfigurerar du utgående proxyautentisering med hjälp av datorkontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

Verifiera att enheten kan komma åt ovanstående Microsoft-resurser under systemkontot med hjälp av skriptet För registrering av [testenhet.](https://gallery.technet.microsoft.com/Test-Device-Registration-3dc944c0)

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Så här konfigurerar du en hybrid-Azure AD-koppling med Hjälp av Azure AD Connect:

1. Starta Azure AD Connect och välj sedan **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-managed-domains/welcome-azure-ad-connect.png)

1. I **Ytterligare uppgifter**väljer du Konfigurera **enhetsalternativ**och väljer sedan **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-additional-tasks.png)

1. Välj **Nästa**i **Översikt**.

   ![Översikt](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-overview.png)

1. I **Anslut till Azure AD**anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation.  

   ![Anslut till Azure AD](./media/hybrid-azuread-join-managed-domains/connect-to-azure-ad-username-password.png)

1. I **Enhetsalternativ**väljer du **Konfigurera Hybrid Azure AD-koppling**och välj sedan **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-options.png)

1. I **SCP-konfiguration**, för varje skog där du vill Azure AD Connect för att konfigurera SCP, slutföra följande steg och välj sedan **Nästa**.

   1. Välj **skog**.
   1. Välj en **autentiseringstjänst**.
   1. Välj **Lägg till** om du vill ange autentiseringsuppgifter för företagsadministratören.

   ![SCP](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-scp-configuration.png)

1. I **Enhetsoperativsystem**väljer du de operativsystem som enheterna i Active Directory-miljön använder och väljer sedan **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-device-operating-systems.png)

1. Välj **Konfigurera**i **Klar att konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-ready-to-configure.png)

1. Välj **Avsluta**i **Konfigurationen.**

   ![Konfigurationen är klar](./media/hybrid-azuread-join-managed-domains/azure-ad-connect-configuration-complete.png)

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om vissa av dina domänanslutna enheter är Enheter på Windows-nivå måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös SSO
- Installera datorer på Microsoft Workplace Join för Windows på lägre nivå

> [!NOTE]
> Windows 7-supporten upphörde den 14 januari 2020. Mer information finns i [Windows 7-supporten avslutad](https://support.microsoft.com/help/4057281/windows-7-support-ended-on-january-14-2020).

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

Om du vill slutföra hybrid-Azure AD-anslutning till dina Windows-enheter på lägre nivå och undvika certifikatuppmaningar när enheter autentiserar till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande url:er i den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Du måste också aktivera **Tillåt uppdateringar av statusfältet via skript** i användarens lokala intranätzon.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös SSO

Om du vill slutföra hybrid-Azure AD-anslutning till dina Windows-enheter på lägre nivå i en hanterad domän som använder [synkronisering av lösenord hash-enheter](../hybrid/whatis-phs.md) eller direktautentisering som din Azure [AD-molnautentiseringsmetod,](../hybrid/how-to-connect-pta.md) måste du också konfigurera sömlös [SSO](../hybrid/how-to-connect-sso-quick-start.md#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>Installera datorer på Microsoft Workplace Join för Windows på lägre nivå

Om du vill registrera Windows enheter på lägre nivå måste organisationer installera [Microsoft Workplace Join för datorer som inte är Windows 10.](https://www.microsoft.com/download/details.aspx?id=53554) Microsoft Workplace Join för datorer som inte är Windows 10 finns i Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett programdistributionssystem som [Microsoft Endpoint Configuration Manager](/configmgr/). Paketet stöder standardalternativen för `quiet` tyst installation med parametern. Den aktuella versionen av Configuration Manager erbjuder fördelar jämfört med tidigare versioner, till exempel möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet i systemet som körs i användarkontexten. Uppgiften utlöses när användaren loggar in i Windows. Uppgiften ansluter tyst enheten till Azure AD med hjälp av användarautentiseringsuppgifterna när den har autentiserats med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

Verifiera enhetsregistreringstillståndet i din Azure-klient med hjälp av **[Get-MsolDevice](/powershell/msonline/v1/get-msoldevice)**. Den här cmdleten finns i [Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0).

När du använder **Cmdlet Get-MSolDevice** för att kontrollera serviceinformationen:

- Det måste finnas ett objekt med **enhets-ID:t** som matchar ID:t på Windows-klienten.
- Värdet för **DeviceTrustType** är **Domänansluten**. Den här inställningen motsvarar **tillståndet Hybrid Azure AD-anslutna** på sidan **Enheter** i Azure AD-portalen.
- För enheter som används i villkorlig åtkomst är värdet för **Aktiverad** **Sant** och **DeviceTrustLevel** **hanteras**.

Så här kontrollerar du tjänstinformationen:

1. Öppna Windows PowerShell som administratör.
1. Ange `Connect-MsolService` för att ansluta till din Azure-klient.  
1. Ange `get-msoldevice -deviceId <deviceId>`.
1. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du får problem med att slutföra hybrid-Azure AD-anslutning för domänanslutna Windows-enheter läser du:

- [Felsöka hybrid-Azure Active Directory-anslutna enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka hybrid-Azure Active Directory-anslutna enheter på lägre nivå](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

Gå vidare till nästa artikel om du vill lära dig hur du hanterar enhetsidentiteter med hjälp av Azure-portalen.
> [!div class="nextstepaction"]
> [Hantera enhetsidentiteter](device-management-azure-portal.md)
