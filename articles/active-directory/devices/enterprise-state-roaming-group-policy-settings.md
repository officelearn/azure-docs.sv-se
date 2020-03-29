---
title: Grupprincip- och MDM-inställningar för ESR - Azure Active Directory
description: Hanteringsinställningar för Roaming i företagstillstånd
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: troubleshooting
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: bdffbc3a140bd13dcd6d352db8c192803d39b03e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672374"
---
# <a name="group-policy-and-mdm-settings"></a>Grupprincip- och MDM-inställningar

Använd dessa grupprincip- och mdm-inställningar (Mobile Device Management) endast på företagsägda enheter eftersom dessa principer tillämpas på användarens hela enhet. Om du använder en MDM-princip för att inaktivera synkronisering av inställningar för en personlig, användad enhet påverkas användningen av den enheten negativt. Dessutom påverkas även andra användarkonton på enheten av principen.

Företag som vill hantera roaming för personliga (ohanterade) enheter kan använda Azure-portalen för att aktivera eller inaktivera roaming i stället för att använda grupprincip eller MDM.
I följande tabeller beskrivs tillgängliga principinställningar.

> [!NOTE]
> Den här artikeln gäller den HTML-baserade webbläsaren Microsoft Edge Legacy som lanserades med Windows 10 i juli 2015. Artikeln gäller inte den nya Microsoft Edge Chromium-baserade webbläsaren som släpptes den 15 januari 2020. Mer information om synkroniseringsbeteendet för den nya Microsoft Edge finns i artikeln [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="mdm-settings"></a>MDM-inställningar

Mdm-principinställningarna gäller för både Windows 10 och Windows 10 Mobile.  Windows 10 Mobile-supporten finns endast för Microsoft-kontobaserad roaming via användarens OneDrive-konto. Mer information om vilka enheter som stöds för Azure AD-baserad synkronisering finns på [enheter och slutpunkter.](enterprise-state-roaming-windows-settings-reference.md)

| Namn | Beskrivning |
| --- | --- |
| Tillåt Anslutning till Microsoft-konto |Tillåter användare att autentisera med ett Microsoft-konto på enheten |
| Tillåt synkronisera mina inställningar |Tillåter användare att flytta Windows-inställningar och appdata. Om du inaktiverar den här principen inaktiveras synkronisering och säkerhetskopior på mobila enheter |

## <a name="group-policy-settings"></a>Grupprincipinställningar

Grupprincipinställningarna gäller för Windows 10-enheter som är anslutna till en Active Directory-domän. Tabellen innehåller också äldre inställningar som verkar hantera synkroniseringsinställningar, men som inte fungerar för Enterprise State Roaming för Windows 10, som noteras med "Använd inte" i beskrivningen.

Dessa inställningar finns på:`Computer Configuration > Administrative Templates > Windows Components > Sync your settings` 

| Namn | Beskrivning |
| --- | --- |
| Konton: Blockera Microsoft-konton |Den här principinställningen hindrar användare från att lägga till nya Microsoft-konton på den här datorn |
| Synkronisera inte |Förhindrar användare att flytta Windows-inställningar och appdata |
| Synkronisera inte anpassa |Inaktiverar synkronisering av gruppen Teman |
| Synkronisera inte webbläsarinställningar |Inaktiverar synkronisering av Internet Explorer-gruppen |
| Synkronisera inte lösenord |Inaktiverar synkronisering av gruppen Lösenord |
| Synkronisera inte andra Windows-inställningar |Inaktiverar synkronisering av gruppen Andra Windows-inställningar |
| Synkronisera inte anpassning av skrivbord |Använd inte; har ingen effekt |
| Synkronisera inte på anslutningar med datapriser |Inaktiverar roaming på anslutningar med datapriser, till exempel mobil 3G |
| Synkronisera inte appar |Använd inte; har ingen effekt |
| Synkronisera inte appinställningar |Inaktiverar roaming av appdata |
| Synkronisera inte startinställningar |Använd inte; har ingen effekt |

## <a name="next-steps"></a>Nästa steg

En översikt finns i [översikt över roaming i företagsstat](enterprise-state-roaming-overview.md).
