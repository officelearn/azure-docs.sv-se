---
title: 'Snabb start: Konfigurera arbets belastnings isolering – Portal'
description: Använd Azure Portal för att konfigurera arbets belastnings isolering.
services: synapse-analytics
author: ronortloff
manager: craigg
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 05/04/2020
ms.author: rortloff
ms.reviewer: jrasnick
ms.custom: azure-synapse
ms.openlocfilehash: 70ac4942c397e8ca5db2d1b5041d0d9d43ae7222
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/05/2020
ms.locfileid: "82794058"
---
# <a name="quickstart-configure-synapse-sql-pool-workload-isolation-using-a-workload-group-in-the-azure-portal"></a>Snabb start: Konfigurera Synapse för arbets belastnings isolering i SQL-pool med en arbets belastnings grupp i Azure Portal

I den här snabb starten konfigurerar du [arbets belastnings isolering](sql-data-warehouse-workload-isolation.md) genom att skapa en arbets belastnings grupp för reservering av resurser.  I den här självstudien skapar vi arbets belastnings gruppen för inläsning av `DataLoads`data som anropas. Arbets belastnings gruppen kommer att reservera 20% av system resurserna.  Med 20% isolering för data inläsningar är de garanterat resurser som gör att de kan trycka på service avtal.  När du har skapat arbets belastnings gruppen [skapar du en klassificering för arbets belastning](quickstart-create-a-workload-classifier-portal.md) för att tilldela frågor till den här arbets belastnings gruppen


Om du inte har en Azure-prenumeration kan du skapa ett [kostnads fritt](https://azure.microsoft.com/free/) konto innan du börjar.


## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure Portal](https://portal.azure.com/).

> [!NOTE]
> Att skapa en instans av SQL-poolen i Azure Synapse Analytics kan resultera i en ny fakturerbar tjänst.  Mer information finns i [priser för Azure Synapse Analytics](https://azure.microsoft.com/pricing/details/sql-data-warehouse/).

## <a name="prerequisites"></a>Krav

Den här snabb starten förutsätter att du redan har en instans av SQL-poolen i Synapse SQL och att du har behörighet att kontrol lera databasen. Om du behöver skapa ett använder du [Skapa och ansluta – portal](create-data-warehouse-portal.md) för att skapa ett informationslager med namnet **mySampleDataWarehouse**.

>[!IMPORTANT] 
>SQL-poolen måste vara online för att konfigurera arbets belastnings hantering. 

## <a name="configure-workload-isolation"></a>Konfigurera arbets belastnings isolering
Resurser i SQL-poolen kan isoleras och reserveras för vissa arbets belastningar genom att skapa arbets belastnings grupper.  I dokumentationen för arbets belastnings [isolering](sql-data-warehouse-workload-isolation.md) finns mer information om hur arbets belastnings grupper kan hjälpa dig att hantera arbets belastningen.  Snabb starten [skapa och Anslut – portalen](create-data-warehouse-portal.md) skapade **mySampleDataWarehouse** och initierade den med 400 DWU: er. Följande steg skapar en arbets belastnings grupp i **mySampleDataWarehouse**.

Så här skapar du en arbets belastnings grupp med 20% isolering:
1.  Klicka på **Azure Synapse Analytics (tidigare SQL DW)** på den vänstra sidan i Azure Portal.
2.  Välj **mySampleDataWarehouse** på sidan **Azure Synapse Analytics (tidigare SQL DW)** . SQL-poolen öppnas.
3.  Klicka på **arbets belastnings hantering**.
4.  Klicka på **ny arbets belastnings grupp**.
5.  Klicka på **anpassad**.

    ![Klicka på anpassad](./media/quickstart-configure-workload-isolation-portal/create-wg.png)

6.  Ange `DataLoads` för **arbets belastnings gruppen**.
7.  Ange `20` för **min. Resources%**.
8.  Ange `5` för **min. resurser% per begäran**.
9.  Ange `100` för **Cap-resurser%**.
10.   Klicka på **Spara**.

   ![Klicka på Spara](./media/quickstart-configure-workload-isolation-portal/configure-wg.png)

Ett Portal meddelande visas när arbets belastnings gruppen skapas.  Resurserna för arbets belastnings gruppen visas i diagrammen under de konfigurerade värdena.

   ![Klicka på slutgiltig](./media/quickstart-configure-workload-isolation-portal/display-wg.png)

## <a name="clean-up-resources"></a>Rensa resurser

Ta bort `DataLoads` arbets belastnings gruppen som skapats i den här självstudien:
1. Klicka på **`...`** till höger om arbets `DataLoads` belastnings gruppen.
2. Klicka på **ta bort arbets belastnings grupp**.
3. Klicka på **Ja** när du uppmanas att bekräfta borttagningen av arbets belastnings gruppen.
4. Klicka på **Spara**.

   ![Klicka på ta bort](./media/quickstart-configure-workload-isolation-portal/delete-wg.png)



Du debiteras för data lager enheter och data som lagras i ditt informations lager. Dessa beräknings- och lagringsresurser debiteras separat.

- Om du vill behålla data i lagringsutrymmet kan du pausa beräkningarna när du inte använder informationslagret. Genom att pausa beräkning debiteras du bara för data lagring. När du är redo att arbeta med data återupptar du beräkningen.
- Om du vill undvika framtida avgifter kan du ta bort informationslagret.

Rensa resurserna genom att följa dessa steg.

1. Logga in på [Azure Portal](https://portal.azure.com)och välj på informations lagret.

    ![Rensa resurser](./media/load-data-from-azure-blob-storage-using-polybase/clean-up-resources.png)

2. Om du vill pausa beräkningen väljer du knappen **pausa** . När informationslagret har pausats visas knappen **Starta**.  Om du vill återuppta beräkningen väljer du **Start**.

3. Om du vill ta bort data lagret så att du inte debiteras för beräkning eller lagring väljer du **ta bort**.

4. Om du vill ta bort den SQL-Server som du har skapat väljer du **sqlpoolservername.Database.Windows.net** i föregående bild och väljer sedan **ta bort**.  Var försiktig med den här borttagningen eftersom du även tar bort alla databaser som har tilldelats servern.

5. Om du vill ta bort resurs gruppen väljer du **myResourceGroup**och väljer sedan **ta bort resurs grupp**.

## <a name="next-steps"></a>Nästa steg

Om du vill `DataLoads` använda arbets belastnings gruppen måste en [arbets belastnings klassificerare](/sql/t-sql/statements/create-workload-classifier-transact-sql?toc=/azure/synapse-analytics/sql-data-warehouse/toc.json&bc=/azure/synapse-analytics/sql-data-warehouse/breadcrumb/toc.json&view=azure-sqldw-latest) skapas för att dirigera begär anden till arbets belastnings gruppen.  Fortsätt till självstudien [skapa arbets belastnings klassificering](quickstart-create-a-workload-classifier-portal.md) för att skapa `DataLoads`en klassificering för arbets belastning för.

## <a name="see-also"></a>Se även
Se artikeln om hur du [hanterar och övervakar arbets belastnings hantering](sql-data-warehouse-how-to-manage-and-monitor-workload-importance.md) för mer information om hur du övervakar arbets belastningar för hantering av arbets belastning.
