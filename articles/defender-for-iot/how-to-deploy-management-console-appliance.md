---
title: Distribuera hanterings konsolen i Azure Defender för IoT
description: Lär dig mer om hur du distribuerar hanterings konsolen i Azure Defender för IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094535"
---
# <a name="deploy-the-management-console"></a>Distribuera hanterings konsolen
Den här artikeln beskriver hur du distribuerar den lokala hanterings konsolen för Azure Defender för IoT.

## <a name="the-on-premises-management-console"></a>Den lokala hanterings konsolen

Den lokala hanterings konsolen innehåller en samlad vy över alla nätverks till gångar och levererar en real tids visning av viktiga IoT-och stötande risk indikatorer och aviseringar i alla dina lokaler. Den är nära integrerad med dina SOC-arbetsflöden och kör böcker, vilket möjliggör enkel prioritering av minsknings aktiviteter och korrelationer mellan olika webbplatser av hot.

- Holistisk – minska komplexiteten med en enda enhetlig plattform för till gångs hantering, risk-och sårbarhets hantering, samt hot övervakning med incident svar.

- Agg regering och korrelation – Visa, Sammanställ och analysera data och aviseringar som samlats in från alla platser.

- Kontrol lera alla sensorer – konfigurera och övervaka alla sensorer från en enda plats.

   ![Vy över Azure Defender för plats hantering i IoT](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Distribuera den lokala hanterings konsolens installation

Den här processen kräver att du köper din egen maskin vara och installerar program vara. Lösningen körs på certifierade apparater. Se [Guide för maskin varu specifikationer för Azure Defender för IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) som referens när du köper din certifierade apparat.

I [installations guiden för Azure Defender för IoT](https://aka.ms/AzureDefenderforIoTInstallSensorISO) finns mer information om hur du hämtar ISO-avbildningen och installerar sensor program varan.

**Så här distribuerar du den lokala hanterings konsolen:**

1. Navigera till Microsoft Azure Defender för IoT.

2. Välj **lokal hanterings konsol**.

   ![Vyn Azure Defender för lokal hanterings konsol i IoT](media/updates/image15.png)

3. Välj version 3,1 på menyn **Välj version** .

4. Välj **Hämta** och spara filen.

5. När program varan har installerats kan du utföra nätverks konfigurations åtgärder. Mer information finns i [installations guiden för Azure Defender för IoT-nätverk](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="next-steps"></a>Nästa steg

Om du vill veta mer om konfigurations alternativ fortsätter du till instruktions guiden för konfiguration av modulen.
> [!div class="nextstepaction"]
> [Guide för konfiguration av moduler](./how-to-agent-configuration.md)
