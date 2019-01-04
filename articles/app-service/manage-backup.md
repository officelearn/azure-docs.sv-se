---
title: Säkerhetskopiera program – Azure App Service
description: Lär dig hur du skapar säkerhetskopior av dina appar i Azure App Service.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: jimbe
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2016
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: 1491068248d20a917e28db5a75dee3d4b3753f5c
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/21/2018
ms.locfileid: "53731450"
---
# <a name="back-up-your-app-in-azure"></a>Säkerhetskopiera din app i Azure
Funktionen för säkerhetskopiering och återställning i [Azure App Service](overview.md) kan du lätt skapa säkerhetskopior av appar manuellt eller enligt ett schema. Du kan återställa appen till en ögonblicksbild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app. 

Information om hur du återställer en app från säkerhetskopia finns i [Återställ en app i Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Vad säkerhetskopieras
App Service kan säkerhetskopiera följande information till en Azure-lagringskonto och en behållare som du har konfigurerat din app ska använda. 

* Appkonfiguration
* Filinnehåll
* Databasen som är ansluten till din app

Följande databaslösningar stöds med säkerhetskopieringsfunktionen: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
   - [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
   - [MySQL via app](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Varje säkerhetskopiering är en fullständig offline kopia av din app, inte en inkrementell uppdatering.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Krav och begränsningar
* Funktionen för säkerhetskopiering och återställning kräver App Service-plan i den **Standard** nivå eller **Premium** nivå. Läs mer om att skala din App Service-plan för att använda en högre nivå, [skala upp en app i Azure](web-sites-scale.md).  
  **Premium** nivån kan ett större antal dagliga säkerhetskopior än **Standard** nivå.
* Du behöver ett Azure storage-konto och en behållare i samma prenumeration som den app som du vill säkerhetskopiera. Mer information om Azure storage-konton finns i den [länkar](#moreaboutstorage) i slutet av den här artikeln.
* Säkerhetskopieringar kan vara upp till 10 GB av app- och innehåll. Om säkerhetskopians storlek överskrider den här gränsen kan få du ett felmeddelande.
* Säkerhetskopior av SSL aktiverat Azure Database för MySQL inte stöds. Om en säkerhetskopiering konfigureras, får du säkerhetskopieringar.
* Säkerhetskopior av SSL aktiverat Azure Database för PostgreSQL inte stöds. Om en säkerhetskopiering konfigureras, får du säkerhetskopieringar.
* I-app MySQL-databaser säkerhetskopieras automatiskt utan konfiguration. Om du manuellt kontrollera inställningarna för app MySQL-databaser, till exempel lägga till anslutningssträngar, fungerar inte säkerhetskopiorna.
* Med hjälp av en brandvägg, aktiverat lagringskonto som mål för dina säkerhetskopior inte stöds. Om en säkerhetskopiering konfigureras, får du säkerhetskopieringar.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Skapa en manuell säkerhetskopia
1. I den [Azure-portalen](https://portal.azure.com)inloggningsinformationen för din app, markera **säkerhetskopior**. Den **säkerhetskopior** visas.
   
    ![Sidan för säkerhetskopior][ChooseBackupsPage]
   
   > [!NOTE]
   > Om följande meddelande visas klickar du på den för att uppgradera din App Service-plan innan du kan fortsätta med säkerhetskopieringar.
   > Mer information finns i [skala upp en app i Azure](web-sites-scale.md).  
   > ![Välj lagringskonto](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. I den **Backup** klickar du på **konfigurera**
![klickar du på Konfigurera](./media/web-sites-backup/ClickConfigure1.png)
3. I den **Säkerhetskopieringskonfigurationen** klickar du på **lagring: Inte konfigurerad** att konfigurera ett lagringskonto.
   
    ![Välj lagringskonto][ChooseStorageAccount]
4. Välj ditt mål för säkerhetskopian genom att välja en **Lagringskonto** och **behållare**. Storage-kontot måste tillhöra samma prenumeration som den app du vill säkerhetskopiera. Om du vill kan skapa du ett nytt lagringskonto eller en ny behållare i respektive sidor. När du är klar klickar du på **Välj**.
   
    ![Välj lagringskonto](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. I den **Säkerhetskopieringskonfigurationen** som lämnas fortfarande öppna kan du konfigurera **Backup Database**, sedan väljer du databaserna som du vill ska ingå i säkerhetskopiering (SQL-databas eller MySQL) och sedan på **OK**.  
   
    ![Välj lagringskonto](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > För en databas ska visas i den här listan, sin anslutningssträng måste finnas i den **anslutningssträngar** delen av den **programinställningar** för din app. 
   >
   > I-app MySQL-databaser säkerhetskopieras automatiskt utan konfiguration. Om du manuellt kontrollera inställningarna för app MySQL-databaser, till exempel lägga till anslutningssträngar, fungerar inte säkerhetskopiorna.
   > 
   > 
6. I den **Säkerhetskopieringskonfigurationen** klickar du på **spara**.    
7. I den **säkerhetskopior** klickar du på **Backup**.
   
    ![BackUpNow knappen][BackUpNow]
   
    Du ser något pågående meddelande under säkerhetskopieringen.

När lagringskontot och behållaren har konfigurerats kan initiera du en manuell säkerhetskopiering när som helst.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurera automatiska säkerhetskopieringar
1. I den **Säkerhetskopieringskonfiguration** ställer du in **schemalagd säkerhetskopiering** till **på**. 
   
    ![Välj lagringskonto](./media/web-sites-backup/05ScheduleBackup1.png)
2. Schema för säkerhetskopiering alternativ visas, ange **schemalagd säkerhetskopiering** till **på**, konfigurera schemat för säkerhetskopiering som du vill och klicka på **OK**.
   
    ![Aktivera automatiska säkerhetskopieringar][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurera partiella säkerhetskopieringar
Ibland vill du inte säkerhetskopiera allt på din app. Några exempel:

* Du [konfigurera veckovisa säkerhetskopior](#configure-automated-backups) för din app med statiskt innehåll som aldrig ändras, till exempel gamla blogginlägg eller bilder.
* Din app har över 10 GB av innehåll (som är den maximala du kan säkerhetskopiera i taget).
* Du vill inte säkerhetskopiera filerna.

Partiella säkerhetskopior kan du välja exakt vilka filer som du vill säkerhetskopiera.

### <a name="exclude-files-from-your-backup"></a>Undanta filer från säkerhetskopian
Anta att du har en app som innehåller loggfiler och statiska bilder som har varit säkerhetskopierade en gång och inte kommer att ändras. I sådana fall kan undanta du dessa mappar och filer som lagras i dina framtida säkerhetskopieringar. Om du vill undanta filer och mappar från dina säkerhetskopior, skapa en `_backup.filter` fil i den `D:\home\site\wwwroot` -mappen i appen. Ange en lista över filer och mappar som ska läggas till i den här filen. 

Ett enkelt sätt att komma åt dina filer är att använda Kudu. Klicka på **avancerade verktyg -> Go** för din webbapp till Kudu.

![Kudu med hjälp av portalen][kudu-portal]

Identifiera de mappar som du vill undanta från säkerhetskopiorna.  Exempelvis kan du filtrera bort den markerade mappen och filer.

![Bildmappen][ImagesFolder]

Skapa en fil med namnet `_backup.filter` och lägga till den föregående listan i filen, men ta bort `D:\home`. Lista en mapp eller fil per rad. Innehållet i filen bör vara:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Ladda upp `_backup.filter` filen till den `D:\home\site\wwwroot\` för din webbplats med hjälp av [ftp](deploy-ftp.md) eller någon annan metod. Om du vill kan du skapa filen direkt med Kudu `DebugConsole` och infoga det innehållet.

Köra säkerhetskopieringar på samma sätt som du skulle göra det, [manuellt](#create-a-manual-backup) eller [automatiskt](#configure-automated-backups). Nu alla filer och mappar som anges i `_backup.filter` är exkluderad från framtida säkerhetskopieringar schemalagda eller initieras manuellt. 

> [!NOTE]
> Du har återställt partiella säkerhetskopior av webbplatsen på samma sätt som du skulle [återställa en säkerhetskopia av regelbundna](web-sites-restore.md). Återställningsprocessen uppnår rätt mål.
> 
> När en fullständig säkerhetskopia återställs ersätts allt innehåll på webbplatsen med säkerhetskopieringen. Om en fil finns på webbplatsen, men inte i säkerhetskopian av den tas bort. Men när en partiell säkerhetskopia återställs allt innehåll som finns i någon av de svartlistade katalogerna eller en fil kan svartlistade, lämnas skick.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Hur säkerhetskopior lagras
När du har gjort en eller flera säkerhetskopieringar för din app, säkerhetskopiorna som är synliga på den **behållare** i ditt storage-konto och din app. I storage-konto varje säkerhetskopiering består av en`.zip` -fil som innehåller den säkerhetskopierade data och en `.xml` -fil som innehåller ett manifest för den `.zip` filinnehåll. Du kan packa upp och bläddra bland dessa filer om du vill komma åt dina säkerhetskopior utan att faktiskt utföra en återställning för appen.

Säkerhetskopian av databasen för appen lagras i roten på .zip-filen. För en SQL-databas, detta är en BACPAC-fil (utan filtillägget) och kan importeras. Om du vill skapa en SQL-databas utifrån BACPAC-export [importera en BACPAC-fil för att skapa en ny användardatabas](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Ändra någon av filerna i din **websitebackups** behållare kan orsaka att säkerhetskopiera till blir ogiltiga och därför icke-återställningsbara.
> 
> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera hantering av säkerhetskopiering med skript med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel finns här:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel för Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Nästa steg
Information om hur du återställer en app från en säkerhetskopia finns i [Återställ en app i Azure](web-sites-restore.md). 


<!-- IMAGES -->
[ChooseBackupsPage]: ./media/web-sites-backup/01ChooseBackupsPage1.png
[ChooseStorageAccount]: ./media/web-sites-backup/02ChooseStorageAccount-1.png
[BackUpNow]: ./media/web-sites-backup/04BackUpNow1.png
[SetAutomatedBackupOn]: ./media/web-sites-backup/06SetAutomatedBackupOn1.png
[SaveIcon]: ./media/web-sites-backup/10SaveIcon.png
[ImagesFolder]: ./media/web-sites-backup/11Images.png
[LogsFolder]: ./media/web-sites-backup/12Logs.png
[GhostUpgradeWarning]: ./media/web-sites-backup/13GhostUpgradeWarning.png
[kudu-portal]:./media/web-sites-backup/kudu-portal.PNG

