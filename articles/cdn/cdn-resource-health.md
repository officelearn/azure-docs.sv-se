---
title: Övervaka hälsotillståndet hos Azure CDN resurser | Microsoft Docs
description: Lär dig att övervaka hälsotillståndet hos dina Azure CDN-resurser med hjälp av Azure Resource Health.
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
ms.openlocfilehash: 37fe208f5087f318e665e76825127854b4a11c98
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23842897"
---
# <a name="monitor-the-health-of-azure-cdn-resources"></a>Övervaka hälsotillståndet hos Azure CDN-resurser
  
Azure CDN Resource health är en delmängd av [Azure resurshälsa](../resource-health/resource-health-overview.md).  Du kan använda Azure-resurs hälsotillstånd att övervaka hälsotillståndet hos CDN-resurser och ta emot tillämplig vägledning för att felsöka problem.

>[!IMPORTANT] 
>Azure CDN-resurshälsa svarar bara för närvarande för hälsotillståndet för leverans av CDN-globala och API-funktioner.  Azure CDN-resurshälsa verifierar inte enskilda CDN-slutpunkter.
>
>Signalerar som matas Azure CDN resurshälsa kan vara upp till 15 minuter försenad.

## <a name="how-to-find-azure-cdn-resource-health"></a>Så här hittar du Azure CDN resurshälsa

1. I den [Azure-portalen](https://portal.azure.com), bläddra till CDN-profilen.

2. Klicka på den **inställningar** knappen.

    ![Inställningar för](./media/cdn-resource-health/cdn-profile-settings.png)

3. Under *stöd + felsökning*, klickar du på **Resource health**.

    ![CDN-resurshälsa](./media/cdn-resource-health/cdn-resource-health3.png)

>[!TIP] 
>Du kan också hitta CDN-resurserna som visas i den *resurshälsa* panelen i den *hjälp + support* bladet.  Du kan snabbt få till *hjälp + support* genom att klicka på den inringad **?** i det övre högra hörnet av portalen.
>
> ![Hjälp + support](./media/cdn-resource-health/cdn-help-support.png)

## <a name="azure-cdn-specific-messages"></a>Azure CDN-specifika meddelanden

Statusvärdena som är relaterade till Azure CDN resurshälsa finns nedan.

|Meddelande | Rekommenderad åtgärd |
|---|---|
|Om du har stoppats, tas bort eller felkonfigurerad en eller flera av dina CDN-slutpunkter | Du kanske har stoppats, borttagen eller felkonfigurerad en eller flera av dina CDN-slutpunkter.|
|Tyvärr, CDN management-tjänsten är inte tillgänglig | Återkom för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden.|
|Tyvärr CDN-slutpunkter kan påverkas av pågående problem med några av våra CDN-providers | Återkom för statusuppdateringar; Använd felsökning för att lära dig hur du testar din ursprung och CDN-slutpunkten; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden. |
|Tyvärr konfigurationsändringar för CDN-slutpunkten fördröjs spridning | Återkom för statusuppdateringar; Kontakta supporten om konfigurationsändringarna inte sprids helt inom förväntad tid.|
|Vi beklagar, men vi har problem läser in den kompletterande portalen | Återkom för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden.|
Tyvärr vi har problem med några av våra CDN-providers | Återkom för statusuppdateringar; Kontakta supporten om problemet kvarstår efter den förväntade lösningstiden. |

## <a name="next-steps"></a>Nästa steg

- [Läs en översikt över Azure resurshälsa](../resource-health/resource-health-overview.md)
- [Felsökning av problem med CDN komprimering](./cdn-troubleshoot-compression.md)
- [Felsöka problem med 404-fel](./cdn-troubleshoot-endpoint.md)