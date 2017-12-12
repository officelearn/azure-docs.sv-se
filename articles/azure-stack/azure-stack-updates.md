---
title: "Hantera uppdateringar i Azure-stacken översikt | Microsoft Docs"
description: "Mer information om uppdateringshantering för Azure-stacken integrerat system."
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 9b0781f4-2cd5-4619-a9b1-59182b4a6e43
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: mabrigg
ms.openlocfilehash: 4355e7a8c220dea03df5eda54ed4e94d6ebc5c94
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/11/2017
---
# <a name="manage-updates-in-azure-stack-overview"></a>Hantera uppdateringar i Azure Stack-översikt

*Gäller för: Azure Stack integrerat system*

Microsoft kommer att släppa uppdateringspaket för Azure-stacken integrerat system i en vanlig takt som ankommer vanligtvis den fjärde tisdagen varje månad från och vid allmän tillgänglighet. Be OEM-tillverkaren om sina specifika meddelandeprocessen för att se till att uppdateringen meddelanden nå din organisation eller kontrollera här under Concepts\Release notes\Integrated system viktig information för mer information om specifika versioner.

Varje version av Microsoft-programuppdateringar tillsammans i en enda uppdateringspaketet. Som operatör Azure Stack du enkelt kan importera, installera och övervaka installationsförloppet för dessa uppdateringspaket från administratörsportalen. 

Maskinvaruleverantören OEM-tillverkaren (OEM) kommer också att släppa uppdateringar, till exempel drivrutinen och firmware-uppdateringar. Dessa uppdateringar levereras som separata paket av maskinvarutillverkaren OEM och hanteras separat från Microsoft Update.

För att hålla datorn under stöd, måste du behålla Azure Stack uppdateras till en viss versionsnivå. Kontrollera att du läser den [Azure Stack behandling av princip](azure-stack-servicing-policy.md).

> [!NOTE]
> Du kan inte använda Azure Stack uppdateringspaket Azure Stack Development Kit. Uppdateringspaket är utformade för integrerade system.

## <a name="the-update-resource-provider"></a>Resursprovidern uppdatering

Azure-stacken innehåller en Update-resursprovidern som samordnar tillämpningen av Microsoft-programuppdateringar. Den här resursprovidern innebär att uppdateringarna tillämpas på alla fysiska värdar, Service Fabric-program och körningar, och alla infrastruktur för virtuella datorer och deras tillhörande tjänster.

Du kan enkelt se översiktliga status som uppdateringen processen mål olika delsystem i Azure-stacken (till exempel fysiska värdar och infrastruktur för virtuella datorer) som uppdateringar installeras.

## <a name="plan-for-updates"></a>Planera för uppdateringar

Vi rekommenderar starkt att du meddela användare om eventuella underhållsåtgärder och att du schemalägger normal underhållsfönster under icke kontorstid så mycket som möjligt. Underhåll kan påverka både klienternas arbetsbelastningar och portalen åtgärder.

## <a name="using-the-update-tile-to-manage-updates"></a>Hantera uppdateringar med hjälp av panelen uppdatering
Hantera uppdateringar från administratörsportalen är en enkel process. Operatör Azure Stack kan gå till panelen uppdatering i instrumentpanelen för att:

- Visa viktig information, till exempel den aktuella versionen.
- installera uppdateringar och övervaka förloppet.
- Granska uppdateringshistorik för tidigare installerade uppdateringar.
 
## <a name="determine-the-current-version"></a>Fastställa den aktuella versionen

Uppdatera panelen visar den aktuella versionen av Azure-stacken. Du kan få till panelen uppdateringen med hjälp av någon av följande metoder i administratörsportalen:

- På instrumentpanelen och visa den aktuella versionen i den **uppdatering** panelen.
 
   ![Uppdateringar panelen på standardinstrumentpanelen](./media/azure-stack-updates/image1.png)
 
- På den **Region management** panelen, klickar du på regionsnamnet. Visa den aktuella versionen i den **uppdatering** panelen.

## <a name="next-steps"></a>Nästa steg

- [Azure-stacken behandling av princip](azure-stack-servicing-policy.md) 
- [Regionhantering av i Azure-stacken](azure-stack-region-management.md)     


