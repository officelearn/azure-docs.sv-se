---
title: Översikt över Enterprise Tillståndsväxling | Microsoft Docs
description: Innehåller information om Enterprise State Roaming-inställningar i Windows-enheter. Enterprise State Roaming ger användarna en enhetlig upplevelse på sina Windows-enheter och minskar den tid som behövs för att konfigurera en ny enhet.
services: active-directory
keywords: Vad är Enterprise State Roaming, enterprise-synkronisering, molnbaserad för windows
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 83b3b58f-94c1-4ab0-be05-20e01f5ae3f0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2018
ms.author: markvi
ms.openlocfilehash: c2c94f488da6399965c59f761ff9f0539cf4de2f
ms.sourcegitcommit: 44fa77f66fb68e084d7175a3f07d269dcc04016f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/24/2018
ms.locfileid: "39228118"
---
# <a name="enterprise-state-roaming-overview"></a>Översikt över enterprise tillståndsväxling
Med Windows 10, [Azure Active Directory (Azure AD)](fundamentals/active-directory-whatis.md) användare få möjlighet att synkronisera sina användarinställningar och inställningar för programdata till molnet på ett säkert sätt. Enterprise State Roaming ger användarna en enhetlig upplevelse på sina Windows-enheter och minskar den tid som behövs för att konfigurera en ny enhet. Enterprise State Roaming fungerar ungefär som standard [synkronisering av konsumenten](http://windows.microsoft.com/en-US/windows-8/sync-settings-pcs) som introducerades i Windows 8. Enterprise State Roaming erbjuder dessutom:

* **Uppdelning av företagets och konsumentdata** – organisationer har kontroll över sina data och det finns ingen blandning av företagets data i ett molnkonto för konsumenter eller konsumentdata i en enterprise cloud-konto.
* **Förbättrad säkerhet** – Data krypteras automatiskt innan de lämnar användarens Windows 10-enhet med hjälp av Azure Rights Management (Azure RMS) och data förblir krypterade i vila i molnet. Allt innehåll förblir krypterade i vila i molnet, förutom namnområden, till exempel namn på inställningar och Windows-app.  
* **Bättre hantering och övervakning** – ger kontroll och synlighet över som synkroniserar inställningar i din organisation och på vilka enheter via Azure AD portal-integrering. 

Enterprise State Roaming är tillgängligt i flera Azure-regioner. Du hittar den uppdaterade listan över tillgängliga regioner på den [Azure-tjänster efter regioner](https://azure.microsoft.com/regions/#services) sidan under Azure Active Directory.

| Artikel | Beskrivning |
| --- | --- |
| [Aktivera Enterprise Tillståndsväxling i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md) |Enterprise State Roaming är tillgänglig för alla företag med en Premium Azure Active Directory (Azure AD)-prenumeration. Mer information om hur du hämtar en Azure AD-prenumeration finns i den [Azure AD-produkt](https://azure.microsoft.com/services/active-directory) sidan. |
| [Inställningar och dataväxling vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md) |Det här avsnittet får du svar på några frågor som IT-administratörer kan ha om inställningar och data appsynkronisering. |
| [Gruppen principer och MDM-inställningar för synkronisering av inställningar](active-directory-windows-enterprise-state-roaming-group-policy-settings.md) |Windows 10 ger en Grupprincip och hantering av mobilenheter (MDM)-principinställningar för att begränsa inställningar för synkronisering. |
| [Centrala inställningsreferens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) |Följande är en fullständig lista över alla inställningar som kommer att flyttade och/eller säkerhetskopierade i Windows 10. |
| [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md) |Det här avsnittet går igenom några grundläggande steg för att felsöka och innehåller en lista över kända problem. |

