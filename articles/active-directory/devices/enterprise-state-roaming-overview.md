---
title: Vad är roaming i företagstillstånd i Azure Active Directory?
description: Företagstillståndsroaming ger användarna en enhetlig upplevelse på sina Windows-enheter
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c22baf0a08718883f0c0c9844cc395f607b5b20d
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "77194287"
---
# <a name="what-is-enterprise-state-roaming"></a>Vad är Enterprise State Roaming?

Med Windows 10 får [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) användare möjlighet att på ett säkert sätt synkronisera sina användarinställningar och programinställningar data till molnet. Företagstillståndsroaming ger användarna en enhetlig upplevelse på sina Windows-enheter och minskar den tid som krävs för att konfigurera en ny enhet. Företagsstat Roaming fungerar ungefär som [standardinställningarna för konsumentinställningar](https://go.microsoft.com/fwlink/?linkid=2015135) som först introducerades i Windows 8. Dessutom erbjuder Roaming i företagsstater:

* **Separation av företags- och konsumentdata** – Organisationer har kontroll över sina data och det finns ingen blandning av företagsdata i ett konsumentmolnkonto eller konsumentdata i ett företagsmolnkonto.
* **Förbättrad säkerhet** – Data krypteras automatiskt innan de lämnar användarens Windows 10-enhet med hjälp av Azure Rights Management (Azure RMS) och data förblir krypterade i vila i molnet. Allt innehåll förblir krypterat i vila i molnet, förutom namnområden, till exempel inställningsnamn och Windows-appnamn.  
* **Bättre hantering och övervakning** – Ger kontroll och synlighet över vem som synkroniserar inställningar i din organisation och på vilka enheter via Azure AD-portalintegrationen. 

Roaming i företagstillstånd är tillgängligt i flera Azure-regioner. Du hittar den uppdaterade listan över tillgängliga regioner på sidan [Azure Services by Regions](https://azure.microsoft.com/regions/#services) under Azure Active Directory.

| Artikel | Beskrivning |
| --- | --- |
| [Aktivera enterprise tillståndsväxling i Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming är tillgängligt för alla organisationer med en Azure Active Directory-prenumeration (Premium Active Directory). Mer information om hur du skaffar en Azure AD-prenumeration finns på [produktsidan för Azure AD.](https://azure.microsoft.com/services/active-directory) |
| [Vanliga frågor och svar om inställningar och dataväxling](enterprise-state-roaming-faqs.md) |Den här artikeln besvarar några frågor som IT-administratörer kan ha om inställningar och synkronisering av appdata. |
| [Grupprincip- och MDM-inställningar för inställningar synkroniseras](enterprise-state-roaming-group-policy-settings.md) |Windows 10 tillhandahåller mdm-principinställningar (Group Policy och Mobile Device Management) för att begränsa synkroniseringen av inställningar. |
| [Referens för Windows 10-växlingsinställningar](enterprise-state-roaming-windows-settings-reference.md) |En lista över inställningar som kommer att flyttas och/eller säkerhetskopieras i Windows 10. |
| [Troubleshooting](enterprise-state-roaming-troubleshooting.md) (Felsökning) |Den här artikeln går igenom några grundläggande steg för felsökning och innehåller en lista över kända problem. |

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar roaming i företagstillstånd finns i [aktivera roaming i företagstillstånd](enterprise-state-roaming-enable.md).
