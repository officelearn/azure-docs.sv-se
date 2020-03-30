---
title: SaaS-självstudie för flera innehavare
description: Etablera och katalogisera nya klienter med det fristående programmönstret
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
ms.date: 09/24/2018
ms.openlocfilehash: 02682a18f14e7ecbf5b42783ab84a1b55a4bb77b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74133124"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Etablera och katalogisera nya klienter med hjälp av programmet per klient SaaS-mönster

Den här artikeln beskriver etablering och katalogisering av nya klienter med hjälp av den fristående appen per klient SaaS-mönster.
Denna artikel har två huvuddelar:
* Konceptuell diskussion om etablering och katalogisering av nya klienter
* En självstudiekurs som belyser exempel på PowerShell-kod som åstadkommer etablering och katalogisering
    * Självstudien använder Wingtip Tickets-exempelet SaaS-programmet, anpassat till den fristående appen per klientmönster.

## <a name="standalone-application-per-tenant-pattern"></a>Fristående program per klientmönster

Den fristående appen per klientmönster är ett av flera mönster för SaaS-program med flera innehavare.  I det här mönstret etableras en fristående app för varje klient. Programmet består av komponenter på programnivå och en SQL-databas.  Varje klientapp kan distribueras i leverantörens prenumeration.  Alternativt erbjuder Azure ett [hanterat program](https://docs.microsoft.com/azure/managed-applications/overview) där en app kan distribueras i en klients prenumeration och hanteras av leverantören för klientens räkning.

   ![app-per-klient-mönster](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

När du distribuerar ett program för en klient etableras appen och databasen i en ny resursgrupp som skapats för klienten.  Med hjälp av separata resursgrupper isoleras varje klients programresurser och gör att de kan hanteras oberoende av varandra. Inom varje resursgrupp konfigureras varje programinstans för att komma åt motsvarande databas direkt.  Den här anslutningsmodellen kontrasterar mot andra mönster som använder en katalog för att mäkla anslutningar mellan appen och databasen.  Och eftersom det inte finns någon resursdelning måste varje klientdatabas etableras med tillräckliga resurser för att hantera sin toppbelastning. Det här mönstret tenderar att användas för SaaS-program med färre klienter, där det finns en stark betoning på klientisolering och mindre betoning på resurskostnader.

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Använda en klientkatalog med programmet per klientmönster

Medan varje klients app och databas är helt isolerade kan olika hanterings- och analysscenarier fungera över klienter.  Om du till exempel använder en schemaändring för en ny version av programmet krävs ändringar i schemat för varje klientdatabas. Rapporterings- och analysscenarier kan också kräva åtkomst till alla klientdatabaser oavsett var de distribueras.

   ![app-per-klient-mönster](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Klientkatalogen innehåller en mappning mellan en klientidentifierare och en klientdatabas, vilket gör att en identifierare kan matchas till ett server- och databasnamn.  I Wingtip SaaS-appen beräknas klientidentifieraren som en hash för klientnamnet, även om andra scheman kan användas.  Även om fristående program inte behöver katalogen för att hantera anslutningar, kan katalogen användas för att begränsa andra åtgärder till en uppsättning klientdatabaser. Elastisk fråga kan till exempel använda katalogen för att bestämma vilken uppsättning databaser över vilka frågor distribueras för rapportering mellan klienter.

## <a name="elastic-database-client-library"></a>Klientbibliotek för elastisk databas

I exempelprogrammet Wingtip implementeras katalogen av fragmenthanteringsfunktionerna i EDCL [(Elastic Database Client Library).](sql-database-elastic-database-client-library.md)  Biblioteket gör det möjligt för ett program att skapa, hantera och använda en fragmentkarta som lagras i en databas. I exemplet Wingtip Tickets lagras katalogen i *klientkatalogdatabasen.*  Fragmentet mappar en klientnyckel till fragmentet (databasen) där klientens data lagras.  EDCL-funktioner hanterar en *global fragmentkarta* som lagras i tabeller i *klientkatalogdatabasen* och en *lokal fragmentkarta* som lagras i varje shard.

EDCL-funktioner kan anropas från program eller PowerShell-skript för att skapa och hantera posterna i fragmentkartan. Andra EDCL-funktioner kan användas för att hämta uppsättningen shards eller ansluta till rätt databas för given klientnyckel.

> [!IMPORTANT]
> Redigera inte data i katalogdatabasen eller den lokala fragmentmappningen i klientdatabaserna direkt. Direkta uppdateringar stöds inte på grund av den höga risken för dataskador. Redigera i stället mappningsdata med hjälp av EDCL API:er.

## <a name="tenant-provisioning"></a>Etablering av klient

Varje klient kräver en ny Azure-resursgrupp som måste skapas innan resurser kan etableras i den. När resursgruppen finns kan en Azure Resource Management-mall användas för att distribuera programkomponenterna och databasen och sedan konfigurera databasanslutningen. Om du vill initiera databasschemat kan mallen importera en bacpac-fil.  Alternativt kan databasen skapas som en kopia av en "malldatabas".  Databasen uppdateras sedan ytterligare med inledande platsdata och registreras i katalogen.

## <a name="tutorial"></a>Självstudier

I den här guiden får du lära du dig hur man:

* Etablera en katalog
* Registrera exempelklientdatabaserna som du distribuerade tidigare i katalogen
* Etablera ytterligare en klient och registrera den i katalogen

En Azure Resource Manager-mall används för att distribuera och konfigurera programmet, skapa klientdatabasen och sedan importera en bacpac-fil för att initiera den. Importbegäran kan köas i flera minuter innan den har åtgärdats.

I slutet av den här självstudien har du en uppsättning fristående klientprogram, där varje databas är registrerad i katalogen.

## <a name="prerequisites"></a>Krav

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* De tre exempelklient-apparna distribueras. Information om hur du distribuerar dessa appar på mindre än fem minuter finns i [Distribuera och utforska mönstret För att visa Wingtip Tickets SaaS-fristående program](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Etablera katalogen

I den här uppgiften får du lära dig hur du etablerar katalogen som används för att registrera alla klientdatabaser. Du kommer att:

* **Etablera katalogdatabasen** med hjälp av en Azure-resurshanteringsmall. Databasen initieras genom att importera en bacpac-fil.
* **Registrera exempelklientapparna** som du distribuerade tidigare.  Varje klient registreras med en nyckel som skapats från en hash-fil i klientnamnet.  Klientnamnet lagras också i en tilläggstabell i katalogen.

1. I PowerShell ISE öppnar *du ...\Utbildningsmoduler\UserConfig.psm* och uppdaterar ** \<användarvärdet\> ** till det värde du använde när du distribuerade de tre exempelprogrammen.  **Spara filen**.
1. I PowerShell ISE öppnar *du ...\Utbildningsmoduler\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ställer in **$Scenario = 1**. Distribuera klientkatalogen och registrera de fördefinierade klienterna.

1. Lägg till en brytpunkt genom att sätta markören var som helst på raden som säger, `& $PSScriptRoot\New-Catalog.ps1`och tryck sedan på **F9**.

    ![ställa in en brytpunkt för spårning](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Kör skriptet genom att trycka på **F5**.
1.  När skriptkörningen stoppas vid brytpunkten trycker du på **F11** för att gå in i skriptet New-Catalog.ps1.
1.  Spåra skriptets körning med hjälp av debug-menyalternativen F10 och F11 för att gå över eller till anropade funktioner.
    *   Mer information om felsökning av PowerShell-skript finns i [Tips om hur du arbetar med och felsöker PowerShell-skript](https://docs.microsoft.com/powershell/scripting/components/ise/how-to-debug-scripts-in-windows-powershell-ise).

När skriptet är klart kommer katalogen att finnas och alla exempelklienter registreras.

Titta nu på de resurser som du har skapat.

1. Öppna [Azure-portalen](https://portal.azure.com/) och bläddra bland resursgrupperna.  Öppna **resursgruppen wingtip-sa-catalog-\<användarresurs\> ** och notera katalogservern och databasen.
1. Öppna databasen i portalen och välj *Datautforskaren* på menyn till vänster.  Klicka på kommandot Logga in och ange sedan lösenord = **P\@ssword1**.


1. Utforska schemat för *klientkategoridatabasen.*
   * Objekten `__ShardManagement` i schemat tillhandahålls alla av klientbiblioteket för elastisk databas.
   * Tabellen `Tenants` och `TenantsExtended` vyn läggs till i exemplet som visar hur du kan utöka katalogen för att ange ytterligare värde.
1. Kör frågan. `SELECT * FROM dbo.TenantsExtended`

   ![datautforskaren](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Som ett alternativ till att använda Data Explorer kan du ansluta till databasen från SQL Server Management Studio. För att göra detta, anslut till servern wingtip-


    Observera att du inte bör redigera data direkt i katalogen - använd alltid fragmenthanterings-API:erna.

## <a name="provision-a-new-tenant-application"></a>Etablera ett nytt klientprogram

I den här uppgiften får du lära dig hur du etablerar ett enda klientprogram. Du kommer att:

* **Skapa en ny resursgrupp** för klienten.
* **Etablera programmet och databasen** i den nya resursgruppen med hjälp av en Azure-resurshanteringsmall.  Den här åtgärden omfattar att initiera databasen med vanliga schema- och referensdata genom att importera en bacpac-fil.
* **Initiera databasen med grundläggande klientinformation**. Den här åtgärden omfattar att ange platstypen, som bestämmer det fotografi som används som bakgrund på dess evenemangswebbplats.
* **Registrera databasen i katalogdatabasen**.

1. I PowerShell ISE öppnar *du ...\Utbildningsmoduler\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ställer in **$Scenario = 2**. Distribuera klientkatalogen och registrera de fördefinierade klienterna

1. Lägg till en brytpunkt i skriptet genom att sätta markören var som helst på rad 49 som säger, `& $PSScriptRoot\New-TenantApp.ps1`och tryck sedan på **F9**.
1. Kör skriptet genom att trycka på **F5**.
1.  När skriptkörningen stoppas vid brytpunkten trycker du på **F11** för att gå in i skriptet New-Catalog.ps1.
1.  Spåra skriptets körning med hjälp av debug-menyalternativen F10 och F11 för att gå över eller till anropade funktioner.

När klienten har etablerats öppnas den nya klientens evenemangswebbplats.

   ![röd lönn racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Du kan sedan granska de nya resurserna som skapats i Azure-portalen.

   ![red maple racing resurser](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Om du vill stoppa faktureringen tar du bort resursgrupper

När du har utforskat exemplet tar du bort alla resursgrupper som du har skapat för att stoppa den associerade faktureringen.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om SaaS-databasprogram med flera innehavare finns i [Designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Hur man distribuerar Wingtip Biljetter SaaS fristående program.
> * Om de servrar och databaser som utgör appen.
> * Så här tar du bort exempelresurser för att stoppa relaterad fakturering.

Du kan utforska hur katalogen används för att stödja olika scenarier mellan innehavare med hjälp av databasen per klientversion av [Wingtip Tickets SaaS-programmet](saas-dbpertenant-wingtip-app-overview.md).
