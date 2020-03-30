---
title: Källkontrollsintegrering
description: Databasutvecklar i företagsklass för SQL-pool med inbyggd källkontrollintegrering med Azure Repos (Git och GitHub).
services: synapse-analytics
author: kevinvngo
manager: craigg
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 9898addfa840752e27de8716d2185e767aac809b
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80350095"
---
# <a name="source-control-integration-for-sql-pool"></a>Integrering av källkontroll för SQL-pool

Den här självstudien beskriver hur du integrerar databasprojektet för SQL Server Data (SSDT) med källkontroll.  Källkontrollintegrering är det första steget i att bygga din kontinuerliga integrations- och distributionspipeline med SQL-poolresursen i Azure Synapse Analytics. 

## <a name="before-you-begin"></a>Innan du börjar

- Registrera dig för en [Azure DevOps-organisation](https://azure.microsoft.com/services/devops/)
- Gå igenom självstudien [Skapa och anslut](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Installera Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurera och ansluta till Azure DevOps

1. Skapa ett projekt som är värd för ditt SSDT-databasprojekt via en Azure Repo-databas i din Azure DevOps-organisation

   ![Skapa projekt](./media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Skapa projekt")

2. Öppna Visual Studio och anslut till din Azure DevOps-organisation och -projekt från steg 1 genom att välja Hantera anslutningar

   ![Hantera anslutningar](./media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Hantera anslutningar")

   ![Anslut](./media/sql-data-warehouse-source-control-integration/3-connect.png "Anslut")

3. Klona din Azure Repo-databas från projektet till din lokala dator

   ![Klona repo](./media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klona repo")

## <a name="create-and-connect-your-project"></a>Skapa och ansluta projektet

1. Skapa ett nytt SQL Server-databasprojekt med både en katalog och en lokal Git-databas i den **lokala klonade databasen** i Visual Studio

   ![Skapa nytt projekt](./media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Skapa nytt projekt")  

2. Högerklicka på det tomma sql-projektet och importera ditt informationslager till databasprojektet

   ![Importera projekt](./media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importera projekt")  

3. I team explorer i Visual Studio genomför du alla ändringar i din lokala Git-databas 

   ![Begå](./media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Checka in")  

4. Nu när du har ändringarna som har gjorts lokalt i den klonade databasen synkroniserar och skickar du ändringarna till din Azure Repo-databas i ditt Azure DevOps-projekt.

   ![Synkronisera och push - mellanlagring](./media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synkronisera och push - mellanlagring")

   ![Synkronisera och tryck](./media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synkronisera och tryck")  

## <a name="validation"></a>Validering

1. Verifiera att ändringar har överförts till din Azure Repo genom att uppdatera en tabellkolumn i databasprojektet från Visual Studio SQL Server Data Tools (SSDT)

   ![Verifiera uppdateringskolumnen](./media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Verifiera uppdateringskolumnen")

2. Genomföra och skicka ändringen från din lokala databas till din Azure Repo

   ![Push-överföring av ändringar](./media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Push-överföring av ändringar")

3. Kontrollera att ändringen har flyttats i din Azure Repo-databas

   ![Verifiera](./media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verifiera ändringar")

4. (**Valfritt**) Använd Schemajämförelse och uppdatera ändringarna i ditt måldatalager med SSDT för att säkerställa att objektdefinitionerna i din Azure Repo-databas och lokala databas återspeglar ditt informationslager

## <a name="next-steps"></a>Nästa steg

- [Utveckla för SQL pool](sql-data-warehouse-overview-develop.md)
