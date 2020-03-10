---
title: Säkerhetskopiera en app
description: Lär dig hur du skapar säkerhets kopior av dina appar i Azure App Service. Kör manuella eller schemalagda säkerhets kopieringar. Anpassa säkerhets kopior genom att inkludera den bifogade databasen.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: 783737729601bfef3bee8741a097d4319349f18e
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/05/2020
ms.locfileid: "78373769"
---
# <a name="back-up-your-app-in-azure"></a>Säkerhetskopiera din app i Azure
Med säkerhets kopierings-och återställnings funktionen i [Azure App Service](overview.md) kan du enkelt skapa säkerhets kopior av appar manuellt eller enligt ett schema. Du kan konfigurera säkerhets kopiorna så att de behålls på obestämd tid. Du kan återställa appen till en ögonblicks bild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app.

Information om hur du återställer en app från en säkerhets kopia finns i [återställa en app i Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Vad säkerhets kopie ras
App Service kan säkerhetskopiera följande information till ett Azure Storage-konto och en behållare som du har konfigurerat din app för att använda. 

* App-konfiguration
* Filinnehåll
* Databas ansluten till din app

Följande databas lösningar stöds med funktionen säkerhets kopiering: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL in-app](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Varje säkerhets kopia är en fullständig offlinekopia av appen, inte en stegvis uppdatering.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Krav och begränsningar
* Funktionen säkerhets kopiering och återställning kräver att App Service plan finnas på **standard** -eller **Premium** -nivån. Mer information om hur du skalar App Service plan att använda en högre nivå finns i [skala upp en app i Azure](manage-scale-up.md). **Premium** -nivån tillåter ett större antal dagliga säkerhets kopieringar än **standard** nivån.
* Du behöver ett Azure Storage-konto och en behållare i samma prenumeration som den app som du vill säkerhetskopiera. Mer information om Azure Storage-konton finns i [Översikt över Azure Storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Säkerhets kopieringar kan vara upp till 10 GB app-och databas innehåll. Om säkerhets kopierings storleken överskrider den här gränsen får du ett fel meddelande.
* Säkerhets kopiering av SSL-aktiverade Azure Database for MySQL stöds inte. Om du har konfigurerat en säkerhets kopia får du inte säkerhets kopior.
* Säkerhets kopiering av SSL-aktiverade Azure Database for PostgreSQL stöds inte. Om du har konfigurerat en säkerhets kopia får du inte säkerhets kopior.
* MySQL-databaser i app säkerhets kopie ras automatiskt utan någon konfiguration. Om du gör inställningar manuellt för MySQL-databaser i-app, till exempel att lägga till anslutnings strängar, kanske säkerhets kopieringarna inte fungerar korrekt.
* Det finns inte stöd för att använda ett brand Väggs aktiverat lagrings konto eftersom målet för dina säkerhets kopior inte stöds. Om du har konfigurerat en säkerhets kopia får du inte säkerhets kopior.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Skapa en manuell säkerhetskopia
1. I [Azure Portal](https://portal.azure.com)navigerar du till appens sida och väljer **säkerhets kopiering**. Sidan **säkerhets kopior** visas.

    ![Sidan säkerhets kopior](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Om du ser följande meddelande klickar du på det för att uppgradera ditt App Service plan innan du kan fortsätta med säkerhets kopieringar.
    > Mer information finns i [skala upp en app i Azure](manage-scale-up.md).
    > ![välja lagrings konto](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. På sidan **säkerhets kopiering** väljer du **säkerhets kopiering inte konfigurerad. Klicka här för att konfigurera säkerhets kopiering för din app**.

    ![Klicka på Konfigurera](./media/manage-backup/configure-start.png)

3. På sidan **säkerhets kopierings konfiguration** klickar du på **lagring som inte är konfigurerad** för att konfigurera ett lagrings konto.

    ![Välj lagrings konto](./media/manage-backup/configure-storage.png)

4. Välj mål för säkerhets kopian genom att välja ett **lagrings konto** och en **behållare**. Lagrings kontot måste tillhöra samma prenumeration som den app som du vill säkerhetskopiera. Om du vill kan du skapa ett nytt lagrings konto eller en ny behållare på respektive sidor. När du är klar klickar du på **Välj**.

5. På sidan **säkerhets kopierings konfiguration** som fortfarande är öppen kan du konfigurera **säkerhets kopierings databasen**och sedan välja de databaser som du vill inkludera i säkerhets kopiorna (SQL Database eller MySQL) och sedan klicka på **OK**.

    ![Välj lagrings konto](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > För att en databas ska visas i listan måste dess anslutnings sträng finnas i avsnittet **anslutnings strängar** på sidan **program inställningar** för din app. 
    >
    > MySQL-databaser i app säkerhets kopie ras automatiskt utan någon konfiguration. Om du gör inställningar manuellt för MySQL-databaser i-app, till exempel att lägga till anslutnings strängar, kanske säkerhets kopieringarna inte fungerar korrekt.
    > 
    > 

6. På sidan **säkerhets kopierings konfiguration** klickar du på **Spara**.
7. På sidan **säkerhets kopior** klickar du på **säkerhets kopiering**.

    ![BackUpNow-knapp](./media/manage-backup/manual-backup.png)

    Du ser ett förlopps meddelande under säkerhets kopierings processen.

När lagrings kontot och behållaren har kon figurer ATS kan du när som helst påbörja en manuell säkerhets kopiering.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurera automatiska säkerhets kopieringar
1. På sidan **säkerhets kopierings konfiguration** anger du **schemalagd säkerhets kopiering** till **på**. 

    ![Aktivera automatiska säkerhets kopieringar](./media/manage-backup/scheduled-backup.png)

2. Konfigurera schemat för säkerhets kopiering efter behov och välj **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurera partiell säkerhets kopiering
Ibland vill du inte säkerhetskopiera allt i din app. Några exempel:

* Du [ställer in vecko Visa säkerhets kopior](#configure-automated-backups) av din app som innehåller statiskt innehåll som aldrig ändras, till exempel gamla blogg inlägg eller bilder.
* Din app har över 10 GB innehåll (det är det högsta antalet som du kan säkerhetskopiera i taget).
* Du vill inte säkerhetskopiera loggfilerna.

Med partiella säkerhets kopieringar kan du välja exakt vilka filer du vill säkerhetskopiera.

> [!NOTE]
> Enskilda databaser i säkerhets kopian kan vara högst 4 GB, men den totala maximala storleken på säkerhets kopian är 10 GB

### <a name="exclude-files-from-your-backup"></a>Undanta filer från säkerhets kopian
Anta att du har en app som innehåller loggfiler och statiska avbildningar som har säkerhetskopierats en gång och inte kommer att ändras. I sådana fall kan du undanta dessa mappar och filer från att lagras i dina framtida säkerhets kopior. Om du vill undanta filer och mappar från dina säkerhets kopior skapar du en `_backup.filter`-fil i mappen `D:\home\site\wwwroot` i appen. Ange listan med filer och mappar som du vill undanta i den här filen. 

Du kan komma åt dina filer genom att gå till `https://<app-name>.scm.azurewebsites.net/DebugConsole`. Logga in på ditt Azure-konto om du uppmanas till det.

Identifiera de mappar som du vill undanta från dina säkerhets kopior. Till exempel vill du filtrera bort den markerade mappen och filerna.

![Mappen bilder](./media/manage-backup/kudu-images.png)

Skapa en fil med namnet `_backup.filter` och Lägg till föregående lista i filen, men ta bort `D:\home`. Lista en katalog eller fil per rad. Därför bör innehållet i filen vara:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Ladda upp `_backup.filter`-filen till `D:\home\site\wwwroot\`s katalog för platsen med [FTP](deploy-ftp.md) eller någon annan metod. Om du vill kan du skapa filen direkt med kudu `DebugConsole` och infoga innehållet där.

Kör säkerhets kopieringar på samma sätt som vanligt, [manuellt](#create-a-manual-backup) eller [automatiskt](#configure-automated-backups). Nu undantas alla filer och mappar som anges i `_backup.filter` från framtida säkerhets kopieringar som schemalagts eller startas manuellt. 

> [!NOTE]
> Du återställer delvis säkerhets kopior av din plats på samma sätt som du skulle [återställa en vanlig säkerhets kopia](web-sites-restore.md). Återställnings processen gör rätt sak.
> 
> När en fullständig säkerhets kopiering återställs ersätts allt innehåll på platsen med det som finns i säkerhets kopian. Om en fil finns på webbplatsen, men inte i den säkerhets kopia som tas bort. Men när en partiell säkerhets kopia återställs, är allt innehåll som finns i någon av de svartlistade katalogerna eller någon av de svartlistade filerna kvar som de är.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Så här lagras säkerhets kopior
När du har gjort en eller flera säkerhets kopior för din app visas säkerhets kopiorna på sidan **behållare** för ditt lagrings konto och din app. I lagrings kontot består varje säkerhets kopiering av en`.zip`-fil som innehåller säkerhets kopierings data och en `.xml`-fil som innehåller ett manifest av `.zip` fil innehållet. Du kan packa upp och bläddra igenom dessa filer om du vill komma åt dina säkerhets kopior utan att faktiskt utföra en återställning av appen.

Säkerhets kopieringen av databasen för appen lagras i roten i zip-filen. För en SQL-databas är detta en BACPAC-fil (inget fil namns tillägg) och kan importeras. Om du vill skapa en SQL-databas baserad på BACPAC-exporten kan du läsa [Importera en BACPAC-fil för att skapa en ny användar databas](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Att ändra någon av filerna i **websitebackups** -behållaren kan orsaka att säkerhets kopieringen blir ogiltig och därför inte återställas.
> 
> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhets kopierings hanteringen med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

Exempel finns i:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel för Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Nästa steg
Information om hur du återställer en app från en säkerhets kopia finns i [återställa en app i Azure](web-sites-restore.md). 
