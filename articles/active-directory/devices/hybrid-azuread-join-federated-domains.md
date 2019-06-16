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
ms.openlocfilehash: ea834a0fc1d92cc8d2326bd94dde2e0a983c90a1
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67110749"
---
# <a name="tutorial-configure-hybrid-azure-active-directory-join-for-federated-domains"></a>Självstudier: Konfigurera Azure Active Directory-hybridanslutningar för federerade domäner

På liknande sätt till en användare är en enhet en annan core identitet som du vill skydda och använda den för att skydda dina resurser när som helst och var som helst. Du kan göra det här målet genom att föra och hantera enhetsidentiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång, kan du skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Lär dig hur du konfigurerar hybrid Azure AD-anslutning för AD-enheter för domänanslutna datorer i en federerad miljö använder AD FS i de här självstudierna.

> [!NOTE]
> Om din federerad miljö använder en identitetsprovider än AD FS, måste du se till att din identitetsprovider har stöd för WS-Trust-protokollet. WS-Trust krävs för att autentisera din Windows aktuella hybrid Azure AD-anslutna enheter med Azure AD. Dessutom måste din identitetsprovider för WIAORMULTIAUTHN anspråk om du har Windows äldre enheter som du behöver hybrid Azure AD-anslutning. 


> [!div class="checklist"]
> * Konfigurera Hybrid Azure Active Directory-anslutning
> * Aktivera äldre Windows-enheter
> * Verifiera registreringen
> * Felsöka

## <a name="prerequisites"></a>Nödvändiga komponenter

I den här självstudien förutsätts att du känner till:

- [Introduktion till Identitetshantering för enheten i Azure Active Directory](../device-management-introduction.md)
- [Så här planerar du Azure Active Directory Join-hybridimplementeringen](hybrid-azuread-join-plan.md)
- [Hur du gör kontrollerad validering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md)

För att kunna konfigurera scenariot i den här självstudien behöver du:

- Windows Server 2012 R2 med AD FS
- [Azure AD Connect](https://www.microsoft.com/download/details.aspx?id=47594) version 1.1.819.0 eller senare.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för konfiguration av Hybrid Azure AD-anslutning. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Relaterad guide:

- Konfigurerar tjänstanslutningspunkter (SCP) för enhetsregistrering
- Säkerhetskopierar ditt befintliga förlitande part-förtroende för Azure AD
- Uppdaterar anspråksreglerna i ditt Azure AD-förtroende

Konfigurationsstegen i den här artikeln baseras på guiden. Om du har en äldre version av Azure AD Connect installerad måste du uppgradera den till 1.1.819 eller senare. Om du installerar den senaste versionen av Azure AD Connect inte är ett alternativ för dig, se [manuellt konfigurera hybrid Azure AD-anslutning](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual).

Hybrid Azure AD-koppling kräver att enheterna har åtkomst till följande Microsoft-resurser från organisationens nätverk:  

- `https://enterpriseregistration.windows.net`
- `https://login.microsoftonline.com`
- `https://device.login.microsoftonline.com`
- Din organisations STS (federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (Om du använder eller planerar att använda enkel inloggning)

Från och med Windows 10-1803 om omedelbara Hybrid Azure AD-anslutning för federerad miljö använder AD FS misslyckas kan vi förlitar sig på Azure AD Connect att synkronisera datorobjektet i Azure AD som senare används för att slutföra enhetsregistreringen för Azure AD ansluta till. Kontrollera att Azure AD Connect har synkroniserat datorobjekten för enheterna du vill ska vara Hybrid Azure AD-anslutna till Azure AD. Om datorobjekten tillhör specifika organisationsenheter (OU) måste även organisationsenheterna konfigureras för synkronisering i Azure AD. Mer information om hur du synkroniserar med Azure AD Connect-datorobjekt finns i artikeln på [konfigurera filtrering med Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-configure-filtering#organizational-unitbased-filtering).

Om din organisation kräver åtkomst till Internet via en utgående proxy, Microsoft rekommenderar [implementera Web Proxy Auto-Discovery (WPAD)](https://docs.microsoft.com/previous-versions/tn-archive/cc995261(v%3dtechnet.10)) att aktivera Windows 10-datorer att göra enhetsregistrering med Azure AD. Om du stöter på problem med att konfigurera och hantera WPAD, gå till [felsökning automatisk identifiering](https://docs.microsoft.com/previous-versions/tn-archive/cc302643(v=technet.10)). 

Om du inte använder WPAD och behöva konfigurera proxyinställningarna på datorn, kan du göra så från och med Windows 10 1709 av [konfigurera WinHTTP-inställningar med hjälp av ett grupprincipobjekt (GPO)](https://blogs.technet.microsoft.com/netgeeks/2018/06/19/winhttp-proxy-settings-deployed-by-gpo/).

> [!NOTE]
> Om du konfigurerar proxyinställningarna på datorn med hjälp av WinHTTP-inställningar, misslyckas alla datorer som inte kan ansluta till den konfigurerade proxyn att ansluta till internet.

Om din organisation kräver Internetåtkomst via en autentiserad proxyserver för utgående trafik måste du se till att dina Windows 10-datorer kan autentisera till den utgående proxyn. Eftersom Windows 10-datorer utför enhetsregistrering med maskinkontext måste autentiseringen för den utgående proxyn konfigureras med maskinkontext. Kontrollera konfigurationskraven med leverantören av den utgående proxyn.

## <a name="configure-hybrid-azure-ad-join"></a>Konfigurera Hybrid Azure Active Directory-anslutning

Om du vill konfigurera en Hybrid Azure AD-anslutning med Azure AD Connect behöver du:

- Autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation.  
- Autentiseringsuppgifterna för företagsadministratören för varje skog.
- Autentiseringsuppgifterna för AD FS-administratören.

**Så här konfigurerar du en Hybrid Azure AD-anslutning med Azure AD Connect:**

1. Starta Azure AD Connect och klicka på **Konfigurera**.

   ![Välkommen](./media/hybrid-azuread-join-federated-domains/11.png)

1. På sidan **Ytterligare uppgifter** väljer du **Konfigurera enhetsalternativ** och klickar på **Nästa**.

   ![Ytterligare uppgifter](./media/hybrid-azuread-join-federated-domains/12.png)

1. På sidan **Översikt** klickar du på **Nästa**.

   ![Översikt](./media/hybrid-azuread-join-federated-domains/13.png)

1. På sidan **Anslut till Azure AD** anger du autentiseringsuppgifterna för en global administratör för din Azure AD-klientorganisation och klickar sedan på **Nästa**.

   ![Anslut till Azure AD](./media/hybrid-azuread-join-federated-domains/14.png)

1. På sidan **Enhetsalternativ** väljer du **Konfigurera Hybrid Azure AD-anslutning** och klickar på **Nästa**.

   ![Enhetsalternativ](./media/hybrid-azuread-join-federated-domains/15.png)

1. På sidan **SCP** sidan gör du följande och klickar sedan på **Nästa**:

   ![SCP](./media/hybrid-azuread-join-federated-domains/16.png)

   1. Välj skogen.
   1. Välj autentiseringstjänst. Du måste välja AD FS-server om inte din organisation uteslutande har Windows 10-klienter och du har konfigurerat dator/enhet-synkronisering eller organisationen använder SeamlessSSO.
   1. Klicka på **Lägg till** för att ange autentiseringsuppgifter för företagsadministratör.

1. På sidan **Enhetsoperativsystem** väljer du de operativsystem som används i din Active Directory-miljö och klickar sedan på **Nästa**.

   ![Enhetsoperativsystem](./media/hybrid-azuread-join-federated-domains/17.png)

1. På sidan **Federationskonfiguration** anger du autentiseringsuppgifterna för din AD FS-administratör och klickar sedan på **Nästa**.

   ![Federationskonfiguration](./media/hybrid-azuread-join-federated-domains/18.png)

1. På sidan **Klart att konfigurera** klickar du på **Konfigurera**.

   ![Klart att konfigurera](./media/hybrid-azuread-join-federated-domains/19.png)

1. På sidan **Konfigurationen är klar** klickar du på **Avsluta**.

   ![Konfigurationen är klar](./media/hybrid-azuread-join-federated-domains/20.png)

## <a name="enable-windows-down-level-devices"></a>Aktivera äldre Windows-enheter

Om några av dina domänanslutna enheter är äldre Windows-enheter måste du:

- Konfigurera inställningarna för det lokala intranätet för enhetsregistrering
- installera Microsoft Workplace Join för Windows äldre datorer

### <a name="configure-the-local-intranet-settings-for-device-registration"></a>Konfigurera inställningarna för det lokala intranätet för enhetsregistrering

För att slutföra Azure AD-hybridanslutningen för dina äldre Windows-enheter och undvika certifikatuppmaningar när enheter ska autentisera till Azure AD kan du skicka en princip till dina domänanslutna enheter för att lägga till följande webbadresser till den lokala intranätzonen i Internet Explorer:

- `https://device.login.microsoftonline.com`
- Din organisations säkerhetstokentjänst (STS – federerade domäner)
- `https://autologon.microsoftazuread-sso.com` (för enkel inloggning, SSO).

Dessutom kan du behöva aktivera **Tillåt uppdateringar i statusfältet via skript** i användarens lokala intranätzon.

### <a name="install-microsoft-workplace-join-for-windows-down-level-computers"></a>installera Microsoft Workplace Join för Windows äldre datorer

Om du vill registrera Windows äldre enheter, organisationer måste installera [Microsoft Workplace Join för Windows 10-datorer](https://www.microsoft.com/download/details.aspx?id=53554) finns på Microsoft Download Center.

Du kan distribuera paketet med hjälp av ett system för programvarudistribution som [System Center Configuration Manager](https://www.microsoft.com/cloud-platform/system-center-configuration-manager). Paketet stöder alternativen standard tyst installation med parametern tyst. Den aktuella grenen av Configuration Manager ger fördelar jämfört med tidigare versioner, som möjligheten att spåra slutförda registreringar.

Installationsprogrammet skapar en schemalagd aktivitet på system som körs i användarkontexten. Aktiviteten utlöses när du gör ett tecken att Windows. Uppgiften kopplar tyst enhet med Azure AD med autentiseringsuppgifterna för användaren när de har autentiserat med Azure AD.

## <a name="verify-the-registration"></a>Verifiera registreringen

För verifiering av enhetsregistreringsstatus i din Azure-klientorganisation kan du använda cmdlet:en **[Get-MsolDevice](https://docs.microsoft.com/powershell/msonline/v1/get-msoldevice)** i  **[Azure Active Directory PowerShell-modulen](/powershell/azure/install-msonlinev1?view=azureadps-2.0)** .

Följande gäller när du använder cmdlet:en **Get-MSolDevice** för att kontrollera tjänstinformation:

- Ett objekt med den **enhets-ID** som matchar ID på Windows klient måste finnas.
- Värdet för **DeviceTrustType** måste vara **Domänansluten**. Detta motsvarar statusen **Hybrid Azure AD-ansluten** på enhetssidan i Azure AD-portalen.
- Värdet för **aktiverad** måste vara **SANT** och **DeviceTrustLevel** måste vara **hanterade** för enheter som används i villkorlig åtkomst.

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

<!--Image references-->
[1]: ./media/active-directory-conditional-access-automatic-device-registration-setup/12.png
