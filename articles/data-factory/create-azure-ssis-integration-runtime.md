---
title: Skapa en Azure-SSIS-integreringskörning i Azure Data Factory
description: Lär dig hur du skapar en Azure-SSIS-integreringskörning i Azure Data Factory så att du kan distribuera och köra SSIS-paket i Azure.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/27/2020
author: swinarko
ms.author: sawinark
ms.reviewer: douglasl
manager: mflasko
ms.openlocfilehash: 18555fbffbc48594793163894c010998094b3b59
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "80336234"
---
# <a name="create-an-azure-ssis-integration-runtime-in-azure-data-factory"></a>Skapa en Azure-SSIS-integreringskörning i Azure Data Factory

Den här artikeln innehåller steg för att etablera en SSIS-programkörning (Azure-SQL Server Integration Services) i Azure Data Factory. En Azure-SSIS IR stöder:

- Köra paket som distribueras till SSIS-katalogen (SSISDB) som finns på en Azure SQL Database-server eller en hanterad instans (Project Deployment Model).
- Köra paket som distribueras till filsystem, filresurser eller Azure-filer (Package Deployment Model). 

När en Azure-SSIS IR har etablerats kan du använda välbekanta verktyg för att distribuera och köra dina paket i Azure. Dessa verktyg inkluderar SQL Server Data Tools (SSDT), SQL Server Management Studio `dtinstall` `dtutil`(SSMS) och kommandoradsverktyg som , och `dtexec`.

Självstudien [Etablera Azure-SSIS IR](tutorial-create-azure-ssis-runtime-portal.md) visar hur du skapar en Azure-SSIS IR via Azure-portalen eller Data Factory-appen. Självstudien visar också hur du kan använda en Azure SQL Database-server eller hanterad instans som värd för SSISDB. Den här artikeln utökar självstudien och beskriver hur du utför dessa valfria uppgifter:

- Använd en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB. Som en förutsättning måste du konfigurera virtuella nätverksbehörigheter och inställningar för din Azure-SSIS IR för att gå med i ett virtuellt nätverk.

- Använd Azure Active Directory(Azure AD) autentisering med den hanterade identiteten för din datafabrik för att ansluta till en Azure SQL Database-server eller hanterad instans. Som en förutsättning måste du lägga till den hanterade identiteten för din datafabrik som en databasanvändare som kan skapa en SSISDB-instans.

- Gå med i din Azure-SSIS IR till ett virtuellt nätverk eller konfigurera en självvärd IR som proxy för din Azure-SSIS IR för att komma åt data lokalt.

Den här artikeln visar hur du etablerar en Azure-SSIS IR med hjälp av Azure-portalen, Azure PowerShell och en Azure Resource Manager-mall.

## <a name="prerequisites"></a>Krav

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

- **Azure-prenumeration**. Om du inte redan har en prenumeration kan du skapa ett [kostnadsfritt utvärderingskonto.](https://azure.microsoft.com/pricing/free-trial/)

- **Azure SQL Database server eller hanterad instans (valfritt)**. Om du inte redan har en databasserver kan du skapa en i Azure-portalen innan du börjar. Data Factory skapar i sin tur en SSISDB-instans på den här databasservern. 

  Vi rekommenderar att du skapar databasservern i samma Azure-region som Integration Runtime. Med den här konfigurationen kan körningskörningsloggarna för integrering skriva till SSISDB utan att korsa Azure-regioner.

  Tänk på följande:

  - Baserat på den valda databasservern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Den kan vara tillgänglig i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Mer information om hur du väljer vilken typ av databasserver som ska vara värd för SSISDB finns i avsnittet [Jämför en enda Azure SQL-databas, elastisk pool och hanterad instans](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) i den här artikeln. 
  
    Om du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en självvärd IR, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [Ansluta till en Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

  - Bekräfta att inställningen **Tillåt åtkomst till Azure-tjänster** är aktiverad för databasservern. Den här inställningen är inte tillämplig när du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB. Mer information finns i [säkra din Azure SQL-databas](../sql-database/sql-database-security-tutorial.md#create-firewall-rules). Om du vill aktivera den här inställningen med PowerShell finns i [New-AzSqlServerFirewallRule](/powershell/module/az.sql/new-azsqlserverfirewallrule).

  - Lägg till IP-adressen för klientdatorn, eller ett intervall av IP-adresser som innehåller KLIENTDATOR:s IP-adress, i klient-IP-adresslistan i brandväggsinställningarna för databasservern. För mer information, se [Azure SQL Database-brandväggsregler på servernivå och databasnivå](../sql-database/sql-database-firewall-configure.md).

  - Du kan ansluta till databasservern med hjälp av SQL-autentisering med serveradministratörsuppgifterna eller genom att använda Azure AD-autentisering med den hanterade identiteten för din datafabrik. För det senare måste du lägga till den hanterade identiteten för din datafabrik i en Azure AD-grupp med åtkomstbehörigheter till databasservern. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir).

  - Bekräfta att databasservern inte redan har någon SSISDB-instans. Etableringen av en Azure-SSIS IR stöder inte användning av en befintlig SSISDB-instans.

- **Virtuellt nätverk i Azure Resource Manager (valfritt)**. Du måste ha ett virtuellt Azure Resource Manager-nätverk om minst ett av följande villkor är sant:
  - Du är värd för SSISDB på en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt.
  - Du vill ansluta till lokala datalager från SSIS-paket som körs på din Azure-SSIS IR utan att konfigurera en självvärd IR.

- **Azure PowerShell (valfritt)**. Följ instruktionerna i [Så här installerar och konfigurerar](/powershell/azure/install-az-ps)du Azure PowerShell , om du vill köra ett PowerShell-skript för att etablera din Azure-SSIS IR.

### <a name="regional-support"></a>Regional support

En lista över Azure-regioner där Data Factory och en Azure-SSIS IR är tillgängliga finns i [Data Factory- och SSIS IR-tillgänglighet per region](https://azure.microsoft.com/global-infrastructure/services/?products=data-factory&regions=all).

### <a name="comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance"></a>Jämförelse av en enda SQL-databas, elastisk pool och hanterad instans

I följande tabell jämförs vissa funktioner på en Azure SQL Database-server och hanterad instans när de relaterar till Azure-SSIR IR:

| Funktion | Enkel databas/elastisk pool| Hanterad instans |
|---------|--------------|------------------|
| **Schemaläggning** | SQL Server Agent är inte tillgänglig.<br/><br/>Se [Schemalägga en paketkörning i en Data Factory-pipeline](https://docs.microsoft.com/sql/integration-services/lift-shift/ssis-azure-schedule-packages?view=sql-server-2017#activity).| Den hanterade instansagenten är tillgänglig. |
| **Autentisering** | Du kan skapa en SSISDB-instans med en innehållen databasanvändare som representerar alla Azure AD-grupper med den hanterade identiteten för din datafabrik som medlem i **den db_owner** rollen.<br/><br/>Se [Aktivera Azure AD-autentisering för att skapa en SSISDB-instans på en Azure SQL Database-server](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database). | Du kan skapa en SSISDB-instans med en innehållen databasanvändare som representerar den hanterade identiteten för din datafabrik. <br/><br/>Se [Aktivera Azure AD-autentisering för att skapa en SSISDB-instans i en hanterad Azure SQL-databas-instans](enable-aad-authentication-azure-ssis-ir.md#enable-azure-ad-on-azure-sql-database-managed-instance). |
| **Tjänstenivå** | När du skapar en Azure-SSIS IR med din Azure SQL Database-server kan du välja tjänstnivå för SSISDB. Det finns flera tjänstnivåer. | När du skapar en Azure-SSIS IR med din hanterade instans kan du inte välja tjänstnivån för SSISDB. Alla databaser i den hanterade instansen har samma resurs som allokerats till den instansen. |
| **Virtuellt nätverk** | Din Azure-SSIS IR kan ansluta till ett virtuellt Azure Resource Manager-nätverk om du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster. | Din Azure-SSIS IR kan ansluta till ett virtuellt Azure Resource Manager-nätverk om du använder en hanterad instans med privat slutpunkt. Det virtuella nätverket krävs när du inte aktiverar en offentlig slutpunkt för din hanterade instans.<br/><br/>Om du ansluter din Azure-SSIS IR till samma virtuella nätverk som din hanterade instans kontrollerar du att din Azure-SSIS IR finns i ett annat undernät än din hanterade instans. Om du ansluter din Azure-SSIS IR till ett annat virtuellt nätverk än din hanterade instans rekommenderar vi antingen en virtuell nätverks peering eller en nätverks-till-nätverksanslutning. Se [Ansluta ditt program till en hanterad Azure SQL-databas-hanterad instans](../sql-database/sql-database-managed-instance-connect-app.md). |
| **Distribuerade transaktioner** | Den här funktionen stöds genom elastiska transaktioner. MsDTC-transaktioner (Microsoft Distributed Transaction Coordinator) stöds inte. Om dina SSIS-paket använder MSDTC för att samordna distribuerade transaktioner kan du överväga att migrera till elastiska transaktioner för Azure SQL Database. Mer information finns i [Distribuerade transaktioner mellan molndatabaser](../sql-database/sql-database-elastic-transactions-overview.md). | Stöds inte. |
| | | |

## <a name="use-the-azure-portal-to-create-an-integration-runtime"></a>Använda Azure-portalen för att skapa en integrationskörning

I det här avsnittet använder du Azure-portalen, särskilt användargränssnittet för Data Factory (UI) eller app, för att skapa en Azure-SSIS IR.

### <a name="create-a-data-factory"></a>Skapa en datafabrik

Om du vill skapa din datafabrik via Azure-portalen följer du steg-för-steg-instruktionerna i [Skapa en datafabrik via användargränssnittet](https://docs.microsoft.com/azure/data-factory/quickstart-create-data-factory-portal#create-a-data-factory). Välj **Fäst på instrumentpanelen** när du gör det, så att snabb åtkomst skapas när den har skapats. 

När din datafabrik har skapats öppnar du översiktssidan i Azure-portalen. Välj panelen **Författare & Bildskärm** för att öppna sidan Låt oss komma **igång** på en separat flik. Där kan du fortsätta att skapa din Azure-SSIS IR.   

### <a name="provision-an-azure-ssis-integration-runtime"></a>Etablering av en Azure-SSIS-integreringskörning

1. På sidan **Nu börjar vi** väljer du ikonen för att **konfigurera en SSIS-integreringskörning**.

   ![Ikonen Konfigurera SSIS-integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/configure-ssis-integration-runtime-tile.png)

1. Gör följande steg i avsnittet **Allmänna inställningar** på **inställningspanelen för integrationskörning.**

   ![Allmänna inställningar](./media/tutorial-create-azure-ssis-runtime-portal/general-settings.png)

   1. För **Namn**, ange namnet på din integreringsruntime.

   1. För **beskrivning** anger du en beskrivning av integrationskörningen.

   1. Välj en plats för integreringskörningen under **Plats**. Endast platser som stöds visas. Vi rekommenderar att du väljer samma plats för din databasserver som värd för SSISDB.

   1. För **Nod Size**väljer du storleken på noden i integrationskörningsklustret. Endast nodstorlekar som stöds visas. Välj en stor nodstorlek (skala upp) om du vill köra många beräkningsintensiva eller minnesintensiva paket.

   1. För **nodantal** välj antalet noder i ditt integreringskörningskluster. Endast nodantal som stöds visas. Välj ett stort kluster med många noder (skala ut) om du vill köra många paket parallellt.

   1. För **Edition/License**väljer du SQL Server-utgåvan för din integrationskörning: Standard eller Enterprise. Välj Företag om du vill använda avancerade funktioner på din integrationskörning.

   1. För **Spara pengar**väljer du alternativet Azure Hybrid Benefit för din integrationskörning: **Ja** eller **Nej**. Välj **Ja** om du vill ta med din egen SQL Server-licens med Software Assurance för att dra nytta av kostnadsbesparingar vid hybridanvändning.

   1. Välj **Nästa**.

1. Gör så här i avsnittet **SQL Settings.**

   ![SQL-inställningar](./media/tutorial-create-azure-ssis-runtime-portal/sql-settings.png)

   1. Markera kryssrutan **Skapa SSIS-katalog (SSISDB) som finns i Azure SQL Database server/Managed Instance för att lagra dina projekt/paket/miljöer/körningsloggar** för att välja distributionsmodellen för paket som ska köras på din Azure-SSIS IR. Du väljer antingen projektdistributionsmodellen där paket distribueras till SSISDB som finns på databasservern eller paketdistributionsmodellen där paketdistributionsmodell där paket distribueras till filsystem, filresurser eller Azure-filer. 
    
      Om du markerar kryssrutan måste du ta med din egen databasserver för att vara värd för SSISDB-instansen som vi skapar och hanterar för din räkning.
   
      1. Välj Azure-prenumerationen som har din databasserver som värd för SSISDB under **Prenumeration**. 

      1. Vi rekommenderar att du väljer samma **plats** för din databasserver som värd för SSISDB. Vi rekommenderar att du väljer samma plats för din integreringskörning. 

      1. För **Serverslutpunkt för katalogdatabas**, välj en slutpunkt på din databasserver som värd för SSISDB. 
    
         Baserat på den valda databasservern kan SSISDB-instansen skapas för din räkning som en enda databas, som en del av en elastisk pool eller i en hanterad instans. Den kan vara tillgänglig i ett offentligt nätverk eller genom att ansluta till ett virtuellt nätverk. Mer information om hur du väljer vilken typ av databasserver som ska vara värd för SSISDB finns i avsnittet [Jämför en enda Azure SQL-databas, elastisk pool och hanterad instans](#comparison-of-a-sql-database-single-database-elastic-pool-and-managed-instance) i den här artikeln. 
    
         Om du väljer en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data utan att konfigurera en självvärd IR, måste du ansluta till din Azure-SSIS IR till ett virtuellt nätverk. Mer information finns i [Ansluta till en Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      1. Markera kryssrutan **Använd AAD-autentisering med den hanterade identiteten för ADF för** att välja autentiseringsmetoden för databasservern som värd för SSISDB. Du väljer antingen SQL-autentisering eller Azure AD-autentisering med den hanterade identiteten för din datafabrik. 
    
         Om du markerar kryssrutan måste du lägga till den hanterade identiteten för din datafabrik i en Azure AD-grupp med åtkomstbehörigheter till databasservern. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). 

      1. Om **du vill ha administratörsanvändarnamn**anger du användarnamnet för SQL-autentisering för att databasservern ska vara värd för SSISDB. 

      1. För **admin-lösenord**anger du SQL-autentiseringslösenordet för databasservern som värd för SSISDB. 

      1. För **servicenivå för katalogdatabas**väljer du tjänstnivån för databasservern som värd för SSISDB. Välj nivån Basic, Standard eller Premium eller välj ett elastiskt poolnamn. 

      1. Välj **Testanslutning**. Om testet lyckas väljer du **Nästa**. 

1. Gör följande i avsnittet **Avancerade inställningar.**

   ![Avancerade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings.png)

   1. För **maximala parallella körningar per nod**väljer du det maximala antalet paket som ska köras samtidigt per nod i integrationskörningsklustret. Endast paketnummer som stöds visas. Välj ett lågt tal om du vill använda mer än en kärna för att köra ett enda stort paket som är beräknings- eller minnesintensivt. Välj ett högt tal om du vill köra ett eller flera små paket i en enda kärna.

   1. Markera kryssrutan **Anpassa din Azure-SSIS-integrationskörningstid med ytterligare systemkonfigurationer/komponentinstallationer** för att välja om du vill lägga till anpassade standard-/express-inställningar på din Azure-SSIS IR. Mer information finns i [Anpassad installation för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/how-to-configure-azure-ssis-ir-custom-setup).

      Om du markerar kryssrutan gör du följande steg.

      ![Avancerade inställningar med anpassade inställningar](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-custom.png)
   
      1. För **SAS URI för anpassad konfiguration**anger du SAS URI-för din behållare där du lagrar skript och associerade filer för anpassade standardinställningar.

      1. För **anpassad expressinställning**väljer du **Ny** för att öppna panelen Lägg till **expressanpassad inställning** och väljer **Run cmdkey command**sedan alla typer **Add environment variable**under den anpassade **inställningsmenyn** **Express,** t.ex.

         Om du väljer **komponenttypen Installera licensierad** kan du sedan välja alla integrerade komponenter från våra ISV-partner under listrutan **Komponentnamn** och ange vid behov produktlicensnyckeln som du köpte från dem i fältet **Licensnyckel.**
  
         De tillagda anpassade standardinställningarna visas i avsnittet **Avancerade inställningar.** Om du vill ta bort dem kan du markera deras kryssrutor och sedan välja **Ta bort**.

   1. Markera kryssrutan **Välj ett virtuellt nätverk för din Azure-SSIS-integrationskörningstid, tillåt ADF att skapa vissa nätverksresurser och ta med egna statiska offentliga IP-adresser** för att välja om du vill ansluta till din integrationskörning till ett virtuellt nätverk. 

      Välj den om du använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt som värd för SSISDB, eller om du behöver åtkomst till lokala data (det vill säga du har lokala datakällor eller mål i dina SSIS-paket) utan att konfigurera en självvärd IR. Mer information finns i [Gå med i Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network). 

      Om du markerar kryssrutan gör du följande steg.

      ![Avancerade inställningar med ett virtuellt nätverk](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-vnet.png)

      1. För **prenumeration**väljer du den Azure-prenumeration som har ditt virtuella nätverk.

      1. För **Plats**väljs samma plats för integrationskörningen.

      1. För **Typ**väljer du typ av virtuellt nätverk: klassiskt eller Azure Resource Manager. Vi rekommenderar att du väljer ett virtuellt Azure Resource Manager-nätverk, eftersom klassiska virtuella nätverk snart kommer att inaktuell.

      1. För **VNet-namn**väljer du namnet på det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med slutpunkter för virtuella nätverkstjänster eller hanterad instans med privat slutpunkt som värd för SSISDB. Eller så ska den vara samma som är ansluten till ditt lokala nätverk. Annars kan det vara valfritt virtuellt nätverk för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

      1. För **Undernätsnamn**väljer du namnet på undernätet för det virtuella nätverket. Det bör vara samma som används för din Azure SQL Database-server med virtuella nätverkstjänstslutpunkter som värd för SSISDB. Eller så ska det vara ett annat undernät än det som används för din hanterade instans med privat slutpunkt som värd för SSISDB. Annars kan det vara vilket undernät som helst för att ta med egna statiska offentliga IP-adresser för Azure-SSIS IR.

      1. Markera kryssrutan **Ta med statiska offentliga IP-adresser för din Azure-SSIS Integration Runtime** för att välja om du vill ta med egna statiska offentliga IP-adresser för Azure-SSIS IR, så att du kan tillåta dem i brandväggen för dina datakällor.

         Om du markerar kryssrutan gör du följande steg.

         1. För **Första statiska offentliga IP-adress**väljer du den första statiska offentliga IP-adressen som uppfyller kraven för din Azure-SSIS IR. Om du inte har några klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure-portalen och klicka sedan på uppdateringsknappen här, så att du kan välja dem.
      
         1. För **andra statiska offentliga IP-adress**väljer du den andra statiska offentliga IP-adressen som uppfyller kraven för din Azure-SSIS IR. Om du inte har några klickar du på **Skapa ny** länk för att skapa statiska offentliga IP-adresser på Azure-portalen och klicka sedan på uppdateringsknappen här, så att du kan välja dem.

   1. Markera kryssrutan **Konfigurera självvärdförd integrationskörning som en proxy för din Azure-SSIS Integration Runtime för** att välja om du vill konfigurera en självvärd IR som proxy för din Azure-SSIS IR. Mer information finns i [Konfigurera en självvärd IR som proxy](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis). 

      Om du markerar kryssrutan gör du följande steg.

      ![Avancerade inställningar med en självvärd IR](./media/tutorial-create-azure-ssis-runtime-portal/advanced-settings-shir.png)

      1. För **Självvärdförd integrationskörning**väljer du din befintliga självvärderade IR som proxy för Azure-SSIS IR.

      1. För **Mellanlagringslagringslänktjänst**väljer du din befintliga Azure Blob storage-länkade tjänst eller skapar en ny för mellanlagring.

      1. För **mellanlagringssökväg**anger du en blob-behållare i ditt valda Azure Blob storage-konto eller lämnar den tom om du vill använda en standard för mellanlagring.

   1. Välj **VNet-validering** > **Fortsätt**. 

1. I avsnittet **Sammanfattning** går du igenom alla etableringsinställningar, bokmärker de rekommenderade dokumentationslänkarna och väljer **Slutför** för att starta skapandet av integrationskörningen.

   > [!NOTE]
   > Exklusive en anpassad inställningstid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
   >
   > Om du använder SSISDB ansluter datafabrikstjänsten till databasservern för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och ansluter din Azure-SSIS IR till det virtuella nätverket.
   > 
   > När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, utöver de datakällor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [Anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

1. På fliken **Anslutningar** växlar du till **Integreringskörningar** om det behövs. Välj **Uppdatera** för att uppdatera statusen.

   ![Skapandestatus](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-creation-status.png)

1. Använd länkarna i kolumnen **Åtgärder** till att stoppa/starta, redigera eller ta bort integreringskörningen. Använd den sista länken för att se JSON-kod för integreringskörningen. Knapparna för att redigera och ta bort är endast aktiverade när IR har stoppats.

   ![Azure SSIS IR-åtgärder](./media/tutorial-create-azure-ssis-runtime-portal/azure-ssis-ir-actions.png)

### <a name="azure-ssis-integration-runtimes-in-the-portal"></a>Azure SSIS-integreringskörningar i portalen

1. I Azure Data Factory-användargränssnittet växlar du till fliken **Redigera** och väljer **Anslutningar**. Växla sedan till fliken **Integrationskörningar** för att visa befintliga integrationskörningar i datafabriken.

   ![Visa befintliga IR](./media/tutorial-create-azure-ssis-runtime-portal/view-azure-ssis-integration-runtimes.png)

1. Välj **Ny** om du vill skapa en ny Azure-SSIS IR.

   ![Integreringskörning via menyn](./media/tutorial-create-azure-ssis-runtime-portal/edit-connections-new-integration-runtime-button.png)

1. På **panelen Inställningar för integrationskörning** väljer du de **Lyft-och-flytta befintliga SSIS-paket som ska köras i Azure-panelen** och väljer sedan **Nästa**.

   ![Ange typ av integreringskörning](./media/tutorial-create-azure-ssis-runtime-portal/integration-runtime-setup-options.png)

1. De återstående stegen för att konfigurera en Azure-SSIS IR finns i avsnittet [Etablera en Azure SSIS-integreringskörning.](#provision-an-azure-ssis-integration-runtime)

## <a name="use-azure-powershell-to-create-an-integration-runtime"></a>Använda Azure PowerShell för att skapa en integrationskörning

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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

### <a name="validate-the-connection-to-database-server"></a>Verifiera anslutningen till databasservern

Lägg till följande skript för att validera din Azure SQL Database-server eller hanterad instans.

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

Lägg till följande skript för att automatiskt konfigurera virtuella nätverksbehörigheter och inställningar för din Azure-SSIS-integreringskörning att gå med i.

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

Skapa en [Azure-resursgrupp](../azure-resource-manager/management/overview.md) med kommandot [New-AzResourceGroup.](/powershell/module/az.resources/new-azresourcegroup) En resursgrupp är en logisk container där Azure-resurser distribueras och hanteras som en grupp.

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

Kör följande kommandon för att skapa en Azure-SSIS-integreringskörning som kör SSIS-paket i Azure.

Om du inte använder SSISDB kan du `CatalogServerEndpoint` `CatalogPricingTier`utelämna `CatalogAdminCredential` parametrarna och parametrarna .

Om du inte använder en Azure SQL Database-server med IP-brandväggsregler/slutpunkter för virtuella nätverkstjänster eller en hanterad instans med privat slutpunkt `VNetId` som `Subnet` värd för SSISDB, eller kräver åtkomst till lokala data, kan du utelämna parametrarna och eller skicka tomma värden för dem. Du kan också utelämna dem om du konfigurerar en självvärd IR som proxy för din Azure-SSIS IR för att komma åt data lokalt. Annars kan du inte utelämna dem och måste skicka giltiga värden från din virtuella nätverkskonfiguration. Mer information finns i [Ansluta till en Azure-SSIS IR till ett virtuellt nätverk](https://docs.microsoft.com/azure/data-factory/join-azure-ssis-integration-runtime-virtual-network).

Om du använder hanterad instans som värd för `CatalogPricingTier` SSISDB kan du utelämna parametern eller skicka ett tomt värde för den. Annars kan du inte utelämna det och måste skicka ett giltigt värde från listan över prisnivåer som stöds för Azure SQL Database. Mer information finns i [SQL Database-resursgränser](../sql-database/sql-database-resource-limits.md).

Om du använder Azure AD-autentisering med den hanterade identiteten för din `CatalogAdminCredential` datafabrik för att ansluta till databasservern kan du utelämna parametern. Men du måste lägga till den hanterade identiteten för din datafabrik i en Azure AD-grupp med åtkomstbehörigheter till databasservern. Mer information finns i [Aktivera Azure AD-autentisering för en Azure-SSIS IR](https://docs.microsoft.com/azure/data-factory/enable-aad-authentication-azure-ssis-ir). Annars kan du inte utelämna det och måste skicka ett giltigt objekt som skapats från serveradministratörens användarnamn och lösenord för SQL-autentisering.

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
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
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

### <a name="start-the-integration-runtime"></a>Starta integrationskörningen

Kör följande kommandon för att starta Azure-SSIS-integreringskörningen.

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
> Exklusive en anpassad inställningstid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
>
> Om du använder SSISDB ansluter datafabrikstjänsten till databasservern för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och ansluter din Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, utöver de datakällor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [Anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

### <a name="full-script"></a>Fullständigt skript

Här är hela skriptet som skapar en Azure-SSIS-integreringskörning.

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
$ExpressCustomSetup = "[RunCmdkey|SetEnvironmentVariable|SentryOne.TaskFactory|oh22is.SQLPhonetics.NET|oh22is.HEDDA.IO|KingswaySoft.IntegrationToolkit|KingswaySoft.ProductivityPack|Theobald.XtractIS or leave it empty]" # OPTIONAL to configure an express custom setup without script
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
    if($ExpressCustomSetup -eq "KingswaySoft.IntegrationToolkit")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }
    if($ExpressCustomSetup -eq "KingswaySoft.ProductivityPack")
    {
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString("YourLicenseKey")
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
    }    
    if($ExpressCustomSetup -eq "Theobald.XtractIS")
    {
        $jsonData = Get-Content -Raw -Path YourLicenseFile.json
        $jsonData = $jsonData -replace '\s',''
        $jsonData = $jsonData.replace('"','\"')
        $licenseKey = New-Object Microsoft.Azure.Management.DataFactory.Models.SecureString($jsonData)
        $setup = New-Object Microsoft.Azure.Management.DataFactory.Models.ComponentSetup($ExpressCustomSetup, $licenseKey)
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

## <a name="use-an-azure-resource-manager-template-to-create-an-integration-runtime"></a>Använda en Azure Resource Manager-mall för att skapa en integrationskörning

I det här avsnittet använder du en Azure Resource Manager-mall för att skapa Azure-SSIS-integreringskörningen. Här är en exempelgenomgång:

1. Skapa en JSON-fil med följande Azure Resource Manager-mall. Ersätt värden i vinkelparenteserna (platshållarna) med dina egna värden.

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

2. Om du vill distribuera Azure `New-AzResourceGroupDeployment` Resource Manager-mallen kör du kommandot som visas i följande exempel. I exemplet `ADFTutorialResourceGroup` är namnet på din resursgrupp. `ADFTutorialARM.json`är filen som innehåller JSON-definitionen för din datafabrik och Azure-SSIS IR.

    ```powershell
    New-AzResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile ADFTutorialARM.json
    ```

    Det här kommandot skapar din datafabrik och Azure-SSIS IR i det, men det startar inte IR.

3. Starta ditt Azure-SSIS IR: `Start-AzDataFactoryV2IntegrationRuntime`

    ```powershell
    Start-AzDataFactoryV2IntegrationRuntime -ResourceGroupName "<Resource Group Name>" `
        -DataFactoryName "<Data Factory Name>" `
        -Name "<Azure SSIS IR Name>" `
        -Force
    ```

> [!NOTE]
> Exklusive en anpassad inställningstid bör den här processen avslutas inom 5 minuter. Men det kan ta 20-30 minuter för Azure-SSIS IR att ansluta till ett virtuellt nätverk.
>
> Om du använder SSISDB ansluter datafabrikstjänsten till databasservern för att förbereda SSISDB. Den konfigurerar också behörigheter och inställningar för ditt virtuella nätverk, om det anges, och ansluter din Azure-SSIS IR till det virtuella nätverket.
> 
> När du etablerar en Azure-SSIS IR installeras även Access Redistributable och Azure Feature Pack för SSIS. Dessa komponenter ger anslutning till Excel-filer, Access-filer och olika Azure-datakällor, utöver de datakällor som inbyggda komponenter redan stöder. Information om andra komponenter som du kan installera finns i [Anpassad installation för en Azure-SSIS IR](how-to-configure-azure-ssis-ir-custom-setup.md).

## <a name="deploy-ssis-packages"></a>Distribuera SSIS-paket

Om du använder SSISDB kan du distribuera dina paket till det och köra dem på dina Azure-SSIS IR med hjälp av verktyg för SQL Server Data Tools (SSDT) eller SQL Server Management Studio (SSMS). Dessa verktyg ansluter till databasservern via serverslutpunkten: 

- För en Azure SQL Database-server är `<server name>.database.windows.net`serverslutpunktsformatet .
- För en hanterad instans med privat slutpunkt `<server name>.<dns prefix>.database.windows.net`är serverslutpunktsformatet .
- För en hanterad instans med offentlig slutpunkt `<server name>.public.<dns prefix>.database.windows.net,3342`är serverslutpunktsformatet . 

Om du inte använder SSISDB kan du distribuera dina paket till filsystem, filresurser eller Azure-filer och köra `dtinstall` `dtutil`dem `dtexec` på din Azure-SSIS IR med hjälp av verktygen , och kommandoraden. Mer information finns i [Distribuera SSIS-paket](/sql/integration-services/packages/deploy-integration-services-ssis-projects-and-packages#deploy-packages-to-integration-services-server). 

I båda fallen kan du också köra dina distribuerade paket på Azure-SSIS IR med hjälp av aktiviteten Kör SSIS-paket i Data Factory-pipelines. Mer information finns i [Anropa SSIS-paketkörning som en förstklassig datafabriksaktivitet](https://docs.microsoft.com/azure/data-factory/how-to-invoke-ssis-package-ssis-activity).

## <a name="next-steps"></a>Nästa steg

Se andra Azure-SSIS IR-avsnitt i den här dokumentationen:

- [Azure-SSIS-integreringskörning](concepts-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln innehåller information om integrationskörningar i allmänhet, inklusive Azure-SSIS IR.
- [Övervaka en Azure-SSIS IR](monitor-integration-runtime.md#azure-ssis-integration-runtime). Den här artikeln visar hur du hämtar och förstår information om din Azure-SSIS IR.
- [Hantera en Azure-SSIS IR](manage-azure-ssis-integration-runtime.md). Den här artikeln visar hur du stoppar, startar eller tar bort din Azure-SSIS IR. Den visar också hur du skalar ut din Azure-SSIS IR genom att lägga till fler noder.
- [Distribuera, köra och övervaka SSIS-paket i Azure](/sql/integration-services/lift-shift/ssis-azure-deploy-run-monitor-tutorial)   
- [Ansluta till SSISDB i Azure](/sql/integration-services/lift-shift/ssis-azure-connect-to-catalog-database)
- [Ansluta till lokala data med Windows-autentisering](/sql/integration-services/lift-shift/ssis-azure-connect-with-windows-auth) 
- [Schemalägga paketkörningar i Azure](/sql/integration-services/lift-shift/ssis-azure-schedule-packages)