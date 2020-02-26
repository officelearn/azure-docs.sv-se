---
title: Anslut Office 365-data till Azure Sentinel | Microsoft Docs
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
ms.openlocfilehash: b00f9c9e7de9568a29d2b7a7f4ef84c022ef5679
ms.sourcegitcommit: 7f929a025ba0b26bf64a367eb6b1ada4042e72ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2020
ms.locfileid: "77588169"
---
# <a name="connect-data-from-office-365-logs"></a>Anslut data från Office 365-loggar



Du kan strömma gransknings loggar från [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) till Azure Sentinel med ett enda klick. Du kan strömma gransknings loggar från din Office 365 till din Azure Sentinel-arbetsyta på samma klient. Office 365 aktivitets logg Connector ger inblick i pågående användar aktiviteter. Du får information om olika användar-, administratörs-, system-och princip åtgärder och händelser från Office 365. Genom att ansluta Office 365-loggar till Azure Sentinel kan du använda dessa data för att visa instrument paneler, skapa anpassade aviseringar och förbättra din gransknings process.

> [!IMPORTANT]
> Om du har en E3-licens, innan du kan komma åt data via API: t för hanterings aktivitet i Office 365, måste du aktivera enhetlig gransknings loggning för din Office 365-organisation. Du gör detta genom att aktivera gransknings loggen för Office 365. Instruktioner finns i [Aktivera eller inaktivera gransknings loggs ökning i Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Mer information finns i [API-referens för Office 365 Management Activity](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara global administratör eller säkerhets administratör för din klient.
- Om din klient organisation inte har en Office 365 E3-eller Office 365 E5-licens måste du aktivera enhetlig granskning på din klient med hjälp av någon av följande processer:
    - [Använd cmdleten Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) och aktivera parametern "UnifiedAuditLogIngestionEnabled").
    - [Eller med hjälp av säkerhets-och EFTERLEVNADSCENTER gränssnittet](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Ansluta till Office 365

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Office 365** .

2. Om du inte redan har aktiverat det kan du göra det genom att gå till bladet **data kopplingar** och välja **Office 365** -anslutning. Här kan du klicka på **sidan öppna koppling** och under konfigurations avsnittet märkt **konfiguration** väljer du alla Office 365-aktivitets loggar som du vill ansluta till Azure Sentinel. 
   > [!NOTE]
   > Om du redan har anslutit flera klienter i en tidigare version av Office 365-anslutningen i Azure Sentinel, kommer du att kunna visa och ändra vilka loggar som du samlar in från varje klient. Du kommer inte att kunna lägga till fler klienter, men du kan ta bort tidigare tillagda klienter.
3. Om du vill använda det relevanta schemat i Log Analytics för Office 365-loggarna söker du efter **OfficeActivity**.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats-built-in.md).

