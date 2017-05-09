---
title: "Utvärdering av säkerhetswebbaslinjen i säkerhets- och granskningslösningen i Operations Management | Microsoft Docs"
description: "Det här dokumentet beskriver hur du använder säkerhets- och granskningslösningen i OMS för att, i efterlevnads- och säkerhetssyfte, göra en utvärdering av säkerhetswebbaslinjen för alla övervakade webbservrar."
services: operations-management-suite
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ROBOTS: NOINDEX
redirect_url: https://www.microsoft.com/cloud-platform/security-and-compliance
ms.assetid: 17837c8b-3e79-47c0-9b83-a51c6ca44ca6
ms.service: operations-management-suite
ms.custom: oms-security
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/27/2017
ms.author: yurid
ms.translationtype: Human Translation
ms.sourcegitcommit: be3ac7755934bca00190db6e21b6527c91a77ec2
ms.openlocfilehash: 0d4707dc0c0ffbf40d0d10a6d12b9709a9655258
ms.contentlocale: sv-se
ms.lasthandoff: 05/03/2017


---
# <a name="web-baseline-assessment-in-operations-management-suite-security-and-audit-solution"></a>Utvärdering av säkerhetswebbaslinjen i säkerhets- och granskningslösningen i Operations Management Suite
Det här dokumentet beskriver hur du använder funktionerna för utvärdering av säkerhetswebbaslinjen i [säkerhets- och granskningslösningen i Operations Management Suite (OMS)](operations-management-suite-overview.md) för att utvärdera de övervakade resursernas säkerhetsstatus.

## <a name="what-is-web-baseline-assessment"></a>Vad är utvärdering av webbaslinje?
För närvarande tillhandahåller säkerheten i OMS utvärdering av säkerhetsbaslinje för operativsystem. Den söker igenom operativsystemsinställningarna för dina servrar var 24: e timme och ger en översikt över potentiellt sårbara inställningar. Läs [bedömning av baslinjen i Operations Management Suite säkerhet och granska lösningen](oms-security-baseline.md) för mer information om det här.

Målet med utvärderingen av webbaslinjen är att hitta potentiellt sårbara webbserverinställningar. De tre primära källorna för konfiguration av webbaslinje är: .NET, ASP.NET och IIS.  Precis som baslinjeutvärderingen för operativsystemet genomsöker OMS-säkerheten dina webbservrar var 24:e timme och ger en översikt över potentiellt sårbara inställningar deras säkerhetsstatus.  I Internet Information Service (IIS) är konfigurationer mycket anpassningsbara, vilket gör att olika nivåer av webbplatsen och programmet kan åsidosättas. Skannern kontrollerar inställningar på varje nivå för programmet/webbplatsen utöver standardrotnivå. Det hjälper dig att identifiera potentiella sårbara inställningsplatser och att åtgärda det snabbt.


## <a name="web-security-baseline-assessment"></a>Utvärdering av säkerhetswebb-baslinje
För den här förhandsversionen kan den här funktionen användas med alternativet OMS-sökning. Följ stegen nedan för att ställa lämplig fråga:

1. Klicka på panelen **Säkerhet och granskning** på huvudinstrumentpanelen för **Microsoft Operations Management Suite**.
2. På instrumentpanelen **Säkerhet och granskning** klickar du på knappen **Loggsökning**.
3. Den första frågan du kan använda är **Sammanfattning av utvärdering av webbaslinje**. I fältet **Begin search here** (Starta sökning här) skriver du den här frågan: Type *=SecurityBaselineSummary BaselineType=web*. Följande skärm visar ett exempel på utdata:

![Sammanfattning av utvärdering av webbaslinje](./media/oms-security-web-baseline/oms-security-web-baseline-fig1-new.png)

> [!NOTE]
> I den här fråga indikerar varje post en utvärderingssammanfattning på en enskild server.

När du använder **Loggsökning** kan du ange olika frågor för att få mer information om utvärderingen av webbaslinje. Utöver den föregående frågan kan du använda följande i den här förhandsversionen.

**Utvärdering av webbaslinje**: Varje post motsvarar en utvärdering av en webb-baslinjeregel på en enskild server. Den innehåller alla data för regel, plats, förväntat resultat och faktiskt resultat.

**Fråga**: Type*=SecurityBaseline BaselineType=web*

![Utvärdering av webbaslinje](./media/oms-security-web-baseline/oms-security-web-baseline-fig2.png)

**Visa alla resultat för en specifik server**: Den här frågan visar hur du ser resultat för en specifik server.

**Fråga**: *Type=SecurityBaseline BaselineType=web Computer=BaselineTestVM1*

![Alla resultat](./media/oms-security-web-baseline/oms-security-web-baseline-fig3.png)

Du kan också använda dessa poster/frågor för att skapa dina egna instrumentpaneler, rapporter och aviseringar. Skärmen nedan visar ett exempel på UI-kontroll som du kan lägga till i din instrumentpanel. Lär dig att visualisera dina data med OMS View Designer [här](https://blogs.technet.microsoft.com/msoms/2016/06/30/oms-view-designer-visualize-your-data-your-way/). Skärmen nedan är ett exempel på hur panelen ser ut när du har gjort den här anpassningen.

![Exempel-UI](./media/oms-security-web-baseline/oms-security-web-baseline-fig4.png)

> [!NOTE]
> Om du vill veta vilka inställningar som kontrolleras för utvärderingen av baslinjen kan du hämta [det här Excel-kalkylbladet](https://gallery.technet.microsoft.com/OMS-Web-Baseline-1e811690) som innehåller inställningarna.

## <a name="see-also"></a>Se även
I det här dokumentet har du lärt dig om Utvärdering av säkerhetswebbaslinje i säkerhets- och granskningslösningen i OMS. Mer information om säkerheten i OMS finns i följande artiklar:

* [Översikt över Operations Management Suite (OMS)](operations-management-suite-overview.md)
* [Övervaka och svara på säkerhetsaviseringar i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-responding-alerts.md)
* [Övervaka resurser i säkerhets- och granskningslösningen i Operations Management Suite](oms-security-monitoring-resources.md)


