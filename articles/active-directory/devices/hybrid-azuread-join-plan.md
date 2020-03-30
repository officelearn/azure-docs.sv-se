---
title: Planera hybrid-Azure Active Directory-anslutning - Azure Active Directory
description: Lär dig att konfigurera Hybrid Azure Active Directory-anslutningsenheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 152ff52ce52b573d7f24cbb2fafc944b1794f6d7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129262"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Så här planerar du implementering av hybrid-Azure Active Directory-anslutning

På ett liknande sätt som en användare är en enhet en annan kärnidentitet som du vill skydda och använda den för att skydda dina resurser när som helst och från vilken plats som helst. Du kan uppnå det här målet genom att ta med och hantera enhetsidentiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. Samtidigt kan du skydda åtkomsten till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Om du har en lokal Active Directory -miljö (AD) och vill ansluta till ad-domänens anslutna datorer till Azure AD kan du åstadkomma detta genom att göra hybrid-Azure AD-koppling. Den här artikeln innehåller relaterade steg för att implementera en hybrid Azure AD-koppling i din miljö. 

## <a name="prerequisites"></a>Krav

Den här artikeln förutsätter att du är bekant med [introduktion till enhetsidentitetshantering i Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Den minsta obligatoriska domänkontrollantversionen för Windows 10 hybrid Azure AD-anslutning är Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planera implementeringen

Om du vill planera din hybridimpurens AD-implementering bör du bekanta dig med:

|   |   |
| --- | --- |
| ![Markera][1] | Granska enheter som stöds |
| ![Markera][1] | Granska saker du bör veta |
| ![Markera][1] | Granska kontrollerad validering av azure AD-hybridanslutning |
| ![Markera][1] | Välj scenario baserat på din identitetsinfrastruktur |
| ![Markera][1] | Granska lokalt AD UPN-stöd för hybrid-Azure AD-koppling |

## <a name="review-supported-devices"></a>Granska enheter som stöds

Hybrid Azure AD-koppling stöder ett brett utbud av Windows-enheter. Eftersom konfigurationen för enheter som kör äldre versioner av Windows kräver ytterligare eller olika steg grupperas enheterna som stöds i två kategorier:

### <a name="windows-current-devices"></a>Windows-aktuella enheter

- Windows 10
- Windows Server 2016
  - **Azure**National-molnkunder kräver version 1809
- Windows Server 2019

För enheter som kör operativsystemet Windows-skrivbord visas version som stöds i den här artikeln [information om Windows 10-utgivning](/windows/release-information/). Som ett bästa praxis rekommenderar Microsoft att du uppgraderar till den senaste versionen av Windows 10.

### <a name="windows-down-level-devices"></a>Enheter på ned-nivå i Windows

- Windows 8.1
- Windows 7-supporten upphörde den 14 januari 2020. Mer information finns i [Support för Windows 7 har avslutats](https://support.microsoft.com/en-us/help/4057281/windows-7-support-ended-on-january-14-2020).
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2. Supportinformation om Windows Server 2008 och 2008 R2 finns i [Förbered för Windows Server 2008 på support](https://www.microsoft.com/cloud-platform/windows-server-2008).

Som ett första planeringssteg bör du granska din miljö och avgöra om du behöver ha stöd för Windows-enheter på lägre nivå.

## <a name="review-things-you-should-know"></a>Granska saker du bör veta

### <a name="unsupported-scenarios"></a>Scenarier som inte stöds
- Hybrid Azure AD-anslutning stöds för närvarande inte om din miljö består av en enda AD-skog som synkroniserar identitetsdata till mer än en Azure AD-klientorganisation.

- Hybrid Azure AD-anslutning stöds inte för Windows Server som kör domänkontrollanten (DC) roll.

- Hybrid Azure AD-anslutning stöds inte på Windows-enheter på lägre nivå när du använder roaming för autentiseringsuppgifter eller i en central profil i användarprofilen eller obligatorisk profil.

- Server Core OS stöder inte någon typ av enhetsregistrering.

### <a name="os-imaging-considerations"></a>Os-bildbehandlingshänsyn
- Om du förlitar dig på System preparation tool (Sysprep) och om du använder en **avbildning före Windows 10 1809** för installation, kontrollerar du att avbildningen inte kommer från en enhet som redan är registrerad med Azure AD som Hybrid Azure AD-koppling.

- Om du förlitar dig på en ögonblicksbild av virtuella datorer (VM) för att skapa ytterligare virtuella datorer kontrollerar du att ögonblicksbilden inte kommer från en virtuell dator som redan är registrerad med Azure AD som Hybrid Azure AD-koppling.

- Om du använder [Unified Write Filter](/windows-hardware/customize/enterprise/unified-write-filter) och liknande tekniker som rensar ändringar på disken vid omstart, måste de tillämpas när enheten är Hybrid Azure AD-ansluten. Om du aktiverar sådan teknik innan Hybrid Azure AD-kopplingen har slutförts kommer enheten att bli osammanhängande vid varje omstart

### <a name="handling-devices-with-azure-ad-registered-state"></a>Hantera enheter med registrerat Azure AD-tillstånd
Om din Windows 10-domän som anslöt sig till enheter är [Azure AD-registrerade](overview.md#getting-devices-in-azure-ad) till din klientorganisation kan det leda till ett dubbelt tillstånd av Hybrid Azure AD-ansluten och Azure AD-registrerad enhet. Vi rekommenderar att du uppgraderar till Windows 10 1803 (med KB4489894 tillämpad) eller högre för att automatiskt ta itu med det här scenariot. I versioner före 1803 måste du ta bort azure AD-registrerade tillståndet manuellt innan du aktiverar Hybrid Azure AD-anslutning. I 1803 och högre utgåvor, följande ändringar har gjorts för att undvika detta dubbla tillstånd:

- Alla befintliga Azure AD-registrerade tillstånd för en användare tas bort automatiskt <i>när enheten är Hybrid Azure AD-ansluten och samma användare loggar in</i>. Om användare A till exempel hade ett Azure AD-registrerat tillstånd på enheten rensas det dubbla tillståndet för användare A endast när användare A loggar in på enheten. Om det finns flera användare på samma enhet rensas det dubbla tillståndet individuellt när dessa användare loggar in.
- Du kan förhindra att din domän gick med i enheten från att bli Azure AD-registrerad genom att lägga till den här registernyckeln - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, "BlockAADWorkplaceJoin"=dword:00000001.
- I Windows 10 1803, om du har Windows Hello för företag konfigurerat, måste användaren konfigurera om Windows Hello för företag efter att den dubbla tillståndsrensningen har rensats. Det här problemet har åtgärdats med KB4512509

> [!NOTE]
> Den Azure AD-registrerade enheten tas inte bort automatiskt om den hanteras av Intune.

### <a name="additional-considerations"></a>Annat som är bra att tänka på
- Om din miljö använder virtuell skrivbordsinfrastruktur (VDI) läser du [Enhetsidentitet och skrivbordsvirtualisering](/azure/active-directory/devices/howto-device-identity-virtual-desktop-infrastructure).

- Hybrid Azure AD-anslutning stöds för FIPS-kompatibel TPM 2.0 och stöds inte för TPM 1.2. Om dina enheter har FIPS-kompatibel TPM 1.2 måste du inaktivera dem innan du fortsätter med Hybrid Azure AD-anslutning. Microsoft tillhandahåller inga verktyg för att inaktivera FIPS-läge för TPMs eftersom det är beroende av TPM-tillverkaren. Kontakta maskinvaru-OEM-tillverkaren för support. 

- Från och med Windows 10 1903-versionen används inte TPMs 1.2 med hybrid-Azure AD-anslutning och enheter med dessa TPM:er betraktas som om de inte har en TPM.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Granska kontrollerad validering av azure AD-hybridanslutning

När alla förutsättningar finns på plats registreras Windows-enheter automatiskt som enheter i din Azure AD-klientorganisation. Tillståndet för dessa enhetsidentiteter i Azure AD kallas hybrid Azure AD-koppling. Mer information om begreppen som beskrivs i den här artikeln finns i artikeln [Introduktion till enhetsidentitetshantering i Azure Active Directory](overview.md).

Organisationer kanske vill göra en kontrollerad validering av hybrid Azure AD-koppling innan de aktiverar den i hela organisationen på en gång. Granska artikeln [kontrollerad validering av hybrid Azure AD-koppling](hybrid-azuread-join-control.md) för att förstå hur du åstadkommer den.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Välj scenario baserat på din identitetsinfrastruktur

Hybrid Azure AD-koppling fungerar med både hanterade och federerade miljöer beroende på om UPN är dirigerbar eller icke-dirigerbar. Se längst ned på sidan för tabell på scenarier som stöds.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan distribueras antingen via [PASSWORD Hash Sync (PHS)](/azure/active-directory/hybrid/whatis-phs) eller [Pass Through Authentication (PTA)](/azure/active-directory/hybrid/how-to-connect-pta) med [Seamless Single Sign On](/azure/active-directory/hybrid/how-to-connect-sso).

Dessa scenarier kräver inte att du konfigurerar en federationsserver för autentisering.

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsprovider som stöder följande krav. Om du har en federerad miljö med Hjälp av Active Directory Federation Services (AD FS) stöds redan nedanstående krav.

- **WIAORMULTIAUTHN-anspråk:** Det här anspråket krävs för att göra hybrid Azure AD-koppling för Windows-enheter på lägre nivå.
- **WS-Trust-protokollet:** Det här protokollet krävs för att autentisera Windows nuvarande hybrid Azure AD-anslutna enheter med Azure AD. När du använder AD FS måste du aktivera följande WS-Trust-slutpunkter:`/adfs/services/trust/2005/windowstransport`  
`/adfs/services/trust/13/windowstransport`  
  `/adfs/services/trust/2005/usernamemixed` 
  `/adfs/services/trust/13/usernamemixed`
  `/adfs/services/trust/2005/certificatemixed` 
  `/adfs/services/trust/13/certificatemixed` 

> [!WARNING] 
> Både **adfs/services/trust/2005/windowstransport** eller **adfs/services/trust/13/windowstransport** ska aktiveras som endast intranätvända slutpunkter och får INTE exponeras som extranätvända slutpunkter via webbprogramproxyn. Mer information om hur du inaktiverar WS-Trust Windows-slutpunkter finns i [Inaktivera WS-Trust Windows-slutpunkter i proxyn](/windows-server/identity/ad-fs/deployment/best-practices-securing-ad-fs#disable-ws-trust-windows-endpoints-on-the-proxy-ie-from-extranet). Du kan se vilka slutpunkter som är aktiverade via AD FS-hanteringskonsolen under > **Tjänstslutpunkter**. **Service**

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för konfiguration av Hybrid Azure AD-anslutning. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Om det inte är ett alternativ för dig att installera den nödvändiga versionen av Azure AD Connect läser du hur du [konfigurerar enhetsregistrering manuellt](hybrid-azuread-join-manual.md). 

Baserat på scenariot som matchar din identitetsinfrastruktur läser du:

- [Konfigurera hybrid-Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid-Azure Active Directory-anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-users-upn-support-for-hybrid-azure-ad-join"></a>Granska lokalt AD-användare UPN-stöd för Hybrid Azure AD-koppling

Ibland kan dina lokala AD-användare UPN skilja sig från dina Azure AD UPN.Sometimes, your on-premises AD users UPN could be different from your Azure AD UPNs. I sådana fall ger Windows 10 Hybrid Azure AD-anslutning begränsat stöd för lokala AD UPN-nätverk baserat på [autentiseringsmetod,](/azure/security/fundamentals/choose-ad-authn)domäntyp och Windows 10-version. Det finns två typer av lokala AD-UPN:er som kan finnas i din miljö:

- Routable användare UPN: En dirigerbar UPN har en giltig verifierad domän, som är registrerad hos en domänregistratorer. Om contoso.com till exempel är den primära domänen i Azure AD är contoso.org den primära domänen i lokalt AD som ägs av Contoso och [verifieras i Azure AD](/azure/active-directory/fundamentals/add-custom-domain)
- Icke-dirigerbara användare UPN: En icke-dirigerbar UPN har ingen verifierad domän. Den gäller endast i organisationens privata nätverk. Om contoso.com till exempel är den primära domänen i Azure AD, är contoso.local den primära domänen i lokala AD men är inte en verifierbar domän på internet och används endast i Contosos nätverk.

> [!NOTE]
> Informationen i det här avsnittet gäller endast för lokala användare UPN. Det är inte tillämpligt på ett lokalt datordomänsuffix (exempel: computer1.contoso.local).

Tabellen nedan innehåller information om stöd för dessa lokala AD UPN i Windows 10 Hybrid Azure AD-koppling

| Typ av lokalt AD UPN | Domäntyp | Windows 10-version | Beskrivning |
| ----- | ----- | ----- | ----- |
| Dirigerbara | Federerade | Från 1703 release | Allmänt tillgänglig |
| Ej dirigerbar | Federerade | Från 1803 release | Allmänt tillgänglig |
| Dirigerbara | Hanterade | Från 1803 release | Azure AD SSPR på Windows-låsskärmen stöds inte i allmänhet |
| Ej dirigerbar | Hanterade | Stöds inte | |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
> [Konfigurera hybrid Azure Active Directory-anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
