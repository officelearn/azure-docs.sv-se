---
title: Hantera Läs repliker – Azure Portal-Azure Database for MariaDB
description: Den här artikeln beskriver hur du konfigurerar och hanterar Läs repliker i Azure Database for MariaDB med hjälp av portalen
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 4/21/2020
ms.openlocfilehash: 20d8e46d6fa6b031c809d629a6af41e8e682bcef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "82025092"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-portal"></a>Skapa och hantera Läs repliker i Azure Database for MariaDB med hjälp av Azure Portal

I den här artikeln får du lära dig hur du skapar och hanterar Läs repliker i Azure Database for MariaDB-tjänsten med hjälp av Azure Portal.

## <a name="prerequisites"></a>Krav

- En [Azure Database for MariaDB-Server](quickstart-create-mariadb-server-database-using-azure-portal.md) som ska användas som huvud server.

> [!IMPORTANT]
> Funktionen Läs replik är bara tillgänglig för Azure Database for MariaDB servrar i Generell användning eller Minnesoptimerade pris nivåer. Se till att huvud servern är i någon av dessa pris nivåer.

## <a name="create-a-read-replica"></a>Skapa en Läs replik

Du kan skapa en Läs replik server med följande steg:

1. Logga in på [Azure Portal](https://portal.azure.com/).

2. Välj den befintliga Azure Database for MariaDB-server som du vill använda som huvud server. Den här åtgärden öppnar **översikts** sidan.

3. Välj **replikering** på menyn under **Inställningar**.

4. Välj **Lägg till replik**.

   ![Azure Database for MariaDB-replikering](./media/howto-read-replica-portal/add-replica.png)

5. Ange ett namn på replik servern.

    ![Azure Database for MariaDB-replik namn](./media/howto-read-replica-portal/replica-name.png)

6. Välj plats för replik servern. Standard platsen är samma som för huvud servern.

    ![Azure Database for MariaDB-replik plats](./media/howto-read-replica-portal/replica-location.png)

7. Bekräfta skapandet av repliken genom att klicka på **OK** .

> [!NOTE]
> Läs repliker skapas med samma server konfiguration som huvud servern. Replik Server konfigurationen kan ändras efter att den har skapats. Vi rekommenderar att replik serverns konfiguration måste vara lika med eller större än huvud värden, för att repliken ska kunna fortsätta med huvud servern.

När replik servern har skapats kan den visas från bladet **replikering** .

   ![Repliker i Azure Database for MariaDB lista](./media/howto-read-replica-portal/list-replica.png)

## <a name="stop-replication-to-a-replica-server"></a>Stoppa replikering till en replik Server

> [!IMPORTANT]
> Att stoppa replikeringen till en server går inte att ångra. När replikeringen har stoppats mellan en huvud server och en replik kan den inte återställas. Replik servern blir sedan en fristående server och stöder nu både läsning och skrivning. Den här servern kan inte göras till en replik igen.

Använd följande steg för att stoppa replikeringen mellan en huvud server och en replik Server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MariaDB servern. 

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill stoppa replikering för.

   ![Azure Database for MariaDB-stoppa replikering Välj server](./media/howto-read-replica-portal/stop-replication-select.png)

4. Välj **stoppa replikering**.

   ![Azure Database for MariaDB-stoppa replikering](./media/howto-read-replica-portal/stop-replication.png)

5. Bekräfta att du vill stoppa replikeringen genom att klicka på **OK**.

   ![Bekräfta Azure Database for MariaDB-stoppa replikering](./media/howto-read-replica-portal/stop-replication-confirm.png)

## <a name="delete-a-replica-server"></a>Ta bort en replik Server

Gör så här om du vill ta bort en Läs replik Server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MariaDB servern.

2. Välj **replikering** på menyn under **Inställningar**.

3. Välj den replik server som du vill ta bort.

   ![Azure Database for MariaDB-ta bort replikering Välj server](./media/howto-read-replica-portal/delete-replica-select.png)

4. Välj **ta bort replik**

   ![Azure Database for MariaDB-ta bort replik](./media/howto-read-replica-portal/delete-replica.png)

5. Skriv namnet på repliken och klicka på **ta bort** för att bekräfta borttagningen av repliken.  

   ![Bekräfta Azure Database for MariaDB-ta bort replik](./media/howto-read-replica-portal/delete-replica-confirm.png)

## <a name="delete-a-master-server"></a>Ta bort en huvud server

> [!IMPORTANT]
> Om du tar bort en huvudserver stoppas replikeringen till alla replikservrar och själva huvudservern tas bort. Replikservrar blir fristående servrar som nu stöder både läsningar och skrivningar.

Gör så här om du vill ta bort en huvud server från Azure Portal:

1. I Azure Portal väljer du huvud Azure Database for MariaDB servern.

2. Välj **ta bort**i **översikten**.

   ![Azure Database for MariaDB-ta bort huvud](./media/howto-read-replica-portal/delete-master-overview.png)

3. Skriv namnet på huvud servern och klicka på **ta bort** för att bekräfta att huvud servern tas bort.  

   ![Azure Database for MariaDB-ta bort huvud](./media/howto-read-replica-portal/delete-master-confirm.png)

## <a name="monitor-replication"></a>Övervaka replikering

1. I [Azure Portal](https://portal.azure.com/)väljer du den replik Azure Database for MariaDBs server som du vill övervaka.

2. Under avsnittet **övervakning** på sid panelen väljer du **mått**:

3. Välj **replikeringsfördröjning i sekunder** från List rutan med tillgängliga mått.

   ![Välj replikeringsfördröjning](./media/howto-read-replica-portal/monitor-select-replication-lag.png)

4. Välj det tidsintervall som du vill visa. Bilden nedan väljer ett tidsintervall på 30 minuter.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range.png)

5. Visa replikeringens fördröjning för det valda tidsintervallet. Bilden nedan visar de senaste 30 minuterna för en stor arbets belastning.

   ![Välj tidsintervall](./media/howto-read-replica-portal/monitor-replication-lag-time-range-thirty-mins.png)

## <a name="next-steps"></a>Nästa steg

- Läs mer om [Läs repliker](concepts-read-replicas.md)