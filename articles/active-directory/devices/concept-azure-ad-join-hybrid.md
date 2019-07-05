---
title: Vad är en hybrid Azure AD ansluten enhet?
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
ms.openlocfilehash: 5c57180ba10322cb790c05b3f8f48043ca08b545
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/28/2019
ms.locfileid: "67462751"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

I över ett årtionde har många organisationer använt domänanslutningen för att ansluta till sin lokala Active Directory för att göra det möjligt för:

- IT-avdelningar att hantera företagsägda enheter från en central plats.
- Användare att logga in på sina enheter med sina arbets- eller skolkonton i Active Directory.

Vanligtvis förlitar sig organisationer med ett lokalt fotavtryck på bildhanteringsmetoder för att etablera enheter, och de använder ofta **System Center Configuration Manager (SCCM)** eller **grupprinciper (GP)** för att hantera dem.

Om din miljö har ett lokalt AD-fotavtryck och du vill dra nytta av funktionerna i Azure Active Directory kan du implementera Hybrid Azure AD-anslutna enheter. Dessa enheter är enheter som är anslutna till din lokala Active Directory och registrerad med Azure Active Directory.

|   | Hybrid Azure AD-anslutning |
| --- | --- |
| **Definition** | Ansluten till den lokala AD och Azure AD kräver organisationskonto för att logga in på enheten |
| **Primära målgruppen** | Lämpligt för hybridorganisationer med befintliga lokala AD-infrastruktur |
|   | Gäller för alla användare i en organisation |
| **Ägarskap för enhet** | Organisation |
| **Operativsystem** | Windows 10, 8.1 och 7 |
|   | Windows Server 2008/R2, 2012/R2 och 2016 och 2019 |
| **Etablering** | Windows 10, Windows Server 2016/2019 |
|   | Domänanslutning av IT och koppling via Azure AD Connect eller AD FS-konfiguration |
|   | Domänanslutning av Windows Autopilot och koppling via Azure AD Connect eller AD FS-konfiguration |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2 - kräver MSI |
| **Enheten inloggningsalternativ** | Organisationskonton med: |
|   | Lösenord |
|   | Windows Hello för företag för Win10 |
| **Enhetshantering** | En Grupprincip |
|   | System Center Configuration Manager fristående eller samhantering med Microsoft Intune |
| **Viktiga funktioner** | Enkel inloggning till både i molnet och lokala resurser |
|   | Villkorlig åtkomst via en domänanslutning eller Intune om hanteras tillsammans |
|   | Självbetjäning för återställning av lösenord och Windows PIN-kod för återställning på låsskärm |
|   | Enterprise State Roaming mellan enheter |

![Hybrid Azure AD-anslutna enheter](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenarier

Använd Azure AD-anslutna hybridenheter om:

- Du har Win32-appar som är distribuerade till enheterna som förlitar sig på Active Directory-autentisering för datorn.
- Vill du fortsätta att använda grupprinciper för att hantera enhetskonfiguration.
- Vill du fortsätta att använda befintliga avbildning lösningar för att distribuera och konfigurera enheter.
- Du måste ha stöd för äldre Windows 7 och 8.1-enheter utöver Windows 10

## <a name="next-steps"></a>Nästa steg

- [Planera implementering av Azure AD Join-hybrid](hybrid-azuread-join-plan.md)
- [Hantera enhetsidentiteter med Azure portal](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
