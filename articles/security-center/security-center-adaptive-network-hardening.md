---
title: Adaptiv nätverkshärdning i Azure Security Center | Microsoft-dokument
description: Lär dig hur du använder faktiska trafikmönster för att härda dina NSG-regler (Network Security Groups) och förbättra din säkerhetsposition ytterligare.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 09d62d23-ab32-41f0-a5cf-8d80578181dd
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/11/2020
ms.author: memildin
ms.openlocfilehash: a75be23e2e8215d86aebcfd7f4317f2f597d3c5b
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/29/2020
ms.locfileid: "80385086"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Adaptiv nätverkshärdning i Azure Security Center
Lär dig hur du konfigurerar Adaptiv nätverkshärdning i Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Vad är Adaptiv nätverkshärdning?
Genom att använda [nätverkssäkerhetsgrupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) för att filtrera trafik till och från resurser förbättras nätverkssäkerhetspositionen. Det kan dock fortfarande finnas vissa fall där den faktiska trafiken som flyter genom NSG är en delmängd av de fastställda NSG-reglerna. I dessa fall kan ytterligare förbättra säkerhetshållningen uppnås genom att skärpa NSG-reglerna, baserat på de faktiska trafikmönstren.

Adaptiv nätverkshärdning ger rekommendationer för att ytterligare härda NSG-reglerna. Den använder en maskininlärningsalgoritm som faktorer i faktisk trafik, känd betrodd konfiguration, hotinformation och andra kompromissindikatorer och ger sedan rekommendationer som endast tillåter trafik från specifika IP/port-tupplar.

Anta till exempel att den befintliga NSG-regeln är att tillåta trafik från 140.20.30.10/24 på port 22. Adaptive Network Hardening rekommendation, baserat på analysen, skulle vara att begränsa intervallet och tillåta trafik från 140.23.30.10/29 - vilket är en smalare IP-intervall, och neka all annan trafik till den porten.

>[!TIP]
> Adaptiva rekommendationer för nätverkshärdning stöds endast på följande specifika portar (för både UDP och TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301 , 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985, 5986, 6379, 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017, 37215


![Vyn För härdning av nätverk](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visa varningar och regler för adaptiv nätverkshärdning

1. Välj **Nätverksanpassad** -> **nätverkshärdning**i Säkerhetscenter . De virtuella nätverks-datorerna visas under tre separata flikar:
   * **Felaktiga resurser:** Virtuella datorer som för närvarande har rekommendationer och aviseringar som utlöstes genom att köra algoritmen Adaptiv nätverkshärdning. 
   * **Felfria resurser:** Virtuella datorer utan aviseringar och rekommendationer.
   * **Icke-skannade resurser:** Virtuella datorer som algoritmen för adaptiv nätverkshärdning inte kan köras på på grund av något av följande:
      * **Virtuella datorer är klassiska virtuella datorer:** Endast virtuella azure resource manager-datorer stöds.
      * **Det finns inte tillräckligt med data**: För att generera korrekta trafikhärdningsrekommendationer kräver Security Center minst 30 dagars trafikdata.
      * **DEN VIRTUELLA DATORN skyddas inte av ASC-standarden:** Endast virtuella datorer som är inställda på Security Centers standardprisnivå är kvalificerade för den här funktionen.

     ![ohälsosamma resurser](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. På fliken **Felresurs** väljer du en virtuell dator för att visa dess aviseringar och de rekommenderade härdningsreglerna som ska tillämpas.

    ![härdningsvarningar](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Granska och tillämpa rekommenderade regler för adaptiv nätverkshärdning

1. Välj en virtuell dator på fliken **Felfritt resurser.** Aviseringar och rekommenderade härdningsregler visas.

     ![härdningsregler](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > På fliken **Regler** visas de regler som adaptiv nätverkshärdning rekommenderar att du lägger till. På fliken **Aviseringar** visas de aviseringar som genererades på grund av trafik, som flödar till resursen, vilket inte ligger inom det IP-intervall som tillåts i de rekommenderade reglerna.

2. Om du vill ändra några av parametrarna för en regel kan du ändra den, som förklaras i [Ändra en regel](#modify-rule).
   > [!NOTE]
   > Du kan också [ta bort](#delete-rule) eller [lägga till](#add-rule) en regel.

3. Markera de regler som du vill tillämpa på NSG och klicka på **Verkställ**.

      > [!NOTE]
      > De tillämpningsreglerna läggs till i NSG(s) som skyddar den virtuella datorn. (En virtuell dator kan skyddas av en NSG som är kopplad till dess nätverkskort, eller undernätet där den virtuella datorn finns, eller båda)

    ![genomdriva regler](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Ändra en <a name ="modify-rule"> </a> regel

Du kanske vill ändra parametrarna för en regel som har rekommenderats. Du kanske till exempel vill ändra de rekommenderade IP-intervallen.

Några viktiga riktlinjer för att ändra en regel för adaptiv nätverkshärdning:

* Du kan bara ändra parametrarna för "tillåt"-regler. 
* Du kan inte ändra "tillåt"-regler för att bli "neka" regler. 

  > [!NOTE]
  > Skapa och ändra "neka" regler görs direkt på NSG. Mer information finns i [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* En **neka all trafikregel** är den enda typen av "neka"-regel som skulle visas här och den kan inte ändras. Du kan dock ta bort den (se [Ta bort en regel](#delete-rule)).
  > [!NOTE]
  > En **neka all trafikregel** rekommenderas när Säkerhetscenter som ett resultat av att algoritmen körs inte identifierar trafik som ska tillåtas, baserat på den befintliga NSG-konfigurationen. Därför är den rekommenderade regeln att neka all trafik till den angivna porten. Namnet på den här typen av regel visas som "*Systemgenererad*". När den här regeln har tillämpats kommer dess faktiska namn i NSG att vara en sträng som består av protokollet, trafikriktningen, "DENY" och ett slumptal.

*Så här ändrar du en regel för adaptiv nätverkshärdning:*

1. Om du vill ändra några av parametrarna för en regel klickar du på de tre punkterna (...) i slutet av regelns rad på fliken **Regler** och klickar på **Redigera**.

   ![redigera regel](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. Uppdatera informationen som du vill ändra i fönstret **Redigera regel** och klicka på **Spara**.

   > [!NOTE]
   > När du har klickat på **Spara**har du ändrat regeln. *Du har dock inte tillämpat den på NSG.* Om du vill använda den måste du välja regeln i listan och klicka på **Verkställ** (som förklaras i nästa steg).

   ![redigera regel](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Om du vill tillämpa den uppdaterade regeln markerar du den uppdaterade regeln i listan och klickar på **Verkställ**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Lägga till <a name ="add-rule"> </a> en ny regel

Du kan lägga till en "tillåt"-regel som inte rekommenderades av Security Center.

> [!NOTE]
> Endast "tillåt" regler kan läggas till här. Om du vill lägga till "neka" regler, kan du göra det direkt på NSG. Mer information finns i [Skapa, ändra eller ta bort en nätverkssäkerhetsgrupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Så här lägger du till en regel för adaptiv nätverkshärdning:*

1. Klicka på **Lägg till regel** (finns i det övre vänstra hörnet).

   ![lägga till regel](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. I fönstret **Ny regel** anger du informationen och klickar på **Lägg till**.

   > [!NOTE]
   > När du har klickat **på Lägg till**har du lagt till regeln och visas med de andra rekommenderade reglerna. Du har dock inte tillämpat det på NSG. Om du vill aktivera den måste du välja regeln i listan och klicka på **Verkställ** (som förklaras i nästa steg).

3. Om du vill tillämpa den nya regeln markerar du den nya regeln i listan och klickar på **Verkställ**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Ta bort <a name ="delete-rule"> </a> en regel

Vid behov kan du ta bort en rekommenderad regel för den aktuella sessionen. Du kan till exempel bestämma att om du tillämpar en föreslagen regel kan den blockera legitim trafik.

*Så här tar du bort en regel för adaptiv nätverkshärdning för den aktuella sessionen:*

1. Klicka på de tre punkterna (...) i slutet av regelns rad på fliken **Regler** och klicka på **Ta bort**.  

    ![härdningsregler](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)