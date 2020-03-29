---
title: Vad är en hybrid-Azure AD-ansluten enhet?
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
ms.openlocfilehash: 15cdaba7d63d72aab25757e7ba6f5eadc48e026a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512257"
---
# <a name="hybrid-azure-ad-joined-devices"></a>Hybrid Azure AD-anslutna enheter

I över ett årtionde har många organisationer använt domänanslutningen för att ansluta till sin lokala Active Directory för att göra det möjligt för:

- IT-avdelningar att hantera företagsägda enheter från en central plats.
- Användare att logga in på sina enheter med sina arbets- eller skolkonton i Active Directory.

Vanligtvis är organisationer med ett lokalt fotavtryck beroende av bildhanteringsmetoder för att etablera enheter, och de använder ofta **Configuration Manager** eller **grupprincip (GP)** för att hantera dem.

Om din miljö har ett lokalt AD-fotavtryck och du vill dra nytta av funktionerna i Azure Active Directory kan du implementera Hybrid Azure AD-anslutna enheter. Dessa enheter är enheter som är anslutna till din lokala Active Directory och som är registrerade med din Azure Active Directory.

|   | Hybrid Azure AD-koppling |
| --- | --- |
| **Definition** | Gick med i lokala AD och Azure AD som kräver att organisationskontot loggar in på enheten |
| **Primär målgrupp** | Lämplig för hybridorganisationer med befintlig lokal AD-infrastruktur |
|   | Gäller för alla användare i en organisation |
| **Äganderätt till enhet** | Organisation |
| **Operativsystem** | Windows 10, 8.1 och 7 |
|   | Windows Server 2008/R2, 2012/R2, 2016 och 2019 |
| **Etablering** | Windows 10, Windows Server 2016/2019 |
|   | Domänkoppling via IT och autojoin via Azure AD Connect eller ADFS config |
|   | Domänkoppling via Windows Autopilot och autojoin via Azure AD Connect eller ADFS config |
|   | Windows 8.1, Windows 7, Windows Server 2012 R2, Windows Server 2012 och Windows Server 2008 R2 – Kräv MSI |
| **Alternativ för inloggning på enheten** | Organisationskonton med hjälp av: |
|   | lösenord |
|   | Windows Hello för företag för Win10 |
| **Enhetshantering** | Grupprincip |
|   | Fristående Configuration Manager- eller samhantering med Microsoft Intune |
| **De viktigaste funktionerna** | SSO till både molnresurser och lokala resurser |
|   | Villkorlig åtkomst via domänanslutning eller via Intune om samhanterad |
|   | Återställning av lösenord med självbetjäning och Windows Hello PIN-återställning på låsskärmen |
|   | Roaming i företagstillstånd på olika enheter |

![Hybrid Azure AD-anslutna enheter](./media/concept-azure-ad-join-hybrid/azure-ad-hybrid-joined-device.png)

## <a name="scenarios"></a>Scenarier

Använd Azure AD hybrid-anslutna enheter om:

- Du har Win32-appar som är distribuerade till enheterna som förlitar sig på Active Directory-autentisering för datorn.
- Du vill fortsätta att använda grupprincipen för att hantera enhetskonfiguration.
- Du vill fortsätta att använda befintliga bildhanteringslösningar för att distribuera och konfigurera enheter.
- Du måste ha stöd för enheter på lägre nivå i Windows 7 och 8.1 utöver Windows 10

## <a name="next-steps"></a>Nästa steg

- [Planera implementering av Azure AD-anslutningshybriden](hybrid-azuread-join-plan.md)
- [Hantera enhetsidentiteter med Azure-portalen](device-management-azure-portal.md)
- [Hantera inaktuella enheter i Azure AD](manage-stale-devices.md)
