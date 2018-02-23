---
title: "Skapa och hantera Azure-databas för MySQL-servern med hjälp av Azure portal | Microsoft Docs"
description: "Den här artikeln beskriver hur du kan snabbt skapa en ny Azure-databas för MySQL-server och hantera servern med hjälp av Azure-portalen."
services: mysql
author: v-chenyh
ms.author: nolanwu
editor: jasonwhowell
manager: jhubbard
ms.service: mysql-database
ms.topic: article
ms.date: 09/15/2017
ms.openlocfilehash: b73fe2214a165d7c02c0a58551d8b84bee39f919
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/21/2018
---
# <a name="create-and-manage-azure-database-for-mysql-server-using-azure-portal"></a>Skapa och hantera Azure-databas för MySQL-servern med hjälp av Azure-portalen
Det här avsnittet beskrivs hur du snabbt kan skapa en ny Azure-databas för MySQL-servern. Det innehåller även information om hur du hanterar servern med hjälp av Azure portal. Server management innehåller visa serverinformation och databaser att återställa lösenordet och ta bort servern.

## <a name="log-in-to-the-azure-portal"></a>Logga in på Azure Portal
Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-an-azure-database-for-mysql-server"></a>Skapa en Azure Database för MySQL-server
Följ dessa steg om du vill skapa en Azure-databas för MySQL-server med namnet ”mysqlserver4demo”.

1. Klicka på den **skapar du en resurs** knapp som finns i det övre vänstra hörnet i Azure-portalen.

2. På den nya sidan Välj **databaser**, och databaser på sidan Välj **Azure-databas för MySQL**.

    > En Azure-databas för MySQL-server har skapats med en definierad uppsättning [beräkning och lagring](./concepts-compute-unit-and-storage.md) resurser. Databasen har skapats i en Azure-resursgrupp och i Azure-databasen för MySQL-servern.

   ![create-new-server](./media/howto-create-manage-server-portal/create-new-server.png)

3. Fylla i Azure-databasen för MySQL formulär med hjälp av följande information:

    | **Formulärfält** | **Fältbeskrivning** |
    |----------------|-----------------------|
    | *Servernamn* | Azure-mysql (servernamnet är globalt unik) |
    | *Prenumeration* | MySQLaaS (Välj på menyn) |
    | *Resursgrupp* | MyResource (skapa en ny resursgrupp eller använda en befintlig) |
    | *Inloggning för serveradministratör* | myadmin (konfigurera namn på administratörskonto) |
    | *Lösenord* | kontolösenord för installationsprogrammet admin |
    | *Bekräfta lösenord* | bekräfta lösenord för administratörskonto |
    | *Plats* | Norra Europa (Välj mellan Norra Europa och västra USA) |
    | *Version* | 5.6 (Välj Azure-databas för MySQL server-version) |

4. Klicka på **prisnivå** att ange tjänstenivå för tjänstnivå och prestandanivå för den nya servern. Compute-enhet kan konfigureras mellan 50 och 100 i grundläggande nivån mellan 100 och 200 i standardnivån, och lagringsutrymme kan läggas till baserat på hur ingår. Den här guiden Ta väljer vi 50 beräknings-enhet och 50 GB. Klicka på **OK** att spara ditt val.

   ![create-server-pricing-tier](./media/howto-create-manage-server-portal/create-server-pricing-tier.png)

5. Klicka på **Skapa** för att etablera servern. Etableringen tar några minuter.

    > Välj den **fäst på instrumentpanelen** alternativet för att tillåta enkel spårning av dina distributioner.
    > [!NOTE]
    > Även om upp till 1 000 GB i grundläggande nivån och 10 000 GB standardnivån stöds för lagring, för Public Preview är maximalt lagringsutrymme tillfälligt begränsat till 1 000 GB.</Include>

## <a name="update-an-azure-database-for-mysql-server"></a>Uppdatera en Azure-databas för MySQL-server
När nya servern är etablerad användaren har två alternativ för att redigera en befintlig server: återställa administratörslösenord eller skala upp eller ned servern genom att ändra beräknings-enheter.

### <a name="change-the-administrator-user-password"></a>Ändra administratörslösenordet för användaren
1. På servern **översikt** bladet, klickar du på **Återställ lösenord** att fylla i ett lösenord indata och bekräftelsen fönster.

2. Ange nya lösenord och bekräfta lösenordet i fönstret som visas:

   ![reset-password](./media/howto-create-manage-server-portal/reset-password.png)

3. Klicka på **OK** att spara det nya lösenordet.

### <a name="scale-updown-by-changing-compute-units"></a>Skala upp eller ned genom att ändra Compute-enheter

1. I bladet server under **inställningar**, klickar du på **prisnivå** att öppna bladet nivå priser för Azure-databas för MySQL-servern.

2. Följ steg 4 i **skapa en Azure-databas för MySQL server** ändra Compute enheter i samma prisnivå.

## <a name="delete-an-azure-database-for-mysql-server"></a>Ta bort en Azure-databas för MySQL-server

1. På servern **översikt** bladet, klickar du på den **ta bort** kommandot för att öppna bladet om du tar bort bekräftelse.

2. Ange rätt servernamn i textrutan på bladet för dubbla bekräftelse.

3. Klicka på den **ta bort** för igen för att bekräfta borttagning åtgärd och sedan vänta tills ”om du tar bort lyckades” popup ska visas i meddelandefältet.

## <a name="list-the-azure-database-for-mysql-databases"></a>Visa en lista med Azure-databas för MySQL-databaser
På servern **översikt** bladet Bläddra nedåt tills du ser databasen panelen längst ned. Alla databaser visas i tabellen. Klicka på den **ta bort** kommandot för att öppna bladet om du tar bort bekräftelse.

   ![Visa-databaser](./media/howto-create-manage-server-portal/show-databases.png)

## <a name="show-details-of-an-azure-database-for-mysql-server"></a>Visa detaljer för en Azure-databas för MySQL-server
I bladet server under **inställningar**, klickar du på **egenskaper** att öppna den **egenskaper** bladet och sedan visa detaljerad information om servern.

## <a name="next-steps"></a>Nästa steg

[Snabbstart: Skapa Azure-databas för MySQL-server med hjälp av Azure portal](./quickstart-create-mysql-server-database-using-azure-portal.md)