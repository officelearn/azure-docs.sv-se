---
title: Säkerhetskopiera din app i Azure
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
ms.openlocfilehash: b87838a80c7c7706b9af2bd4ea274335d04a5c52
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/25/2018
ms.locfileid: "36751521"
---
# <a name="back-up-your-app-in-azure"></a>Säkerhetskopiera din app i Azure
Funktionen säkerhetskopiering och återställning i [Azure App Service](app-service-web-overview.md) kan du enkelt skapa app säkerhetskopiering manuellt eller enligt ett schema. Du kan återställa appen till en ögonblicksbild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app. 

Information om hur du återställer en app från en säkerhetskopia finns [återställa en app i Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Vad säkerhetskopieras
Apptjänst kan säkerhetskopiera följande information till en Azure storage-konto och behållare som du har konfigurerat din app ska använda. 

* Appkonfiguration
* Filinnehåll
* Databas som är ansluten till din app

Följande databaslösningar stöds med funktionen för säkerhetskopiering: 
   - [SQL Database](https://azure.microsoft.com/services/sql-database/)
   - [Azure-databas för MySQL (förhandsgranskning)](https://azure.microsoft.com/services/mysql)
   - [Azure-databas för PostgreSQL (förhandsgranskning)](https://azure.microsoft.com/services/postgresql)
   - [MySQL i appen](https://blogs.msdn.microsoft.com/appserviceteam/2017/03/06/announcing-general-availability-for-mysql-in-app)
 

> [!NOTE]
>  Varje säkerhetskopiering är en fullständig offline kopia av din app, inte en inkrementell uppdatering.
>  

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Krav och begränsningar
* Funktionen säkerhetskopiering och återställning kräver App Service-plan i den **Standard** nivå eller **Premium** nivå. Läs mer om att skala din programtjänstplan att använda en högre nivå, [skala upp en app i Azure](web-sites-scale.md).  
  **Premium** nivå kan ett större antal daglig säkerhetskopiering än **Standard** nivå.
* Du behöver ett Azure storage-konto och behållaren i samma prenumeration som den app som du vill säkerhetskopiera. Mer information om Azure storage-konton finns i [länkar](#moreaboutstorage) i slutet av den här artikeln.
* Säkerhetskopieringar kan vara upp till 10 GB app- och innehåll. Om säkerhetskopians storlek överskrider denna gräns, inträffar ett fel.

<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Skapa en manuell säkerhetskopia
1. I den [Azure-portalen](https://portal.azure.com)navigerar du till appens sida, Välj **säkerhetskopieringar**. Den **säkerhetskopieringar** visas.
   
    ![Säkerhetskopieringar sida][ChooseBackupsPage]
   
   > [!NOTE]
   > Om följande meddelande visas klickar du på den för att uppgradera din programtjänstplan innan du kan fortsätta med säkerhetskopiering.
   > Mer information finns i [skala upp en app i Azure](web-sites-scale.md).  
   > ![Välj lagringskonto](./media/web-sites-backup/01UpgradePlan1.png)
   > 
   > 

2. I den **säkerhetskopiering** sidan, klicka på **konfigurera**
![Klicka på Konfigurera](./media/web-sites-backup/ClickConfigure1.png)
3. I den **konfigurering av säkerhetskopiering** klickar du på **lagring: inte konfigurerat** att konfigurera ett lagringskonto.
   
    ![Välj lagringskonto][ChooseStorageAccount]
4. Välj ditt mål för säkerhetskopian genom att välja en **Lagringskonto** och **behållare**. Storage-konto måste tillhöra samma prenumeration som den app du vill säkerhetskopiera. Om du vill kan skapa du ett nytt lagringskonto eller en ny behållare i respektive sidor. När du är klar klickar du på **Välj**.
   
    ![Välj lagringskonto](./media/web-sites-backup/02ChooseStorageAccount1-1.png)
5. I den **konfigurering av säkerhetskopiering** som lämnas fortfarande öppen kan du konfigurera **Backup Database**, sedan väljer du databaserna som du vill inkludera i säkerhetskopiering (SQL-databas eller MySQL) och sedan på **OK**.  
   
    ![Välj lagringskonto](./media/web-sites-backup/03ConfigureDatabase1.png)
   
   > [!NOTE]
   > För en databas ska visas i den här listan sin anslutningssträng måste finnas i den **anslutningssträngar** avsnitt i den **programinställningar** för din app.
   > 
   > 
6. I den **konfigurering av säkerhetskopiering** klickar du på **spara**.    
7. I den **säkerhetskopieringar** klickar du på **säkerhetskopiering**.
   
    ![Knappen BackUpNow][BackUpNow]
   
    Du kan se något pågående meddelande under säkerhetskopieringen.

När lagringskontot och behållaren har konfigurerats kan starta du en manuell säkerhetskopiering när som helst.  

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurera automatisk säkerhetskopiering
1. I den **Säkerhetskopieringskonfigurationen** ställer du in **schemalagd säkerhetskopiering** till **på**. 
   
    ![Välj lagringskonto](./media/web-sites-backup/05ScheduleBackup1.png)
2. Ange schemat för säkerhetskopiering alternativ visas, **schemalagd säkerhetskopiering** till **på**, konfigurerar schemat för säkerhetskopiering enligt önskemål och klickar på **OK**.
   
    ![Aktivera automatisk säkerhetskopiering][SetAutomatedBackupOn]

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurera partiella säkerhetskopieringar
Ibland vill du säkerhetskopiera allt på din app. Några exempel:

* Du [konfigurera veckovisa säkerhetskopior](web-sites-backup.md#configure-automated-backups) för din app med statiskt innehåll som aldrig ändras, till exempel gamla blogginlägg eller bilder.
* Appen har över 10 GB innehåll (som är den högsta du kan säkerhetskopiera i taget).
* Du vill inte säkerhetskopiera filerna.

Partiella säkerhetskopior kan du välja exakt vilka filer som du vill säkerhetskopiera.

### <a name="exclude-files-from-your-backup"></a>Undanta filer från säkerhetskopian
Anta att du har en app som innehåller loggfiler och statiska bilder som har säkerhetskopiera en gång och inte kommer att ändras. I sådana fall kan utesluta du de mappar och filer som lagras i dina framtida säkerhetskopieringar. Om du vill undanta filer och mappar från säkerhetskopiorna, skapa en `_backup.filter` filen i den `D:\home\site\wwwroot` mappen för din app. Ange en lista över filer och mappar som ska läggas till i den här filen. 

Ett enkelt sätt att komma åt dina filer är att använda Kudu. Klicka på **avancerade verktyg -> Gå** för ditt webbprogram att komma åt Kudu.

![Kudu med hjälp av portalen][kudu-portal]

Identifiera de mappar som du vill undanta från säkerhetskopiorna.  Till exempel att du vill filtrera bort de markerade kataloger och filer.

![Mappen avbildningar][ImagesFolder]

Skapa en fil med namnet `_backup.filter` och placera listan ovan i filen, men ta bort `D:\home`. Ange en mapp eller fil per rad. Så att innehållet i filen bör vara:
 ```bash
    \site\wwwroot\Images\brand.png
    \site\wwwroot\Images\2014
    \site\wwwroot\Images\2013
```

Överför `_backup.filter` filen till den `D:\home\site\wwwroot\` för din webbplats med [ftp](app-service-deploy-ftp.md) eller någon annan metod. Om du vill kan du skapa filen direkt med Kudu `DebugConsole` och infoga det innehållet.

Köra säkerhetskopieringar på samma sätt som du skulle göra det, [manuellt](#create-a-manual-backup) eller [automatiskt](#configure-automated-backups). Nu alla filer och mappar som anges i `_backup.filter` är exkluderad från framtida säkerhetskopieringar schemalagd eller manuell start. 

> [!NOTE]
> Du kan återställa partiella säkerhetskopior av din webbplats på samma sätt som [återställa en säkerhetskopia av vanliga](web-sites-restore.md). Återställningen har rätta.
> 
> När en fullständig säkerhetskopia återställs ersätts allt innehåll på webbplatsen med säkerhetskopieringen. Om en fil på platsen, men inte i säkerhetskopian tas bort. Men när en partiell säkerhetskopia återställs allt innehåll som finns i en av de svartlistat katalogerna eller en svartlistat fil är kvar.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Hur säkerhetskopior lagras
När du har gjort en eller flera säkerhetskopieringar för din app, säkerhetskopiorna är synliga på den **behållare** i ditt lagringskonto och din app. Storage-konto varje säkerhetskopiering består av en`.zip` -fil som innehåller den säkerhetskopiera informationen och en `.xml` -fil som innehåller ett manifest för den `.zip` filinnehåll. Du kan packa och bläddra bland dessa filer om du vill komma åt dina säkerhetskopieringar utan att faktiskt utföra en app-återställning.

Säkerhetskopieringen av databasen för appen lagras i roten på ZIP-filen. För en SQL-databas är en BACPAC-fil (utan filtillägget) och kan importeras. Om du vill skapa en SQL-databas som är baserat på BACPAC export finns [importera en BACPAC-fil för att skapa en ny databas](http://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Ändra alla filer i din **websitebackups** behållare kan orsaka att säkerhetskopieringen blir ogiltigt och därför icke-återställningsbara.
> 
> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhetskopieringen hantering med skript, med hjälp av den [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel på finns:

- [Azure CLI-exempel](app-service-cli-samples.md)
- [Azure PowerShell-exempel](app-service-powershell-samples.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Nästa steg
Information om hur du återställer en app från en säkerhetskopia finns [återställa en app i Azure](web-sites-restore.md). 


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

