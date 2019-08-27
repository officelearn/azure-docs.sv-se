---
title: Anslut Office 365-data till Azure Sentinel Preview | Microsoft Docs
description: Lär dig hur du ansluter Office 365-data till Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2019
ms.author: rkarlin
ms.openlocfilehash: 39245cb43dacfeec2b647936d5e5790d4a185467
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/26/2019
ms.locfileid: "70019004"
---
# <a name="connect-data-from-office-365-logs"></a>Anslut data från Office 365-loggar

> [!IMPORTANT]
> Azure Sentinel är för närvarande en offentlig för hands version.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma gransknings loggar från [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) till Azure Sentinel med ett enda klick. Du kan strömma gransknings loggar från flera klienter till en enda arbets yta i Azure Sentinel. Office 365 aktivitets logg Connector ger inblick i pågående användar aktiviteter. Du får information om olika användar-, administratörs-, system-och princip åtgärder och händelser från Office 365. Genom att ansluta Office 365-loggar till Azure Sentinel kan du använda dessa data för att visa instrument paneler, skapa anpassade aviseringar och förbättra din gransknings process.

> [!IMPORTANT]
> Om du har en E3-licens, innan du kan komma åt data via API: t för hanterings aktivitet i Office 365, måste du aktivera enhetlig gransknings loggning för din Office 365-organisation. Du gör detta genom att aktivera gransknings loggen för Office 365. Instruktioner finns i [Aktivera eller inaktivera gransknings loggs ökning i Office 365](https://docs.microsoft.com/office365/securitycompliance/turn-audit-log-search-on-or-off). Mer information finns i [API-referens för Office 365 Management Activity](https://docs.microsoft.com/office/office-365-management-api/office-365-management-activity-api-reference).

## <a name="prerequisites"></a>Förutsättningar

- Du måste vara global administratör eller säkerhets administratör för din klient
- Kontrol lera att port 4433 är öppen för webb trafik från vilken du loggade in på Azure Sentinel för att skapa anslutningen på datorn.
- Om din klient organisation inte har en Office 365 E3-eller Office 365 E5-licens, måste du aktivera enhetlig granskning med en av följande processer:
    - [Använd cmdleten Set-AdminAuditLogConfig](https://docs.microsoft.com/powershell/module/exchange/policy-and-compliance-audit/set-adminauditlogconfig?view=exchange-ps) och aktivera parametern "UnifiedAuditLogIngestionEnabled").
    - [Eller med hjälp av säkerhets-och EFTERLEVNADSCENTER gränssnittet](https://docs.microsoft.com/office365/securitycompliance/search-the-audit-log-in-security-and-compliance#before-you-begin).

## <a name="connect-to-office-365"></a>Ansluta till Office 365

1. I Azure Sentinel väljer du **data kopplingar** och klickar sedan på panelen **Office 365** .

2. Om du inte redan har aktiverat den, under **anslutning** använder du knappen **Aktivera** för att aktivera Office 365-lösningen. Om den redan har Aktiver ATS identifieras den på anslutnings skärmen som redan aktive rad.
1. Med Office 365 kan du strömma data från flera klienter till Azure Sentinel. Lägg till klienten under **Anslut klienter till Azure Sentinel**för varje klient som du vill ansluta till. 
1. En Active Directory skärmen öppnas. Du uppmanas att autentisera med en global administratörs användare på varje klient som du vill ansluta till Azure Sentinel och ge behörighet till Azure Sentinel för att läsa dess loggar. 
5. Under strömma Office 365 aktivitets loggar klickar du på **Välj** för att välja vilka logg typer som du vill strömma till Azure Sentinel. För närvarande stöder Azure Sentinel Exchange och SharePoint.

4. Klicka på **tillämpa ändringar**.

3. Om du vill använda det relevanta schemat i Log Analytics för Office 365-loggarna söker du efter **OfficeActivity**.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [med att identifiera hot med Azure Sentinel](tutorial-detect-threats.md).

