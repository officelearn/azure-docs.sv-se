---
title: Hantera uppdateringar i Azure Stack – översikt | Microsoft Docs
description: Läs mer om hantering av uppdateringar för integrerade Azure Stack-system.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/22/2019
ms.author: mabrigg
ms.lastreviewed: 01/22/2019
ms.openlocfilehash: 6cddb513249b424ef0df2ed2d1b5609de17c0744
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57894044"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Hantera uppdateringar i Azure Stack – översikt

*Gäller för: Integrerade Azure Stack-system*

Microsoft update-paket släpp för Azure Stack-integrerat system vanligtvis runt den fjärde tisdagen varje månad. Fråga OEM-tillverkaren om deras specifika meddelande registreringsprocessen så att meddelanden om uppdateringar nå din organisation. Du kan också kontrollera i det här dokumentationsbiblioteket under **översikt** > **viktig** information om versioner som är aktiva support. 

Varje version av Microsoft-programuppdateringar tillsammans i en enda uppdateringspaketet. Du kan importera som en Azure Stack-operator, installation och övervaka installationsförloppet för dessa uppdateringspaket från administratörsportalen. 

Maskinvaruleverantören OEM-tillverkare (original equipment manufacturer) kommer också att släppa uppdateringar, t.ex drivrutinen och firmware-uppdateringar. Även om de här uppdateringarna levereras som separata paket av maskinvaruleverantören OEM, de importeras, installeras och hanteras på samma sätt uppdateringspaket från Microsoft uppdateringspaket importeras, installeras och hanteras.

Du måste hålla Azure Stack uppdateras till en specifik version-nivå för att hålla datorn under support. Se till att du läser igenom den [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).

> [!NOTE]
> Du kan inte använda Azure Stack-uppdateringspaket Azure Stack Development Kit. Uppdateringspaket är utformade för integrerade system. Mer information finns i [distribuera om ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Update-resursprovidern

Azure Stack innehåller en Update-resursprovidern som samordnar tillämpningen av Microsoft-programuppdateringar. Den här resursprovidern säkerställer att uppdateringar tillämpas på alla fysiska värdar, Service Fabric-program och körningar, och alla infrastrukturens virtuella datorer och deras tillhörande tjänster.

Uppdateringar installeras, kan du visa övergripande status som uppdatering processen mål olika delsystem i Azure Stack (till exempel fysiska värdar och infrastrukturens virtuella datorer).

## <a name="plan-for-updates"></a>Planera för uppdateringar

Vi rekommenderar starkt att du meddela användare om eventuella underhållsåtgärder, och att du schemalägger normala underhållsfönster under tider utanför kontorstid om möjligt. Underhåll kan påverka både klienternas arbetsbelastningar och åtgärder.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.  

  ```PowerShell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Hantera uppdateringar med hjälp av uppdatera panel
Du kan hantera uppdateringar från administratörsportalen. Som Azure Stack-operatör kan du använda uppdatera panel i instrumentpanelen för att:

- Visa viktig information, till exempel den aktuella versionen.
- installera uppdateringar och övervaka förloppet.
- Granska uppdateringshistorik för tidigare installerade uppdateringar.
 
## <a name="determine-the-current-version"></a>Fastställa den aktuella versionen

Uppdatera panel visar den aktuella versionen av Azure Stack. Du kan få till Update-panelen genom att använda någon av följande metoder i administratörsportalen:

- På instrumentpanelen för att visa den aktuella versionen i den **uppdatering** panelen.
 
   ![Uppdateringar-rutan på standardinstrumentpanel](./media/azure-stack-updates/image1.png)
 
- På den **regionshantering** panelen, klickar du på namnet på region. Visa den aktuella versionen i den **uppdatering** panelen.

## <a name="next-steps"></a>Nästa steg

- [Azure Stack som hanteringsprincip](azure-stack-servicing-policy.md) 
- [Regionshantering i Azure Stack](azure-stack-region-management.md)     


