---
title: Ta bort Azure Sentinel | Microsoft Docs
description: Ta bort din Azure Sentinel-instans.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/29/2019
ms.author: yelevin
ms.openlocfilehash: 0d918de4669e1b4bac6c1775a813eb1d224f39cf
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662007"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Ta bort Azure Sentinel från din arbets yta

Om du inte längre vill använda Azure Sentinel förklarar den här artikeln hur du tar bort den från din arbets yta.

## <a name="how-to-remove-azure-sentinel"></a>Så här tar du bort Azure Sentinel

Följ den här processen för att ta bort Azure Sentinel från din arbets yta:

1. Gå till **Azure Sentinel**, följt av **inställningarna**och välj fliken **ta bort Azure Sentinel**.

1. Innan du tar bort Azure Sentinel ska du använda kryss rutorna för att berätta varför du tar bort den.

1. Välj **ta bort Azure Sentinel från din arbets yta**.
    
    ![Ta bort SecurityInsights-lösningen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Vad händer i bakgrunden?

När du tar bort lösningen tar Azure Sentinel upp till 48 timmar innan den första fasen av borttagnings processen slutförs.

När från kopplingen har identifierats börjar offboarding-processen.

**Konfigurationen av dessa anslutningar tas bort:**
-   Office 365

-   AWS

-   Säkerhets aviseringar för Microsoft-tjänster: Microsoft Defender för identitet (*tidigare Azure ATP*) Microsoft Cloud App Security inklusive Cloud Discovery Shadow IT repor ting, Azure AD Identity Protection, Microsoft Defender för slut punkt (*tidigare Microsoft Defender ATP*), Azure Defender (*tidigare Azure Security Center*)

-   Hotinformation

-   Vanliga säkerhets loggar (inklusive CEF-baserade loggar, Barracuda och syslog) (om du har Azure Defender kommer dessa loggar att fortsätta att samlas in.)

-   Windows säkerhets händelser (om du har Azure Defender kommer dessa loggar att fortsätta att samlas in.)

Inom de första 48 timmarna går det inte längre att komma åt data-och analys reglerna (inklusive automatiserings konfiguration i real tid) eller fråga i Azure Sentinel.

**Efter 30 dagar tas dessa resurser bort:**

-   Incidenter (inklusive undersöknings-metadata)

-   Analys regler

-   Bokmärken

Din spel böcker, sparade arbets böcker, sparade jakt frågor och antecknings böcker tas inte bort. **Vissa kan brytas på grund av borttagna data. Du kan ta bort dem manuellt.**

När du har tagit bort tjänsten finns det en respitperiod på 30 dagar då du kan återaktivera lösningen och dina data och analys regler återställs, men de konfigurerade anslutningarna som kopplades från måste återanslutas.

> [!NOTE]
> Om du tar bort lösningen fortsätter prenumerationen att registreras med Azure Sentinel Resource Provider. **Du kan ta bort den manuellt.**




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du tar bort Azure Sentinel-tjänsten. Om du ändrar dig och vill installera det igen:
- Kom igång [med att använda Azure Sentinel](quickstart-onboard.md).
