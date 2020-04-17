---
title: Säkerhetskopiera en app
description: Lär dig hur du skapar säkerhetskopior av dina appar i Azure App Service. Kör manuella eller schemalagda säkerhetskopior. Anpassa säkerhetskopior genom att inkludera den bifogade databasen.
ms.assetid: 6223b6bd-84ec-48df-943f-461d84605694
ms.topic: article
ms.date: 10/16/2019
ms.custom: seodec18
ms.openlocfilehash: b812ae10b3462dbeff05c8a67e7ebb725281e7e8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81535765"
---
# <a name="back-up-your-app-in-azure"></a>Säkerhetskopiera din app i Azure
Med funktionen Säkerhetskopiering och återställning i [Azure App Service](overview.md) kan du enkelt skapa appsäkerhetskopior manuellt eller enligt ett schema. Du kan konfigurera säkerhetskopiorna så att de behålls upp till en obestämd tid. Du kan återställa appen till en ögonblicksbild av ett tidigare tillstånd genom att skriva över den befintliga appen eller återställa till en annan app.

Information om hur du återställer en app från säkerhetskopiering finns [i Återställa en app i Azure](web-sites-restore.md).

<a name="whatsbackedup"></a>

## <a name="what-gets-backed-up"></a>Vad får backas upp
Apptjänsten kan säkerhetskopiera följande information till ett Azure-lagringskonto och en behållare som du har konfigurerat din app för användning. 

* Appkonfiguration
* Filinnehåll
* Databas som är ansluten till appen

Följande databaslösningar stöds med säkerhetskopieringsfunktionen: 

- [SQL Database](https://azure.microsoft.com/services/sql-database/)
- [Azure Database for MySQL](https://azure.microsoft.com/services/mysql)
- [Azure Database for PostgreSQL](https://azure.microsoft.com/services/postgresql)
- [MySQL i appen](https://azure.microsoft.com/blog/mysql-in-app-preview-app-service/)
 

> [!NOTE]
> Varje säkerhetskopia är en fullständig offlinekopia av appen, inte en inkrementell uppdatering.
>

<a name="requirements"></a>

## <a name="requirements-and-restrictions"></a>Krav och begränsningar
* Funktionen Säkerhetskopiering och återställning kräver att App Service-planen finns på **standardnivån** eller **Premium-nivån.** Mer information om hur du skalar apptjänstplanen så att den använder en högre nivå finns [i Skala upp en app i Azure](manage-scale-up.md). **Premium-nivån** tillåter ett större antal dagliga säkerhetskopieringar än **standardnivå.**
* Du behöver ett Azure-lagringskonto och en behållare i samma prenumeration som appen som du vill säkerhetskopiera. Mer information om Azure-lagringskonton finns i [Översikt över Azure storage-konto](https://docs.microsoft.com/azure/storage/common/storage-account-overview).
* Säkerhetskopior kan vara upp till 10 GB app- och databasinnehåll. Om säkerhetskopieringsstorleken överskrider den här gränsen får du ett felmeddelande.
* Säkerhetskopieringar av TLS-aktiverade Azure Database för MySQL stöds inte. Om en säkerhetskopia har konfigurerats får du misslyckade säkerhetskopior.
* Säkerhetskopior av TLS-aktiverad Azure-databas för PostgreSQL stöds inte. Om en säkerhetskopia har konfigurerats får du misslyckade säkerhetskopior.
* MySQL-databaser i appen säkerhetskopieras automatiskt utan någon konfiguration. Om du gör manuellt inställningarna för MySQL-databaser i appen, till exempel lägga till anslutningssträngar, kanske säkerhetskopiorna inte fungerar korrekt.
* Det går inte att använda ett brandväggsaktiverat lagringskonto som mål för dina säkerhetskopior. Om en säkerhetskopia har konfigurerats får du misslyckade säkerhetskopior.


<a name="manualbackup"></a>

## <a name="create-a-manual-backup"></a>Skapa en manuell säkerhetskopia
1. I [Azure-portalen](https://portal.azure.com)väljer du **Säkerhetskopior**till appens sida . Sidan **Säkerhetskopior** visas.

    ![Sidan Säkerhetskopior](./media/manage-backup/access-backup-page.png)

    > [!NOTE]
    > Om följande meddelande visas klickar du på det för att uppgradera appserviceplanen innan du kan fortsätta med säkerhetskopior.
    > Mer information finns [i Skala upp en app i Azure](manage-scale-up.md).
    > ![Välj lagringskonto](./media/manage-backup/upgrade-plan.png)
    > 
    > 

2. På sidan **Säkerhetskopiering** har du **inte konfigurerat Säkerhetskopiering. Klicka här om du vill konfigurera säkerhetskopiering för appen**.

    ![Klicka på Konfigurera](./media/manage-backup/configure-start.png)

3. På sidan **Konfiguration av säkerhetskopiering** klickar du på **Lagring som inte är konfigurerat** för att konfigurera ett lagringskonto.

    ![Välj lagringskonto](./media/manage-backup/configure-storage.png)

4. Välj ditt mål för säkerhetskopiering genom att välja ett **lagringskonto** och **en behållare**. Lagringskontot måste tillhöra samma prenumeration som appen som du vill säkerhetskopiera. Om du vill kan du skapa ett nytt lagringskonto eller en ny behållare på respektive sida. När du är klar klickar du på **Välj**.

5. På sidan **Konfiguration av säkerhetskopiering** som fortfarande är öppen kan du konfigurera **säkerhetskopieringsdatabasen**och sedan välja de databaser som du vill inkludera i säkerhetskopiorna (SQL-databasen eller MySQL) och sedan klicka på **OK**.

    ![Välj lagringskonto](./media/manage-backup/configure-database.png)

    > [!NOTE]
    > För att en databas ska visas i den här listan måste dess anslutningssträng finnas i avsnittet **Anslutningssträngar** på sidan **Programinställningar** för appen. 
    >
    > MySQL-databaser i appen säkerhetskopieras automatiskt utan någon konfiguration. Om du gör manuellt inställningarna för MySQL-databaser i appen, till exempel lägga till anslutningssträngar, kanske säkerhetskopiorna inte fungerar korrekt.
    > 
    > 

6. Klicka på Spara på sidan **Konfiguration för säkerhetskopiering.** **Save**
7. Klicka på **Säkerhetskopiering**på sidan **Säkerhetskopior.**

    ![Knappen BackUpNow](./media/manage-backup/manual-backup.png)

    Du ser ett förloppsmeddelande under säkerhetskopieringsprocessen.

När lagringskontot och behållaren har konfigurerats kan du när som helst initiera en manuell säkerhetskopiering.

<a name="automatedbackups"></a>

## <a name="configure-automated-backups"></a>Konfigurera automatiska säkerhetskopieringar
1. På sidan **Konfiguration av säkerhetskopiering** ställer du in **Schemalagd säkerhetskopiering** **till På**. 

    ![Aktivera automatiska säkerhetskopieringar](./media/manage-backup/scheduled-backup.png)

2. Konfigurera säkerhetskopieringsschemat efter behov och välj **OK**.

<a name="partialbackups"></a>

## <a name="configure-partial-backups"></a>Konfigurera partiella säkerhetskopior
Ibland vill du inte säkerhetskopiera allt i din app. Några exempel:

* Du [ställer in veckovisa säkerhetskopior](#configure-automated-backups) av appen som innehåller statiskt innehåll som aldrig ändras, till exempel gamla blogginlägg eller bilder.
* Din app har över 10 GB innehåll (det är det högsta belopp du kan säkerhetskopiera åt gången).
* Du vill inte säkerhetskopiera loggfilerna.

Partiella säkerhetskopior kan du välja exakt vilka filer du vill säkerhetskopiera.

> [!NOTE]
> Enskilda databaser i säkerhetskopian kan vara 4GB max men den totala maxstorleken på säkerhetskopian är 10GB

### <a name="exclude-files-from-your-backup"></a>Exkludera filer från säkerhetskopian
Anta att du har en app som innehåller loggfiler och statiska bilder som har säkerhetskopierats en gång och inte kommer att ändras. I sådana fall kan du utesluta att dessa mappar och filer lagras i framtida säkerhetskopior. Om du vill utesluta filer och mappar `_backup.filter` från `D:\home\site\wwwroot` säkerhetskopiorna skapar du en fil i mappen i appen. Ange listan över filer och mappar som du vill utesluta i den här filen. 

Du kan komma åt dina `https://<app-name>.scm.azurewebsites.net/DebugConsole`filer genom att navigera till . Logga in på ditt Azure-konto om du uppmanas att göra det.

Identifiera de mappar som du vill utesluta från dina säkerhetskopior. Du vill till exempel filtrera bort den markerade mappen och filerna.

![Mappen Bilder](./media/manage-backup/kudu-images.png)

Skapa en `_backup.filter` fil som anropas och placera föregående `D:\home`lista i filen, men ta bort . Lista en katalog eller fil per rad. Så innehållet i filen bör vara:

 ```
\site\wwwroot\Images\brand.png
\site\wwwroot\Images\2014
\site\wwwroot\Images\2013
```

Ladda `_backup.filter` upp `D:\home\site\wwwroot\` filen till katalogen på din webbplats med [ftp](deploy-ftp.md) eller någon annan metod. Om du vill kan du skapa filen `DebugConsole` direkt med Kudu och infoga innehållet där.

Kör säkerhetskopior på samma sätt som du normalt skulle göra det, [manuellt](#create-a-manual-backup) eller [automatiskt](#configure-automated-backups). Nu utesluts alla filer och mappar `_backup.filter` som anges i från framtida säkerhetskopior som schemalagts eller initierats manuellt. 

> [!NOTE]
> Du återställer partiella säkerhetskopior av webbplatsen på samma sätt som du skulle [återställa en vanlig säkerhetskopia](web-sites-restore.md). Återställningsprocessen gör det rätta.
> 
> När en fullständig säkerhetskopia återställs ersätts allt innehåll på webbplatsen med det som finns i säkerhetskopian. Om en fil finns på webbplatsen, men inte i säkerhetskopian tas den bort. Men när en partiell säkerhetskopiering återställs lämnas allt innehåll som finns i en av de svartlistade katalogerna, eller någon svartlistad fil, som det är.
> 


<a name="aboutbackups"></a>

## <a name="how-backups-are-stored"></a>Så här lagras säkerhetskopior
När du har gjort en eller flera säkerhetskopior för din app visas säkerhetskopiorna på sidan **Behållare** i ditt lagringskonto och din app. I lagringskontot består varje`.zip` säkerhetskopia av en fil `.xml` som innehåller säkerhetskopierade data `.zip` och en fil som innehåller ett manifest av filinnehållet. Du kan packa upp och bläddra bland dessa filer om du vill komma åt dina säkerhetskopior utan att utföra en appåterställning.

Säkerhetskopieringen av databasen för appen lagras i roten till ZIP-filen. För en SQL-databas är detta en BACPAC-fil (inget filnamnstillägg) och kan importeras. Information om hur du skapar en SQL-databas baserad på BACPAC-exporten finns i [Importera en BACPAC-fil för att skapa en ny användardatabas](https://technet.microsoft.com/library/hh710052.aspx).

> [!WARNING]
> Om du ändrar någon av filerna i behållaren för **webbplatsbackups** kan säkerhetskopian bli ogiltig och därför inte återställas.
> 
> 

## <a name="automate-with-scripts"></a>Automatisera med skript

Du kan automatisera säkerhetskopieringshantering med skript med hjälp av [Azure CLI](/cli/azure/install-azure-cli) eller [Azure PowerShell](/powershell/azure/overview).

För exempel, se:

- [Azure CLI-exempel](samples-cli.md)
- [Exempel på Azure PowerShell](samples-powershell.md)

<a name="nextsteps"></a>

## <a name="next-steps"></a>Efterföljande moment
Information om hur du återställer en app från en säkerhetskopia finns [i Återställa en app i Azure](web-sites-restore.md). 
