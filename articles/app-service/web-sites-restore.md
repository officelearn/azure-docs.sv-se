---
title: Återställ app – Azure App Service
description: Lär dig mer om att återställa din app från en säkerhetskopia.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1e8bebdb3f54ac59ec19ef798cc3e794473bbec0
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60832509"
---
# <a name="restore-an-app-in-azure"></a>Återställ en app i Azure
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/overview.md) som du tidigare har säkerhetskopierat (se [säkerhetskopiera din app i Azure](manage-backup.md)). Du kan återställa appen med dess länkade databaser på begäran till ett tidigare tillstånd eller skapa en ny app utifrån en säkerhetskopia av den ursprungliga appen. Azure App Service stöder följande databaser för säkerhetskopiering och återställning:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL via app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Återställer från säkerhetskopior är tillgänglig för appar som körs i **Standard** och **Premium** nivå. Information om att skala upp appar finns i [skala upp en app i Azure](web-sites-scale.md). **Premium** nivån kan ett större antal dagliga säkerhetskopior som ska utföras än **Standard** nivå.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Återställ en app från en befintlig säkerhetskopia
1. På den **inställningar** sidan för din app i Azure portal, klickar du på **säkerhetskopior** att visa den **säkerhetskopior** sidan. Klicka sedan på **återställa**.
   
    ![Välj Återställ nu][ChooseRestoreNow]
2. I den **återställa** först väljer säkerhetskopiering källan.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Den **säkerhetskopia av App** alternativet visas alla befintliga säkerhetskopior för den aktuella appen och du kan enkelt välja ett.
    Den **Storage** alternativet kan du välja alla säkerhetskopierade zipfilen från alla befintliga Azure Storage-konto och behållare i din prenumeration.
    Om du försöker återställa en säkerhetskopia av en annan app kan använda den **Storage** alternativet.
3. Sedan kan ange mål för app-återställning i **databasfilens Återställningsmål**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Om du väljer **överskrivning**, alla befintliga data i din aktuella app raderas och skrivas över. Innan du klickar på **OK**, se till att det är exakt vad du vill göra.
   > 
   > 
   
   > [!WARNING]
   > Om App Service skriver data till databasen när du återställer dem, kan det resultera i symptom som bryter mot PRIMÄRNYCKEL och dataförlust. Du rekommenderas för att stoppa tjänsten App först innan du börjar att återställa databasen.
   > 
   > 
   
    Du kan välja **befintlig App** att återställa säkerhetskopian av appen till en annan app i samma resursgrupp. Innan du använder det här alternativet kan bör du redan har skapat en annan app i resursgruppen med spegling databaskonfiguration till den som definierats i app-säkerhetskopiering. Du kan också skapa en **New** app för att återställa ditt innehåll till.

4. Klicka på **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Ladda ned eller ta bort en säkerhetskopia från ett lagringskonto
1. Från primära **Bläddra** i Azure-portalen, väljer **lagringskonton**. En lista över dina befintliga lagringskonton visas.
2. Välj det lagringskonto som innehåller säkerhetskopian som du vill ladda ned eller ta bort. Sidan för lagringskontot visas.
3. På sidan för lagringskontot, markera den behållare som du vill
   
    ![Visa behållare][ViewContainers]
4. Markera den säkerhetskopierade filen som du vill ladda ned eller ta bort.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klicka på **hämta** eller **ta bort** beroende på vad du vill göra.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Övervaka en återställningsåtgärd
Om du vill se information om lyckad eller misslyckad för återställningsåtgärden app, navigerar du till den **aktivitetsloggen** sidan på Azure portal.  
 

Rulla ned till önskad återställningsåtgärden och markera den.

Sidan visar tillgängliga information relaterad till återställningen.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hantering av säkerhetskopiering med skript med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel finns här:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel för Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
