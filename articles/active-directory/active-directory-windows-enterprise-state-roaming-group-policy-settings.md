---
title: "Principen och MDM-inställningar | Microsoft Docs"
description: "Innehåller information om grupprinciper och mobila enheter (MDM) inställningar som ska användas på företagsägda enheter. Dessa principer som tillämpas på användarens hela enheten."
services: active-directory
keywords: "Vad är grupp princip och MDM-inställningar för Enterprise tillstånd Roaming, Enterprise tillstånd Roaming, windows molnet"
documentationcenter: 
author: tanning
manager: femila
editor: curtand
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2017
ms.author: markvi
ms.openlocfilehash: 71dd5281a618fe7367eab3e97daac069f77ab491
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="group-policy-and-mdm-settings"></a>Inställningar för Grupprincip och MDM
Dessa Grupprincip och hanteringsinställningar för mobila enheter (MDM) endast användas på företagsägda enheter eftersom dessa principer tillämpas på användarens hela enheten. Använda en MDM-princip för att inaktivera synkronisering av inställningar för en personlig ska enhet som ägs av användare påverka användningen av enheten. Andra konton på enheten kommer dessutom också att påverkas av principen.

Företag som vill hantera centrala för personliga (ohanterade) enheter kan använda Azure-portalen för att aktivera eller inaktivera centrala i stället för att använda en grupprincip eller MDM.
I följande tabeller beskrivs de tillgängliga inställningarna.

## <a name="mdm-settings"></a>MDM-inställningar
MDM-principinställningar gäller för både Windows 10 och Windows 10 Mobile.  Det finns stöd för Windows 10 Mobile endast för Microsoft-konto baserat centrala via användarens OneDrive-konto.  Se avsnittet ”enheter och slutpunkter” mer information om vilka enheter som stöds för Azure AD-baserade synkronisering.

| Namn | Beskrivning |
| --- | --- |
| Tillåt Microsoft-konto-anslutning |Tillåter användare att autentisera med ett Microsoft-konto på enheten |
| Tillåt synkronisering av Mina inställningar |Tillåter användare att flytta Windows-inställningar och data i appen. Om du inaktiverar den här principen inaktiverar synkronisering samt säkerhetskopieringar på mobila enheter |

## <a name="group-policy-settings"></a>Grupprincipinställningar
Inställningarna för grupprinciper gäller för Windows 10-enheter som är anslutna till en Active Directory-domän. Tabellen innehåller också äldre inställningar som visas för att hantera synkroniseringsinställningar för, men som inte arbetar för Enterprise tillstånd Roaming för Windows 10, som är märkta med ”Använd inte” i beskrivningen.

| Namn | Beskrivning |
| --- | --- |
| Konton: Blockera Microsoft-konton |Den här inställningen förhindrar att användare lägger till nya Microsoft-konton på den här datorn |
| Är inte synkroniserade |Förhindrar användare att flytta Windows-inställningar och AppData |
| Synkroniserar inte anpassa |Inaktiverar synkroniseringen av gruppen teman |
| Synkronisera inte inställningar för webbläsaren |Inaktiverar synkroniseringen av Internet Explorer-grupp |
| Synkronisera inte lösenord |Inaktiverar synkronisering av lösenord grupp |
| Synkronisera inte andra Windows-inställningar |Inaktiverar synkroniseringen av gruppen för andra Windows-inställningar |
| Synkronisera inte skrivbordsanpassning |Använd inte; har ingen effekt |
| Synkronisera inte i med datapriser |Inaktiverar roaming på förbrukade anslutningar, till exempel mobil 3 G |
| Är inte synkroniserade appar |Använd inte; har ingen effekt |
| Synkronisera inte app-inställningar |Inaktiverar roaming för AppData |
| Synkronisera inte inställningarna för start |Använd inte; har ingen effekt |

## <a name="related-topics"></a>Relaterade ämnen
* [Företagsroaming](active-directory-windows-enterprise-state-roaming-overview.md)
* [Aktivera företagsroaming i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Inställningar och dataroaming vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Centrala referens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

