---
title: Hantera lästa repliker - Azure-portal - Azure Database för PostgreSQL - Single Server
description: Lär dig hur du hanterar läsa repliker Azure Database för PostgreSQL - Single Server från Azure-portalen.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/24/2020
ms.openlocfilehash: dd79618b8d9f016c92166edb9ecdb0bfb113947e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76768948"
---
# <a name="create-and-manage-read-replicas-in-azure-database-for-postgresql---single-server-from-the-azure-portal"></a>Skapa och hantera läsande repliker i Azure Database for PostgreSQL - Single Server från Azure-portalen

I den här artikeln får du lära dig hur du skapar och hanterar läsande repliker i Azure Database för PostgreSQL från Azure-portalen. Mer information om läsrepliker finns i [översikten](concepts-read-replicas.md).


## <a name="prerequisites"></a>Krav
En [Azure-databas för PostgreSQL-server](quickstart-create-server-database-portal.md) som huvudserver.

## <a name="prepare-the-master-server"></a>Förbereda huvudservern
Dessa steg måste användas för att förbereda en huvudserver på nivåerna Allmänt ändamål eller Minnesoptimerade. Huvudservern förbereds för replikering genom att ange parametern azure.replication_support. När replikeringsparametern ändras krävs en omstart av servern för att ändringen ska börja gälla. I Azure-portalen kapslas dessa två steg in av en enda knapp, **Aktivera replikeringsstöd**.

1. I Azure-portalen väljer du den befintliga Azure-databasen för PostgreSQL-server som ska användas som huvudhanterare.

2. Välj **Replikering**under **INSTÄLLNINGAR**på serverns sidofält .

> [!NOTE] 
> Om du ser **Inaktivera replikeringsstöd** nedtonat är replikeringsinställningarna redan inställda på servern som standard. Du kan hoppa över följande steg och gå för att skapa en läsreplik. 

3. Välj **Aktivera replikeringsstöd**. 

   ![Aktivera replikeringsstöd](./media/howto-read-replicas-portal/enable-replication-support.png)

4. Bekräfta att du vill aktivera replikeringsstöd. Den här åtgärden startar om huvudservern. 

   ![Bekräfta aktivera replikeringsstöd](./media/howto-read-replicas-portal/confirm-enable-replication.png)
   
5. Du får två Azure-portalmeddelanden när åtgärden är klar. Det finns ett meddelande om uppdatering av serverparametern. Det finns ett annat meddelande för servern omstart som följer omedelbart.

   ![Meddelanden om lyckade meddelanden – aktivera](./media/howto-read-replicas-portal/success-notifications-enable.png)

6. Uppdatera Azure-portalsidan för att uppdatera verktygsfältet Replikering. Du kan nu skapa läsrepliker för den här servern.

   ![Verktygsfältet Uppdaterad](./media/howto-read-replicas-portal/updated-toolbar.png)
   
Aktivera replikeringsstöd är en engångsåtgärd per huvudserver. En knappen **Inaktivera replikeringsstöd** finns för din bekvämlighet. Vi rekommenderar inte att du inaktiverar replikeringsstöd, såvida du inte är säker på att du aldrig kommer att skapa en replik på den här huvudservern. Du kan inte inaktivera replikeringsstöd medan huvudservern har befintliga repliker.


## <a name="create-a-read-replica"></a>Skapa en läsreplik
Så här skapar du en läsreplik:

1. Välj den befintliga Azure-databasen för PostgreSQL-server som ska användas som huvudserver. 

2. Välj **Replikering**under **INSTÄLLNINGAR**på serverns sidofält .

3. Välj **Lägg till replik**.

   ![Lägga till en replik](./media/howto-read-replicas-portal/add-replica.png)

4. Ange ett namn på läsrepliken. 

    ![Namnge repliken](./media/howto-read-replicas-portal/name-replica.png)

5. Välj en plats för repliken. Standardplatsen är samma som huvudserverns.

    ![Välja en plats](./media/howto-read-replicas-portal/location-replica.png)

   > [!NOTE]
   > Mer information om vilka regioner du kan skapa en replik i finns i [artikeln läsreplikbegrepp](concepts-read-replicas.md). 

6. Välj **OK** för att bekräfta skapandet av repliken.

En replik skapas med samma beräknings- och lagringsinställningar som huvudsã¤nde. När en replik har skapats kan flera inställningar ändras oberoende av huvudservern: beräkningsgenerering, virtuella kärnor, lagring och säkerhetskopieringsperiod. Prisnivån kan också ändras oberoende av dem, förutom till eller från basic-nivån.

> [!IMPORTANT]
> Innan en huvudserverinställning uppdateras till ett nytt värde uppdaterar du replikinställningen till ett lika stort eller större värde. Den här åtgärden hjälper repliken att hålla jämna steg med alla ändringar som gjorts i bakgrunden.

När läsrepliken har skapats kan den visas från **replikeringsfönstret:**

![Visa den nya repliken i replikeringsfönstret](./media/howto-read-replicas-portal/list-replica.png)
 

## <a name="stop-replication"></a>Stoppa replikering
Du kan stoppa replikeringen mellan en huvudserver och en läsreplik.

> [!IMPORTANT]
> När du har stoppat replikeringen till en huvudserver och en läsreplik kan den inte ångras. Läsrepliken blir en fristående server som stöder både läsningar och skrivningar. Den fristående servern kan inte göras till en replik igen.

Så här stoppar du replikeringen mellan en huvudserver och en läsreplik från Azure-portalen:

1. I Azure-portalen väljer du din huvud-Azure-databas för PostgreSQL-server.

2. Välj **Replikering**under **INSTÄLLNINGAR**på servermenyn .

3. Välj den replikserver som replikservern ska stoppas för.

   ![Markera repliken](./media/howto-read-replicas-portal/select-replica.png)
 
4. Välj **Stoppa replikering**.

   ![Välj stoppreplikering](./media/howto-read-replicas-portal/select-stop-replication.png)
 
5. Välj **OK** för att stoppa replikeringen.

   ![Bekräfta att replikeringen stoppas](./media/howto-read-replicas-portal/confirm-stop-replication.png)
 

## <a name="delete-a-master-server"></a>Ta bort en huvudserver
Om du vill ta bort en huvudserver använder du samma steg som att ta bort en fristående Azure-databas för PostgreSQL-server. 

> [!IMPORTANT]
> När du tar bort en huvudserver stoppas replikeringen till alla lästa repliker. Läsrepliker blir fristående servrar som nu stöder både läsningar och skrivningar.

Så här tar du bort en server från Azure-portalen:

1. I Azure-portalen väljer du din huvud-Azure-databas för PostgreSQL-server.

2. Öppna sidan **Översikt** för servern. Välj **Ta bort**.

   ![På sidan Översikt över servern väljer du att ta bort huvudservern](./media/howto-read-replicas-portal/delete-server.png)
 
3. Ange namnet på huvudservern som ska tas bort. Välj **Ta bort** för att bekräfta borttagning av huvudservern.

   ![Bekräfta att huvudservern ska tas bort](./media/howto-read-replicas-portal/confirm-delete.png)
 

## <a name="delete-a-replica"></a>Ta bort en replik
Du kan ta bort en läsreplik som liknar hur du tar bort en huvudserver.

- Öppna sidan **Översikt** för den lästa repliken i Azure-portalen. Välj **Ta bort**.

   ![På sidan Repliköversikt väljer du att ta bort repliken](./media/howto-read-replicas-portal/delete-replica.png)
 
Du kan också ta bort läsrepliken från **replikeringsfönstret** genom att följa dessa steg:

1. I Azure-portalen väljer du din huvud-Azure-databas för PostgreSQL-server.

2. Välj **Replikering**under **INSTÄLLNINGAR**på servermenyn .

3. Markera den läsreplik som ska tas bort.

   ![Markera den replik som ska tas bort](./media/howto-read-replicas-portal/select-replica.png)
 
4. Välj **Ta bort replik**.

   ![Välj borttagningsreplik](./media/howto-read-replicas-portal/select-delete-replica.png)
 
5. Ange namnet på den replik som ska tas bort. Välj **Ta bort** för att bekräfta borttagning av repliken.

   ![Bekräfta att te-repliken tas bort](./media/howto-read-replicas-portal/confirm-delete-replica.png)
 

## <a name="monitor-a-replica"></a>Övervaka en replik
Två mått är tillgängliga för att övervaka läsrepliker.

### <a name="max-lag-across-replicas-metric"></a>Max fördröjning över repliker mått
Måttet **Max fördröjning över repliker** visar fördröjningen i byte mellan huvudservern och den mest eftersläpande repliken. 

1.  I Azure-portalen väljer du huvudservern för Azure-databasen för PostgreSQL-servern.

2.  Välj **Mått**. Välj **Max fördröjning över repliker i**fönstret **Mått** .

    ![Övervaka max fördröjningen över repliker](./media/howto-read-replicas-portal/select-max-lag.png)
 
3.  För **aggregeringen**väljer du **Max**.


### <a name="replica-lag-metric"></a>Mätvärde för replikfördröjning
**Repliklagningsmåttet** visar tiden sedan den senast återspelade transaktionen på en replik. Om det inte sker några transaktioner på din huvudsida återspeglar måttet den här tidsfördröjningen.

1. I Azure-portalen väljer du Azure-databasen för PostgreSQL-läsreplik.

2. Välj **Mått**. Välj **Repliklagning i**fönstret **Mått** .

   ![Övervaka replikfördröjningen](./media/howto-read-replicas-portal/select-replica-lag.png)
 
3. För **aggregeringen**väljer du **Max**. 
 
## <a name="next-steps"></a>Nästa steg
* Läs mer om [att läsa repliker i Azure Database för PostgreSQL](concepts-read-replicas.md).
* Lär dig hur du [skapar och hanterar läsande repliker i Azure CLI och REST API](howto-read-replicas-cli.md).