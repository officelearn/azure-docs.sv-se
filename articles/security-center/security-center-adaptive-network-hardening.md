---
title: Anpassad nätverks härdning i Azure Security Center | Microsoft Docs
description: Lär dig hur du använder faktiska trafik mönster för att förstärka reglerna för nätverks säkerhets grupper (NSG) och ytterligare förbättra din säkerhets position.
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
ms.openlocfilehash: d12df01fe1506f7f5ade9cce60ae7af0412e3010
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/23/2020
ms.locfileid: "87080819"
---
# <a name="adaptive-network-hardening-in-azure-security-center"></a>Anpassad nätverks härdning i Azure Security Center
Lär dig hur du konfigurerar anpassad nätverks härdning i Azure Security Center.

## <a name="what-is-adaptive-network-hardening"></a>Vad är anpassad nätverks härdning?
Genom att använda [nätverks säkerhets grupper (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview) för att filtrera trafik till och från resurser förbättras nätverks säkerhets position. Det kan dock fortfarande finnas fall där den faktiska trafiken som flödar genom NSG är en del av de NSG-regler som definierats. I dessa fall kan ytterligare förbättra säkerheten position genom att skärpa NSG-reglerna, baserat på de faktiska trafik mönstren.

Anpassad nätverks härdning ger rekommendationer för ytterligare härdning av NSG-reglerna. Den använder en Machine Learning-algoritm som faktorer i faktisk trafik, känd betrodd konfiguration, Hot information och andra risk indikatorer och ger rekommendationer för att endast tillåta trafik från specifika IP/port-tupler.

Anta till exempel att den befintliga NSG-regeln är att tillåta trafik från 140.20.30.10/24 på port 22. Den anpassningsbara nätverks härdnings rekommendationen, baserat på analysen, skulle vara att begränsa intervallet och tillåta trafik från 140.23.30.10/29 – vilket är ett smalare IP-intervall och neka all annan trafik till porten.

>[!TIP]
> Rekommendationer för anpassningsbar nätverks härdning stöds endast på följande angivna portar (för både UDP och TCP): 13, 17, 19, 22, 23, 53, 69, 81, 111, 119, 123, 135, 137, 138, 139, 161, 162, 389, 445, 512, 514, 593, 636, 873, 1433, 1434, 1900, 2049, 2301, 2323, 2381, 3268, 3306, 3389, 4333, 5353, 5432, 5555, 5800, 5900, 5900, 5985 , 6379, 7000, 7001, 7199, 8081, 8089, 8545, 9042, 9160, 9300, 11211, 16379, 26379, 27017


![Vy över nätverks härdning](./media/security-center-adaptive-network-hardening/traffic-hardening.png)


## <a name="view-adaptive-network-hardening-alerts-and-rules"></a>Visa aviseringar och regler för anpassad nätverks härdning

1. I Security Center väljer du **nätverk**  ->  **anpassad härdning av nätverk**. De virtuella nätverks datorerna visas under tre separata flikar:
   * **Felaktiga resurser**: virtuella datorer som för närvarande har rekommendationer och aviseringar som har utlösts genom att köra algoritmen för anpassad nätverks härdning. 
   * **Felfria resurser**: virtuella datorer utan aviseringar och rekommendationer.
   * Ej **genomsökta resurser**: virtuella datorer som den anpassningsbara algoritmen för nätverks härdning inte kan köras på på grund av någon av följande orsaker:
      * **Virtuella datorer är klassiska virtuella**datorer: endast Azure Resource Manager virtuella datorer stöds.
      * Det finns **inte tillräckligt med data**: för att skapa korrekta rekommendationer för trafik härdning måste Security Center minst 30 dagars trafik data.
      * Den **virtuella datorn skyddas inte av ASC-standarden**: endast virtuella datorer som är inställda på Security Center standard pris nivån är berättigade till den här funktionen.

     ![resurser som inte är felfria](./media/security-center-adaptive-network-hardening/unhealthy-resources.png)

2. På fliken **felaktiga resurser** väljer du en virtuell dator för att visa aviseringar och de rekommenderade regler för härdning som ska tillämpas.

    ![härdning av aviseringar](./media/security-center-adaptive-network-hardening/anh-recommendation-rules.png)


## <a name="review-and-apply-adaptive-network-hardening-recommended-rules"></a>Granska och tillämpa rekommenderade regler för anpassad nätverks härdning

1. Välj en virtuell dator från fliken **felaktiga resurser** . De aviseringar och rekommenderade regler för härdning visas i listan.

     ![härdnings regler](./media/security-center-adaptive-network-hardening/hardening-alerts.png)

   > [!NOTE]
   > Fliken **regler** visar en lista över regler som rekommenderar att du lägger till anpassade nätverks härdningar. Fliken **aviseringar** visar en lista över de aviseringar som har genererats på grund av trafik, som flödar till resursen, som inte ligger inom det tillåtna IP-intervallet i de rekommenderade reglerna.

2. Om du vill ändra några av parametrarna för en regel kan du ändra den, enligt beskrivningen i [ändra en regel](#modify-rule).
   > [!NOTE]
   > Du kan också [ta bort](#delete-rule) eller [lägga till](#add-rule) en regel.

3. Välj de regler som du vill tillämpa på NSG och klicka på **tillämpa**.

      > [!NOTE]
      > De tvingade reglerna läggs till i NSG (s) som skyddar den virtuella datorn. (En virtuell dator kan skyddas av en NSG som är kopplad till dess nätverkskort, eller under nätet där den virtuella datorn finns, eller båda)

    ![tillämpa regler](./media/security-center-adaptive-network-hardening/enforce-hard-rule2.png)


### <a name="modify-a-rule"></a>Ändra en regel <a name ="modify-rule"> </a>

Du kanske vill ändra parametrarna för en regel som har rekommenderats. Till exempel kanske du vill ändra de rekommenderade IP-intervallen.

Några viktiga rikt linjer för att ändra en regel för anpassad nätverks härdning:

* Du kan bara ändra parametrarna för "Tillåt"-regler. 
* Du kan inte ändra reglerna för "Tillåt" för att bli "Neka"-regler. 

  > [!NOTE]
  > Att skapa och ändra "Neka"-regler görs direkt på NSG. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

* En regel för att **neka all trafik** är den enda typen av "Neka"-regel som visas här, och den kan inte ändras. Du kan dock ta bort den (se [ta bort en regel](#delete-rule)).
  > [!NOTE]
  > En **neka all trafik** regel rekommenderas när Security Center, som ett resultat av algoritmen, inte identifierar trafik som ska tillåtas, baserat på den befintliga NSG-konfigurationen. Därför är den rekommenderade regeln att neka all trafik till den angivna porten. Namnet på den här typen av regel visas som "*systemgenererat*". Efter att ha tillämpat den här regeln kommer det faktiska namnet i NSG att vara en sträng som består av protokollet, trafik riktningen, "Neka" och ett slumpmässigt nummer.

*Så här ändrar du en regel för anpassad nätverks härdning:*

1. Om du vill ändra några av parametrarna för en regel går du till fliken **regler** , klickar på de tre punkterna (...) i slutet av regelns rad och klickar på **Redigera**.

   ![Redigera regel](./media/security-center-adaptive-network-hardening/edit-hard-rule.png)

1. I fönstret **Redigera regel** uppdaterar du informationen som du vill ändra och klickar på **Spara**.

   > [!NOTE]
   > När du har klickat på **Spara**har du ändrat regeln. *Men du har inte tillämpat den på NSG.* Om du vill tillämpa det måste du välja regeln i listan och klicka på **tillämpa** (som förklaras i nästa steg).

   ![Redigera regel](./media/security-center-adaptive-network-hardening/edit-hard-rule3.png)

3. Om du vill tillämpa den uppdaterade regeln väljer du den uppdaterade regeln i listan och klickar på **Använd**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)

### <a name="add-a-new-rule"></a>Lägg till en ny <a name ="add-rule"> </a> regel

Du kan lägga till en regel för "Tillåt" som inte rekommenderades av Security Center.

> [!NOTE]
> Det går bara att lägga till regler för "Tillåt" här. Om du vill lägga till regler för neka kan du göra det direkt på NSG. Mer information finns i [skapa, ändra eller ta bort en nätverks säkerhets grupp](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group).

*Så här lägger du till en regel för anpassad nätverks härdning:*

1. Klicka på **Lägg till regel** (finns i det övre vänstra hörnet).

   ![lägg till regel](./media/security-center-adaptive-network-hardening/add-hard-rule.png)

1. I fönstret **ny regel** anger du informationen och klickar på **Lägg till**.

   > [!NOTE]
   > När du har klickat på **Lägg till**har du lagt till regeln och den visas med de andra rekommenderade reglerna. Men du har inte använt den på NSG. Om du vill aktivera det måste du välja regeln i listan och klicka på **tillämpa** (som förklaras i nästa steg).

3. Om du vill använda den nya regeln väljer du den nya regeln i listan och klickar på **Använd**.

    ![tillämpa regel](./media/security-center-adaptive-network-hardening/enforce-hard-rule.png)


### <a name="delete-a-rule"></a>Ta bort en <a name ="delete-rule"> </a> regel

Vid behov kan du ta bort en rekommenderad regel för den aktuella sessionen. Du kan till exempel bestämma att tillämpa en föreslagen regel kan blockera legitim trafik.

*Så här tar du bort en regel för anpassad nätverks härdning för den aktuella sessionen:*

1. Klicka på de tre punkterna (...) i slutet av regelns rad på fliken **regler** och klicka sedan på **ta bort**.  

    ![härdnings regler](./media/security-center-adaptive-network-hardening/delete-hard-rule.png)