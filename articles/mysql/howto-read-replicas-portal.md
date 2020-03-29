---
title: Hantera lästa repliker - Azure portal - Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar läsande repliker i Azure Database för MySQL med Hjälp av Azure-portalen.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: b7226e5ae8c468339e02dbe87e279266e4609da8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063483"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Så här skapar och hanterar du läsande repliker i Azure Database for MySQL med Azure-portalen

I den här artikeln får du lära dig hur du skapar och hanterar läsande repliker i Azure Database for MySQL-tjänsten med hjälp av Azure-portalen.

## <a name="prerequisites"></a>Krav

- En [Azure-databas för MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som huvudserver.

> [!IMPORTANT]
> Funktionen läsreplik är endast tillgänglig för Azure Database for MySQL-servrar på prisnivåerna Allmänt syfte eller Minnesoptimerade. Kontrollera att huvudservern finns på en av dessa prisnivåer.

## <a name="create-a-read-replica"></a>Skapa en läsreplik

En läsreplikserver kan skapas med hjälp av följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj den befintliga Azure-databasen för MySQL-server som du vill använda som huvudhanterare. Den här åtgärden öppnar sidan **Översikt.**

3. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

4. Välj **Lägg till replik**.

   ![Azure-databas för MySQL - Replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn på replikservern.

    ![Azure-databas för MySQL - Repliknamn](./media/howto-read-replica-portal/replica-name.png)

6. Välj plats för replikservern. Standardplatsen är samma som huvudserverns.

    ![Azure-databas för MySQL - Replikplats](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Mer information om vilka regioner du kan skapa en replik i finns i [artikeln läsreplikbegrepp](concepts-read-replicas.md). 

7. Välj **OK** för att bekräfta att repliken skapas.

> [!NOTE]
> Läsrepliker skapas med samma serverkonfiguration som huvudprogrammet. Replikserverkonfigurationen kan ändras när den har skapats. Vi rekommenderar att replikserverns konfiguration ska hållas på lika med eller större värden än huvudprogrammet för att säkerställa att repliken kan hålla jämna steg med huvudprogrammet.

När replikservern har skapats kan den visas från **bladet Replikering.**

   ![Azure-databas för MySQL - Lista repliker](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Att stoppa replikering till en server är oåterkalleligt. När replikeringen har stoppats mellan en huvudsida och replik kan den inte ångras. Replikservern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Så här stoppar du replikeringen mellan en huvudhanterare och en replikserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MySQL-server i Azure-portalen. 

2. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

3. Välj den replikserver som du vill stoppa replikeringen för.

   ![Azure Database för MySQL - Välj server för stoppa replikering](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **Stoppa replikering**.

   ![Azure-databas för MySQL - Stoppa replikering](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   ![Azure Database för MySQL - Stoppa replikering bekräfta](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replikserver

Så här tar du bort en läsreplikserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MySQL-server i Azure-portalen.

2. Välj **Replikering** på menyn under **INSTÄLLNINGAR**.

3. Välj den replikserver som du vill ta bort.

   ![Azure Database for MySQL – Ta bort replikväljarserver](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **Ta bort replik**

   ![Azure-databas för MySQL – Ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på repliken och klicka på **Ta bort** för att bekräfta borttagningen av repliken.  

   ![Azure Database for MySQL - Delete replica confirm Azure Database for MySQL - Delete replica confirm Azure Database for MySQL - Delete replica confirm Azure Database](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Så här tar du bort en huvudserver från Azure-portalen:

1. Välj din huvud-Azure-databas för MySQL-server i Azure-portalen.

2. Välj **Overview** **Ta bort**.

   ![Azure-databas för MySQL - Ta bort huvudsida](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på huvudservern och klicka på **Ta bort** för att bekräfta borttagningen av huvudservern.  

   ![Azure-databas för MySQL - Ta bort huvudsida](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure-portalen](https://portal.azure.com/)väljer du den replik Azure-databas för MySQL-server som du vill övervaka.

2. Under avsnittet **Övervakning** i sidofältet väljer du **Mått:**

3. Välj **Fördröjning för replikering i sekunder** från listrutan över tillgängliga mått.

   ![Välj fördröjning för replikering](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett 30 minuters tidsintervall.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringsfördröjningen för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsrepliker](concepts-read-replicas.md)