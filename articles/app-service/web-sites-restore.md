---
title: "Återställ en app i Azure"
description: "Lär dig hur du återställer din app från en säkerhetskopia."
services: app-service
documentationcenter: 
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
ms.openlocfilehash: 60b55acaf687dae76e054b428b8d6584d8eb09f8
ms.sourcegitcommit: 933af6219266cc685d0c9009f533ca1be03aa5e9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/18/2017
---
# <a name="restore-an-app-in-azure"></a>Återställ en app i Azure
Den här artikeln visar hur du återställer en app i [Azure App Service](../app-service/app-service-web-overview.md) som du tidigare har säkerhetskopierat (se [säkerhetskopiera din app i Azure](web-sites-backup.md)). Du kan återställa din app med dess länkade databaser på begäran till ett tidigare tillstånd eller skapa en ny app baserat på en av din ursprungliga app säkerhetskopieringar. Azure Apptjänst har stöd för följande databaser för säkerhetskopiering och återställning:
- [SQL Database](https://azure.microsoft.com/en-us/services/sql-database/)
- [Azure-databas för MySQL (förhandsgranskning)](https://azure.microsoft.com/en-us/services/mysql)
- [Azure-databas för PostgreSQL (förhandsgranskning)](https://azure.microsoft.com/en-us/services/postgres)
- [MySQL i appen](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Återställning från säkerhetskopior är tillgängliga för appar som körs **Standard** och **Premium** nivå. Information om skala upp appar finns [skala upp en app i Azure](web-sites-scale.md). **Premium** nivå kan ett större antal daglig säkerhetskopiering ska utföras än **Standard** nivå.

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Återställa en app från en befintlig säkerhetskopia
1. På den **inställningar** sidan i din app i Azure-portalen klickar du på **säkerhetskopieringar** att visa den **säkerhetskopieringar** sidan. Klicka på **återställa**.
   
    ![Välj Återställ nu][ChooseRestoreNow]
2. I den **återställa** först väljer säkerhetskopiering källan.
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Den **App säkerhetskopiering** alternativet visas alla befintliga säkerhetskopior för den aktuella appen och du kan enkelt välja ett.
    Den **lagring** alternativet kan du välja en säkerhetskopiering ZIP-fil från en befintlig Azure Storage-konto och en behållare i din prenumeration.
    Om du försöker återställa en säkerhetskopia av en annan app kan du använda den **lagring** alternativet.
3. Ange sedan mål för app-återställning i **Återställningsmål**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**, alla befintliga data i din aktuella appen bort och skrivas över. Innan du klickar på **OK**, se till att den är exakt vad du vill göra.
   > 
   > 
   
    Du kan välja **befintlig App** att återställa säkerhetskopian app till en annan app i samma resoure grupp. Innan du använder det här alternativet måste bör du redan har skapat en annan app i resursgruppen med spegling databaskonfiguration med det som definieras i säkerhetskopian för appen. Du kan också skapa en **ny** app att återställa ditt innehåll till.

4. Klicka på **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Hämta eller ta bort en säkerhetskopia från ett lagringskonto
1. Från huvudsakliga **Bläddra** sidan i Azure portal, Välj **lagringskonton**. En lista över dina befintliga lagringskonton visas.
2. Välj lagringskontot som innehåller säkerhetskopian som du vill hämta eller ta bort. Sidan för storage-konto visas.
3. Markera den behållare som du vill på sidan storage-konto
   
    ![Visa behållare][ViewContainers]
4. Välj säkerhetskopian som du vill hämta eller ta bort.
   
    ![ViewContainers](./media/web-sites-restore/03ViewFiles.png)
5. Klicka på **hämta** eller **ta bort** beroende på vad du vill göra.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Övervaka en återställning
Om du vill se information om lyckad eller misslyckad återställningen app, navigerar du till den **aktivitetsloggen** sida i Azure-portalen.  
 

Rulla ned till den önskade återställningsåtgärden och markera den.

Informationssidan visas tillgänglig information som rör återställningen.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhetskopieringen hantering med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel på finns:

- [Azure CLI-exempel](app-service-cli-samples.md)
- [Azure PowerShell-exempel](app-service-powershell-samples.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
