---
title: Vad är enterprise tillståndsväxling i Azure Active Directory? | Microsoft Docs
description: Enterprise State Roaming ger användarna en enhetlig upplevelse på sina Windows-enheter och minskar den tid som behövs för att konfigurera en ny enhet.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: overview
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: c5b60970592180a2353860369e637d4b9a9bb8f9
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481911"
---
# <a name="what-is-enterprise-state-roaming"></a>Vad är Enterprise State Roaming?

Med Windows 10, [Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) användare få möjlighet att synkronisera sina användarinställningar och inställningar för programdata till molnet på ett säkert sätt. Enterprise State Roaming ger användarna en enhetlig upplevelse på sina Windows-enheter och minskar den tid som behövs för att konfigurera en ny enhet. Enterprise State Roaming fungerar ungefär som standard [synkronisering av konsumenten](https://go.microsoft.com/fwlink/?linkid=2015135) som introducerades i Windows 8. Enterprise State Roaming erbjuder dessutom:

* **Uppdelning av företagets och konsumentdata** – organisationer har kontroll över sina data och det finns ingen blandning av företagets data i ett molnkonto för konsumenter eller konsumentdata i en enterprise cloud-konto.
* **Förbättrad säkerhet** – Data krypteras automatiskt innan de lämnar användarens Windows 10-enhet med hjälp av Azure Rights Management (Azure RMS) och data förblir krypterade i vila i molnet. Allt innehåll förblir krypterade i vila i molnet, förutom namnområden, till exempel namn på inställningar och Windows-app.  
* **Bättre hantering och övervakning** – ger kontroll och synlighet över som synkroniserar inställningar i din organisation och på vilka enheter via Azure AD portal-integrering. 

Enterprise State Roaming är tillgängligt i flera Azure-regioner. Du hittar den uppdaterade listan över tillgängliga regioner på den [Azure-tjänster efter regioner](https://azure.microsoft.com/regions/#services) sidan under Azure Active Directory.

| Artikel | Beskrivning |
| --- | --- |
| [Aktivera Enterprise Tillståndsväxling i Azure Active Directory](enterprise-state-roaming-enable.md) |Enterprise State Roaming är tillgänglig för alla företag med en Premium Azure Active Directory (Azure AD)-prenumeration. Mer information om hur du hämtar en Azure AD-prenumeration finns i den [Azure AD-produkt](https://azure.microsoft.com/services/active-directory) sidan. |
| [Inställningar och dataväxling vanliga frågor och svar](enterprise-state-roaming-faqs.md) |Det här avsnittet får du svar på några frågor som IT-administratörer kan ha om inställningar och data appsynkronisering. |
| [Gruppen principer och MDM-inställningar för synkronisering av inställningar](enterprise-state-roaming-group-policy-settings.md) |Windows 10 ger en Grupprincip och hantering av mobilenheter (MDM)-principinställningar för att begränsa inställningar för synkronisering. |
| [Centrala inställningsreferens för Windows 10](enterprise-state-roaming-windows-settings-reference.md) |Följande är en fullständig lista över alla inställningar som kommer att flyttade och/eller säkerhetskopierade i Windows 10. |
| [Felsökning](enterprise-state-roaming-troubleshooting.md) |Det här avsnittet går igenom några grundläggande steg för att felsöka och innehåller en lista över kända problem. |

## <a name="next-steps"></a>Nästa steg

Information om hur du aktiverar enterprise tillståndsväxling finns i [aktivera enterprise tillståndsväxling](enterprise-state-roaming-enable.md).
