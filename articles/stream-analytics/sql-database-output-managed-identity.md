---
title: Använda hanterade identiteter för att komma åt Azure SQL Database-Azure Stream Analytics
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb till Azure SQL DB-utdata.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: 70ad69c1a34f656347b0cf53b28a1c35ac6ad043
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83598243"
---
# <a name="use-managed-identities-to-access-azure-sql-database-from-an-azure-stream-analytics-job-preview"></a>Använda hanterade identiteter för att få åtkomst till Azure SQL Database från ett Azure Stream Analytics jobb (förhands granskning)

Azure Stream Analytics stöder [autentisering med hanterad identitet](../active-directory/managed-identities-azure-resources/overview.md) för Azure SQL Database utgående mottagare. Hanterade identiteter eliminerar begränsningar för användarbaserade autentiseringsmetoder, t. ex. behovet av att autentisera på grund av lösen ords ändringar eller förfallo datum för användar-token som inträffar var 90: e dag. När du tar bort behovet av att autentisera manuellt, kan dina Stream Analytics-distributioner helt automatiseras.

En hanterad identitet är ett hanterat program registrerat i Azure Active Directory som representerar ett angivet Stream Analytics jobb. Det hanterade programmet används för att autentisera till en mål resurs. Den här artikeln visar hur du aktiverar hanterad identitet för en Azure SQL Database utdata för ett Stream Analytics jobb via Azure Portal.

## <a name="prerequisites"></a>Förutsättningar

Följande krävs för den här funktionen:

- Ett Azure Stream Analytics jobb.

- En Azure SQL Database resurs.

## <a name="create-a-managed-identity"></a>Skapa en hanterad identitet

Först skapar du en hanterad identitet för ditt Azure Stream Analytics jobb.

1. Öppna Azure Stream Analytics-jobbet i [Azure Portal](https://portal.azure.com).

1. I den vänstra navigerings menyn väljer du **hanterad identitet** som finns under **Konfigurera**. Markera sedan kryss rutan bredvid **Använd tilldelad hanterad identitet** och välj **Spara**.

   ![Välj systemtilldelad hanterad identitet](./media/sql-db-output-managed-identity/system-assigned-managed-identity.png)


   Ett tjänst huvud namn för Stream Analytics jobbets identitet skapas i Azure Active Directory. Livs cykeln för den nyligen skapade identiteten hanteras av Azure. När Stream Analytics jobb tas bort, tas den tillhör ande identiteten (dvs. tjänstens huvud namn) automatiskt bort av Azure. 

1. När du sparar konfigurationen visas objekt-ID: t (OID) för tjänstens huvud namn som huvud-ID: t enligt nedan: 

   ![Objekt-ID som visas som huvud-ID](./media/sql-db-output-managed-identity/principal-id.png)

   Tjänstens huvud namn har samma namn som Stream Analyticss jobbet. Om namnet på ditt jobb till exempel är *MyASAJob*, är namnet på tjänstens huvud namn också *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Välj en Active Directory administratör

När du har skapat en hanterad identitet väljer du en Active Directory administratör.

1. Navigera till Azure SQL Database resurs och välj den SQL Server som databasen finns under. Du hittar SQL Server namnet bredvid *Server namnet* på resurs översikts sidan. 

1. Välj **Active Directory admin** under **Inställningar**. Välj sedan **Ange administratör**. 

   ![Sidan Active Directory administratör](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. På sidan Active Directory administratör söker du efter en användare eller grupp som administratör för SQL Server och klickar på **Välj**.  

   ![Lägg till Active Directory administratör](./media/sql-db-output-managed-identity/add-admin.png)

1. Välj **Spara** på sidan **Active Directory administratör** . Processen för att ändra admin tar några minuter.  

## <a name="create-a-database-user"></a>Skapa en databasanvändare

Därefter skapar du en innesluten databas användare i SQL Database som är mappad till Azure Active Directory identiteten. Den inneslutna databas användaren har ingen inloggning för huvud databasen, men den mappas till en identitet i katalogen som är associerad med databasen. Azure Active Directory identiteten kan vara ett enskilt användar konto eller en grupp. I det här fallet vill du skapa en innesluten databas användare för ditt Stream Analytics jobb. 

1. Anslut till SQL-databasen med hjälp av SQL Server Management Studio. **Användar namnet** är en Azure Active Directory användare med behörigheten **ändra alla användare** . Administratören som du ställer in på SQL Server är ett exempel. Använd **Azure Active Directory – universell med MFA** -autentisering. 

   ![Anslut till SQL Server](./media/sql-db-output-managed-identity/connect-sql-server.png)

   Server namnet `<SQL Server name>.database.windows.net` kan vara ett annat i olika regioner. Till exempel bör regionen Kina använda `<SQL Server name>.database.chinacloudapi.cn` .
 
   Du kan ange en speciell SQL Database genom att gå till **alternativ > anslutnings egenskaper > ansluta till databasen**.  

   ![SQL Server anslutnings egenskaper](./media/sql-db-output-managed-identity/sql-server-connection-properties.png)

1. När du ansluter för första gången kan du stöta på följande fönster:

   ![Fönstret ny brand Väggs regel](./media/sql-db-output-managed-identity/new-firewall-rule.png)

   1. I så fall, går du till SQL Server resursen på Azure Portal. Öppna sidan **brand väggar och virtuellt nätverk** under avsnittet **säkerhet** . 
   1. Lägg till en ny regel med valfritt regel namn.
   1. Använd IP *-adressen från fönstret* **ny brand Väggs regel** för *Start-IP*.
   1. Använd *till* -IP-adress från fönstret **ny regel för brand vägg** för *slut-IP*. 
   1. Välj **Spara** och försök att ansluta från SQL Server Management Studio igen. 

1. När du är ansluten skapar du den inneslutna databas användaren. Följande SQL-kommando skapar en innesluten databas användare som har samma namn som ditt Stream Analytics-jobb. Se till att inkludera hakparenteser runt *ASA_JOB_NAME*. Använd följande T-SQL-syntax och kör frågan. 

   ```sql
   CREATE USER [ASA_JOB_NAME] FROM EXTERNAL PROVIDER; 
   ```

## <a name="grant-stream-analytics-job-permissions"></a>Bevilja Stream Analytics jobb behörigheter

Stream Analyticss jobbet har behörighet från hanterad identitet för att **ansluta** till din SQL Database-resurs. Förmodligen är det effektivt att tillåta att Stream Analytics jobb kör kommandon som **Select**. Du kan bevilja dessa behörigheter till Stream Analytics jobb med SQL Server Management Studio. Mer information finns i [Grant (Transact-SQL)-](https://docs.microsoft.com/sql/t-sql/statements/grant-transact-sql?view=sql-server-ver15) referensen.

Du kan också högerklicka på din SQL-databas i SQL Server Management Studio och välja **egenskaper > behörigheter**. Från menyn behörigheter kan du se Stream Analytics jobb som du har lagt till tidigare och du kan manuellt bevilja eller neka behörigheter när du ser anpassa.

## <a name="create-an-azure-sql-database-output"></a>Skapa en Azure SQL Database utdata

Nu när din hanterade identitet har kon figurer ATS är du redo att lägga till Azure SQL Database som utdata till ditt Stream Analytics-jobb.

1. Gå tillbaka till Stream Analytics jobbet och gå till sidan **utdata** under **jobb sto pol Ogin**. 

1. Välj **Lägg till > SQL Database**. Välj **hanterad identitet** i list rutan autentiseringsläge i fönstret utmatnings egenskaper i SQL Database utgående mottagare.

1. Fyll i resten av egenskaperna. Mer information om hur du skapar en SQL Database utdata finns i [skapa ett SQL Database utdata med Stream Analytics](stream-analytics-define-outputs.md#sql-database). När du är färdig väljer du **Spara**. 

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md)
