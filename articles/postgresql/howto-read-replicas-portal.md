---
title: Hantera skrivskyddade repliker i Azure-portalen för Azure Database för PostgreSQL
description: Den här artikeln beskrivs hantera Azure Database för PostgreSQL läsa repliker i Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/17/2019
ms.openlocfilehash: 6c1a0a4a13a70daec157ede98f850f87150f8d93
ms.sourcegitcommit: ba9f95cf821c5af8e24425fd8ce6985b998c2982
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/17/2019
ms.locfileid: "54383530"
---
# <a name="how-to-create-and-manage-read-replicas-in-the-azure-portal"></a>Hur du skapar och hanterar läsa repliker i Azure portal
I den här artikeln får lära du dig att skapa och hantera skrivskyddade repliker i tjänsten Azure Database for PostgreSQL med Azure portal. Mer information om skrivskyddade repliker [dokumentationen begrepp](concepts-read-replicas.md).

## <a name="prerequisites"></a>Förutsättningar
- En [Azure Database for PostgreSQL-server](quickstart-create-server-database-portal.md) som ska vara huvudservern.

## <a name="prepare-the-master-server"></a>Förbereda huvudservern
Det här steget för förberedelse av master gäller för generell användning och Minnesoptimerad servrar.

Den **azure.replication_support** parametern måste anges till REPLIKEN på huvudservern. Ändra den här parametern kräver en omstart av servern ska börja gälla.

1. Välj befintlig Azure Database for PostgreSQL-server som du vill använda som en Azure-portalen.

2. Välj **serverparametrar** på menyn till vänster.

3. Sök efter **azure.replication_support**.

   ![Azure Database för PostgreSQL – azure.replication_support](./media/howto-read-replicas-portal/azure-replication-parameter.png)

4. Ange **azure.replication_support** till REPLIKEN. **Spara** ändringen.

   ![Azure Database för PostgreSQL – replikering och spara](./media/howto-read-replicas-portal/save-parameter-replica.png)

5. När du sparar är klar får du ett meddelande.

   ![Azure Database för PostgreSQL – spara meddelande](./media/howto-read-replicas-portal/parameter-save-notification.png)

6. Starta om servern för att tillämpa ändringen när det sparats. Se [omstart dokumentationen](howto-restart-server-portal.md) att lära dig hur du startar om en server.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik
Läs repliker kan skapas med följande steg:
1.  Välj befintlig Azure Database for PostgreSQL-server som du vill använda som en. 

2.  Välj replikering från menyn, under inställningar.

   Om du inte angett **azure.replication_support** till REPLIKEN för generell användning eller Minnesoptimerade huvud- och startats om servern, visas ett meddelande med instruktioner att göra detta. Göra det innan du fortsätter med att skapa.

3.  Välj Lägg till repliken.

   ![Azure Database för PostgreSQL – lägga till replik](./media/howto-read-replicas-portal/add-replica.png)

4.  Ange ett namn för replikservern och välj OK om du vill bekräfta skapandet av repliken.

   ![Azure Database för PostgreSQL – namn på repliken](./media/howto-read-replicas-portal/name-replica.png) 

> [!IMPORTANT]
> Läs repliker skapas med samma serverkonfiguration som huvudserver. När en replik har skapats, prisnivån (förutom till och från Basic), compute-generering, vCores, lagring och kvarhållningsperiod för säkerhetskopiering kan ändras oberoende från huvudservern.

> [!IMPORTANT]
> Innan en master-serverkonfigurationen har uppdaterats till nya värden, bör de replikerna konfiguration uppdateras till samma eller högre värden. Försök att göra annars genereras ett fel. Detta säkerställer att repliker är kan hålla jämna steg med ändringar som gjorts i huvuddatabasen. 


När du har skapat replikservern kan det visas i fönstret replikering.

![Azure Database för PostgreSQL – nya repliken](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Stoppa replikering

> [!IMPORTANT]
> Stoppa replikering till en server kan inte ångras. När replikering har upphört mellan huvud- och repliken och kan inte den ångras. Replikservern sedan blir en fristående server och stöder nu både läs- och skrivåtgärder. Den här servern kan inte göras i en replik igen.

Om du vill stoppa replikering mellan en och en replik från Azure portal, använder du följande steg:
1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  Välj replikering från menyn, under inställningar.

3.  Välj replikservern som du vill stoppa replikering för.

   ![Azure Database för PostgreSQL – Välj replik](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Välj replikeringen stoppas.

   ![Azure Database för PostgreSQL – Välj Stoppa replikering](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5.  Bekräfta att du vill stoppa replikering genom att klicka på OK.

   ![Azure Database för PostgreSQL – bekräfta Stoppa replikering](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master"></a>Tar bort en mall

> [!IMPORTANT]
> Tar bort en huvudserver stoppar replikeringen till alla replikservrar. Replikservrar bli fristående servrar som stöder nu både läs- och skrivåtgärder.
Tar bort en följer samma steg för en fristående Azure Database for PostgreSQL-server. Om du vill ta bort en server från Azure-portalen gör du följande:

1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  Välj Ta bort från en översikt.

   ![Azure Database för PostgreSQL – ta bort server](./media/howto-read-replicas-portal/delete-server.png)
 
3.  Skriv namnet på huvudservern och välj Ta bort för att bekräfta borttagningen av huvudservern.

   ![Azure Database för PostgreSQL – Bekräfta borttagning](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Ta bort en replik
Om du vill ta bort en skrivskyddad replik följer du samma steg som tar bort en huvudserver som ovan. Först öppna sidan översikt av repliken och välj sedan ta bort.

   ![Azure Database för PostgreSQL – ta bort replik](./media/howto-read-replicas-portal/delete-replica.png)
 
Alternativt kan du radera den från fönstret replikering.
1.  Välj master Azure Database för PostgreSQL-server i Azure-portalen.

2.  Välj replikering från menyn, under inställningar.

3.  Välj replikservern som du vill ta bort. 

   ![Azure Database för PostgreSQL – Välj replik](./media/howto-read-replicas-portal/select-replica.png)
 
4.  Välj Ta bort replik.

   ![Azure Database för PostgreSQL – Välj Ta bort replik](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5.  Skriv namnet på repliken och välj Ta bort för att bekräfta borttagning av repliken.

   ![Azure Database för PostgreSQL – Bekräfta ta bort replik](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Övervaka en replik
### <a name="max-lag-across-replicas"></a>Max Lag Across Replicas
Den **Maximal fördröjning mellan repliker** måttet visar fördröjning mellan huvudservern och mest släpar repliken. 

1.  I Azure-portalen väljer du den **master** Azure Database for PostgreSQL-server.

2.  Välja mått. I fönstret mått väljer **Max fördröjning mellan repliker**.

    ![Azure Database för PostgreSQL – övervaka Maximal fördröjning mellan repliker](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  Välj **Max** som din aggregering. 

### <a name="replica-lag"></a>Repliken fördröjning
Den **repliken fördröjning** mått visar tiden sedan senast återupprepas transaktion på den här repliken. Om det finns inga transaktioner som sker i bakgrunden, visar den här tidsförskjutningen i måttet.

1.  I Azure-portalen väljer du en **repliken** Azure Database for PostgreSQL-server.

2.  Välja mått. I fönstret mått väljer **repliken fördröjning**.

   ![Azure Database för PostgreSQL – övervaka repliken fördröjning](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3.  Välj **Max** som din aggregering. 
 
## <a name="next-steps"></a>Nästa steg
- Läs mer om [läsa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).