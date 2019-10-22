---
title: Käll kontrolls integrering | Microsoft Docs
description: Databas DevOps-upplevelse i företags klass för SQL Data Warehouse med intern käll kontroll integrering med Azure databaser (git och GitHub).
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: overview
ms.subservice: integration
ms.date: 08/23/2019
ms.author: kevin
ms.reviewer: igorstan
ms.openlocfilehash: 10a10882efe05ef9e6bb86e54fcfcf8c5d73d225
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2019
ms.locfileid: "70098746"
---
# <a name="source-control-integration-for-azure-sql-data-warehouse"></a>Käll kontrolls integrering för Azure SQL Data Warehouse

I den här självstudien beskrivs hur du integrerar ditt SSDT-databas projekt (SQL Server data verktyg) med käll kontroll.  Käll kontroll integrering är det första steget i att skapa din pipeline för kontinuerlig integrering och distribution med SQL Data Warehouse. 

## <a name="before-you-begin"></a>Innan du börjar

- Registrera dig för en [Azure DevOps-organisation](https://azure.microsoft.com/services/devops/)
- Gå igenom självstudien [skapa och Anslut](https://docs.microsoft.com/azure/sql-data-warehouse/create-data-warehouse-portal)
-  [Installera Visual Studio 2019](https://visualstudio.microsoft.com/vs/older-downloads/) 

## <a name="set-up-and-connect-to-azure-devops"></a>Konfigurera och Anslut till Azure-DevOps

1. I din Azure DevOps-organisation skapar du ett projekt som ska vara värd för ditt SSDT Database-projekt via en Azure lagrings platsen-lagringsplats

   ![Skapa projekt](media/sql-data-warehouse-source-control-integration/1-create-project-azure-devops.png "Skapa projekt")

2. Öppna Visual Studio och Anslut till din Azure DevOps-organisation och projekt från steg 1 genom att välja hantera anslutningar

   ![Hantera anslutningar](media/sql-data-warehouse-source-control-integration/2-manage-connections.png "Hantera anslutningar")

   ![Anslut](media/sql-data-warehouse-source-control-integration/3-connect.png "Connect")

3. Klona din Azure lagrings platsen-lagringsplats från projektet till din lokala dator

   ![Klona lagrings platsen](media/sql-data-warehouse-source-control-integration/4-clone-repo.png "Klona lagrings platsen")

## <a name="create-and-connect-your-project"></a>Skapa och Anslut ditt projekt

1. I Visual Studio skapar du ett nytt SQL Server Database-projekt med både en katalog och en lokal git-lagringsplats i din **lokala klonade lagrings plats**

   ![Skapa nytt projekt](media/sql-data-warehouse-source-control-integration/5-create-new-project.png "Skapa nytt projekt")  

2. Högerklicka på din tomma sqlproject och importera data lagret till databas projektet

   ![Importera projekt](media/sql-data-warehouse-source-control-integration/6-import-new-project.png "Importera projekt")  

3. I team Explorer i Visual Studio ska du spara alla ändringar i din lokala git-lagringsplats 

   ![Förbinder](media/sql-data-warehouse-source-control-integration/6.5-commit-push-changes.png "Checka in")  

4. Nu när du har gjort ändringarna lokalt i den klonade lagrings platsen synkroniserar du och skickar ändringarna till din Azure lagrings platsen-lagringsplats i Azure DevOps-projektet.

   ![Synkronisering och push-mellanlagring](media/sql-data-warehouse-source-control-integration/7-commit-push-changes.png "Synkronisering och push-mellanlagring")

   ![Synkronisera och push](media/sql-data-warehouse-source-control-integration/7.5-commit-push-changes.png "Synkronisera och push")  

## <a name="validation"></a>Validering

1. Verifiera att ändringarna har flyttats till din Azure-lagrings platsen genom att uppdatera en tabell kolumn i ditt databas projekt från Visual Studio SQL Server Data Tools (SSDT)

   ![Verifiera uppdaterings kolumn](media/sql-data-warehouse-source-control-integration/8-validation-update-column.png "Verifiera uppdaterings kolumn")

2. Genomför och skicka ändringarna från din lokala lagrings plats till din Azure-lagrings platsen

   ![Push-ändringar](media/sql-data-warehouse-source-control-integration/9-push-column-change.png "Push-överföring av ändringar")

3. Kontrol lera att ändringen har överförts i din Azure lagrings platsen-lagringsplats

   ![Verifiera](media/sql-data-warehouse-source-control-integration/10-verify-column-change-pushed.png "Verifiera ändringar")

4. (**Valfritt**) Använd schema jämför och uppdatera ändringarna i mål informations lagret med hjälp av SSDT för att se till att objekt definitionerna i Azure lagrings platsen-lagringsplatsen och den lokala lagrings platsen återspeglar ditt informations lager

## <a name="next-steps"></a>Nästa steg

- [Utveckla för Azure SQL Data Warehouse](sql-data-warehouse-overview-develop.md)

<!--Image references-->

<!--Article references-->


<!--MSDN references-->

<!--Other Web references-->

