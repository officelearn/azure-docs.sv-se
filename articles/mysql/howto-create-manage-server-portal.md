---
title: "Skapa och hantera Azure-databas för MySQL-servern med hjälp av Azure-portalen"
description: "Den här artikeln beskriver hur du kan snabbt skapa en ny Azure-databas för MySQL-server och hantera servern med hjälp av Azure-portalen."
services: mysql
author: ajlam
ms.author: nolanwu
editor: jasonwhowell
manager: kfile
ms.service: mysql-database
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 0e274c0ada3de5e9000ae41516e5b9b67ef1490b
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/28/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Skapa och hantera Azure-databas för MySQL-servern med hjälp av Azure-portalen
Det här avsnittet beskrivs hur du snabbt kan skapa en ny Azure-databas för MySQL-servern. Det innehåller även information om hur du hanterar servern med hjälp av Azure portal. Server management innehåller visa serverinformation och databaser, återställer lösenordet, skalning resurser och ta bort servern.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Följ dessa steg om du vill skapa en Azure-databas för MySQL-server med namnet ”mydemoserver”.

1. Klicka på den **skapar du en resurs** knapp som finns i det övre vänstra hörnet i Azure-portalen.

2. På den nya sidan Välj **databaser**, och databaser på sidan Välj **Azure-databas för MySQL**.

    > En Azure-databas för MySQL-server har skapats med en definierad uppsättning [beräkning och lagring](./concepts-pricing-tiers.md) resurser. Databasen har skapats i en Azure-resursgrupp och i Azure-databasen för MySQL-servern.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Fylla i Azure-databasen för MySQL formulär med hjälp av följande information:

    | **Formulärfält** | **Fältbeskrivning** |
    |----------------|-----------------------|
    | *Servernamn* | mydemoserver (servernamnet är globalt unik) |
    | *Prenumeration* | mysubscription (Välj på menyn) |
    | *Resursgrupp* | myresourcegroup (skapa en ny resursgrupp eller använda en befintlig) |
    | *Välj källa* | Tomt (skapa en tom MySQL-server) |
    | *Inloggning för serveradministratör* | myadmin (konfigurera namn på administratörskonto) |
    | *Lösenord* | Ange administratörslösenordet för kontot |
    | *Bekräfta lösenord* | bekräfta lösenord för administratörskonto |
    | *Plats* | Sydostasien (Välj mellan Norra Europa och västra USA) |
    | *Version* | 5.7 (Välj Azure-databas för MySQL server-version) |

4. Klicka på **prisnivå** att ange tjänstenivå för tjänstnivå och prestandanivå för den nya servern. Välj den **generella** fliken. *Gen 4*, *2 vCores*, *5 GB*, och *7 dagar* standardvärdena för **Compute Generation**, **vCore** , **Lagring**, och **säkerhetskopiera kvarhållningsperiod**. Du kan lämna dessa skjutreglage som är. Din server säkerhetskopieringarna geo-redundant lagring Välj **geografiskt Redundant** från den **redundansalternativ**.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klicka på **Skapa** för att etablera servern. Etableringen tar några minuter.

    > Välj den **fäst på instrumentpanelen** alternativet för att tillåta enkel spårning av dina distributioner.

## <a name="update-an-azure-database-for-mysql-server"></a>Uppdatera en Azure-databas för MySQL-server
När den nya servern har etablerats har användaren flera alternativ för att konfigurera befintliga servern, inklusive att återställa administratörslösenordet och skala upp eller ned servern genom att ändra vCore eller lagring.

### <a name="change-the-administrator-user-password"></a>Ändra administratörslösenordet för användaren
1. Från servern **översikt**, klickar du på **Återställ lösenord** att visa fönster för återställning av lösenord.

   ![översikt](./media/howto-create-manage-server-portal/overview.png)

2. Ange ett nytt lösenord och bekräfta lösenordet i fönstret som visas:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Klicka på **OK** att spara det nya lösenordet.

### <a name="scale-vcores-updown"></a>Skala vCores upp/ned

1. Klicka på **prisnivå**, som finns under **inställningar**.

2. Ändra den **vCore** inställningen genom att flytta skjutreglaget till din önskade värdet.

    ![scale-compute](./media/howto-create-manage-server-portal/scale-compute.png)

3. Spara ändringarna genom att klicka på **OK**.

### <a name="scale-storage-up"></a>Lagring för skala upp

1. Klicka på **prisnivå**, som finns under **inställningar**.

2. Ändra den **lagring** inställningen genom att flytta skjutreglaget till din önskade värdet.

    ![skala lagring](./media/howto-create-manage-server-portal/scale-storage.png)

3. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-an-azure-database-for-mysql-server"></a>Ta bort en Azure-databas för MySQL-server

1. Från servern **översikt**, klicka på den **ta bort** för att öppna delete bekräftelse.

    ![radera](./media/howto-create-manage-server-portal/delete.png)

2. Skriv namnet på servern i textrutan för dubbla bekräftelse.

    ![confirm-delete](./media/howto-create-manage-server-portal/confirm.png)

3. Klicka på den **ta bort** för att bekräfta ta bort servern. Vänta tills ”har tagits bort MySQL-server” pop upp till visas i meddelandefältet.

## <a name="list-the-azure-database-for-mysql-databases"></a>Visa en lista med Azure-databas för MySQL-databaser
Från servern **översikt**, bläddra nedåt tills du ser databasen panelen längst ned. Alla databaser på servern visas i tabellen.

   ![Visa-databaser](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Visa detaljer för en Azure-databas för MySQL-server
Klicka på **egenskaper**, som finns under **inställningar** att visa detaljerad information om servern.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure-databas för MySQL-server med hjälp av Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)