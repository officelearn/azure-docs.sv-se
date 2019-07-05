---
title: Principen och MDM-inställningar | Microsoft Docs
description: Innehåller information om Grupprincip och mobila enheter (MDM) hanteringsinställningar som ska användas på företagsägda enheter.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3f2b1afa67ec36da4d4da57b296e696fd6c6910
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/01/2019
ms.locfileid: "67481949"
---
# <a name="group-policy-and-mdm-settings"></a>Inställningar för grupprinciper och MDM
Använd dessa Grupprincip och hanteringsinställningar för mobila enheter (MDM) endast på företagsägda enheter eftersom dessa principer tillämpas på användarens hela enheten. Tillämpa en MDM-principen för att inaktivera synkronisering av inställningar för en personlig påverkar användarägda enheter negativt användningen av enheten. Dessutom kommer andra användarkonton på enheten också att påverkas av principen.

Företag som vill hantera för personliga (ohanterade) enheter kan använda Azure-portalen för att aktivera eller inaktivera roaming, snarare än att med hjälp av en grupprincip eller MDM.
I följande tabeller beskrivs de tillgängliga inställningarna.

## <a name="mdm-settings"></a>MDM-inställningar
Inställningarna för MDM-principen gäller för både Windows 10 och Windows 10 Mobile.  Windows 10 Mobile-stöd finns endast för Microsoft-konto baserat nätverksväxling via användarens OneDrive-konto.  Se [enheter och slutpunkter](enterprise-state-roaming-windows-settings-reference.md) mer information om vilka enheter som stöds för Azure AD-baserad synkronisering.

| Namn | Beskrivning |
| --- | --- |
| Tillåt Microsoft-konto-anslutning |Tillåter användare att autentisera med ett Microsoft-konto på enheten |
| Tillåt synkronisering av Mina inställningar |Tillåter användare att flytta Windows-inställningar och AppData; Inaktiverar den här principen kommer att inaktivera synkronisering samt säkerhetskopior på mobila enheter |

## <a name="group-policy-settings"></a>Grupprincipinställningar
Grupprincipinställningar som gäller för Windows 10-enheter som är anslutna till en Active Directory-domän. Tabellen innehåller också äldre inställningar som skulle visas för att hantera synkroniseringsinställningar, men som inte arbetar för företaget tillstånd Roaming för Windows 10, som är märkta med ”Använd inte” i beskrivningen.

De här inställningarna finns på: `Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

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

## <a name="next-steps"></a>Nästa steg

En översikt finns i [enterprise State Roaming översikt](enterprise-state-roaming-overview.md).


