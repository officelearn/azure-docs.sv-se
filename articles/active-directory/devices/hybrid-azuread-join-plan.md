---
title: Hur du planerar hybridimplementeringen Azure Active Directory join i Azure Active Directory (Azure AD) | Microsoft Docs
description: Lär dig att konfigurera anslutna Azure Active Directory-hybridenheter.
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
ms.openlocfilehash: c476c2b326045db37c54a358d68f4b5f8bbaed9a
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509604"
---
# <a name="how-to-plan-your-hybrid-azure-active-directory-join-implementation"></a>Instruktioner: Planera implementeringen hybrid Azure Active Directory join

På liknande sätt till en användare är en enhet en annan core identitet som du vill skydda och använda den för att skydda dina resurser när som helst och var som helst. Du kan göra det här målet genom att föra och hantera enhetsidentiteter i Azure AD med någon av följande metoder:

- Azure Active Directory-anslutning
- Hybrid Azure Active Directory-anslutning
- Azure Active Directory-registrering

När du börjar använda dina enheter med Azure Active Directory maximerar du användarnas produktivitet med enkel inloggning (SSO) mellan dina molnresurser och lokala resurser. På samma gång, kan du skydda åtkomst till molnet och lokala resurser med [villkorlig åtkomst](../active-directory-conditional-access-azure-portal.md).

Om du har en lokal Active Directory (AD)-miljö och du vill ansluta till din AD domänanslutna datorer till Azure AD, kan du göra detta genom att göra hybrid Azure AD-anslutning. Den här artikeln innehåller du med relaterade stegen för att implementera en hybrid Azure AD join i din miljö. 

## <a name="prerequisites"></a>Förutsättningar

Den här artikeln förutsätter att du är bekant med den [introduktionen till enhetshantering identitet i Azure Active Directory](../device-management-introduction.md).

> [!NOTE]
> Den begärda minimiversionen domänfunktionsnivån och skogens funktionsnivåer för Windows 10 hybrid Azure AD-anslutning är Windows Server 2008 R2.

## <a name="plan-your-implementation"></a>Planera implementeringen

Om du vill planera hybridimplementeringen Azure AD, bör du bekanta dig med:

|   |   |
| --- | --- |
| ![Markera][1] | Granska stöd för enheter |
| ![Markera][1] | Saker du bör känna till granskning |
| ![Markera][1] | Granska kontrollerad validering av hybrid Azure AD-anslutning |
| ![Markera][1] | Välj ditt scenario baserat på din infrastruktur för Identitetshantering |
| ![Markera][1] | Granska lokala AD UPN-stöd för hybrid Azure AD join |

## <a name="review-supported-devices"></a>Granska stöd för enheter

Hybrid Azure AD-anslutning har stöd för en bred Windows-enheter. Eftersom konfigurationen för enheter som kör äldre versioner av Windows kräver ytterligare eller andra steg, är enheter som stöds grupperade i två kategorier:

### <a name="windows-current-devices"></a>Befintliga Windows-enheter

- Windows 10
- Windows Server 2016
- Windows Server 2019

För enheter som kör Windows operativsystem, version som stöds visas i den här artikeln [versionsinformation om Windows 10](https://docs.microsoft.com/windows/release-information/). Som bästa praxis rekommenderar Microsoft att du uppgraderar till den senaste versionen av Windows 10.

### <a name="windows-down-level-devices"></a>Windows äldre enheter

- Windows 8.1
- Windows 7. Supportinformation på Windows 7, finns i den här artikeln [stöd för Windows 7 slut](https://www.microsoft.com/en-us/windowsforbusiness/end-of-windows-7-support)
- Windows Server 2012 R2
- Windows Server 2012
- Windows Server 2008 R2

Som ett första steg för planering, bör du granska din miljö och Bestäm om du behöver stöd för Windows äldre enheter.

## <a name="review-things-you-should-know"></a>Saker du bör känna till granskning

Hybrid Azure AD join stöds för närvarande inte om din miljö som består av en enda AD-skog som synkroniserar identitetsdata till mer än en Azure AD-klient.

Hybrid Azure AD-anslutning stöds inte för närvarande när du använder virtuell datorinfrastruktur (VDI).

Hybrid Azure AD-anslutning stöds inte för FIPS-kompatibel TPM: er. Om dina enheter har FIPS-kompatibel TPM: er, måste du inaktivera dem innan du fortsätter med Hybrid Azure AD-anslutning. Microsoft tillhandahåller inte några verktyg för att inaktivera FIPS-läge för TPM: er eftersom den är beroende av TPM-tillverkaren. Kontakta din maskinvara OEM för support.

Hybrid Azure AD-anslutning stöds inte för Windows Server som kör domänkontrollanten (DC)-rollen.

Hybrid Azure AD-anslutning stöds inte på Windows äldre enheter när du använder centrala autentiseringsuppgifter eller användaren profil nätverksväxling.

Om du lita på systemförberedelseverktyget (Sysprep) och om du använder en **innan Windows 10 1809** bild för installation, se till att avbildningen inte från en enhet som redan har registrerats med Azure AD som Hybrid Azure AD-anslutning.

Om du lita på en virtuell dator (VM)-ögonblicksbild för att skapa ytterligare virtuella datorer, se till att ögonblicksbilden inte är från en virtuell dator som redan har registrerats med Azure AD som Hybrid Azure AD-anslutning.

Om din Windows 10-domänanslutna enheter är redan [Azure AD-registrerad](https://docs.microsoft.com/azure/active-directory/devices/overview#azure-ad-registered-devices) till din klient, vi rekommenderar starkt att ta bort det aktuella tillståndet innan du aktiverar Hybrid Azure AD-anslutning. Följande ändringar har gjorts att undvika det här dubbel tillståndet från Windows 10 1809 version:

- Alla befintliga Azure AD-registrerad tillstånd skulle tas bort automatiskt när enheten är Hybrid Azure AD-anslutna.
- Du kan förhindra att dina domänansluten enhet som Azure AD-registrerad genom att lägga till den här registernyckeln - HKLM\SOFTWARE\Policies\Microsoft\Windows\WorkplaceJoin, ”BlockAADWorkplaceJoin” = DWORD: 00000001.
- Den här ändringen är nu tillgänglig för Windows 10-1803 versionen med KB4489894 tillämpas. Men om du har Windows Hello för företag som har konfigurerats kan behöver användaren re-setup Windows Hello för företag när du har dubbel tillståndet Rensa.

## <a name="review-controlled-validation-of-hybrid-azure-ad-join"></a>Granska kontrollerad validering av hybrid Azure AD-anslutning

När alla krav är uppfyllda, kommer Windows-enheter automatiskt att registreras som enheter i din Azure AD-klient. Tillståndet för de här enhetsidentiteter i Azure AD kallas hybrid Azure AD-anslutning. Mer information om begrepp i den här artikeln finns i artiklarna [introduktionen till enhetshantering identitet i Azure Active Directory](overview.md) och [planera hybrid Azure Active Directory-anslutning implementering](hybrid-azuread-join-plan.md).

Organisationer vilja göra en kontrollerad validering av hybrid Azure AD-anslutning innan du aktiverar i hela organisationen på samma gång. I artikeln [kontrollerad validering av hybrid Azure AD-anslutning](hybrid-azuread-join-control.md) att förstå hur du utför den.

## <a name="select-your-scenario-based-on-your-identity-infrastructure"></a>Välj ditt scenario baserat på din infrastruktur för Identitetshantering

Hybrid Azure AD-anslutning fungerar med både hanterade och federerade miljöer.  

### <a name="managed-environment"></a>Hanterad miljö

En hanterad miljö kan vara distribueras antingen via [lösenord hash-synkronisering (PHS)](https://docs.microsoft.com/azure/active-directory/hybrid/whatis-phs) eller [skicka via autentisering (PTA)](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-pta) med [sömlös enkel inloggning](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sso).

Dessa scenarier måste inte du konfigurera en federationsserver för autentisering.

### <a name="federated-environment"></a>Federerad miljö

En federerad miljö bör ha en identitetsprovider som har stöd för följande krav:

- **WS-Trust-protokollet:** Det här protokollet krävs för att autentisera Windows aktuella hybrid Azure AD-anslutna enheter med Azure AD.
- **WIAORMULTIAUTHN anspråk:** Det här anspråket krävs för att göra hybrid Azure AD-anslutning för Windows äldre enheter.

Om du har en federerad miljö använder Active Directory Federation Services (AD FS), stöds redan ovanstående krav.

> [!NOTE]
> Azure AD stöder inte smartkort eller certifikat i hanterade domäner.

Från och med version 1.1.819.0 tillhandahåller Azure AD Connect en guide för att konfigurera Hybrid Azure AD-koppling. Med guiden kan du förenkla konfigurationsprocessen avsevärt. Om du installerar den nödvändiga versionen av Azure AD Connect inte är ett alternativ för dig, se [hur du manuellt konfigurera enhetsregistrering](https://docs.microsoft.com/azure/active-directory/devices/hybrid-azuread-join-manual). 

Baserat på scenariot som matchar din infrastruktur för Identitetshantering, se:

- [Konfigurera hybrid Azure Active Directory-anslutning för federerad miljö](hybrid-azuread-join-federated-domains.md)
- [Konfigurera hybrid Azure Active Directory-anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

## <a name="review-on-premises-ad-upn-support-for-hybrid-azure-ad-join"></a>Granska lokala AD UPN-stöd för Hybrid Azure AD-anslutning

Ibland kan din lokala AD UPN: er kan skilja sig från din Azure AD UPN-namn. I sådana fall kan Windows 10-Hybrid Azure AD-anslutning har begränsat stöd för lokala AD UPN-namn baserat på den [autentiseringsmetod](https://docs.microsoft.com/azure/security/azure-ad-choose-authn), typ och version av Windows 10. Det finns två typer av lokala AD UPN: er som kan finnas i din miljö:

- Dirigerbara UPN: Ett dirigerbart UPN har en giltig verifierad domän som har registrerats hos en domänregistrator. Till exempel om contoso.com är den primära domänen i Azure AD, contoso.org är den primära domänen i den lokala AD som ägs av Contoso och [verifierats i Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-custom-domain)
- Icke-dirigerbara UPN: Ett icke-dirigerbara UPN har inte en verifierad domän. Det gäller endast inom din organisations privata nätverk. Till exempel om contoso.com är den primära domänen i Azure AD, contoso.local är den primära domänen i lokala AD men är inte en verifierbar domän på internet och används i Contoso endast användarens nätverk.

Tabellen nedan innehåller information om stöd för dessa lokala AD UPN: er i Windows 10-Hybrid Azure AD-anslutning

| Typ av en lokal AD UPN | Domäntyp | Windows 10 version | Beskrivning |
| ----- | ----- | ----- | ----- |
| Dirigerbara | Federerad | Från version 1703 | Allmänt tillgänglig |
| Icke-dirigerbara | Federerad | Från version 1803 | Allmänt tillgänglig |
| Dirigerbara | Hanterad | Stöds inte | |
| Icke-dirigerbara | Hanterad | Stöds inte | |

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Konfigurera hybrid Azure Active Directory-anslutning för federerade miljön](hybrid-azuread-join-federated-domains.md)
> [konfigurera hybrid Azure Active Directory-anslutning för hanterad miljö](hybrid-azuread-join-managed-domains.md)

<!--Image references-->
[1]: ./media/hybrid-azuread-join-plan/12.png
