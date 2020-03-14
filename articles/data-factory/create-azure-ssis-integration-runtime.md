---
title: Skapa en Azure-SSIS integration runtime i Azure Data Factory
description: Lär dig hur du skapar en Azure-SSIS integration runtime i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/01/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 4146191453e7c7a589dacc41345a36a29a540de5
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260779"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa en Azure-SSIS integration runtime i Azure Data Factory

Den här artikeln innehåller steg för att få en Azure-SQL Server Integration Services (SSIS) integration Runtime (IR) i Azure Data Factory. En Azure-SSIS IR stöder:

- Köra paket som distribuerats till SSIS-katalogen (SSISDB) som finns på en Azure SQL Database-Server eller en hanterad instans (projekt distributions modell).
- Köra paket som distribuerats i fil system, fil resurser eller Azure Files (paket distributions modell). 

När en Azure-SSIS IR har tillhandahållits kan du använda välbekanta verktyg för att distribuera och köra dina paket i Azure. Dessa verktyg omfattar SQL Server Data Tools (SSDT), SQL Server Management Studio (SSMS) och kommando rads verktyg som `dtinstall`, `dtutil`och `dtexec`.

I självstudierna för [etablering Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) visas hur du skapar en Azure-SSIS IR via Azure Portal eller Data Factory app. I kursen visas också hur du kan använda en Azure SQL Database-Server eller en hanterad instans som värd för SSISDB. Den här artikeln är utökad i självstudien och beskriver hur du gör dessa valfria uppgifter:

- Använd en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB. Som krav måste du konfigurera virtuella nätverks behörigheter och inställningar för din Azure-SSIS IR att ansluta till ett virtuellt nätverk.

- Använd Azure Active Directory (Azure AD)-autentisering med den hanterade identiteten för din data fabrik för att ansluta till en Azure SQL Database Server eller en hanterad instans. Som en förutsättning måste du lägga till den hanterade identiteten för din data fabrik som en databas användare som kan skapa en SSISDB-instans.

- Anslut Azure-SSIS IR till ett virtuellt nätverk eller konfigurera en lokal IR as-proxy för din Azure-SSIS IR för att komma åt data lokalt.

Den här artikeln visar hur du etablerar en Azure-SSIS IR med hjälp av Azure Portal, Azure PowerShell och en Azure Resource Manager mall.

## <a name="prerequisites"></a>Förutsättningar

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte redan har en prenumeration kan du skapa ett [kostnads fritt utvärderings](https://azure.microsoft.com/pricing/free-trial/) konto.

- **Azure SQL Database Server eller hanterad instans (valfritt)** . Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Data Factory skapar i sin tur en SSISDB-instans på den här databas servern. 

  Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan integrerings körningens Skriv körnings loggar i SSISDB utan att korsa Azure-regioner.

  Tänk på följande saker:

  - Baserat på den valda databas servern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Det kan vara tillgängligt i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i avsnittet [jämföra en Azure SQL Database enkel databas, elastisk pool och hanterad instans](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) i den här artikeln. 
  
    Om du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en IR med egen värd, måste du ansluta din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [ansluta ett Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Den här inställningen gäller inte när du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med hjälp av PowerShell, se [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Lägg till IP-adressen för klient datorn eller ett intervall med IP-adresser som innehåller IP-adressen för klient datorn till klientens IP-adress lista i brand Väggs inställningarna för databas servern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).

  - Du kan ansluta till databas servern genom att använda SQL-autentisering med dina autentiseringsuppgifter för Server administratören eller genom att använda Azure AD-autentisering med den hanterade identiteten för din data fabrik. För den senare måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till databas servern. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Bekräfta att databas servern inte har någon SSISDB-instans redan. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB-instans.

- **Azure Resource Manager virtuellt nätverk (valfritt)** . Du måste ha ett Azure Resource Manager virtuellt nätverk om minst ett av följande villkor är uppfyllt:
  - Du är värd för SSISDB på en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt.
  - Du vill ansluta till lokala data lager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera ett IR med egen värd.

- **Azure PowerShell (valfritt)** . Följ anvisningarna i [så här installerar och konfigurerar du Azure PowerShell](/powershell/azure/install-az-ps), om du vill köra ett PowerShell-skript för att etablera Azure-SSIS IR.

### <a name="regional-support"></a>Regional support

En lista över Azure-regioner där Data Factory och en Azure-SSIS IR är tillgängliga finns i [Data Factory och SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Jämförelse av en SQL Database enskild databas, elastisk pool och hanterad instans

I följande tabell jämförs vissa funktioner i en Azure SQL Database Server och en hanterad instans som relaterar till Azure-SSIR IR:

| Funktion | Enkel databas/elastisk pool| Hanterad instans |
|---------|--------------|------------------|
| **Tids** | SQL Server Agent är inte tillgänglig.<br/><br/>Se [Schemalägga en paket körning i en Data Factory pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Agenten för den hanterade instansen är tillgänglig. |
| **Autentisering** | Du kan skapa en SSISDB-instans med en innesluten databas användare som representerar en Azure AD-grupp med den hanterade identiteten för din data fabrik som medlem i **db_owner** -rollen.<br/><br/>Se [Aktivera Azure AD-autentisering för att skapa en SSISDB-instans på en Azure SQL Database-Server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Du kan skapa en SSISDB-instans med en innesluten databas användare som representerar data fabrikens hanterade identitet. <br/><br/>Se [Aktivera Azure AD-autentisering för att skapa en SSISDB-instans i en Azure SQL Database Hanterad instans](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Tjänstenivå** | När du skapar en Azure-SSIS IR med din Azure SQL Database-Server kan du välja tjänst nivå för SSISDB. Det finns flera tjänst nivåer. | När du skapar en Azure-SSIS IR med din hanterade instans kan du inte välja tjänst nivå för SSISDB. Alla databaser i den hanterade instansen delar samma resurs som allokeras till den instansen. |
| **Virtuellt nätverk** | Din Azure-SSIS IR kan ansluta till ett Azure Resource Manager virtuellt nätverk om du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster. | Din Azure-SSIS IR kan ansluta till ett Azure Resource Manager virtuellt nätverk om du använder en hanterad instans med privat slut punkt. Det virtuella nätverket krävs om du inte aktiverar en offentlig slut punkt för din hanterade instans.<br/><br/>Om du ansluter din Azure-SSIS IR till samma virtuella nätverk som din hanterade instans kontrollerar du att Azure-SSIS IR finns i ett annat undernät än den hanterade instansen. Om du ansluter din Azure-SSIS IR till ett annat virtuellt nätverk från din hanterade instans, rekommenderar vi antingen en virtuell nätverks-peering eller en anslutning från nätverk till nätverk. Se [ansluta ditt program till en Azure SQL Database Hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuerade transaktioner** | Den här funktionen stöds via elastiska transaktioner. Microsoft koordinator för distribuerad transaktion-transaktioner (MSDTC) stöds inte. Om dina SSIS-paket använder MSDTC för att koordinera distribuerade transaktioner bör du överväga att migrera till elastiska transaktioner för Azure SQL Database. Mer information finns i [distribuerade transaktioner i moln databaser](../sql-database/sql-database-elastic-transactions-overview.md). | Stöds inte. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Använd Azure Portal för att skapa en integration runtime

I det här avsnittet använder du Azure Portal, särskilt användar gränssnittet för Data Factory (UI) eller appen, för att skapa en Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa din data fabrik via Azure Portal följer du de stegvisa anvisningarna i [skapa en data fabrik via användar gränssnittet](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Välj **Fäst på instrument panelen** när du gör det, för att tillåta snabb åtkomst efter att den har skapats. 

När data fabriken har skapats öppnar du dess översikts sida i Azure Portal. Välj panelen **författare & Monitor** för att öppna sidan för att **komma igång** på en separat flik. Där kan du fortsätta att skapa Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**.

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. I avsnittet **allmänna inställningar** i **integration runtime inställnings** panelen utför du följande steg.

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. För **Namn**, ange namnet på din integreringsruntime.

   1. För **beskrivning** anger du en beskrivning av integrationskörningen.

   1. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB.

   1. För **Node-storlek**väljer du nodens storlek i integration runtime-klustret. Endast nodstorlekar som stöds visas. Välj en stor Node-storlek (skala upp) om du vill köra många beräknings intensiva eller minnes intensiva paket.

   1. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med många noder (skala ut) om du vill köra många paket parallellt.

   1. För **utgåva/licens**väljer du SQL Servers versionen för integration Runtime: standard eller Enterprise. Välj företag om du vill använda avancerade funktioner i integrerings körningen.

   1. För **Spara pengar**väljer du alternativet Azure Hybrid-förmån för integration Runtime: **Ja** eller **Nej**. Välj **Ja** om du vill ta med din egen SQL Server-licens med Software Assurance för att dra nytta av kostnads besparingar med hybrid användning.

   1. Välj **Nästa**.

1. Utför följande steg i avsnittet **SQL-inställningar** .

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Markera kryss rutan **skapa SSIS-katalog (SSISDB) som hanteras av Azure SQL Database Server/hanterad instans för att lagra dina projekt/paket/miljöer/körnings loggar** för att välja distributions modell för paket som ska köras på din Azure-SSIS IR. Du väljer antingen projekt distributions modellen där paket distribueras till SSISDB som finns på databas servern, eller distributions modellen för paketet där paket distribueras i fil system, fil resurser eller Azure Files. 
    
      Om du markerar kryss rutan måste du ta med din egen databas server som värd för SSISDB-instansen som vi ska skapa och hantera för din räkning.
   
      1. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

      1. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

      1. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. 
    
         Baserat på den valda databas servern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Det kan vara tillgängligt i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Information om hur du väljer vilken typ av databas server som ska vara värd för SSISDB finns i avsnittet [jämföra en Azure SQL Database enkel databas, elastisk pool och hanterad instans](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) i den här artikeln. 
    
         Om du väljer en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en IR med egen värd, måste du ansluta din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [ansluta ett Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Markera kryss rutan **Använd AAD-autentisering med den hanterade identiteten för din ADF** för att välja autentiseringsmetod för din databas server som värd för SSISDB. Du väljer antingen SQL-autentisering eller Azure AD-autentisering med den hanterade identiteten för din data fabrik. 
    
         Om du markerar kryss rutan måste du lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till din databas server. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. För **Administratörs användar namn**anger du användar namnet för SQL-autentisering för din databas server som värd för SSISDB. 

      1. För **Administratörs lösen ord**anger du lösen ordet för SQL-autentisering för din databas server som värd för SSISDB. 

      1. För **tjänst nivån katalog databas**väljer du tjänst nivå för din databas server som värd för SSISDB. Välj nivån Basic, standard eller Premium eller Välj ett namn för elastisk pool. 

      1. Välj **test anslutning**. Om testet lyckas väljer du **Nästa**. 

1. I avsnittet **Avancerade inställningar** utför du följande steg.

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. För **maximala parallella körningar per nod**väljer du det maximala antalet paket som ska köras samtidigt per nod i integration runtime-klustret. Endast paketnummer som stöds visas. Välj ett lågt nummer om du vill använda mer än en kärna för att köra ett enda stort paket som är beräknings-eller minnes krävande. Välj ett högt värde om du vill köra ett eller flera små paket i en enda kärna.

   1. Markera kryss rutan **anpassa Azure-SSIS integration runtime med ytterligare system konfigurationer/komponent installationer** för att välja om du vill lägga till standard-/Express-anpassade inställningar på din Azure-SSIS IR. Mer information finns i [anpassad installation för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Om du markerar kryss rutan utför du följande steg.

      ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. För den **anpassade SAS-URI: n för installations behållaren**anger du SAS-URI för din behållare där du lagrar skript och tillhör ande filer för standard anpassade inställningar.

      1. För **Express-anpassad installation**väljer du **ny** för att öppna panelen **Lägg till anpassad installation för Express** och väljer sedan någon typ i list rutan **snabb anpassad Installations typ** , t. ex. **Kör cmdkey-kommando**, **Lägg till miljövariabel**, **Installera licensierad komponent**osv.

         Om du väljer **komponenten installera licensierad komponent** kan du sedan välja integrerade komponenter från våra ISV-partners under List rutan **komponent namn** och om det behövs, ange den produkt licens nyckel som du har köpt från dem i fältet **licens nyckel** .
  
         De anpassade installations program som har lagts till visas i avsnittet **Avancerade inställningar** . Du kan ta bort dem genom att markera kryss rutorna och sedan **ta bort**.

   1. Välj alternativet **Välj ett VNet för Azure-SSIS integration runtime att ansluta till, Tillåt ADF att skapa vissa nätverks resurser och om du vill ta med egna statiska offentliga IP-adresser** kan du välja om du vill ansluta till integration runtime till ett virtuellt nätverk. 

      Välj den om du använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data (det vill säga att du har lokala data källor eller mål i dina SSIS-paket) utan att konfigurera en lokal IR. Mer information finns i [ansluta Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Om du markerar kryss rutan utför du följande steg.

      ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. För **prenumeration**väljer du den Azure-prenumeration som har ditt virtuella nätverk.

      1. För **plats**är samma plats för integrerings körningen markerad.

      1. I **typ**väljer du typ av virtuellt nätverk: klassisk eller Azure Resource Manager. Vi rekommenderar att du väljer ett Azure Resource Manager virtuellt nätverk, eftersom klassiska virtuella nätverk snart kommer att bli föråldrade.

      1. För **VNet-namn**väljer du namnet på ditt virtuella nätverk. Det bör vara samma som används för din Azure SQL Database-Server med tjänst slut punkter för virtuella nätverk eller en hanterad instans med privat slut punkt som värd för SSISDB. Eller bör vara samma som den som är ansluten till ditt lokala nätverk. Annars kan det vara ett virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

      1. För **under näts namn**väljer du namnet på under nätet för ditt virtuella nätverk. Det bör vara samma som används för din Azure SQL Database-Server med tjänst slut punkter för virtuella nätverk som värd för SSISDB. Eller bör vara ett annat undernät än det som används för din hanterade instans med privat slut punkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska IP-adresser för Azure-SSIS IR.

      1. Markera kryss rutan **Lägg till statiska offentliga IP-adresser för din Azure-SSIS integration runtime** för att välja om du vill ta med egna statiska IP-adresser för Azure-SSIS IR, så att du kan tillåta dem i brand väggen för dina data källor.

         Om du markerar kryss rutan utför du följande steg.

         1. För **första statiska offentliga IP-adress**väljer du den första statiska offentliga IP-adress som uppfyller kraven för din Azure-SSIS IR. Om du inte har några, klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure Portal och klickar sedan på uppdatera-knappen här, så att du kan välja dem.
      
         1. För **andra statiska offentliga IP-adresser**väljer du den andra statiska offentliga IP-adress som uppfyller kraven för din Azure-SSIS IR. Om du inte har några, klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure Portal och klickar sedan på uppdatera-knappen här, så att du kan välja dem.

   1. Markera kryss rutan **Konfigurera egen värd integration runtime som en proxy för din Azure-SSIS integration runtime** för att välja om du vill konfigurera en egen IR as-proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en egen värd för IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Om du markerar kryss rutan utför du följande steg.

      ![Avancerade inställningar med en egen värd-IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. För **integration runtime med egen värd**väljer du din befintliga IR som en proxy för Azure-SSIS IR.

      1. För den **länkade lagrings tjänsten för lagring**väljer du den befintliga Azure Blob Storage-länkade tjänsten eller skapar en ny för mellanlagring.

      1. För **mellanlagringsplatsen**anger du en BLOB-behållare i det valda Azure Blob Storage-kontot eller låter det vara tomt om du vill använda en standard för mellanlagring.

   1. Välj **VNet-validering** > **Fortsätt**. 

1. I avsnittet **Sammanfattning** granskar du alla inställningar för etablering, bok märken rekommenderade dokumentations länkar och väljer **Slutför** för att starta skapandet av integration Runtime.

   > [!NOTE]
   > Om du exkluderar en anpassad konfigurations tid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter innan Azure-SSIS IR ansluter till ett virtuellt nätverk.
   >
   > Om du använder SSISDB kommer tjänsten Data Factory att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, förutom de data källor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen.

   ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats.

   ![Azure SSIS IR-åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen

1. I Azure Data Factory användar gränssnitt växlar du till fliken **Redigera** och väljer **anslutningar**. Växla sedan till fliken **integrerings körningar** för att visa befintliga integrerings körningar i din data fabrik.

   ![Visa befintlig IRs](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **ny** för att skapa en ny Azure-SSIS IR.

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. I panelen **integration runtime installation** väljer du de **befintliga SSIS-paketen lyft och Shift som ska köras på Azure** -panelen och väljer sedan **Nästa**.

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. De återstående stegen för att skapa en Azure-SSIS IR finns i avsnittet [etablera en Azure SSIS integration runtime](#provision-an-azure-ssis-integration-runtime) .

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Använd Azure PowerShell för att skapa en integration runtime

I det här avsnittet använder du Azure PowerShell för att skapa en Azure-SSIS IR.

### <a name="create-variables"></a>Skapa variabler

Kopiera och klistra in följande skript. Ange värden för variablerna. 

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to 4 parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 
```

### <a name="sign-in-and-select-a-subscription"></a>Logga in och välj en prenumeration

Lägg till följande skript för att logga in och välj din Azure-prenumeration.

```powershell
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName
```

### <a name="validate-the-connection-to-database-server"></a>Verifiera anslutningen till databas servern

Lägg till följande skript för att verifiera din Azure SQL Database Server eller en hanterad instans.

```powershell
# Validate only if you use SSISDB and you don't use virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}
```

### <a name="configure-the-virtual-network"></a>Konfigurera det virtuella nätverket

Lägg till följande skript för att automatiskt konfigurera behörigheter och inställningar för virtuella nätverk för Azure-SSIS integration runtime att ansluta till.

```powershell
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}
```

### <a name="create-a-resource-group"></a>Skapa en resursgrupp

Skapa en [Azure-resurs grupp](../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup) . En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

Om resursgruppen redan finns ska du inte kopiera den här koden i skriptet. 

```powershell
New-AzResourceGroup -Location $DataFactoryLocation -Name $ResourceGroupName
```

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Skapa en datafabrik genom att köra följande kommandon.

```powershell
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName
```

### <a name="create-an-integration-runtime"></a>Skapa Integration Runtime

Kör följande kommandon för att skapa en Azure-SSIS integration runtime som kör SSIS-paket i Azure.

Om du inte använder SSISDB kan du utelämna parametrarna `CatalogServerEndpoint`, `CatalogPricingTier`och `CatalogAdminCredential`.

Om du inte använder en Azure SQL Database-Server med IP-brandväggs regler/slut punkter för virtuella nätverks tjänster eller en hanterad instans med privat slut punkt som värd för SSISDB, eller om du behöver åtkomst till lokala data, kan du utelämna parametrarna `VNetId` och `Subnet` eller skicka tomma värden. Du kan också utelämna dem om du konfigurerar en lokal IR as-proxy för din Azure-SSIS IR att komma åt data lokalt. Annars kan du inte utelämna dem och måste skicka giltiga värden från konfigurationen av det virtuella nätverket. Mer information finns i [ansluta ett Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Om du använder hanterad instans som värd för SSISDB kan du utelämna parametern `CatalogPricingTier` eller skicka ett tomt värde för den. Annars kan du inte utelämna det och måste skicka ett giltigt värde från listan över pris nivåer som stöds för Azure SQL Database. Mer information finns i [SQL Database resurs gränser](../sql-database/sql-database-resource-limits.md).

Om du använder Azure AD-autentisering med den hanterade identiteten för din data fabrik för att ansluta till databas servern kan du utelämna parametern `CatalogAdminCredential`. Men du måste lägga till den hanterade identiteten för din data fabrik i en Azure AD-grupp med åtkomst behörighet till databas servern. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Annars kan du inte utelämna det och måste skicka ett giltigt objekt som skapats av Server administratörens användar namn och lösen ord för SQL-autentisering.

```powershell
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add the CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}
```

### <a name="start-the-integration-runtime"></a>Starta integrerings körningen

Kör följande kommandon för att starta integrerings körningen för Azure-SSIS.

```powershell
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

> [!NOTE]
> Om du exkluderar en anpassad konfigurations tid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter innan Azure-SSIS IR ansluter till ett virtuellt nätverk.
>
> Om du använder SSISDB kommer tjänsten Data Factory att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, förutom de data källor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Fullständigt skript

Här är det fullständiga skriptet som skapar en Azure-SSIS integration Runtime.

```powershell
### Azure Data Factory info
# If your input contains a PSH special character like "$", precede it with the escape character "`" - for example, "`$"
$SubscriptionName = "[your Azure subscription name]"
$ResourceGroupName = "[your Azure resource group name]"
# Data factory name - must be globally unique
$DataFactoryName = "[your data factory name]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$DataFactoryLocation = "EastUS"

### Azure-SSIS integration runtime info - This is a Data Factory compute resource for running SSIS packages.
$AzureSSISName = "[your Azure-SSIS IR name]"
$AzureSSISDescription = "[your Azure-SSIS IR description]"
# For supported regions, see https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all
$AzureSSISLocation = "EastUS"
# For supported node sizes, see https://azure.microsoft.com/pricing/details/data-factory/ssis/
$AzureSSISNodeSize = "Standard_D8_v3"
# 1-10 nodes are currently supported
$AzureSSISNodeNumber = 2
# Azure-SSIS IR edition/license info: Standard or Enterprise
$AzureSSISEdition = "Standard" # Standard by default, whereas Enterprise lets you use advanced features on your Azure-SSIS IR
# Azure-SSIS IR hybrid usage info: LicenseIncluded or BasePrice
$AzureSSISLicenseType = "LicenseIncluded" # LicenseIncluded by default, whereas BasePrice lets you bring your own on-premises SQL Server license with Software Assurance to earn cost savings from the Azure Hybrid Benefit option
# For a Standard_D1_v2 node, up to four parallel executions per node are supported. For other nodes, up to (2 x number of cores) are currently supported.
$AzureSSISMaxParallelExecutionsPerNode = 8
# Custom setup info: Standard/express custom setups
$SetupScriptContainerSasUri = "" # OPTIONAL to provide a SAS URI of blob container for standard custom setup where your script and its associated files are stored
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO or leave it empty]" # OPTIONAL to configure an express custom setup without script
# Virtual network info: Classic or Azure Resource Manager
$VnetId = "[your virtual network resource ID or leave it empty]" # REQUIRED if you use an Azure SQL Database server with IP firewall rules/virtual network service endpoints or a managed instance with private endpoint to host SSISDB, or if you require access to on-premises data without configuring a self-hosted IR. We recommend an Azure Resource Manager virtual network, because classic virtual networks will be deprecated soon.
$SubnetName = "[your subnet name or leave it empty]" # WARNING: Use the same subnet as the one used for your Azure SQL Database server with virtual network service endpoints, or a different subnet from the one used for your managed instance with a private endpoint
# Public IP address info: OPTIONAL to provide two standard static public IP addresses with DNS name under the same subscription and in the same region as your virtual network
$FirstPublicIP = "[your first public IP address resource ID or leave it empty]"
$SecondPublicIP = "[your second public IP address resource ID or leave it empty]"

### SSISDB info
$SSISDBServerEndpoint = "[your Azure SQL Database server name.database.windows.net or managed instance name.DNS prefix.database.windows.net or managed instance name.public.DNS prefix.database.windows.net,3342 or leave it empty if you do not use SSISDB]" # WARNING: If you use SSISDB, ensure that there's no existing SSISDB on your database server, so we can prepare and manage one on your behalf
# Authentication info: SQL or Azure AD
$SSISDBServerAdminUserName = "[your server admin username for SQL authentication or leave it empty for Azure AD authentication]"
$SSISDBServerAdminPassword = "[your server admin password for SQL authentication or leave it empty for Azure AD authentication]"
# For the basic pricing tier, specify "Basic," not "B." For standard, premium, and elastic pool tiers, specify "S0," "S1," "S2," "S3," etc. See https://docs.microsoft.com/azure/sql-database/sql-database-resource-limits-database-server.
$SSISDBPricingTier = "[Basic|S0|S1|S2|S3|S4|S6|S7|S9|S12|P1|P2|P4|P6|P11|P15|…|ELASTIC_POOL(name = <elastic_pool_name>) for Azure SQL Database server or leave it empty for managed instance]"

### Self-hosted integration runtime info - This can be configured as a proxy for on-premises data access 
$DataProxyIntegrationRuntimeName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingLinkedServiceName = "" # OPTIONAL to configure a proxy for on-premises data access 
$DataProxyStagingPath = "" # OPTIONAL to configure a proxy for on-premises data access 

### Sign in and select a subscription
Connect-AzAccount
Select-AzSubscription -SubscriptionName $SubscriptionName

### Validate the connection to the database server
# Validate only if you use SSISDB and don't use a virtual network or Azure AD authentication
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    if([string]::IsNullOrEmpty($VnetId) -and [string]::IsNullOrEmpty($SubnetName))
    {
        if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) -and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword))
        {
            $SSISDBConnectionString = "Data Source=" + $SSISDBServerEndpoint + ";User ID=" + $SSISDBServerAdminUserName + ";Password=" + $SSISDBServerAdminPassword
            $sqlConnection = New-Object System.Data.SqlClient.SqlConnection $SSISDBConnectionString;
            Try
            {
                $sqlConnection.Open();
            }
            Catch [System.Data.SqlClient.SqlException]
            {
                Write-Warning "Cannot connect to your Azure SQL Database server, exception: $_";
                Write-Warning "Please make sure the server you specified has already been created. Do you want to proceed? [Y/N]"
                $yn = Read-Host
                if(!($yn -ieq "Y"))
                {
                    Return;
                }
            }
        }
    }
}

### Configure a virtual network
# Make sure to run this script against the subscription to which the virtual network belongs
if(![string]::IsNullOrEmpty($VnetId) -and ![string]::IsNullOrEmpty($SubnetName))
{
    # Register to the Azure Batch resource provider
    $BatchApplicationId = "ddbf3205-c6bd-46ae-8127-60eb93363864"
    $BatchObjectId = (Get-AzADServicePrincipal -ServicePrincipalName $BatchApplicationId).Id
    Register-AzResourceProvider -ProviderNamespace Microsoft.Batch
    while(!(Get-AzResourceProvider -ProviderNamespace "Microsoft.Batch").RegistrationState.Contains("Registered"))
    {
    Start-Sleep -s 10
    }
    if($VnetId -match "/providers/Microsoft.ClassicNetwork/")
    {
        # Assign the VM contributor role to Microsoft.Batch
        New-AzRoleAssignment -ObjectId $BatchObjectId -RoleDefinitionName "Classic Virtual Machine Contributor" -Scope $VnetId
    }
}

### Create a data factory
Set-AzDataFactoryV2 -ResourceGroupName $ResourceGroupName `
    -Location $DataFactoryLocation `
    -Name $DataFactoryName

### Create an integration runtime
Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Description $AzureSSISDescription `
    -Type Managed `
    -Location $AzureSSISLocation `
    -NodeSize $AzureSSISNodeSize `
    -NodeCount $AzureSSISNodeNumber `
    -Edition $AzureSSISEdition `
    -LicenseType $AzureSSISLicenseType `
    -MaxParallelExecutionsPerNode $AzureSSISMaxParallelExecutionsPerNode `
    -VnetId $VnetId `
    -Subnet $SubnetName
       
# Add CatalogServerEndpoint, CatalogPricingTier, and CatalogAdminCredential parameters if you use SSISDB
if(![string]::IsNullOrEmpty($SSISDBServerEndpoint))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -CatalogServerEndpoint $SSISDBServerEndpoint `
        -CatalogPricingTier $SSISDBPricingTier

    if(![string]::IsNullOrEmpty($SSISDBServerAdminUserName) –and ![string]::IsNullOrEmpty($SSISDBServerAdminPassword)) # Add the CatalogAdminCredential parameter if you don't use Azure AD authentication
    {
        $secpasswd = ConvertTo-SecureString $SSISDBServerAdminPassword -AsPlainText -Force
        $serverCreds = New-Object System.Management.Automation.PSCredential($SSISDBServerAdminUserName, $secpasswd)

        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -CatalogAdminCredential $serverCreds
    }
}

# Add custom setup parameters if you use standard/express custom setups
if(![string]::IsNullOrEmpty($SetupScriptContainerSasUri))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -SetupScriptContainerSasUri $SetupScriptContainerSasUri
}
if(![string]::IsNullOrEmpty($ExpressCustomSetup))
{
    if($ExpressCustomSetup -eq "RunCmdkey")
    {
        $addCmdkeyArgument = "YourFileShareServerName or YourAzureStorageAccountName.file.core.windows.net"
        $userCmdkeyArgument = "YourDomainName\YourUsername or azure\YourAzureStorageAccountName"
        $passCmdkeyArgument = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourPassword or YourAccessKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.CmdkeySetup($addCmdkeyArgument, $userCmdkeyArgument, $passCmdkeyArgument)
    }
    if($ExpressCustomSetup -eq "SetEnvironmentVariable")
    {
        $variableName = "YourVariableName"
        $variableValue = "YourVariableValue"
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.EnvironmentVariableSetup($variableName, $variableValue)
    }
    if($ExpressCustomSetup -eq "SentryOne.TaskFactory")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.SQLPhonetics.NET")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "oh22is.HEDDA.IO")
    {
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup)
    }
    # Create an array of one or more express custom setups
    $setups = New-Object System.Collections.ArrayList
    $setups.Add($setup)

    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -ExpressCustomSetup $setups
}

# Add self-hosted integration runtime parameters if you configure a proxy for on-premises data accesss
if(![string]::IsNullOrEmpty($DataProxyIntegrationRuntimeName) -and ![string]::IsNullOrEmpty($DataProxyStagingLinkedServiceName))
{
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -DataProxyIntegrationRuntimeName $DataProxyIntegrationRuntimeName `
        -DataProxyStagingLinkedServiceName $DataProxyStagingLinkedServiceName

    if(![string]::IsNullOrEmpty($DataProxyStagingPath))
    {
        Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
            -DataFactoryName $DataFactoryName `
            -Name $AzureSSISName `
            -DataProxyStagingPath $DataProxyStagingPath
    }
}

# Add public IP address parameters if you bring your own static public IP addresses
if(![string]::IsNullOrEmpty($FirstPublicIP) -and ![string]::IsNullOrEmpty($SecondPublicIP))
{
    $publicIPs = @($FirstPublicIP, $SecondPublicIP)
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
        -DataFactoryName $DataFactoryName `
        -Name $AzureSSISName `
        -PublicIPs $publicIPs
}

### Start the integration runtime
write-host("##### Starting #####")
Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $ResourceGroupName `
    -DataFactoryName $DataFactoryName `
    -Name $AzureSSISName `
    -Force

write-host("##### Completed #####")
write-host("If any cmdlet is unsuccessful, please consider using -Debug option for diagnostics.")
```

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Använd en Azure Resource Manager mall för att skapa en integration runtime

I det här avsnittet ska du använda en Azure Resource Manager-mall för att skapa integrerings körningen för Azure-SSIS. Här är ett exempel på en genom gång:

1. Skapa en JSON-fil med följande Azure Resource Manager mall. Ersätt värden i vinkelparenteserna (plats hållare) med dina egna värden.

    ```json
    {
        "contentVersion": "1.0.0.0",
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": {},
        "variables": {},
        "resources": [{
            "name": "<Specify a name for your data factory>",
            "apiVersion": "2018-06-01",
            "type": "Microsoft.DataFactory/factories",
            "location": "East US",
            "properties": {},
            "resources": [{
                "type": "integrationruntimes",
                "name": "<Specify a name for your Azure-SSIS IR>",
                "dependsOn": [ "<The name of the data factory you specified at the beginning>" ],
                "apiVersion": "2018-06-01",
                "properties": {
                    "type": "Managed",
                    "typeProperties": {
                        "computeProperties": {
                            "location": "East US",
                            "nodeSize": "Standard_D8_v3",
                            "numberOfNodes": 1,
                            "maxParallelExecutionsPerNode": 8
                        },
                        "ssisProperties": {
                            "catalogInfo": {
                                "catalogServerEndpoint": "<Azure SQL Database server name>.database.windows.net",
                                "catalogAdminUserName": "<Azure SQL Database server admin username>",
                                "catalogAdminPassword": {
                                    "type": "SecureString",
                                    "value": "<Azure SQL Database server admin password>"
                                },
                                "catalogPricingTier": "Basic"
                            }
                        }
                    }
                }
            }]
        }]
    }
    ```

2. Distribuera Azure Resource Manager-mallen genom att köra `New-AzResourceGroupDeployment` kommandot som visas i följande exempel. I exemplet är `ADFTutorialResourceGroup` namnet på din resurs grupp. `ADFTutorialARM.json` är den fil som innehåller JSON-definitionen för din data fabrik och Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Med det här kommandot skapar du en data fabrik och Azure-SSIS IR den, men den startar inte IR.

3. Starta Azure-SSIS IR genom att köra kommandot `Start-AzDataFactoryV2IntegrationRuntime`:

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Om du exkluderar en anpassad konfigurations tid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter innan Azure-SSIS IR ansluter till ett virtuellt nätverk.
>
> Om du använder SSISDB kommer tjänsten Data Factory att ansluta till din databas server för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och kopplar Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, förutom de data källor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till den och köra dem på din Azure-SSIS IR genom att använda SQL Server Data Tools-(SSDT) eller SQL Server Management Studio-verktyg (SSMS). Dessa verktyg ansluter till din databas server via dess server slut punkt: 

- För en Azure SQL Database Server är Server slut punktens format `<server name>.database.windows.net`.
- För en hanterad instans med privat slut punkt är Server slut punktens format `<server name>.<dns prefix>.database.windows.net`.
- För en hanterad instans med offentlig slut punkt är Server slut punkts formatet `<server name>.public.<dns prefix>.database.windows.net,3342`. 

Om du inte använder SSISDB kan du distribuera dina paket i fil system, fil resurser eller Azure Files och köra dem på Azure-SSIS IR med hjälp av kommando rads verktygen `dtinstall`, `dtutil`och `dtexec`. Mer information finns i [distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten kör SSIS-paket i Data Factory pipeliner. Mer information finns i [anropa SSIS-paket körning som en första klass Data Factory aktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Nästa steg

Se andra Azure-SSIS IR ämnen i den här dokumentationen:

- [Azure-SSIS integration runtime](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integrerings körningar i allmänhet, inklusive Azure-SSIS IR.
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar och förstår information om din Azure-SSIS IR.
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort Azure-SSIS IR. Det visar också hur du kan skala ut Azure-SSIS IR genom att lägga till fler noder.
- [Anslut Azure-SSIS IR till ett virtuellt nätverk](join-azure-ssis-integration-runtime-virtual-network.md). Den här artikeln innehåller information om hur du ansluter till Azure-SSIS IR till ett virtuellt nätverk.