---
title: Ansluta Office 365-data till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Office 365-data till Azure Sentinel.
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
ms.date: 02/12/2020
ms.author: yelevin
ms.openlocfilehash: 43eba727b1dc724aae6eea3ec77de1363c5db73f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252518"
---
# <a name="connect-data-from-office-365-logs"></a>Ansluta data från Office 365-loggar



Du kan strömma granskningsloggar från [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) till Azure Sentinel med ett enda klick. Du kan strömma granskningsloggar från ditt Office 365 till din Azure Sentinel-arbetsyta på samma klient. Aktivitetsloggen för Office 365 ger en inblick i pågående användaraktiviteter. Du får information om olika användar-, administratörs-, system- och principåtgärder och händelser från Office 365. Genom att ansluta Office 365-loggar till Azure Sentinel kan du använda dessa data för att visa instrumentpaneler, skapa anpassade aviseringar och förbättra din undersökningsprocess.

> [!IMPORTANT]
> Om du har en E3-licens måste du aktivera enhetlig granskningsloggning för din Office 365-organisation innan du kan komma åt data via API:et för Office 365-hanteringsaktivitet. Det gör du genom att aktivera granskningsloggen för Office 365. Instruktioner finns i [Aktivera eller inaktivera granskningsloggsökning för Office 365.](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off) Mer information finns i [Api-referens för Office 365-hanteringsaktivitetsaktiviteter.](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference)

## <a name="prerequisites"></a>Krav

- Du måste vara global administratör eller säkerhetsadministratör på din klient.
- Din klient måste ha enhetlig granskning aktiverad. Klienter med Office 365 E3- eller E5-licenser har enhetlig granskning aktiverad som standard. <br>Om din klient inte har någon av dessa licenser måste du aktivera enhetlig granskning av din klient med någon av dessa metoder:
    - [Använda cmdlet Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) och aktivera parametern "UnifiedAuditLogIngestionEnabled").
    - [Använda användargränssnittet för säkerhetsskydd & Compliance Center](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Ansluta till Office 365

1. I Azure Sentinel väljer du **Datakopplingar** och klickar sedan på **Office 365-panelen.**

2. Om du inte redan har aktiverat den kan du göra det genom att gå till **Data Connectors-bladet** och välja **Office 365-anslutningsapp.** Här kan du klicka på **Öppna anslutningssidan** och under konfigurationsavsnittet med namnet **Konfiguration** väljer du alla Office 365-aktivitetsloggar som du vill ansluta till Azure Sentinel. 
   > [!NOTE]
   > Om du redan har anslutit flera klienter i en tidigare version av Office 365-anslutningen i Azure Sentinel kan du visa och ändra vilka loggar du samlar in från varje klient. Du kan inte lägga till ytterligare klienter, men du kan ta bort tidigare tillagda klienter.
3. Om du vill använda det relevanta schemat i Logganalys för Office 365-loggarna söker du efter **OfficeActivity**.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

