---
title: Vad är en Azure AD-ansluten enhet?
description: Lär dig hur enhetsidentitetshantering kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/27/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: sandeo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40f89fbe19b93601f9e0525f0387e402bd175fe4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672668"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

Azure AD-koppling är avsedd för organisationer som vill vara moln-först eller moln-bara. Alla organisationer kan distribuera Azure AD-anslutna enheter oavsett storlek eller bransch. Azure AD-koppling fungerar även i en hybridmiljö, vilket ger åtkomst till både moln- och lokala appar och resurser.

|   | Azure AD Join |
| --- | --- |
| **Definition** | Gick bara med i Azure AD som kräver att organisationskontot loggar in på enheten |
| **Primär målgrupp** | Lämplig för både moln- och hybridorganisationer. |
|   | Gäller för alla användare i en organisation |
| **Äganderätt till enhet** | Organisation |
| **Operativsystem** | Alla Windows 10-enheter |
| **Etablering** | Självbetjäning: Windows OOBE eller Inställningar |
|   | Massregistrering |
|   | Windows Autopilot |
| **Alternativ för inloggning på enheten** | Organisationskonton med hjälp av: |
|   | lösenord |
|   | Windows Hello för företag |
|   | FIDO2.0-säkerhetsnycklar (förhandsgranskning) |
| **Enhetshantering** | Hantering av mobila enheter (exempel: Microsoft Intune) |
|   | Samhantering med Microsoft Intune och Microsoft Endpoint Configuration Manager |
| **De viktigaste funktionerna** | SSO till både molnresurser och lokala resurser |
|   | Villkorlig åtkomst genom MDM-registrering och MDM-efterlevnadsutvärdering |
|   | Återställning av lösenord med självbetjäning och Windows Hello PIN-återställning på låsskärmen |
|   | Roaming i företagstillstånd på olika enheter |

Azure AD-anslutna enheter loggas in på med ett organisatoriskt Azure AD-konto. Åtkomsten till resurser i organisationen kan begränsas ytterligare baserat på de Azure AD-konto- och [villkorlig åtkomstprinciper](../conditional-access/overview.md) som tillämpas på enhetsidentiteten.

Administratörer kan skydda och ytterligare styra Azure AD-anslutna enheter med mdm-verktyg (Mobile Device Management) som Microsoft Intune eller i samhanteringsscenarier med Microsoft Endpoint Configuration Manager. Dessa verktyg är ett sätt att framtvinga konfigurationer som krävs av organisationen, till exempel att kräva att lagring krypteras, lösenordskomplexitet, programvaruinstallationer och programuppdateringar. Administratörer kan göra organisationsprogram tillgängliga för Azure AD-anslutna enheter med Configuration Manager för att [hantera appar från Microsoft Store för företag och utbildning](/configmgr/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD-koppling kan utföras med självbetjäningsalternativ som OOBE (Out of Box Experience), massregistrering eller [Windows Autopilot](/intune/enrollment-autopilot).

Azure AD-anslutna enheter kan fortfarande ha enkel inloggningsåtkomst till lokala resurser när de finns i organisationens nätverk. Enheter som är Azure AD-anslutna kan fortfarande autentisera till lokala servrar som fil, utskrift och andra program.

## <a name="scenarios"></a>Scenarier

Azure AD Join är främst avsett för organisationer som inte har någon lokal Windows Server Active Directory-infrastruktur, men du kan använda det i scenarier där:

- Du vill gå över till molnbaserad infrastruktur med Azure AD och MDM som Intune.
- Du inte kan använda en lokal domänanslutning, till exempel om du vill hämta mobila enheter som surfplattor och telefoner som kontrolleras.
- Användarna främst behöver åtkomst till Office 365 eller övriga SaaS-appar som är integrerade med Azure AD.
- Du vill hantera en grupp användare i Azure AD istället för Active Directory. Det här scenariot kan till exempel gälla säsongsarbetare, entreprenörer eller studenter.
- Du vill erbjuda anslutningsfunktioner för medarbetare på filialkontor med begränsad lokal infrastruktur.

Du kan konfigurera Azure AD-anslutna enheter för Windows 10-enheter.

Målet med Azure AD-anslutna enheter är att förenkla:

- Windows-distributioner av företagsägda enheter
- Åtkomst till företagsappar och -resurser från valfri Windows-enhet
- Molnbaserad hantering av företagsägda enheter
- Användare att logga in på sina enheter med sina Azure AD eller synkroniserade Active Directory arbets- eller skolkonton.

![Azure AD-anslutna enheter](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD Join kan distribueras på något av följande sätt:

- [Windows Autopilot](/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Massdistribution](/intune/windows-bulk-enroll)
- [Självbetjäning](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Nästa steg

- [Planera implementeringen av Azure AD-anslutningen](azureadjoin-plan.md)
- [Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter](assign-local-admin.md)
- [Hantera enhetsidentiteter med Azure-portalen](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
