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
ms.openlocfilehash: 56031c864fd7edb4c7153d82985ead9cd201d5fe
ms.sourcegitcommit: ef06b169f96297396fc24d97ac4223cabcf9ac33
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/31/2019
ms.locfileid: "66426675"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudier: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner

På liknande sätt som en användare blir en enhet till en identitet som du vill skydda och också använda för att skydda dina resurser, alltid och överallt. Det kan du göra genom att överföra enheternas identiteter till Azure Active Directory på något av följande sätt:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. Samtidigt kan du skydda tillgången till dina resurser i molnet och lokalt med [villkorad åtkomst](../active-directory-conditional-access-azure-portal.md).

I den här självstudien lär du dig att konfigurera en Azure AD-hybridanslutning för enheter i hanterade domäner.

> [!div class="checklist"]
> * Konfigurera Hybrid Azure AD-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:

- [Introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md)
- [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)
- [Så här kontrollerar du Azure Active Directory Join-hybriden för dina enheter](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Om du vill konfigurera scenariot i den här artikeln måste du ha den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller högre) installerad.

Kontrollera att Azure AD Connect har synkroniserat datorobjekten för enheterna du vill ska vara Hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU) måste även organisationsenheterna konfigureras för synkronisering i Azure AD.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för att konfigurera Hybrid Azure AD-koppling. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Den relaterade guiden konfigurerar tjänstanslutningspunkter (SCP) för enhetsregistrering.

Konfigurationsstegen i den här artikeln baseras på guiden.

Hybrid Azure AD-koppling kräver att enheterna har åtkomst till följande Microsoft-resurser från organisationens nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda enkel inloggning)

Om din organisation kräver Internetåtkomst via en utgående proxy från och med Windows 10 1709 kan du [konfigurera proxyinställningarna på datorn med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/). Om du använder en äldre version av Windows än Windows 10 1709 måste du implementera Web Proxy Auto-Discovery (WPAD) för att datorer med Windows 10 ska kunna utföra enhetsregistrering med Azure AD.

Om din organisation kräver Internetåtkomst via en autentiserad proxyserver för utgående trafik måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer utför enhetsregistrering med maskinkontext måste autentiseringen för den utgående proxyn konfigureras med maskinkontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en Hybrid Azure AD-anslutning med Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation.  
- Autentiseringsuppgifter för företagsadministratör för varje skog.

**Så här konfigurerar du en Hybrid Azure AD-anslutning med Azure AD Connect:**

1. Starta Azure AD Connect och klicka på **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-managed-domains/11.png)

1. På sidan **Ytterligare uppgifter** väljer du **Konfigurera enhetsalternativ** och klickar på **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-managed-domains/12.png)

1. På sidan **Översikt** klickar du på **Nästa**.

   ![Översikt](./media/hybrid-azuread-join-managed-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klient.  

   ![Anslut till Azure AD](./media/hybrid-azuread-join-managed-domains/14.png)

1. På sidan **Enhetsalternativ** väljer du **Konfigurera Hybrid Azure AD-anslutning** och klickar på **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-managed-domains/15.png)

1. På **SCP**-sidan utför du följande steg för varje skog du vill att Azure AD Connect ska konfigurera tjänstanslutningspunkten för och klickar på **Nästa**:

   ![Tjänstanslutningspunkt](./media/hybrid-azuread-join-managed-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst.
   1. Klicka på **Lägg till** för att ange autentiseringsuppgifter för företagsadministratör.

1. På sidan **Enhetsoperativsystem** väljer du de operativsystem som används i din Active Directory-miljö och klickar sedan på **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-managed-domains/17.png)

1. På sidan **Klart att konfigurera** klickar du på **Konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-managed-domains/19.png)

1. På sidan **Konfigurationen är klar** klickar du på **Avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-managed-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är äldre Windows-enheter måste du:

- Uppdatera enhetsinställningarna
- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös enkel inloggning (SSO)
- Kontrollera äldre Windows-enheter

### <a name="update-device-settings"></a>Uppdatera enhetsinställningarna

Om du vill registrera äldre Windows-enheter måste du se till att enhetsinställningarna tillåter användarna att registrera enheter i Azure Active Directory. Du hittar den här informationen i Azure-portalen under:

`Home > [Name of your tenant] > Devices - Device settings`  

Följande princip måste anges som **Alla**: **Användarna kan registrera sina enheter med Azure AD**

![Registrera enheter](media/hybrid-azuread-join-managed-domains/23.png)

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra Azure AD-hybridanslutningen för dina äldre Windows-enheter och undvika certifikatuppmaningar när enheter ska autentisera till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande webbadresser till den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Dessutom kan du behöva aktivera **Tillåt uppdateringar i statusfältet via skript** i användarens lokala intranätzon.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös enkel inloggning

För att slutföra fullständig Azure AD-anslutning för dina äldre Windows-enheter i en hanterad domän som använder direktautentisering (PTA) eller synkronisering av lösenordshash (PHS) som Azure AD-molnautentiseringsmetod måste du även [konfigurera sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature).

### <a name="control-windows-down-level-devices"></a>Kontrollera äldre Windows-enheter

För att registrera äldre Windows-enheter måste du ladda ned och installera ett Windows Installer-paket (.msi) från Download Center. Mer information finns i avsnittet [kontrollerad validering av hybrid Azure AD-anslutning på Windows äldre enheter](hybrid-azuread-join-control.md#controlled-validation-of-hybrid-azure-ad-join-on-windows-down-level-devices).

## <a name="verify-the-registration"></a>Verifiera registreringen

För verifiering av enhetsregistreringsstatus i din Azure-klientorganisation kan du använda cmdlet:en **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** i  **[Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Följande gäller när du använder cmdlet:en **Get-MSolDevice** för att kontrollera tjänstinformation:

- Det måste finnas ett objekt med det **enhets-id** som matchar ID:t på Windows-klienten.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Detta motsvarar statusen **Hybrid Azure AD-ansluten** på enhetssidan i Azure AD-portalen.
- Värdet för **Aktiverad** måste vara **True** och **DeviceTrustLevel** måste vara **Hanteras** för enheter som används i villkorsstyrd åtkomst.

**Så här kontrollerar du tjänstinformationen:**

1. Öppna **Windows PowerShell** som administratör.
1. Skriv `Connect-MsolService` för att ansluta till din Azure-klientorganisation.  
1. Skriv `get-msoldevice -deviceId <deviceId>`.
1. Kontrollera att **Aktiverad** är inställd på **SANT**.

## <a name="troubleshoot-your-implementation"></a>Felsöka din implementering

Om du har problem med att slutföra Hybrid Azure AD-anslutningen för domänanslutna Windows-enheter kan du läsa:

- [Felsöka Hybrid Azure AD-anslutningen för aktuella Windows-enheter](troubleshoot-hybrid-join-windows-current.md)
- [Felsöka Hybrid Azure AD-anslutningen för äldre Windows-enheter](troubleshoot-hybrid-join-windows-legacy.md)

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera Hybrid Azure Active Directory-anslutning för federerade domäner](hybrid-azuread-join-federated-domains.md)
> [Konfigurera Hybrid Azure Active Directory-anslutning manuellt](hybrid-azuread-join-manual.md)
