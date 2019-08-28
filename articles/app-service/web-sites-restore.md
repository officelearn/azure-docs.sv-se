---
title: Återställ app-Azure App Service
description: Lär dig hur du återställer din app från en säkerhets kopia.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 519cf5388b095c7ca6e0ae7d978608f0824dc3a2
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/28/2019
ms.locfileid: "70066507"
---
# <a name="restore-an-app-in-azure"></a>Återställ en app i Azure
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/overview.md) som du tidigare har säkerhetskopierat (se [säkerhetskopiera din app i Azure](manage-backup.md)). Du kan återställa din app med dess länkade databaser på begäran till ett tidigare tillstånd, eller skapa en ny app baserat på en av din ursprungliga programs säkerhets kopior. Azure App Service stöder följande databaser för säkerhets kopiering och återställning:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Återställning från säkerhets kopior är tillgängligt för appar som körs på nivån **standard** och **Premium** . Information om hur du skalar upp din app finns i [skala upp en app i Azure](manage-scale-up.md). **Premium** -nivån tillåter att ett större antal dagliga säkerhets kopieringar utförs än **standard** nivån.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Återställa en app från en befintlig säkerhets kopia
1. På sidan **Inställningar** i appen i Azure Portal klickar du på **säkerhets kopiering** för att visa sidan **säkerhets kopior** . Klicka sedan på **Återställ**.
   
    ![Välj Återställ nu][ChooseRestoreNow]
2. På sidan **Återställ** väljer du först säkerhets kopierings källan.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Alternativet **säkerhets kopiering av app** visar alla befintliga säkerhets kopior av den aktuella appen, och du kan enkelt välja en.
    Med alternativet **lagring** kan du välja valfri säkerhets kopia av zip-filen från alla befintliga Azure Storage konto och behållare i din prenumeration.
    Om du försöker återställa en säkerhets kopia av en annan app använder du alternativet **lagring** .
3. Ange sedan målet för appen Återställ i återställnings **målet**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**raderas och skrivs alla befintliga data i din aktuella app över. Kontrol lera att det är precis det du vill göra innan du klickar på **OK**.
   > 
   > 
   
   > [!WARNING]
   > Om App Service skriver data till databasen när du återställer den, kan det leda till symptom som överträdelser av primär nyckel och data förlust. Vi rekommenderar att du stoppar App Service först innan du börjar återställa databasen.
   > 
   > 
   
    Du kan välja en **befintlig app** för att återställa säkerhets kopian av appen till en annan app i samma resurs grupp. Innan du använder det här alternativet bör du redan ha skapat en annan app i resurs gruppen med spegling av databas konfigurationen till den som definierats i säkerhets kopieringen av appen. Du kan också skapa en **ny** app för att återställa innehållet till.

4. Klicka på **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Hämta eller ta bort en säkerhets kopia från ett lagrings konto
1. Välj **lagrings konton**på den huvudsakliga sidan **Bläddra** i Azure Portal. En lista över dina befintliga lagrings konton visas.
2. Välj det lagrings konto som innehåller den säkerhets kopia som du vill ladda ned eller ta bort. Sidan för lagrings kontot visas.
3. På sidan lagrings konto väljer du den behållare som du vill använda
   
    ![Visa behållare][ViewContainers]
4. Välj den säkerhets kopia som du vill ladda ned eller ta bort.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klicka på **Hämta** eller **ta bort** beroende på vad du vill göra.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Övervaka en återställnings åtgärd
Om du vill se information om att återställningen av appen lyckades eller inte fungerar går du till sidan **aktivitets logg** i Azure Portal.  
 

Rulla ned för att hitta önskad återställnings åtgärd och klicka för att markera den.

Informations sidan visar tillgänglig information relaterad till återställnings åtgärden.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhets kopierings hanteringen med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel finns i:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel för Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
