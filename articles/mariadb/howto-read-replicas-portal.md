---
title: Hantera lästa repliker - Azure portal - Azure Database för MariaDB
description: I den här artikeln beskrivs hur du konfigurerar och hanterar läsande repliker i Azure Database för MariaDB med hjälp av portalen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 75089113827229663afed75b8364ab5212d67323
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/22/2020
ms.locfileid: "82025092"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Så här skapar och hanterar du läsande repliker i Azure Database för MariaDB med Azure-portalen

I den här artikeln får du lära dig hur du skapar och hanterar läsande repliker i Azure Database for MariaDB-tjänsten med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Krav

- En [Azure-databas för MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) som ska användas som huvudserver.

> [!IMPORTANT]
> Funktionen läsreplik är endast tillgänglig för Azure Database för MariaDB-servrar på prisnivåerna Allmänt ändamål eller Minne Optimerad. Kontrollera att huvudservern finns på en av dessa prisnivåer.

## <a name="create-a-read-replica"></a>Skapa en läsreplik

En läsreplikserver kan skapas med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj den befintliga Azure-databasen för MariaDB-servern som du vill använda som huvudhanterare. Den här åtgärden öppnar sidan **Översikt.**

3. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

4. Välj **Lägg till replik**.

   ![Azure-databas för MariaDB - Replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn på replikservern.

    ![Azure-databas för MariaDB - Repliknamn](./media/howto-read-replica-portal/replica-name.png)

6. Välj plats för replikservern. Standardplatsen är samma som huvudserverns.

    ![Azure-databas för MariaDB - Replikplats](./media/howto-read-replica-portal/replica-location.png)

7. Välj **OK** för att bekräfta att repliken skapas.

> [!NOTE]
> Läsrepliker skapas med samma serverkonfiguration som huvudprogrammet. Replikserverkonfigurationen kan ändras när den har skapats. Vi rekommenderar att replikserverns konfiguration ska hållas på lika med eller större värden än huvudprogrammet för att säkerställa att repliken kan hålla jämna steg med huvudprogrammet.

När replikservern har skapats kan den visas från **bladet Replikering.**

   ![Azure Database för MariaDB - Lista repliker](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Att stoppa replikering till en server är oåterkalleligt. När replikeringen har stoppats mellan en huvudsida och replik kan den inte ångras. Replikservern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Så här stoppar du replikeringen mellan en huvudhanterare och en replikserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MariaDB-server i Azure-portalen. 

2. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

3. Välj den replikserver som du vill stoppa replikeringen för.

   ![Azure Database för MariaDB - Välj server för stoppa replikering](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **Stoppa replikering**.

   ![Azure-databas för MariaDB - Stoppa replikering](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   ![Azure-databas för MariaDB - Stoppa replikeringsbes bekräfta](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replikserver

Så här tar du bort en läsreplikserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MariaDB-server i Azure-portalen.

2. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

3. Välj den replikserver som du vill ta bort.

   ![Azure Database för MariaDB - Ta bort replikväljarserver](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **Ta bort replik**

   ![Azure-databas för MariaDB - Ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på repliken och klicka på **Ta bort** för att bekräfta borttagningen av repliken.  

   ![Azure Database för MariaDB - Ta bort replik bekräfta](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Så här tar du bort en huvudserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MariaDB-server i Azure-portalen.

2. Välj **Overview** **Ta bort**.

   ![Azure Database för MariaDB - Ta bort huvudsida](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på huvudservern och klicka på **Ta bort** för att bekräfta borttagningen av huvudservern.  

   ![Azure Database för MariaDB - Ta bort huvudsida](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure-portalen](https://portal.azure.com/)väljer du den replik Azure-databas för MariaDB-server som du vill övervaka.

2. Under avsnittet **Övervakning** i sidofältet väljer du **Mått:**

3. Välj **Fördröjning för replikering i sekunder** från listrutan över tillgängliga mått.

   ![Välj fördröjning för replikering](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett 30 minuters tidsintervall.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringsfördröjningen för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna för en stor arbetsbelastning.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsrepliker](concepts-read-replicas.md)