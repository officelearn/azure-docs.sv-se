---
title: Återställa appen från säkerhetskopiering
description: Läs om hur du återställer appen från en säkerhetskopia. Vissa länkade databaser kan återställas tillsammans med appen i en åtgärd.
ms.assetid: 4444dbf7-363c-47e2-b24a-dbd45cb08491
ms.topic: article
ms.date: 07/06/2016
ms.custom: seodec18
ms.openlocfilehash: 1295080d0eec7a4e88029cdadd85863f5f40d034
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74689254"
---
# <a name="restore-an-app-in-azure"></a>Återställ en app i Azure
I den här artikeln visas hur du återställer en app i [Azure App Service](../app-service/overview.md) som du tidigare har säkerhetskopierat (se [Säkerhetskopiera din app i Azure](manage-backup.md)). Du kan återställa appen med dess länkade databaser på begäran till ett tidigare tillstånd eller skapa en ny app baserat på en av den ursprungliga appens säkerhetskopior. Azure App Service stöder följande databaser för säkerhetskopiering och återställning:
- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL i appen](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)

Återställning från säkerhetskopior är tillgängligt för appar som körs på **standard-** och **Premium-nivå.** Information om hur du skalar upp din app finns [i Skala upp en app i Azure](manage-scale-up.md). **Premium-nivån** gör att ett större antal dagliga säkerhetskopior kan utföras än **standardnivå.**

<a name="PreviousBackup"></a>

## <a name="restore-an-app-from-an-existing-backup"></a>Återställa en app från en befintlig säkerhetskopia
1. På sidan **Inställningar** i din app i Azure-portalen klickar du på **Säkerhetskopior** för att visa sidan **Säkerhetskopior.** Klicka sedan på **Återställ**.
   
    ![Välj återställ nu][ChooseRestoreNow]
2. Välj först säkerhetskopian på sidan **Återställ.**
   
    ![](./media/web-sites-restore/021ChooseSource1.png)
   
    Alternativet **App backup** visar alla befintliga säkerhetskopior av den aktuella appen, och du kan enkelt välja en.
    Med alternativet **Lagring** kan du välja en ZIP-säkerhetskopia från ett befintligt Azure Storage-konto och behållare i din prenumeration.
    Om du försöker återställa en säkerhetskopia av en annan app använder du alternativet **Lagring.**
3. Ange sedan målet för appåterställning i **Återställ mål**.
   
    ![](./media/web-sites-restore/022ChooseDestination1.png)
   
   > [!WARNING]
   > Om du väljer **Skriv över**raderas alla befintliga data i den aktuella appen och skrivs över. Innan du klickar på **OK**kontrollerar du att det är precis vad du vill göra.
   > 
   > 
   
   > [!WARNING]
   > Om App-tjänsten skriver data till databasen medan du återställer den kan det leda till symptom som brott mot PRIMÄRNYCKEL och dataförlust. Det föreslås att apptjänsten stoppas först innan du börjar återställa databasen.
   > 
   > 
   
    Du kan välja **Befintlig app** om du vill återställa appsäkerhetskopiorna till en annan app i samma resursgrupp. Innan du använder det här alternativet bör du redan ha skapat en annan app i resursgruppen med spegling av databaskonfigurationen till den som definierats i appsäkerhetskopiningen. Du kan också skapa en **ny** app som du vill återställa innehållet till.

4. Klicka på **OK**.

<a name="StorageAccount"></a>

## <a name="download-or-delete-a-backup-from-a-storage-account"></a>Ladda ned eller ta bort en säkerhetskopia från ett lagringskonto
1. Välj **Lagringskonton**på sidan **Bläddra** i Huvudsidan i Azure-portalen . En lista över dina befintliga lagringskonton visas.
2. Välj det lagringskonto som innehåller säkerhetskopian som du vill hämta eller ta bort. Sidan för lagringskontot visas.
3. På sidan lagringskonto väljer du den behållare du vill använda
   
    ![Visa behållare][ViewContainers]
4. Välj säkerhetskopia som du vill hämta eller ta bort.
   
    ![Visacontainers](./media/web-sites-restore/03ViewFiles.png)
5. Klicka på **Hämta** eller **Ta bort** beroende på vad du vill göra.  

<a name="OperationLogs"></a>

## <a name="monitor-a-restore-operation"></a>Övervaka en återställningsåtgärd
Om du vill se information om hur åtgärden för appåterställning lyckas eller misslyckas navigerar du till sidan **Aktivitetslogg** i Azure-portalen.  
 

Bläddra nedåt för att hitta önskad återställning och klicka för att markera den.

Informationssidan visar tillgänglig information om återställningen.

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhetskopieringshantering med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

För exempel, se:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel på Azure PowerShell](samples-powershell.md)

<!-- ## Next Steps
You can backup and restore App Service apps using REST API. -->


<!-- IMAGES -->
[ChooseRestoreNow]: ./media/web-sites-restore/02ChooseRestoreNow1.png
[ViewContainers]: ./media/web-sites-restore/03ViewContainers.png
