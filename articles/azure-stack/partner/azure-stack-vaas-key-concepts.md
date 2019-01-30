---
title: Azure Stack-verifiering som en tjänst viktiga begrepp | Microsoft Docs
description: Beskriver viktiga begrepp i Azure Stack-verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/07/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 01/07/2019
ROBOTS: NOINDEX
ms.openlocfilehash: 1f5c47dd3453c0c8f02f1b0a87e5f2fff123f8be
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55242815"
---
# <a name="validation-as-a-service-key-concepts"></a>Verifiering som en tjänst viktiga begrepp

Den här artikeln beskriver viktiga begrepp i verifiering som en tjänst (VaaS).

## <a name="solutions"></a>Lösningar

En lösning för VaaS representerar en Azure Stack-lösning med en viss maskinvara struktur (BoM). VaaS lösningen fungerar som en behållare för de arbetsflöden som körs mot Azure Stack-lösning.

### <a name="create-a-solution-in-the-vaas-portal"></a>Skapa en lösning i VaaS-portalen

1. Logga in på den [VaaS portal](https://azurestackvalidation.com).
2. Lösningar-instrumentpanelen, klicka på **ny lösning**.
3. Ange ett namn för lösningen. Namnge förslag, finns i [namnkonventionen för VaaS lösningar](azure-stack-vaas-best-practice.md#naming-convention-for-vaas-solutions).
4. Välj **spara** att skapa lösningen.

## <a name="workflows"></a>Arbetsflöden

Ett arbetsflöde för VaaS fungerar inom kontexten för en VaaS-lösning. Representerar en uppsättning testserier som utnyttja funktionerna i Azure Stack-distributioner. Ett arbetsflöde ska skapas för varje distribution eller en uppdatering för en Azure Stack-lösning.

Arbetsflöden är kategoriserade genom att testa scenariotyp. Inofficiella testa den **testet** arbetsflöde kan du välja tester från alla tillgängliga VaaS säkerheter. Officiella testa den **verifiering** arbetsflöden rikta specifika testscenarier som väljs ut av Microsoft.

![VaaS arbetsflöde paneler](media/tile_all-workflows.png)

> [!NOTE]
> Den **lösning verifiering** arbetsflöde stöder för närvarande två scenarier: [Verifiera OEM-paket](azure-stack-vaas-validate-oem-package.md) och [Validera programuppdateringar från Microsoft](azure-stack-vaas-validate-microsoft-updates.md).

Läs mer på arbetsflödestyper [vad är verifiering som en tjänst för Azure Stack?](azure-stack-vaas-overview.md).

### <a name="getting-started-with-vaas-workflows"></a>Komma igång med VaaS arbetsflöden

1. Skapa en ny lösning eller välj en befintlig på instrumentpanelen för lösningar. Detta uppdaterar och gör att arbetsflödet paneler.
2. Om du vill skapa ett nytt arbetsflöde, väljer du på **starta** på valfri panel. Information som är specifika för varje arbetsflöde finns i följande artiklar:
    - Testet: [Snabbstart: Använda verifieringen som en tjänst-portal för att schemalägga första testet](azure-stack-vaas-schedule-test-pass.md)
    - Lösningen-verifiering: [Verifiera en ny Azure Stack-lösning](azure-stack-vaas-validate-solution-new.md)
    - Lösningen-verifiering: [Verifiera programuppdateringar från Microsoft](azure-stack-vaas-validate-microsoft-updates.md)
    - Lösningen-verifiering: [Verifiera OEM-paket](azure-stack-vaas-validate-oem-package.md)

3. För att hantera eller övervaka ett befintligt arbetsflöde, väljer du på **hantera** på panelen arbetsflöde. Välj namnet på arbetsflödet och Använd den **redigera** för att visa egenskaperna eller ändra parametrarna för testning.

Mer information om egenskaperna för arbetsflödet och parametrar finns i [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md).

## <a name="tests"></a>Tester

Ett test i VaaS består av en uppsättning åtgärder som körs mot en Azure Stack-lösning. Tester har olika syften som identifieras av en kategori som funktionella eller tillförlitlighet, och rikta en eller flera tjänster i Azure Stack. Varje test definierar en egen uppsättning parametrar, vilket anges av gemensamma parametrar för som innehåller arbetsflödet.

Läs mer om att hantera och övervaka tester [övervaka och hantera tester i portalen VaaS](azure-stack-vaas-monitor-test.md).

Mer information om parametrar finns i [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md).

## <a name="agents"></a>Agenter

En agent för VaaS styr testkörning av. Två typer av agenter som kör VaaS test:

- Den **molnagent**. Det här är den standard-agenten som är tillgängliga i VaaS. Ingen installation krävs, men detta kräver att inkommande anslutningar i miljön och Azure Stack-slutpunkter måste kunna lösas från internet. Vissa tester är inte kompatibla med cloud-agenten.
- En **lokal agent**. På så sätt kan du köra verifieringen i scenarier där inkommande anslutningar i miljön är inte är möjligt. Vissa tester kräver körning via lokal agent.

Lokala agenter är inte kopplade till en viss Azure Stack eller VaaS lösning. Som bästa praxis, ska de köras utanför ett Azure Stack-miljön.

Anvisningar för att lägga till en lokal agent finns i [distribuera lokal agent](azure-stack-vaas-local-agent.md).

## <a name="next-steps"></a>Nästa steg

- [Metodtips för verifiering som en tjänst](azure-stack-vaas-best-practice.md)