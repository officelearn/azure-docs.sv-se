---
title: Anslut säkerhetsdata för molnappar till Azure Sentinel| Microsoft-dokument
description: Lär dig hur du ansluter Cloud App Security-data till Azure Sentinel.
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
ms.date: 03/24/2020
ms.author: yelevin
ms.openlocfilehash: 266d97e834247088d40837cbec1436e00d0f4be2
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80422148"
---
# <a name="connect-data-from-microsoft-cloud-app-security"></a>Ansluta data från Microsoft Cloud App Security 



[Med MCAS-anslutningen (Microsoft Cloud App Security)](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security) kan du strömma aviseringar och [Cloud Discovery-loggar](https://docs.microsoft.com/cloud-app-security/tutorial-shadow-it) från MCAS till Azure Sentinel. På så sätt kan du få insyn i dina molnappar, få sofistikerade analyser för att identifiera och bekämpa cyberhot och styra hur dina data färdas.

## <a name="prerequisites"></a>Krav

- Användaren måste ha läs- och skrivbehörighet på arbetsytan.
- Användaren måste ha behörigheten Global administratör eller Säkerhetsadministratör på arbetsytans klient.
- Om du vill strömma Cloud Discovery-loggar till Azure Sentinel [aktiverar du Azure Sentinel som din SIEM i Microsoft Cloud App Security](https://aka.ms/AzureSentinelMCAS).

> [!IMPORTANT]
> Inmatning av Cloud Discovery-loggar är för närvarande i offentlig förhandsversion.
> Den här funktionen tillhandahålls utan ett servicenivåavtal och rekommenderas inte för produktionsarbetsbelastningar.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
 
## <a name="connect-to-cloud-app-security"></a>Ansluta till cloud app-säkerhet

Om du redan har Cloud App Security kontrollerar du att den är [aktiverad i nätverket](https://docs.microsoft.com/cloud-app-security/getting-started-with-cloud-app-security).
Om Cloud App Security distribueras och intag av dina data kan varningsdata enkelt strömmas till Azure Sentinel.


1. Välj Datakopplingar på **navigeringsmenyn**i Azure Sentinel . Klicka på panelen **Microsoft Cloud App Security** i listan över kopplingar och knappen Öppna **anslutningssidan** längst ned till höger.

1. Välj vilka loggar du vill strömma till Azure Sentinel; Du kan välja **Aviseringar** och **Cloud Discovery Logs** (preview). 

1. Klicka på **Använd ändringar**.

1. Om du vill använda det relevanta schemat i `SecurityAlert` Log Analytics for Cloud App Security alerts skriver du i frågefönstret. Skriv för schemat Cloud Discovery `McasShadowItReporting`logs för molnidentifiering.

> [!NOTE]
> Cloud Discovery hjälper till att identifiera och identifiera trender genom att samla in de data som ligger till grund för användarnas anslutningar till molnappar.
>
> Eftersom Cloud Discovery-data aggregeras per dag bör du vara medveten om att upp till 24 timmars värde av de senaste data inte kommer att återspeglas i Azure Sentinel. Om en undersökning på låg nivå kräver mer omedelbara uppgifter bör det göras direkt i källverktyget eller tjänsten där rådata finns.

## <a name="next-steps"></a>Nästa steg
I det här dokumentet fick du lära dig hur du ansluter Microsoft Cloud App Security till Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:
- Läs om hur du [får insyn i dina data och potentiella hot](quickstart-get-visibility.md).
- Kom igång med att identifiera hot med Azure Sentinel med hjälp av [inbyggda](tutorial-detect-threats.md) eller [anpassade](tutorial-detect-threats-custom.md) regler.
