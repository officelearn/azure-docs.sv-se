---
title: Hantera Läs repliker – Azure Portal-Azure Database for PostgreSQL-enskild server
description: Lär dig hur du hanterar Läs repliker Azure Database for PostgreSQL-enskild server från Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "76768948"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for PostgreSQL-enskild server från Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for PostgreSQL från Azure Portal. Mer information om Läs repliker finns i [översikten](concepts-read-replicas.md).


## <a name="prerequisites"></a>Krav
En [Azure Database for postgresql-server](quickstart-create-server-database-portal.md) som ska vara huvud servern.

## <a name="prepare-the-master-server"></a>Förbered huvud servern
De här stegen måste användas för att förbereda en huvud server i Generell användning-eller minnesoptimerade nivåer. Huvud servern förbereds för replikering genom att ange Azure. replication_support-parametern. När parametern Replication ändras krävs en omstart av servern för att ändringen ska börja gälla. I Azure Portal kapslas de här två stegen av en enda knapp, vilket **aktiverar stöd för replikering**.

1. I Azure Portal väljer du den befintliga Azure Database for PostgreSQL-servern som ska användas som huvud server.

2. Välj **replikering**under **Inställningar**på Server panelen.

> [!NOTE] 
> Om du ser **Inaktivera stöd för replikering** nedtonad är replikeringsinställningarna redan inställda på servern som standard. Du kan hoppa över följande steg och gå till skapa en Läs replik. 

3. Välj **Aktivera stöd för replikering**. 

   ![Aktivera stöd för replikering](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Bekräfta att du vill aktivera stöd för replikering. Den här åtgärden startar om huvud servern. 

   ![Bekräfta aktivera stöd för replikering](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Du får två Azure Portal-meddelanden när åtgärden har slutförts. Det finns ett meddelande om att uppdatera Server parametern. Det finns ett annat meddelande för omstart av servern som följer omedelbart.

   ![Lyckade aviseringar – aktivera](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Uppdatera den Azure Portal sidan om du vill uppdatera verktygsfältet replikering. Nu kan du skapa Läs repliker för den här servern.

   ![Uppdaterat verktygsfält](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Att aktivera stöd för replikering är en engångs åtgärd per huvud server. En knapp för att **Inaktivera stöd för replikering** tillhandahålls för din bekvämlighet. Vi rekommenderar inte att du inaktiverar stöd för replikering, såvida du inte är säker på att du aldrig kommer att skapa en replik på den här huvud servern. Du kan inte inaktivera stöd för replikering medan huvud servern har befintliga repliker.


## <a name="create-a-read-replica"></a>Skapa en Läs replik
Följ dessa steg om du vill skapa en Läs replik:

1. Välj den befintliga Azure Database for PostgreSQL-server som ska användas som huvud server. 

2. Välj **replikering**under **Inställningar**på Server panelen.

3. Välj **Lägg till replik**.

   ![Lägg till en replik](./media/howto-read-replicas-portal/add-replica.png)

4. Ange ett namn på Läs repliken. 

    ![Namnge repliken](./media/howto-read-replicas-portal/name-replica.png)

5. Välj en plats för repliken. Standard platsen är samma som för huvud servern.

    ![Välja en plats](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

6. Bekräfta skapandet av repliken genom att klicka på **OK** .

En replik skapas med samma beräknings-och lagrings inställningar som huvud servern. När en replik har skapats kan flera inställningar ändras oberoende från huvud servern: beräknings generation, virtuella kärnor, lagring och säkerhets kopierings perioden. Pris nivån kan också ändras oberoende, förutom till eller från Basic-nivån.

> [!IMPORTANT]
> Innan en huvud server inställning uppdateras till ett nytt värde uppdaterar du replik inställningen till ett lika eller högre värde. Den här åtgärden hjälper repliken att hålla sig uppdaterad med alla ändringar som görs i huvud repliken.

När Läs repliken har skapats kan den visas från fönstret **replikering** :

![Visa den nya repliken i fönstret replikering](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikering mellan en huvud server och en Läs replik.

> [!IMPORTANT]
> När du har stoppat replikering till en huvud server och en Läs replik kan du inte göra det. Läs repliken blir en fristående server som stöder både läsning och skrivning. Den fristående servern kan inte göras till en replik igen.

Följ dessa steg om du vill stoppa replikeringen mellan en huvud server och en Läs replik från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for PostgreSQL servern.

2. På Server-menyn under **Inställningar**väljer du **replikering**.

3. Välj den replik Server för vilken du vill stoppa replikeringen.

   ![Välj repliken](./media/howto-read-replicas-portal/select-replica.png)
 
4. Välj **stoppa replikering**.

   ![Välj stoppa replikering](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Välj **OK** för att stoppa replikeringen.

   ![Bekräfta att replikeringen ska stoppas](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Ta bort en huvud server
Om du vill ta bort en huvud server använder du samma steg som för att ta bort en fristående Azure Database for PostgreSQL Server. 

> [!IMPORTANT]
> När du tar bort en huvud server stoppas replikeringen till alla Läs repliker. De skrivskyddade replikerna blir fristående servrar som nu stöder både läsning och skrivning.

Följ dessa steg om du vill ta bort en server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for PostgreSQL servern.

2. Öppna sidan **Översikt** för servern. Välj **Ta bort**.

   ![På sidan Server översikt väljer du om du vill ta bort huvud servern](./media/howto-read-replicas-portal/delete-server.png)
 
3. Ange namnet på huvud servern som ska tas bort. Bekräfta borttagningen av huvud servern genom att välja **ta bort** .

   ![Bekräfta att huvud servern ska tas bort](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Ta bort en replik
Du kan ta bort en Läs replik som liknar hur du tar bort en huvud server.

- I Azure Portal öppnar du sidan **Översikt** för Läs repliken. Välj **Ta bort**.

   ![På sidan replik översikt väljer du om du vill ta bort repliken](./media/howto-read-replicas-portal/delete-replica.png)
 
Du kan också ta bort Läs repliken från fönstret **replikering** genom att följa dessa steg:

1. I Azure Portal väljer du huvud Azure Database for PostgreSQL servern.

2. På Server-menyn under **Inställningar**väljer du **replikering**.

3. Välj den Läs replik som ska tas bort.

   ![Välj den replik som ska tas bort](./media/howto-read-replicas-portal/select-replica.png)
 
4. Välj **ta bort replik**.

   ![Välj Ta bort replik](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Ange namnet på repliken som ska tas bort. Bekräfta borttagning av repliken genom att välja **ta bort** .

   ![Bekräfta borttagning av te-replik](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Övervaka en replik
Det finns två mått för att övervaka Läs repliker.

### <a name="max-lag-across-replicas-metric"></a>Mått för högsta fördröjning i repliker
Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan huvud servern och den mest isolerings repliken. 

1.  I Azure Portal väljer du huvud Azure Database for PostgreSQLs servern.

2.  Välj **Mått**. I fönstret **mått** väljer du **Max fördröjning över repliker**.

    ![Övervaka maximal fördröjning mellan repliker](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  För din **agg regering**väljer du **Max**.


### <a name="replica-lag-metric"></a>Mått för replik fördröjning
Värdet för **replik fördröjningen** visar tiden sedan den senaste återspelade transaktionen på en replik. Om det inte finns några transaktioner i din Master, återspeglar måttet denna tids fördröjning.

1. I Azure Portal väljer du Azure Database for PostgreSQL Läs replik.

2. Välj **Mått**. I fönstret **mått** väljer du **replik fördröjning**.

   ![Övervaka replik fördröjningen](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. För din **agg regering**väljer du **Max**. 
 
## <a name="next-steps"></a>Nästa steg
* Läs mer om att [läsa repliker i Azure Database for PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar Läs repliker i Azure CLI och REST API](howto-read-replicas-cli.md).