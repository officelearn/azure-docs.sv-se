---
title: Principen och MDM-inställningar | Microsoft Docs
description: Innehåller information om Grupprincip och mobila enheter (MDM) hanteringsinställningar som ska användas på företagsägda enheter. Dessa principer tillämpas på användarens hela enheten.
services: active-directory
keywords: Vad är grupp principen och MDM-inställningar för Enterprise State Roaming, Enterprise State Roaming, windows molnet
documentationcenter: ''
author: MarkusVi
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: 6471a9b3-8dd4-4237-89d1-bfbeca9f8252
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: f1bbbecf1f40b110614b3da9874d5d2c560f9e58
ms.sourcegitcommit: d211f1d24c669b459a3910761b5cacb4b4f46ac9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/06/2018
ms.locfileid: "44024643"
---
# <a name="group-policy-and-mdm-settings"></a>Inställningar för grupprinciper och MDM
Använd dessa Grupprincip och hanteringsinställningar för mobila enheter (MDM) endast på företagsägda enheter eftersom dessa principer tillämpas på användarens hela enheten. Tillämpa en MDM-principen för att inaktivera synkronisering av inställningar för en personlig påverkar användarägda enheter negativt användningen av enheten. Dessutom kommer andra användarkonton på enheten också att påverkas av principen.

Företag som vill hantera för personliga (ohanterade) enheter kan använda Azure-portalen för att aktivera eller inaktivera roaming, snarare än att med hjälp av en grupprincip eller MDM.
I följande tabeller beskrivs de tillgängliga inställningarna.

## <a name="mdm-settings"></a>MDM-inställningar
Inställningarna för MDM-principen gäller för både Windows 10 och Windows 10 Mobile.  Windows 10 Mobile-stöd finns endast för Microsoft-konto baserat nätverksväxling via användarens OneDrive-konto.  Se [enheter och slutpunkter](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md) mer information om vilka enheter som stöds för Azure AD-baserad synkronisering.

| Namn | Beskrivning |
| --- | --- |
| Tillåt Microsoft-konto-anslutning |Tillåter användare att autentisera med ett Microsoft-konto på enheten |
| Tillåt synkronisering av Mina inställningar |Tillåter användare att flytta Windows-inställningar och AppData; Inaktiverar den här principen kommer att inaktivera synkronisering samt säkerhetskopior på mobila enheter |

## <a name="group-policy-settings"></a>Grupprincipinställningar
Inställningarna för grupprinciper gäller för Windows 10-enheter som är anslutna till en Active Directory-domän. Tabellen innehåller också äldre inställningar som skulle visas för att hantera synkroniseringsinställningar, men som inte arbetar för företaget tillstånd Roaming för Windows 10, som är märkta med ”Använd inte” i beskrivningen.

| Namn | Beskrivning |
| --- | --- |
| Konton: Blockera Microsoft-konton |Den här inställningen förhindrar användare från att lägga till nya Microsoft-konton på den här datorn |
| Synkronisera inte |Förhindrar att användare om du vill flytta Windows-inställningar och AppData |
| Synkroniserar inte anpassa |Inaktiverar synkroniseringen av gruppen teman |
| Synkronisera inte inställningar för webbläsaren |Inaktiverar synkronisering av Internet Explorer-grupp |
| Synkronisera inte lösenord |Inaktiverar synkronisering av lösenord grupp |
| Synkronisera inte andra Windows-inställningar |Inaktiverar synkroniseringen i gruppen för andra Windows-inställningar |
| Synkronisera inte skrivbordsanpassning |Använd inte; har ingen effekt |
| Synkronisera inte på anslutningar med datapriser |Inaktiverar roaming på förbrukade anslutningar, till exempel mobilt 3 G |
| Inte synkronisera appar |Använd inte; har ingen effekt |
| Synkronisera inte appinställningar |Inaktiverar nätverksväxling av AppData |
| Synkronisera inte startinställningar för |Använd inte; har ingen effekt |

## <a name="related-topics"></a>Relaterade ämnen
* [Översikt över Enterprise Tillståndsväxling](active-directory-windows-enterprise-state-roaming-overview.md)
* [Aktivera enterprise tillståndsväxling i Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
* [Inställningar och dataväxling vanliga frågor och svar](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Centrala inställningsreferens för Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Felsökning](active-directory-windows-enterprise-state-roaming-troubleshooting.md)

