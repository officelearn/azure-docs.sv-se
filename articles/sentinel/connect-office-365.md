---
title: Samla in Office 365-data i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Lär dig hur du samlar in Office 365-data i Azure Sentinel.
services: sentinel
documentationcenter: na
author: rkarlin
manager: MBaldwin
editor: ''
ms.assetid: ff7c862e-2e23-4a28-bd18-f2924a30899d
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: 42d2bb69ac5f1e6669d82a3fc7236e967a6d9366
ms.sourcegitcommit: f7f4b83996640d6fa35aea889dbf9073ba4422f0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2019
ms.locfileid: "56992924"
---
# <a name="collect-data-from-office-365-logs"></a>Samla in data från Office 365-loggar

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade. Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Du kan strömma granskningsloggar från [Office 365](https://docs.microsoft.com/office365/admin/admin-home?view=o365-worldwide) i Azure Sentinel-med ett enda klick. Du kan strömma granskningsloggar från flera innehavare till en enda arbetsyta i Azure Sentinel. Office 365-anslutning aktivitet log ger inblick i pågående användaraktiviteter. Du får information om olika användare, admin, system, och åtgärder och händelser från Office 365. Du kan använda dessa data att visa instrumentpaneler, skapa anpassade varningar och förbättra undersökningen genom att ansluta Office 365-loggar i Azure Sentinel.


## <a name="prerequisites"></a>Förutsättningar

- Du måste vara en global administratör eller säkerhetsadministratör på din klient


## <a name="connect-to-office-365"></a>Ansluta till Office 365

1. I Azure Sentinel väljer **datainsamling** och klicka sedan på den **Office 365** panelen.

2. Om du inte redan har aktiverat den, under **anslutning** använder den **aktivera** knappen för att aktivera Office 365-lösningen. Om det redan har aktiverats identifieras på skärmen anslutning som redan har aktiverats.
1. Office 365 kan du strömdata från flera klienter kan Azure Sentinel. För varje klient som du vill ansluta till, lägger du till klienten under **ansluta klienter till Azure Sentinel**. 
1. En Active Directory-skärmen öppnas. Du uppmanas att autentisera med en global administratörsanvändare på varje klient som du vill ansluta till Azure Sentinel och ge behörigheter till Azure Sentinel att läsa loggar. 
5. Under Stream Office 365-aktivitetsloggar, klickar du på **Välj** att välja vilka loggtyper som du vill strömma till Sentinel-Azure. Sentinel-Azure stöder för närvarande, Exchange och SharePoint.

4. Klicka på **tillämpa ändringarna**.

3. Om du vill använda relevanta schemat i Log Analytics för Office 365-loggar, Sök efter **OfficeActivity**.


## <a name="next-steps"></a>Nästa steg
I det här dokumentet har du lärt dig hur du ansluter Office 365 till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Lär dig hur du [få insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång [upptäcka hot med Azure Sentinel](tutorial-detect-threats.md).

