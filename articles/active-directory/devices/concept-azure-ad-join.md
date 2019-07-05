---
title: Vad är en Azure AD ansluten enhet?
description: Lär dig hur enheten identity management kan hjälpa dig att hantera enheter som har åtkomst till resurser i din miljö.
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
ms.openlocfilehash: 4af3aea7218ea8792bb66188e8df7baf9f460b0b
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462816"
---
# <a name="azure-ad-joined-devices"></a>Azure AD-anslutna enheter

Azure AD join är avsedd för organisationer som vill vara molnorienterade eller enbart i molnet. Alla organisationer kan distribuera Azure AD-anslutna enheter oavsett storlek eller branschen. Azure AD-anslutning fungerar även i en hybridmiljö att aktivera åtkomst till både i molnet och lokala appar och resurser.

|   | Azure AD Join |
| --- | --- |
| **Definition** | Domänanslutna endast till Azure AD kräver organisationskonto för att logga in på enheten |
| **Primära målgruppen** | Lämpligt för både molnbaserad och hybridorganisationer. |
|   | Gäller för alla användare i en organisation |
| **Ägarskap för enhet** | Organisation |
| **Operativsystem** | Alla Windows 10-enheter |
| **Etablering** | Självbetjäning: Windows OOBE eller inställningar |
|   | Massregistrering |
|   | Windows Autopilot |
| **Enheten inloggningsalternativ** | Organisationskonton med: |
|   | Lösenord |
|   | Windows Hello för företag |
|   | FIDO2.0 säkerhetsnycklar (förhandsversion) |
| **Enhetshantering** | Hantering av mobila enheter (exempel: Microsoft Intune) |
|   | Samhantering med Microsoft Intune och System Center Configuration Manager |
| **Viktiga funktioner** | Enkel inloggning till både i molnet och lokala resurser |
|   | Villkorlig åtkomst via MDM-registrering och utvärdering av MDM-efterlevnad |
|   | Självbetjäning för återställning av lösenord och Windows PIN-kod för återställning på låsskärm |
|   | Enterprise State Roaming mellan enheter |

Azure AD-anslutna enheter är inloggad på med hjälp av en organisations Azure AD-konto. Åtkomst till resurser i organisationen kan vara mer begränsad baserat på det Azure AD-kontot och [principer för villkorlig åtkomst](../conditional-access/overview.md) tillämpas på enhetens identitet.

Administratörer kan skydda och ytterligare kontroll Azure AD-anslutna enheter med hjälp av Mobile Device Management (MDM)-verktyg som Microsoft Intune eller i samhantering scenarier med hjälp av System Center Configuration Manager. Dessa verktyg ger ett sätt att tillämpa organisation krävs konfigurationer som kräver lagring krypteras, lösenordskomplexitet, programinstallationer och uppdateringar. Administratörer kan göra organisationsprogram tillgängliga i Azure AD-anslutna enheter med hjälp av [System Center Configuration Manager och Microsoft Store för företag](https://docs.microsoft.com/sccm/apps/deploy-use/manage-apps-from-the-windows-store-for-business).

Azure AD-anslutning kan utföras med hjälp av alternativ för självbetjäning som de Out of Box Experience (OOBE), massregistrering, eller [Windows Autopilot](https://docs.microsoft.com/intune/enrollment-autopilot).

Azure AD-anslutna enheter kan fortfarande ha enkel inloggning för åtkomst till lokala resurser när de är på organisationens nätverk. Enheter som är ansluten till Azure AD kan fortfarande autentisera till lokala servrar som fil, utskrift och andra program.

## <a name="scenarios"></a>Scenarier

Azure AD Join är främst avsett för organisationer som inte har någon lokal Windows Server Active Directory-infrastruktur, men du kan använda det i scenarier där:

- Du vill gå över till molnbaserad infrastruktur med Azure AD och MDM som Intune.
- Du inte kan använda en lokal domänanslutning, till exempel om du vill hämta mobila enheter som surfplattor och telefoner som kontrolleras.
- Användarna främst behöver åtkomst till Office 365 eller övriga SaaS-appar som är integrerade med Azure AD.
- Du vill hantera en grupp användare i Azure AD istället för Active Directory. Det här scenariot kan exempelvis använda säsongsarbetare, leverantörer eller studenter.
- Du vill erbjuda anslutningsfunktioner för medarbetare på filialkontor med begränsad lokal infrastruktur.

Du kan konfigurera Azure AD-anslutna enheter för Windows 10-enheter.

Målet med Azure AD-anslutna enheter är att förenkla:

- Windows-distributioner av företagsägda enheter
- Åtkomst till företagsappar och -resurser från valfri Windows-enhet
- Molnbaserad hantering av företagsägda enheter
- Användare att logga in på sina enheter med sina Azure AD eller synkroniserade Active Directory arbets- eller skolkonton.

![Azure AD-anslutna enheter](./media/concept-azure-ad-join/azure-ad-joined-device.png)

Azure AD Join kan distribueras på något av följande sätt:

- [Windows Autopilot](https://docs.microsoft.com/windows/deployment/windows-autopilot/windows-10-autopilot)
- [Massdistribution](https://docs.microsoft.com/intune/windows-bulk-enroll)
- [Självbetjäning](azuread-joined-devices-frx.md)

## <a name="next-steps"></a>Nästa steg

- [Planera implementeringen Azure AD join](azureadjoin-plan.md)
- [Hantera den lokala administratörsgruppen på Azure AD-anslutna enheter](assign-local-admin.md)
- [Hantera enhetsidentiteter med Azure portal](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
