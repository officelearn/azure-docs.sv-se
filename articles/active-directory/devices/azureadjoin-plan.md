---
title: Så här planerar du implementering av Azure Active Directory-anslutning
description: I artikeln beskrivs de steg som krävs för att implementera Azure AD-anslutna enheter i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 11/21/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: a6bbecf0e365ba7a8424da775245181fa64c21f6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672686"
---
# <a name="how-to-plan-your-azure-ad-join-implementation"></a>Så här: Planera implementeringen av Azure AD-koppling

Med Azure AD-anslutning kan du ansluta till enheter direkt till Azure AD utan att behöva gå med i den lokala Active Directory samtidigt som användarna är produktiva och säkra. Azure AD-koppling är enterprise-ready för både storskaliga och begränsade distributioner.   

Den här artikeln innehåller den information du behöver för att planera implementeringen av Azure AD-koppling.
 
## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med [introduktion till enhetshantering i Azure Active Directory](../device-management-introduction.md).

## <a name="plan-your-implementation"></a>Planera implementeringen

Om du vill planera implementeringen av Azure AD-koppling bör du bekanta dig med:

|   |   |
|---|---|
|![Markera][1]|Granska dina scenarier|
|![Markera][1]|Granska din identitetsinfrastruktur|
|![Markera][1]|Utvärdera enhetshanteringen|
|![Markera][1]|Förstå överväganden för program och resurser|
|![Markera][1]|Förstå dina etableringsalternativ|
|![Markera][1]|Konfigurera roaming i företagstillstånd|
|![Markera][1]|Konfigurera villkorlig åtkomst|

## <a name="review-your-scenarios"></a>Granska dina scenarier 

Hybrid Azure AD-koppling kan vara att föredra för vissa scenarier, men Azure AD-koppling gör att du kan gå över till en molnmodell med Windows. Om du planerar att modernisera enhetshanteringen och minska enhetsrelaterade IT-kostnader ger Azure AD-koppling en bra grund för att uppnå dessa mål.  
 
Du bör överväga Azure AD-koppling om dina mål överensstämmer med följande kriterier:

- Du använder Microsoft 365 som produktivitetssvit för användarna.
- Du vill hantera enheter med en molnenhetshanteringslösning.
- Du vill förenkla enhetsetablering för geografiskt distribuerade användare.
- Du planerar att modernisera programinfrastrukturen.

## <a name="review-your-identity-infrastructure"></a>Granska din identitetsinfrastruktur  

Azure AD-koppling fungerar med både hanterade och federerade miljöer.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan distribueras antingen via [Password Hash Sync](/azure/active-directory/hybrid/how-to-connect-password-hash-synchronization) eller [Pass Through Authentication](/azure/active-directory/hybrid/how-to-connect-pta-quick-start) med Seamless Single Sign On.

Dessa scenarier kräver inte att du konfigurerar en federationsserver för autentisering.

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsleverantör som stöder både WS-Trust- och WS-Fed-protokoll:

- **WS-Fed:** Det här protokollet krävs för att ansluta en enhet till Azure AD.
- **WS-Förtroende:** Det här protokollet krävs för att logga in på en Azure AD-ansluten enhet.

När du använder AD FS måste du aktivera följande WS-Trust-slutpunkter:`/adfs/services/trust/2005/usernamemixed`
 `/adfs/services/trust/13/usernamemixed`
 `/adfs/services/trust/2005/certificatemixed`
 `/adfs/services/trust/13/certificatemixed`

Om din identitetsprovider inte stöder dessa protokoll fungerar inte Azure AD-koppling internt. 

>[!NOTE]
> Azure AD-anslutning fungerar för närvarande inte med [AD FS 2019 som konfigurerats med externa autentiseringsleverantörer som den primära autentiseringsmetoden](/windows-server/identity/ad-fs/operations/additional-authentication-methods-ad-fs#enable-external-authentication-methods-as-primary). Azure AD-koppling som standard till lösenordsautentisering som den primära metoden, vilket resulterar i autentiseringsfel i det här scenariot


### <a name="smartcards-and-certificate-based-authentication"></a>Smartkort och certifikatbaserad autentisering

Du kan inte använda smartkort eller certifikatbaserad autentisering för att ansluta enheter till Azure AD. Smartcards kan dock användas för att logga in på Azure AD-anslutna enheter om du har AD FS konfigurerat.

**Rekommendation:** Implementera Windows Hello för företag för stark, lösenordslös autentisering till Windows 10-enheter.

### <a name="user-configuration"></a>Användarkonfiguration

Om du skapar användare i din:

- **Lokal Active Directory**måste du synkronisera dem till Azure AD med [Azure AD Connect](/azure/active-directory/hybrid/how-to-connect-sync-whatis). 
- **Azure AD**krävs ingen ytterligare konfiguration.

Lokala UPN:er som skiljer sig från Azure AD UPN stöds inte på Azure AD-anslutna enheter. Om användarna använder ett lokalt UPN bör du planera att byta till att använda deras primära UPN i Azure AD.

## <a name="assess-your-device-management"></a>Utvärdera enhetshanteringen

### <a name="supported-devices"></a>Enheter som stöds

Azure AD-koppling:

- Gäller endast för Windows 10-enheter. 
- Gäller inte tidigare versioner av Windows eller andra operativsystem. Om du har Windows 7/8.1-enheter måste du uppgradera till Windows 10 för att distribuera Azure AD-anslutning.
- Stöds inte på enheter med TPM i FIPS-läge.
 
**Rekommendation:** Använd alltid den senaste Windows 10-versionen för att dra nytta av uppdaterade funktioner.

### <a name="management-platform"></a>Plattform för hantering

Enhetshantering för Azure AD-anslutna enheter baseras på en MDM-plattform som Intune och MDM-CSP:er. Windows 10 har en inbyggd MDM-agent som fungerar med alla kompatibla MDM-lösningar.

> [!NOTE]
> Gruppprinciper stöds inte i Azure AD-anslutna enheter eftersom de inte är anslutna till lokala Active Directory. Hantering av Azure AD-anslutna enheter är endast möjlig via MDM

Det finns två metoder för att hantera Azure AD-anslutna enheter:

- **ENDAST MDM** - En enhet hanteras uteslutande av en MDM-leverantör som Intune. Alla principer levereras som en del av MDM-registreringsprocessen. För Azure AD Premium- eller EMS-kunder är MDM-registrering ett automatiserat steg som är en del av en Azure AD-koppling.
- **Samhantering** - En enhet hanteras av en MDM-leverantör och SCCM. I den här metoden installeras SCCM-agenten på en MDM-hanterad enhet för att administrera vissa aspekter.

Om du använder grupprinciper utvärderar du din MDM-principparitet med hjälp av [MMAT (MDM Migration Analysis Tool).](https://github.com/WindowsDeviceManagement/MMAT) 

Granska principer som stöds och inte stöds för att avgöra om du kan använda en MDM-lösning i stället för grupprinciper. För principer som inte stöds bör du tänka på följande:

- Är principerna som inte stöds nödvändiga för Azure AD-anslutna enheter eller användare?
- Är principerna som inte stöds tillämpliga i en molndriven distribution?

Om mdm-lösningen inte är tillgänglig via Azure AD-appgalleriet kan du lägga till den efter processen som beskrivs i [Azure Active Directory-integrering med MDM](/windows/client-management/mdm/azure-active-directory-integration-with-mdm). 

Genom samhantering kan du använda SCCM för att hantera vissa aspekter av dina enheter medan principer levereras via din MDM-plattform. Microsoft Intune möjliggör samhantering med SCCM. Mer information om samhantering för Windows 10-enheter finns i [Vad är samhantering?](/configmgr/core/clients/manage/co-management-overview). Om du använder en annan MDM-produkt än Intune kontaktar du MDM-leverantören om tillämpliga samhanteringsscenarier.

**Rekommendation:** Tänk mdm endast hantering för Azure AD-anslutna enheter.

## <a name="understand-considerations-for-applications-and-resources"></a>Förstå överväganden för program och resurser

Vi rekommenderar att du migrerar program från lokalt till moln för en bättre användarupplevelse och åtkomstkontroll. Azure AD-anslutna enheter kan dock sömlöst ge åtkomst till både lokala och molnbaserade program. Mer information finns i [Så här fungerar SSO till lokala resurser på Azure AD-anslutna enheter](azuread-join-sso.md).

I följande avsnitt visas överväganden för olika typer av program och resurser.

### <a name="cloud-based-applications"></a>Molnbaserade program

Om ett program läggs till i Azure AD-appgalleriet får användarna SSO via Azure AD-anslutna enheter. Ingen ytterligare konfiguration krävs. Användare får SSO på båda, Microsoft Edge och Chrome webbläsare. För Chrome måste du distribuera [tillägget Windows 10-konton](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji). 

Alla Win32-program som:

- Förlita dig på WEB Account Manager (WAM) för tokenbegäranden får också SSO på Azure AD-anslutna enheter. 
- Lita inte på WAM kan uppmana användare för autentisering. 

### <a name="on-premises-web-applications"></a>Lokala webbapplikationer

Om dina appar är specialbyggda och/eller lokala finns du i webbläsarens betrodda webbplatser för att:

- Aktivera integrerad Windows-autentisering 
- Ge användarna en SSO-upplevelse utan uppmaning. 

Om du använder AD FS läser du [Verifiera och hantera enkel inloggning med AD FS](/previous-versions/azure/azure-services/jj151809(v%3dazure.100)). 

**Rekommendation:** Överväg att vara värd i molnet (till exempel Azure) och integrera med Azure AD för en bättre upplevelse.

### <a name="on-premises-applications-relying-on-legacy-protocols"></a>Lokala program som förlitar sig på äldre protokoll

Användare får SSO från Azure AD-anslutna enheter om enheten har åtkomst till en domänkontrollant. 

**Rekommendation:** Distribuera [Azure AD App-proxy](/azure/active-directory/manage-apps/application-proxy) för att aktivera säker åtkomst för dessa program.

### <a name="on-premises-network-shares"></a>Lokala nätverksresurser

Användarna har SSO från Azure AD-anslutna enheter när en enhet har åtkomst till en lokal domänkontrollant.

### <a name="printers"></a>Skrivare

För skrivare måste du distribuera [hybridmolnutskrift](/windows-server/administration/hybrid-cloud-print/hybrid-cloud-print-deploy) för att upptäcka skrivare på Azure AD-anslutna enheter. 

Även om skrivare inte kan identifieras automatiskt i en molnmiljö kan användarna också använda skrivarens UNC-sökväg för att direkt lägga till dem. 

### <a name="on-premises-applications-relying-on-machine-authentication"></a>Lokala program som är beroende av maskinautentisering

Azure AD-anslutna enheter stöder inte lokala program som förlitar sig på datorautentisering. 

**Rekommendation:** Överväg att pensionera dessa program och flytta till sina moderna alternativ.

### <a name="remote-desktop-services"></a>Fjärrskrivbordstjänster

Anslutning till fjärrskrivbord till en Azure AD-anslutna enheter kräver att värddatorn antingen azure AD-ansluten eller Hybrid Azure AD-ansluten. Fjärrskrivbord från en ojoinerad enhet eller icke-Windows-enhet stöds inte. Mer information finns i [Ansluta till fjärr-Azure AD-ansluten dator](/windows/client-management/connect-to-remote-aadj-pc)

## <a name="understand-your-provisioning-options"></a>Förstå dina etableringsalternativ

Du kan etablera Azure AD-koppling med hjälp av följande metoder:

- **Självbetjäning i OOBE/Inställningar** – I självbetjäningsläget går användarna igenom Azure AD-anslutningsprocessen antingen under OOBE (Windows Out of Box Experience) eller från Windows-inställningar. Mer information finns i [Ansluta till din arbetsenhet till organisationens nätverk](/azure/active-directory/user-help/user-help-join-device-on-network). 
- **Windows Autopilot** - Windows Autopilot möjliggör förkonfiguration av enheter för en smidigare upplevelse i OOBE för att utföra en Azure AD-koppling. Mer information finns i [översikten över Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot). 
- **Massregistrering** - Massregistrering gör det möjligt för en administratörsdriven Azure AD-koppling med hjälp av ett massetableringsverktyg för att konfigurera enheter. Mer information finns i [Massregistrering för Windows-enheter](/intune/windows-bulk-enroll).
 
Här är en jämförelse av dessa tre metoder 
 
|   | Konfiguration av självbetjäning | Windows Autopilot | Massregistrering |
| --- | --- | --- | --- |
| Kräv användarinteraktion för att ställa in | Ja | Ja | Inga |
| Kräv IT-insats | Inga | Ja | Ja |
| Tillämpliga flöden | OOBE & Inställningar | endast OOBE | endast OOBE |
| Lokala administratörsrättigheter till primär användare | Ja, som standard | Konfigurerbar | Inga |
| Kräv OEM-stöd för enheter | Inga | Ja | Inga |
| Versioner som stöds | 1511+ | 1709+ | 1703+ |
 
Välj distributionsmetod eller distributionsmetoder genom att granska tabellen ovan och granska följande överväganden för att anta någon av metoderna:  

- Är dina användare tekniskt kunniga att gå igenom installationen själva? 
   - Självbetjäning kan fungera bäst för dessa användare. Överväg Windows Autopilot för att förbättra användarupplevelsen.  
- Är användarna fjärr- eller företagslokaler? 
   - Självbetjäning eller Autopilot fungerar bäst för fjärranvändare för en problemfri installation. 
- Föredrar du en användardriven eller en administratörshanterad konfiguration? 
   - Massregistrering fungerar bättre för administratörsdriven distribution för att konfigurera enheter innan du lämnar över till användare.     
- Köper du enheter från 1-2 OEMS, eller har du en bred distribution av OEM-enheter?  
   - Om du köper från begränsade OEM-tillverkare som också stöder Autopilot kan du dra nytta av en hårdare integrering med Autopilot. 

## <a name="configure-your-device-settings"></a>Konfigurera enhetsinställningarna

Med Azure-portalen kan du styra distributionen av Azure AD-anslutna enheter i din organisation. Om du vill konfigurera **Azure Active Directory page**relaterade inställningar väljer `Devices > Device settings`du .

### <a name="users-may-join-devices-to-azure-ad"></a>Användare kan ansluta till enheter till Azure AD

Ange det här alternativet till **Alla** eller **Selected** baserat på omfattningen av distributionen och vem du vill tillåta att konfigurera en Azure AD-ansluten enhet. 

![Användare kan ansluta till enheter till Azure AD](./media/azureadjoin-plan/01.png)

### <a name="additional-local-administrators-on-azure-ad-joined-devices"></a>Ytterligare lokala administratörer på Azure AD-anslutna enheter

Välj **Markerad** och väljer de användare som du vill lägga till i den lokala administratörsgruppen på alla Azure AD-anslutna enheter. 

![Ytterligare lokala administratörer på Azure AD-anslutna enheter](./media/azureadjoin-plan/02.png)

### <a name="require-multi-factor-auth-to-join-devices"></a>Kräv multifaktor-Auth för att ansluta till enheter

Välj **"Ja** om du behöver användare för att utföra MFA när du ansluter till enheter till Azure AD. För användare som ansluter enheter till Azure AD med MFA blir själva enheten en andra faktor.

![Kräv multifaktor-Auth för att ansluta till enheter](./media/azureadjoin-plan/03.png)

## <a name="configure-your-mobility-settings"></a>Konfigurera dina mobilitetsinställningar

Innan du kan konfigurera dina mobilitetsinställningar kan du behöva lägga till en MDM-leverantör först.

**Så här lägger du till en MDM-leverantör:**

1. Klicka på i avsnittet **Hantera** på sidan `Mobility (MDM and MAM)` **Azure Active Directory**. 
1. Klicka på **Lägg till program**.
1. Välj din MDM-leverantör i listan.

   ![Lägga till ett program](./media/azureadjoin-plan/04.png)

Välj mdm-providern för att konfigurera relaterade inställningar. 

### <a name="mdm-user-scope"></a>MDM-användaromfattning

Välj **Vissa** eller **Alla** baserat på distributionens omfattning. 

![MDM-användaromfattning](./media/azureadjoin-plan/05.png)

Baserat på ditt omfång händer något av följande: 

- **Användaren är i MDM-scope:** Om du har en Azure AD Premium-prenumeration automatiseras MDM-registreringen tillsammans med Azure AD-anslutning. Alla scoped-användare måste ha en lämplig licens för din MDM. Om MDM-registrering misslyckas i det här scenariot kommer Azure AD-anslutning också att återställas.
- **Användaren är inte i MDM-scope:** Om användare inte är i MDM-scope slutförs Azure AD-anslutning utan MDM-registrering. Detta resulterar i en ohanterlig enhet.

### <a name="mdm-urls"></a>MDM-url:er

Det finns tre url:er som är relaterade till MDM-konfigurationen:

- Webbadress till MDM-användarvillkor
- Webbadress till MDM-identifiering 
- Webbadress till MDM-kompatibilitet

![Lägga till ett program](./media/azureadjoin-plan/06.png)

Varje URL har ett fördefinierat standardvärde. Om dessa fält är tomma kontaktar du MDM-leverantören för mer information.

### <a name="mam-settings"></a>MAM-inställningar

MAM gäller inte för Azure AD-anslutning. 

## <a name="configure-enterprise-state-roaming"></a>Konfigurera roaming i företagstillstånd

Om du vill aktivera tillståndsroaming till Azure AD så att användarna kan synkronisera sina inställningar mellan enheter läser du [Aktivera Roaming i företagstillstånd i Azure Active Directory](enterprise-state-roaming-enable.md). 

**Rekommendation:** Aktivera den här inställningen även för hybrid Azure AD-anslutna enheter.

## <a name="configure-conditional-access"></a>Konfigurera villkorlig åtkomst

Om du har konfigurerat en MDM-provider för dina Azure AD-anslutna enheter flaggar leverantören enheten som kompatibel så snart enheten är under hantering. 

![Kompatibel enhet](./media/azureadjoin-plan/46.png)

Du kan använda den här implementeringen för att [kräva hanterade enheter för molnappåtkomst med villkorlig åtkomst](../conditional-access/require-managed-devices.md).

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Gå med i en ny Windows 10-enhet med Azure AD under en första körning](azuread-joined-devices-frx.md)
> [Anslut din arbetsenhet till organisationens nätverk](/azure/active-directory/user-help/user-help-join-device-on-network)

<!--Image references-->
[1]: ./media/azureadjoin-plan/12.png
