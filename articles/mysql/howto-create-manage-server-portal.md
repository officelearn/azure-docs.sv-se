---
title: Skapa och hantera Azure Database for MySQL server med Azure Portal
description: Den här artikeln beskriver hur du snabbt kan skapa en ny Azure Database for MySQL server och hantera servern med hjälp av Azure Portal.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: fdcb302d3a14b02ea86fb92c8dbf822ef3f42177
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142101"
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Skapa och hantera Azure Database for MySQL server med Azure Portal
I det här avsnittet beskrivs hur du snabbt kan skapa en ny Azure Database for MySQL-server. Den innehåller också information om hur du hanterar servern med hjälp av Azure Portal. Server hantering inkluderar visning av Server information och databaser, återställning av lösen ord, skalning av resurser och borttagning av servern.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure-portalen
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database for MySQL-server
Följ dessa steg om du vill skapa en Azure Database for MySQL-server med namnet "mydemoserver".

1. Klicka på knappen **skapa en resurs** som finns i det övre vänstra hörnet av Azure Portal.

2. På sidan ny väljer du **databaser**och på sidan databaser väljer du **Azure Database for MySQL**.

    > En Azure Database for MySQL-server skapas med en definierad uppsättning beräknings- [och lagrings](./concepts-pricing-tiers.md) resurser. Databasen skapas i en Azure-resurs grupp och i en Azure Database for MySQL-server.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Fyll i Azure Database for MySQL formuläret med hjälp av följande information:

    | **Formulärfält** | **Fältbeskrivning** |
    |----------------|-----------------------|
    | *Servernamn* | mydemoserver (Server namnet är globalt unikt) |
    | *Prenumeration* | min prenumeration (Välj i den nedrullningsbara menyn) |
    | *Resursgrupp* | myresourcegroup (skapa en ny resurs grupp eller Använd en befintlig) |
    | *Välj källa* | Tomt (skapa en tom MySQL-server) |
    | *Inloggning för serveradministratör* | myadmin (konfigurera namn på administratörskonto) |
    | *Lösenord* | Ange lösen ord för administratörs kontot |
    | *Bekräfta lösenord* | bekräfta lösenord för administratörskonto |
    | *Location* | Sydostasien (Välj mellan Nord Europa och västra USA) |
    | *Version* | 5,7 (Välj Azure Database for MySQL Server version) |

   ![create-new-server](./media/howto-create-manage-server-portal/form-field.png)

4. Klicka på **pris nivå** för att ange tjänst nivå och prestanda nivå för den nya servern. Välj fliken **generell användning** . *Gen 5*, *2 virtuella kärnor*, *5 GB*, och *7 dagar* är standardvärdena för **Compute-generering**, **Virtuell kärna** , **Lagring** och **Kvarhållningsperiod för säkerhetskopior**. Du kan lämna dessa skjutreglage som de är. Välj **Geografiskt redundant** bland **redundansalternativen för säkerhetskopiering** om du vill använda geo-redundant lagring för dina serversäkerhetskopior.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klicka på **Skapa** för att etablera servern. Etableringen tar några minuter.

    > Välj alternativet **Fäst på instrument panelen** för att tillåta enkel spårning av dina distributioner.

## <a name="update-an-azure-database-for-mysql-server"></a>Uppdatera en Azure Database for MySQL-Server
När den nya servern har etablerats har användaren flera alternativ för att konfigurera den befintliga servern, inklusive att återställa administratörs lösen ordet, ändra pris nivån och skala upp eller ned servern genom att ändra vCore eller lagring

### <a name="change-the-administrator-user-password"></a>Ändra lösen ordet för administratörs användaren
1. I Server **översikten**klickar du på **Återställ lösen ord** för att visa fönstret för lösen ords återställning.

   ![översikt](./media/howto-create-manage-server-portal/overview.png)

2. Ange ett nytt lösen ord och bekräfta lösen ordet i fönstret som det visas:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Spara det nya lösen ordet genom att klicka på **OK** .

### <a name="change-the-pricing-tier"></a>Ändra pris nivån
> [!NOTE]
> Skalning stöds endast från Generell användning till minnesoptimerade tjänst nivåer och vice versa. Observera att det inte finns stöd för att byta till och från den grundläggande pris nivån efter att servern har skapats i Azure Database for MySQL.
> 
1. Klicka på **pris nivå**, som finns under **Inställningar**.
2. Välj den **pris nivå** som du vill ändra till.

    ![ändra pris nivå](./media/howto-create-manage-server-portal/change-pricing-tier.png)

4. Spara ändringarna genom att klicka på **OK**. 

### <a name="scale-vcores-updown"></a>Skala virtuella kärnor uppåt/nedåt

1. Klicka på **pris nivå**, som finns under **Inställningar**.

2. Ändra **vCore** -inställningen genom att flytta skjutreglaget till önskat värde.

    ![skala – beräkna](./media/howto-create-manage-server-portal/scale-compute.png)

3. Spara ändringarna genom att klicka på **OK**.

### <a name="scale-storage-up"></a>Skala upp lagring

1. Klicka på **pris nivå**, som finns under **Inställningar**.

2. Ändra **lagrings** inställningen genom att flytta skjutreglaget till önskat värde.

    ![skala – lagring](./media/howto-create-manage-server-portal/scale-storage.png)

3. Spara ändringarna genom att klicka på **OK**.

## <a name="delete-an-azure-database-for-mysql-server"></a>Ta bort en Azure Database for MySQL-Server

1. Från Server **översikten**klickar du på knappen **ta bort** för att öppna bekräftelse meddelandet.

    ![radera](./media/howto-create-manage-server-portal/delete.png)

2. Skriv namnet på servern i text rutan för dubbel bekräftelse.

    ![bekräfta-ta bort](./media/howto-create-manage-server-portal/confirm.png)

3. Bekräfta borttagningen av servern genom att klicka på knappen **ta bort** . Vänta tills popup-servern "har tagits bort MySQL-servern" visas i meddelande fältet.

## <a name="list-the-azure-database-for-mysql-databases"></a>Visa en lista över Azure Database for MySQL databaser
Från Server **översikten**bläddrar du nedåt tills du ser panelen databas längst ned. Alla databaser på servern visas i tabellen.

   ![Visa databaser](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Visa information om en Azure Database for MySQL-Server
Klicka på **Egenskaper**, som finns under **Inställningar** , om du vill visa detaljerad information om servern.

![properties](./media/howto-create-manage-server-portal/properties.png)

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure Database for MySQL server med Azure Portal](./quickstart-create-mysql-server-database-using-azure-portal.md)