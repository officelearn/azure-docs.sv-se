---
title: Övervaka hälsotillståndet för Azure CDN-resurser| Microsoft-dokument
description: Lär dig hur du övervakar hälsotillståndet för dina Azure CDN-resurser med Hjälp av Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: azure-cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 6710f5e5b873f751ad21068acdc15d38574f8378
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593447"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Övervaka hälsotillståndet för Azure CDN-resurser
  
Azure CDN Resource health är en delmängd av [Azure-resurshälsa](../resource-health/resource-health-overview.md).  Du kan använda Azure-resurshälsa för att övervaka hälsotillståndet för CDN-resurser och få användbar vägledning för att felsöka problem.

>[!IMPORTANT] 
>Azure CDN-resurshälsa endast för närvarande står för hälsan för globala CDN-leverans- och API-funktioner.  Azure CDN-resurshälsa verifierar inte enskilda CDN-slutpunkter.
>
>Signalerna som matar Azure CDN-resurshälsa kan vara upp till 15 minuter försenade.

## <a name="how-to-find-azure-cdn-resource-health"></a>Så här hittar du azure CDN-resurshälsa

1. I [Azure-portalen](https://portal.azure.com)bläddrar du till din CDN-profil.

2. Klicka på knappen **Inställningar.**

    ![Knappen Inställningar](./media/cdn-resource-health/cdn-profile-settings.png)

3. Klicka på **Resurshälsa**under *Support + felsökning*.

    ![Hälsa för CDN-resurs](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Du kan också hitta CDN-resurser som visas på *panelen Resurshälsa* i *supportbladet Hjälp +.*  Du kan snabbt komma till *Hjälp + stöd* genom att klicka på den inringade? **?** i det övre högra hörnet av portalen.
>
> ![Hjälp + support](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifika meddelanden

Statusar relaterade till Azure CDN-resurshälsa finns nedan.

|Meddelande | Rekommenderad åtgärd |
|---|---|
|Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter | Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter.|
|CDN-hanteringstjänsten är tyvärr inte tillgänglig just nu | Kom tillbaka hit för statusuppdateringar; Om problemet kvarstår efter den förväntade lösningstiden kontaktar du supporten.|
|Ett aktuellt problem med några av våra CDN-leverantörer kan tyvärr påverka dina CDN-slutpunkter | Kom tillbaka hit för statusuppdateringar; Använd felsökningsverktyget för att lära dig hur du testar ditt ursprung och CDN-slutpunkten. Om problemet kvarstår efter den förväntade lösningstiden kontaktar du supporten. |
|Det tar tyvärr längre tid att sprida konfigurationsändringar i CDN-slutpunkter | Kom tillbaka hit för statusuppdateringar; Om konfigurationsändringarna inte sprids helt under den förväntade tiden kontaktar du supporten.|
|Just nu har vi tyvärr problem med att läsa in den kompletterande portalen | Kom tillbaka hit för statusuppdateringar; Om problemet kvarstår efter den förväntade lösningstiden kontaktar du supporten.|
Vi har tyvärr problem med några av våra CDN-leverantörer | Kom tillbaka hit för statusuppdateringar; Om problemet kvarstår efter den förväntade lösningstiden kontaktar du supporten. |

## <a name="next-steps"></a>Nästa steg

- [Läs en översikt över Azure-resurshälsa](../resource-health/resource-health-overview.md)
- [Felsöka problem med CDN-komprimering](./cdn-troubleshoot-compression.md)
- [Felsöka problem med 404 fel](./cdn-troubleshoot-endpoint.md)