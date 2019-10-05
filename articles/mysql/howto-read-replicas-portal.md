---
title: Skapa & hantera Läs repliker (Azure Portal) – Azure Database for MySQL
description: Lär dig hur du konfigurerar och hanterar Läs repliker i Azure Database for MySQL att använda Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/04/2019
ms.openlocfilehash: a90e9cccf8b59dabbee8415818c0e819ba1b26c3
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972877"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mysql-using-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for MySQL med hjälp av Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MySQL-tjänsten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

- En [Azure Database for MySQL-server](quickstart-create-mysql-server-database-using-azure-portal.md) som ska användas som huvudserver.

> [!IMPORTANT]
> Läs replica-funktionen är endast tillgänglig för Azure Database för MySQL-servrar i generell användning eller Minnesoptimerade prisnivåer. Kontrollera huvudservern är i något av dessa prisnivåer.

## <a name="create-a-read-replica"></a>Skapa en skrivskyddad replik

Du kan skapa en Läs replik server med följande steg:

1. Logga in på [Azure-portalen](https://portal.azure.com/).

2. Välj den befintliga Azure Database for MySQL-server som du vill använda som huvud server. Den här åtgärden öppnar den **översikt** sidan.

3. Välj **replikering** på menyn under **Inställningar**.

4. Välj **Lägg till replik**.

   ![Azure Database for MySQL-replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn på replik servern.

    ![Azure Database for MySQL-replik namn](./media/howto-read-replica-portal/replica-name.png)

6. Välj plats för replik servern. Standard platsen är samma som för huvud servern.

    ![Azure Database for MySQL-replik plats](./media/howto-read-replica-portal/replica-location.png)

   > [!NOTE]
   > Om du vill veta mer om vilka regioner du kan skapa en replik i går du till [artikeln Läs replik begrepp](concepts-read-replicas.md). 

7. Bekräfta skapandet av repliken genom att klicka på **OK** .

> [!NOTE]
> Läs repliker skapas med samma serverkonfiguration som huvudserver. Serverkonfigurationen repliken kan ändras när den har skapats. Du rekommenderas att repliken serverkonfigurationen bör hållas lika med eller större värden än huvudservern så repliken kan hålla jämna steg med huvudservern.

När replik servern har skapats kan den visas från bladet **replikering** .

   ![Repliker i Azure Database for MySQL lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replikserver

> [!IMPORTANT]
> Stoppa replikering till en server kan inte ångras. När replikering har upphört mellan huvud- och repliken och kan inte den ångras. Replikservern sedan blir en fristående server och stöder nu både läs- och skrivåtgärder. Den här servern kan inte göras i en replik igen.

Använd följande steg för att stoppa replikeringen mellan en huvud server och en replik Server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MySQL servern. 

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill stoppa replikering för.

   ![Azure Database for MySQL-stoppa replikering Välj server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **stoppa replikering**.

   ![Azure Database for MySQL-stoppa replikering](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   ![Bekräfta Azure Database for MySQL-stoppa replikering](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replikserver

Gör så här om du vill ta bort en Läs replik Server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MySQL servern.

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill ta bort.

   ![Azure Database for MySQL-ta bort replikering Välj server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **ta bort replik**

   ![Azure Database for MySQL-ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på repliken och klicka på **ta bort** för att bekräfta borttagningen av repliken.  

   ![Bekräfta Azure Database for MySQL-ta bort replik](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Ta bort en huvudserver

> [!IMPORTANT]
> Tar bort en huvudserver stoppar replikeringen till alla replikservrar och borttagningar huvudservern själva. Replikservrar bli fristående servrar som stöder nu både läs- och skrivåtgärder.

Gör så här om du vill ta bort en huvud server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MySQL servern.

2. Välj **ta bort**i **översikten**.

   ![Azure Database for MySQL-ta bort huvud](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på huvud servern och klicka på **ta bort** för att bekräfta att huvud servern tas bort.  

   ![Azure Database for MySQL-ta bort huvud](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure Portal](https://portal.azure.com/)väljer du den replik Azure Database for MySQLs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **mått**:

3. Välj **replikeringsfördröjning i sekunder** från List rutan med tillgängliga mått.

   ![Välj replikeringsfördröjning](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett tidsintervall på 30 minuter.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringens fördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [läsa repliker](concepts-read-replicas.md)