---
title: Använda hanterade identiteter för att komma åt Azure SQL Database-Azure Stream Analytics
description: Den här artikeln beskriver hur du använder hanterade identiteter för att autentisera ditt Azure Stream Analytics jobb till Azure SQL DB-utdata.
author: mamccrea
ms.author: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 05/08/2020
ms.openlocfilehash: ec260c2e71d1716eb4de9ad25942f61169356dfb
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/11/2020
ms.locfileid: "94491349"
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

   Tjänstens huvud namn har samma namn som Stream Analyticss jobbet. Om namnet på ditt jobb till exempel är *MyASAJob* , är namnet på tjänstens huvud namn också *MyASAJob*.

## <a name="select-an-active-directory-admin"></a>Välj en Active Directory administratör

När du har skapat en hanterad identitet väljer du en Active Directory administratör.

1. Navigera till Azure SQL Database resurs och välj den SQL Server som databasen finns under. Du hittar SQL Server namnet bredvid *Server namnet* på resurs översikts sidan. 

1. Välj **Active Directory admin** under **Inställningar**. Välj sedan **Ange administratör**. 

   ![Sidan Active Directory administratör](./media/sql-db-output-managed-identity/active-directory-admin-page.png)
 
1. På sidan Active Directory administratör söker du efter en användare eller grupp som administratör för SQL Server och klickar på **Välj**.

   ![Lägg till Active Directory administratör](./media/sql-db-output-managed-identity/add-admin.png)

   På sidan Active Directory admin visas alla medlemmar och grupper av din Active Directory. Det går inte att välja användare eller grupper som är nedtonade eftersom de inte stöds som Azure Active Directory-administratörer. Se listan över administratörer som stöds i avsnittet **Azure Active Directory funktioner och begränsningar**   i [använda Azure Active Directory autentisering för autentisering med SQL Database eller Azure-Synapse](../azure-sql/database/authentication-aad-overview.md#azure-ad-features-and-limitations). Rollbaserad åtkomst kontroll i Azure (Azure RBAC) gäller endast för portalen och har inte spridits till SQL Server. Den valda användaren eller gruppen är också den användare som kommer att kunna skapa den **inneslutna databas användaren** i nästa avsnitt.

1. Välj **Spara** på sidan **Active Directory administratör** . Processen för att ändra admin tar några minuter.

   När du konfigurerar Azure Active Directory-administratören kan inte det nya administratörs namnet (användare eller grupp) finnas i den virtuella primära databasen som en SQL Server autentiserings användare. Om den här inställningen är tillgänglig kommer Azure Active Directory admin-installationen att Miss kopie ras och återställas, vilket anger att det redan finns en administratör (namn). Eftersom SQL Server autentiserings användare inte är en del av Azure Active Directory, vilket innebär att du kan ansluta till servern med Azure Active Directory autentisering när användaren Miss lyckas. 

## <a name="create-a-contained-database-user"></a>Skapa en innesluten databas användare

Därefter skapar du en innesluten databas användare i SQL Database som är mappad till Azure Active Directory identiteten. Den inneslutna databas användaren har ingen inloggning för den primära databasen, men den mappas till en identitet i katalogen som är associerad med databasen. Azure Active Directory identiteten kan vara ett enskilt användar konto eller en grupp. I det här fallet vill du skapa en innesluten databas användare för ditt Stream Analytics jobb. 

1. Anslut till SQL Database med SQL Server Management Studio. **Användar namnet** är en Azure Active Directory användare med behörigheten **ändra alla användare** . Administratören som du ställer in på SQL Server är ett exempel. Använd **Azure Active Directory – universell med MFA** -autentisering. 

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

1. För att Microsofts Azure Active Directory ska kunna verifiera om Stream Analytics-jobbet har åtkomst till SQL Database måste vi ge Azure Active Directory behörighet att kommunicera med databasen. Om du vill göra det går du till sidan "brand väggar och virtuella nätverk" i Azure Portal igen och aktiverar "Tillåt Azure-tjänster och-resurser för att få åtkomst till den här servern". 

   ![Brand vägg och virtuellt nätverk](./media/sql-db-output-managed-identity/allow-access.png)

## <a name="grant-stream-analytics-job-permissions"></a>Bevilja Stream Analytics jobb behörigheter

När du har skapat en innesluten databas användare och fått åtkomst till Azure-tjänster i portalen enligt beskrivningen i föregående avsnitt, har ditt Stream Analytics-jobb behörighet från hanterad identitet för att **ansluta** till din SQL Database resurs via hanterad identitet. Vi rekommenderar att du beviljar behörigheterna Välj och infoga för Stream Analyticss jobbet eftersom de behövs senare i arbets flödet Stream Analytics. Med **Select** -behörighet kan jobbet testa anslutningen till tabellen i SQL Database. **Insert** -behörigheten gör det möjligt att testa Stream Analytics från slut punkt till slut punkt när du har konfigurerat en indata och SQL Database utdata. Du kan bevilja dessa behörigheter till Stream Analytics jobb med SQL Server Management Studio. Mer information finns i GRANT (Transact-SQL)-referensen.

Om du bara vill bevilja behörighet till en viss tabell eller ett visst objekt i databasen använder du följande T-SQL-syntax och kör frågan. 

```sql
GRANT SELECT, INSERT ON OBJECT::TABLE_NAME TO ASA_JOB_NAME; 
```

Du kan också högerklicka på din SQL-databas i SQL Server Management Studio och välja **egenskaper > behörigheter**. Från menyn behörigheter kan du se Stream Analytics jobb som du har lagt till tidigare och du kan manuellt bevilja eller neka behörigheter när du ser anpassa.

## <a name="create-an-azure-sql-database-output"></a>Skapa en Azure SQL Database utdata

Nu när din hanterade identitet har kon figurer ATS är du redo att lägga till Azure SQL Database som utdata till ditt Stream Analytics-jobb.

Se till att du har skapat en tabell i SQL Database med lämpligt schema för utdata. Namnet på den här tabellen är en av de obligatoriska egenskaper som måste fyllas i när du lägger till SQL Database-utdata till Stream Analytics-jobbet. Se också till att jobbet har **valt** och **Infoga** behörigheter för att testa anslutningen och köra Stream Analytics frågor. Se avsnittet [bevilja Stream Analytics jobb behörigheter](#grant-stream-analytics-job-permissions) om du inte redan har gjort det. 

1. Gå tillbaka till Stream Analytics jobbet och gå till sidan **utdata** under **jobb sto pol Ogin**. 

1. Välj **Lägg till > SQL Database**. Välj **hanterad identitet** i list rutan autentiseringsläge i fönstret utmatnings egenskaper i SQL Database utgående mottagare.

1. Fyll i resten av egenskaperna. Mer information om hur du skapar en SQL Database utdata finns i [skapa ett SQL Database utdata med Stream Analytics](sql-database-output.md). När du är färdig väljer du **Spara**. 

## <a name="remove-managed-identity"></a>Ta bort hanterad identitet

Den hanterade identitet som skapats för ett Stream Analytics jobb tas bara bort när jobbet tas bort. Det finns inget sätt att ta bort den hanterade identiteten utan att ta bort jobbet. Om du inte längre vill använda den hanterade identiteten kan du ändra autentiseringsmetoden för utdata. Den hanterade identiteten finns kvar tills jobbet tas bort och kommer att användas om du väljer att använda hanterad identitetsautentisering igen.

## <a name="next-steps"></a>Nästa steg

* [Förstå utdata från Azure Stream Analytics](stream-analytics-define-outputs.md)
* [Azure Stream Analytics utdata till Azure SQL Database](stream-analytics-sql-output-perf.md)