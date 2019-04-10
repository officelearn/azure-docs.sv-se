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
ms.date: 04/04/2019
ms.author: mabrigg
ms.lastreviewed: 04/04/2019
ms.reviewer: justini
ms.openlocfilehash: bf797404b173d6febe133eacbb9d36310160aff6
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281581"
---
# <a name="manage-updates-in-azure-stack-overview"></a>Hantera uppdateringar i Azure Stack – översikt

*Gäller för Azure Stack-integrerade system*

Microsoft update-paket släpp för Azure Stack-integrerat system vanligtvis runt den fjärde tisdagen varje månad. Be tillverkaren (OEM) om deras specifika meddelande registreringsprocessen så att meddelanden om uppdateringar nå din organisation. Du kan också kontrollera i det här dokumentationsbiblioteket under **översikt** > **viktig** information om versioner som är aktiva support. 

Varje version av Microsoft-programuppdateringar tillsammans i en enda uppdateringspaketet. Du kan importera som en Azure Stack-operator, installation och övervaka installationsförloppet för dessa uppdateringspaket från administratörsportalen. 

OEM-leverantör kommer också att släppa uppdateringar, t.ex drivrutinen och firmware-uppdateringar. Även om de här uppdateringarna levereras som separata paket från leverantören av, är de importerats, installeras och hanteras på samma sätt som uppdateringspaket från Microsoft.

Du måste hålla Azure Stack uppdateras till en specifik version-nivå för att hålla datorn under support. Se till att du läser igenom den [Azure Stack hanteringsprincip](azure-stack-servicing-policy.md).

> [!NOTE]
> Du kan inte använda Azure Stack-uppdateringspaket Azure Stack Development Kit. Uppdateringspaket är utformade för integrerade system. Mer information finns i [distribuera om ASDK](https://docs.microsoft.com/azure/azure-stack/asdk).

## <a name="the-update-resource-provider"></a>Update-resursprovidern

Azure Stack innehåller en Update-resursprovidern som hanterar användandet av Microsoft-programuppdateringar. Den här providern kontrollerar att uppdateringarna ska tillämpas på alla fysiska värdar, Service Fabric-program och körningar, och alla infrastrukturens virtuella datorer och deras tillhörande tjänster.

Uppdateringar installeras, kan du visa övergripande status som uppdatering processen mål olika delsystem i Azure Stack (till exempel fysiska värdar och infrastrukturens virtuella datorer).

## <a name="plan-for-updates"></a>Planera för uppdateringar

Vi rekommenderar starkt att du meddela användare om eventuella underhållsåtgärder, och att du schemalägger normala underhållsfönster under tider utanför kontorstid om möjligt. Underhåll kan påverka både klienternas arbetsbelastningar och åtgärder.

- Innan du påbörjar installationen av uppdateringen kör [Test AzureStack](azure-stack-diagnostic-test.md) med följande parametrar för att verifiera statusen för din Azure Stack och lösa alla operativa problem som hittades, inklusive alla varningar och fel. Även granska aktiva aviseringar och lösningar som kräver åtgärd.  

  ```powershell
  Test-AzureStack -Group UpdateReadiness
  ``` 

## <a name="using-the-update-tile-to-manage-updates"></a>Hantera uppdateringar med hjälp av uppdatera panel

Du kan hantera uppdateringar från administratörsportalen. Som Azure Stack-operatör kan du använda uppdatera panel i instrumentpanelen för att:

- Visa viktig information, till exempel den aktuella versionen.
- installera uppdateringar och övervaka förloppet.
- Granska uppdateringshistorik för tidigare installerade uppdateringar.
- Visa Molnets aktuella OEM-Paketversion
 
## <a name="determine-the-current-version"></a>Fastställa den aktuella versionen

Du kan visa den aktuella versionen av Azure Stack i Update-panelen. Så här öppnar panelen:

1. Öppna Azure Stack-administrationsportalen.
2. Välj **instrumentpanelen**. I den **uppdatering** panel, den aktuella versionen visas. 

    ![Uppdateringar-rutan på standardinstrumentpanel](./media/azure-stack-updates/image1.png)

    På skärmen är till exempel versionen 1.1903.0.35.

## <a name="install-updates-and-monitor-progress"></a>Installera uppdateringar och övervaka förloppet


1. Öppna Azure Stack-administrationsportalen.
2. Välj **instrumentpanelen**. Välj panelen uppdatering.
3. Välj **Uppdatera nu**.

    ![Uppdatering av Azure Stack körningsinformation](media/azure-stack-updates/azure-stack-update-button.png)

4.  Du kan visa övergripande status som uppdateringsprocessen går igenom olika delsystem i Azure Stack. Exempel undersystem inkluderar fysiska värdar, Service Fabric infrastrukturens virtuella datorer och tjänster som innehåller både användare och administratör portaler. Under hela processen update rapporterar resursprovidern Update ytterligare information om uppdateringen, till exempel antalet steg som är klara, samt hur många pågår.

5. Välj den **fullständig Hämtningsloggar** kör informationsblad för att hämta fullständig loggar från uppdateringen.

    ![Uppdatering av Azure Stack körningsinformation](media/azure-stack-updates/update-run-details.png)

6. När klar resursprovidern uppdateringen innehåller en **lyckades** bekräftelse så att du vet att uppdateringen har slutförts och hur lång tid det tog. Därifrån kan visa du information om alla uppdateringar, tillgängliga uppdateringar eller installerade uppdateringar med hjälp av filtret.

    ![Uppdatering av Azure Stack kör information lyckades](media/azure-stack-updates/update-success.png)

   Om uppdateringen misslyckas uppdateringen panelen rapporter **behöver åtgärdas**. Använd den **fullständig Hämtningsloggar** att hämta statusen på hög nivå där uppdateringen kan ha misslyckats. Logginsamling för Azure Stack hjälper till att underlätta diagnostik och felsökning.

## <a name="next-steps"></a>Nästa steg

- [Azure Stack som hanteringsprincip](azure-stack-servicing-policy.md) 
- [Regionshantering i Azure Stack](azure-stack-region-management.md)
