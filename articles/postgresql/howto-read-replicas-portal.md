---
title: Hantera skrivskyddade repliker för Azure Database för PostgreSQL i Azure Portal
description: Lär dig mer om att hantera Azure Database för PostgreSQL läsa repliker från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/19/2019
ms.openlocfilehash: b34b103d3b710b90fd7b396f2c8d0e7adc27aaca
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/20/2019
ms.locfileid: "56454675"
---
# <a name="create-and-manage-read-replicas-from-the-azure-portal"></a>Skapa och hantera skrivskyddade repliker från Azure portal

I den här artikeln får du lära dig hur du skapar och hanterar skrivskyddade repliker i Azure Database for PostgreSQL från Azure-portalen. Läs mer om skrivskyddade repliker i den [översikt](concepts-read-replicas.md).

> [!IMPORTANT]
> Läs replica-funktionen är i offentlig förhandsversion.

## <a name="prerequisites"></a>Förutsättningar
En [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md) vara huvudservern.

## <a name="prepare-the-master-server"></a>Förbereda huvudservern
De här stegen måste användas för att förbereda en huvudserver på generell användning och den Minnesoptimerade nivån.

Den `azure.replication_support` parametern måste anges till **REPLIKEN** på huvudservern. När den här parametern har ändrats, krävs en omstart av servern för att ändringen ska börja gälla.

1. Välj befintlig Azure Database for PostgreSQL-server att använda som en Azure-portalen.

2. På menyn till vänster väljer **serverparametrar**.

3. Sök efter den `azure.replication_support` parametern.

   ![Sök efter parametern azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Ange den `azure.replication_support` parametervärde **REPLIKEN**. Välj **spara** behålla dina ändringar.

   ![Ange parametern till REPLIK och spara dina ändringar](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. När du har sparat ändringarna kan få du ett meddelande:

   ![Spara meddelande](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Starta om servern för att tillämpa ändringarna. Läs hur du startar om en server i [starta om en Azure Database for PostgreSQL-server](howto-restart-server-portal.md).


## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik
Följ dessa steg om du vill skapa en skrivskyddad replik:

1.  Välj befintlig Azure Database for PostgreSQL-server att använda som huvudserver. 

2.  På servermenyn under **inställningar**väljer **replikering**.

   Om du inte angett det `azure.replication_support` parameter **REPLIKEN** på en generell användning eller Minnesoptimerade huvudservern och starta om servern, visas ett meddelande. Slutföra de här stegen innan du skapar repliken.

3.  Välj **lägga till en replik**.

   ![Lägg till en replik](./media/howto-read-replicas-portal/add-replica.png)

4.  Ange ett namn för den skrivskyddade repliken. Välj **OK** att bekräfta skapandet av repliken.

   ![Namn på repliken](./media/howto-read-replicas-portal/name-replica.png) 

En replik skapas med hjälp av samma serverkonfiguration som huvudserver. När en replik skapas flera inställningar kan ändras oberoende från huvudservern: compute-generering, vCores, lagring och säkerhetskopiering kvarhållningsperiod. Prisnivån kan också ändras oberoende av varandra, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvudserver-konfiguration har uppdaterats till nya värden, uppdatera konfigurationen för repliken till samma eller högre värden. Den här åtgärden säkerställer att replikeringen kan hålla jämna steg med ändringar som görs till huvudservern.

När den skrivskyddade repliken har skapats kan den kan visas från den **replikering** fönster:

![Visa den nya repliken i fönstret replikering](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikering mellan en huvudserver och en skrivskyddad replik.

> [!IMPORTANT]
> När du har slutat replikering till en huvudserver och en skrivskyddad replik, kan inte den ångras. Läs replik blir en fristående server som har stöd för både läsningar och skrivningar. Den fristående servern kan inte göras i en replik igen.

Följ dessa steg om du vill stoppa replikering mellan en huvudserver och en skrivskyddad replik från Azure portal:

1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  På servermenyn under **inställningar**väljer **replikering**.

3.  Välj replikservern som du vill stoppa replikering.

   ![Väljer du repliken](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Välj **replikeringen stoppas**.

   ![Välj replikeringen stoppas](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Välj **OK** att stoppa replikering.

   ![Bekräfta att du vill stoppa replikering](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Ta bort en huvudserver
Om du vill ta bort en huvudserver måste använda du samma steg att ta bort en fristående Azure Database for PostgreSQL-server. 

> [!IMPORTANT]
> När du tar bort en huvudserver har replikering till alla skrivskyddade repliker stoppats. Skrivskyddade repliker bli fristående servrar som stöder nu både läsningar och skrivningar.

Följ dessa steg om du vill ta bort en server från Azure portal:

1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  Öppna den **översikt** sidan för servern. Välj **Ta bort**.

   ![På sidan Översikt, Välj att ta bort huvudservern](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Ange namnet på huvudservern för att ta bort. Välj **ta bort** att bekräfta borttagningen av huvudservern.

   ![Bekräfta att du vill ta bort huvudservern](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Ta bort en replik
Du kan ta bort en skrivskyddad replik som liknar hur du tar bort en huvudserver.

- I Azure-portalen öppnar du den **översikt** för skrivskyddade replik. Välj **Ta bort**.

   ![Välj att ta bort replikeringen på översiktssidan för replik](./media/howto-read-replicas-portal/delete-replica.png)
 
Du kan också ta bort Läs replik från den **replikering** fönster genom att följa dessa steg:

1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  På servermenyn under **inställningar**väljer **replikering**.

3.  Välj den skrivskyddade repliken för att ta bort.

   ![Välj repliken för att ta bort](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Välj **ta bort replik**.

   ![Välj Ta bort replik](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Ange namnet på repliken för att ta bort. Välj **ta bort** att bekräfta borttagningen av repliken.

   ![Bekräfta att du vill ta bort te replik](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Övervaka en replik
Två måtten är tillgängliga för att övervaka skrivskyddade repliker.

### <a name="max-lag-across-replicas-metric"></a>Maximal fördröjning mellan repliker mått
Den **Max fördröjning mellan repliker** måttet visar förskjutningen i byte mellan huvudservern och de flesta släpar repliken. 

1.  Välj master Azure Database for PostgreSQL-server i Azure-portalen.

2.  Välj **Mått**. I den **mått** väljer **Max fördröjning mellan repliker**.

    ![Övervaka Maximal fördröjning i repliker](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  För din **aggregering**väljer **Max**.


### <a name="replica-lag-metric"></a>Repliken fördröjning mått
Den **repliken fördröjning** mått visar tiden sedan senast återupprepas transaktion på en replik. Om det finns inga transaktioner som sker i bakgrunden, visar den här tidsförskjutningen i måttet.

1.  Välj Azure Database för PostgreSQL Läs replik i Azure-portalen.

2.  Välj **Mått**. I den **mått** väljer **repliken fördröjning**.

   ![Övervaka replik-fördröjning](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  För din **aggregering**väljer **Max**. 
 
## <a name="next-steps"></a>Nästa steg
Läs mer om [läsa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).