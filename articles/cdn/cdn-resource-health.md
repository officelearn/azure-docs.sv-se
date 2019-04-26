---
title: Övervaka hälsotillståndet för Azure CDN-resurser | Microsoft Docs
description: Lär dig mer om att övervaka hälsotillståndet hos dina Azure CDN-resurser med Azure Resource Health.
services: cdn
documentationcenter: .net
author: zhangmanling
manager: zhangmanling
editor: ''
ms.assetid: bf23bd89-35b2-4aca-ac7f-68ee02953f31
ms.service: cdn
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: ad4bf7ae97a08f89b9d82e1d4e025a5bd5d47fc1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60324714"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Övervaka hälsotillståndet för Azure CDN-resurser
  
Azure CDN Resource health är en delmängd av [Azure resource health](../resource-health/resource-health-overview.md).  Du kan använda Azure resource health för att övervaka hälsotillståndet för CDN-resurser och få praktisk vägledning i felsökning av problem.

>[!IMPORTANT] 
>Azure CDN-resurshälsa konton endast för närvarande för hälsotillståndet för global CDN-leverans och API-funktioner.  Azure CDN-resurshälsa kontrollerar inte enskilda CDN-slutpunkter.
>
>Signaler som matas Azure CDN resource health kan vara upp till 15 minuter försenad.

## <a name="how-to-find-azure-cdn-resource-health"></a>Så här hittar du Azure CDN-resurshälsa

1. I den [Azure-portalen](https://portal.azure.com), bläddra till din CDN-profil.

2. Klicka på den **inställningar** knappen.

    ![Knappen Inställningar](./media/cdn-resource-health/cdn-profile-settings.png)

3. Under *Support och felsökning*, klickar du på **resurshälsa**.

    ![CDN-resurshälsa](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Du kan också hitta CDN-resurser som anges i den *resurshälsa* panelen i den *hjälp + support* bladet.  Du kan få snabb tillgång till *hjälp + support* genom att klicka på den inringad **?** i det övre högra hörnet i portalen.
>
> ![Hjälp + support](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN meddelanden

Statusar som rör Azure CDN resource health finns nedan.

|Meddelande | Rekommenderad åtgärd |
|---|---|
|Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter | Du kan ha stoppat, tagit bort eller felaktigt konfigurerat en eller flera CDN-slutpunkter.|
|CDN-hanteringstjänsten är tyvärr inte tillgänglig just nu | Kom tillbaka senare för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden.|
|Ett aktuellt problem med några av våra CDN-leverantörer kan tyvärr påverka dina CDN-slutpunkter | Kom tillbaka senare för statusuppdateringar; Använd felsökningsverktyget att lära dig hur du testar din startpunkt och CDN-slutpunkt Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden. |
|Det tar tyvärr längre tid att sprida konfigurationsändringar i CDN-slutpunkter | Kom tillbaka senare för statusuppdateringar; Kontakta supporten om konfigurationsändringarna inte sprids helt inom förväntad tid.|
|Just nu har vi tyvärr problem med att läsa in den kompletterande portalen | Kom tillbaka senare för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden.|
Vi har tyvärr problem med några av våra CDN-leverantörer | Kom tillbaka senare för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden. |

## <a name="next-steps"></a>Nästa steg

- [Läs en översikt över Azure resource health](../resource-health/resource-health-overview.md)
- [Felsöka problem med CDN-komprimering](./cdn-troubleshoot-compression.md)
- [Felsöka problem med 404-fel](./cdn-troubleshoot-endpoint.md)