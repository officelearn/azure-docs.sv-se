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
ms.openlocfilehash: 6f9daeb5e0de9c53f16efff46e02015acfa7c521
ms.sourcegitcommit: 4cdd4b65ddbd3261967cdcd6bc4adf46b4b49b01
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/06/2019
ms.locfileid: "66734595"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-managed-domains"></a>Självstudier: Konfigurera en Azure Active Directory-hybridanslutning för hanterade domäner

På liknande sätt till en användare är en enhet en annan core identitet som du vill skydda och använda det för att skydda dina resurser när som helst och var som helst. Du kan göra det här målet genom att föra och hantera enhetsidentiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. Samtidigt kan du skydda tillgången till dina resurser i molnet och lokalt med [villkorad åtkomst](../active-directory-conditional-access-azure-portal.md).

I den här självstudien får lära du att konfigurera hybrid Azure AD-anslutning för AD-enheter för domänanslutna datorer i en hanterad miljö. 

En hanterad miljö kan vara distribueras antingen via [lösenord hash-synkronisering (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) eller [skicka via autentisering (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) med [sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).
Dessa scenarier måste inte du konfigurera en federationsserver för autentisering.

> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera anslutna enheter
> * Felsöka

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:

- [Introduktion till Identitetshantering för enheten i Azure Active Directory](../device-management-introduction.md)
- [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)
- [Hur du gör kontrollerad validering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Om du vill konfigurera scenariot i den här artikeln måste du ha den [senaste versionen av Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) (1.1.819.0 eller högre) installerad.

Kontrollera att Azure AD Connect har synkroniserat datorobjekten för enheterna du vill ska vara Hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU) måste även organisationsenheterna konfigureras för synkronisering i Azure AD. Mer information om hur du synkroniserar med Azure AD Connect-datorobjekt finns i artikeln på [konfigurera filtrering med Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för att konfigurera Hybrid Azure AD-koppling. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Den relaterade guiden konfigurerar tjänstanslutningspunkter (SCP) för enhetsregistrering.

Konfigurationsstegen i den här artikeln baseras på guiden.

Hybrid Azure AD-koppling kräver att enheterna har åtkomst till följande Microsoft-resurser från organisationens nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda enkel inloggning)

Om din organisation kräver åtkomst till Internet via en utgående proxy, Microsoft rekommenderar [implementera Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) att aktivera Windows 10-datorer att göra enhetsregistrering med Azure AD. Om du stöter på problem med att konfigurera och hantera WPAD, gå till [felsökning automatisk identifiering](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och behöva konfigurera proxyinställningarna på datorn, kan du göra så från och med Windows 10 1709 av [konfigurera WinHTTP-inställningar med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningarna på datorn med hjälp av WinHTTP-inställningar, misslyckas alla datorer som inte kan ansluta till den konfigurerade proxyn att ansluta till internet.

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

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- Konfigurera sömlös enkel inloggning (SSO)
- installera Microsoft Workplace Join för Windows äldre datorer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra Azure AD-hybridanslutningen för dina äldre Windows-enheter och undvika certifikatuppmaningar när enheter ska autentisera till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande webbadresser till den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`
- `https://autologon.microsoftazuread-sso.com`

Dessutom kan du behöva aktivera **Tillåt uppdateringar i statusfältet via skript** i användarens lokala intranätzon.

### <a name="configure-seamless-sso"></a>Konfigurera sömlös enkel inloggning

Till slutförd hybrid Azure AD join för dina Windows äldre enheter i en hanterad domän som använder [lösenord hash-synkronisering (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) eller [skicka via autentisering (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) som din Azure AD-molnet autentiseringsmetod, du måste också [konfigurerar sömlös SSO](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso-quick-start#step-2-enable-the-feature).

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>installera Microsoft Workplace Join för Windows äldre datorer

Om du vill registrera Windows äldre enheter, organisationer måste installera [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) finns på Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med parametern tyst. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarkontexten. Aktiviteten utlöses när användaren loggar in på Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

För verifiering av enhetsregistreringsstatus i din Azure-klientorganisation kan du använda cmdlet:en **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** i  **[Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Följande gäller när du använder cmdlet:en **Get-MSolDevice** för att kontrollera tjänstinformation:

- Ett objekt med den **enhets-ID** som matchar ID på Windows klient måste finnas.
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

- Mer information om hur du hanterar enhetsidentiteter i Azure AD-portalen finns i [hantera enhetsidentiteter med Azure portal](device-management-azure-portal.md).
