---
title: Ta bort Azure Sentinel| Microsoft-dokument
description: Så här tar du bort din Azure Sentinel-instans.
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
ms.openlocfilehash: 4042f7b9d0f6fdf293f338c89d5783fe266c4edf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77581692"
---
# <a name="remove-azure-sentinel-from-your-workspace"></a>Ta bort Azure Sentinel från arbetsytan

Om du inte längre vill använda Azure Sentinel förklarar den här artikeln hur du tar bort den från arbetsytan.

## <a name="how-to-remove-azure-sentinel"></a>Så här tar du bort Azure Sentinel

Följ den här processen för att ta bort Azure Sentinel från din arbetsyta:

1. Gå till **Azure Sentinel**, följt av **Inställningar**, och välj fliken Ta bort **Azure Sentinel**.

1. Innan du tar bort Azure Sentinel använder du kryssrutorna för att meddela oss varför du tar bort det.

1. Välj **Ta bort Azure Sentinel från arbetsytan**.
    
    ![Ta bort SecurityInsights-lösningen](media/offboard/delete-solution.png)

## <a name="what-happens-behind-the-scenes"></a>Vad händer bakom kulisserna?

När du tar bort lösningen tar det upp till 48 timmar innan Azure Sentinel slutför den första fasen av borttagningsprocessen.

När frånkopplingen har identifierats börjar offboarding-processen.

**Konfigurationen av dessa kopplingar tas bort:**
-   Office 365

-   AWS

-   Säkerhetsaviseringar för Microsoft-tjänster (Azure ATP, Microsoft Cloud App Security inklusive Cloud Discovery Shadow IT-rapportering, Azure AD Identity Protection, Microsoft Defender ATP, Azure Security Center)

-   Hotinformation

-   Vanliga säkerhetsloggar (inklusive CEF-baserade loggar, Barracuda och Syslog) (Om du har Azure Security Center fortsätter dessa loggar att samlas in.)

-   Windows Security Events (Om du har Azure Security Center fortsätter dessa loggar att samlas in.)

Inom de första 48 timmarna kommer data och analytiska regler (inklusive automatiseringskonfiguration i realtid) inte längre att vara tillgängliga eller frågebara i Azure Sentinel.

**Efter 30 dagar tas dessa resurser bort:**

-   Incidenter (inklusive undersökningsmetadata)

-   Analytiska regler

-   Bokmärken

Dina spelböcker, sparade arbetsböcker, sparade jaktfrågor och anteckningsböcker tas inte bort. **Vissa kan gå sönder på grund av borttagna data. Du kan ta bort dem manuellt.**

När du har tagit bort tjänsten finns det en respitperiod på 30 dagar under vilken du kan återaktivera lösningen och dina data och analytiska regler återställs, men de konfigurerade kopplingarna som kopplades från måste återanslutas.

> [!NOTE]
> Om du tar bort lösningen fortsätter prenumerationen att registreras hos Azure Sentinel-resursleverantören. **Du kan ta bort den manuellt.**




## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du tar bort Azure Sentinel-tjänsten. Om du ändrar dig och vill installera det igen:
- Kom igång [med Azure Sentinel.](quickstart-onboard.md)
