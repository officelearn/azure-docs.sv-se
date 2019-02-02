---
title: Flera innehavare SaaS-självstudie – Azure SQL Database | Microsoft Docs
description: Etablera och katalogisera nya klienter med programmönstret för fristående
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: billgib
manager: craigg
ms.date: 09/24/2018
ms.openlocfilehash: 57a8a89c73e2be51e54130d9c37194a7513a47d8
ms.sourcegitcommit: ba035bfe9fab85dd1e6134a98af1ad7cf6891033
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/01/2019
ms.locfileid: "55562522"
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Etablera och katalogisera nya klienter med hjälp av programmet per klient SaaS-mönster

Den här artikeln beskriver den etablera och katalogisera av nya klienter som använder fristående app per klient SaaS-mönster.
Den här artikeln har två huvuddelar:
* Diskussion om etablera och katalogisera nya klienter
* En självstudiekurs som visar exempelkod för PowerShell som genomför etablera och katalogisera
    * I självstudiekursen används den Wingtip biljetter SaaS-exempelprogram, anpassade till fristående app per klient mönster.

## <a name="standalone-application-per-tenant-pattern"></a>Fristående program per klient mönster

Fristående app per klient mönster är ett av flera mönster för delade SaaS-program.  I det här mönstret etableras en fristående app för varje klient. Programmet består av nivån programkomponenter och en SQL-databas.  Varje klient-app kan distribueras i leverantörens prenumeration.  Azure erbjuder också en [hanterade program programmet](https://docs.microsoft.com/azure/managed-applications/overview) i som en app kan distribueras i en klient-prenumeration och hanteras av leverantören för klientens räkning. 

   ![mönster för App-per-klient](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

När du distribuerar ett program för en klient, har programmet och databasen etablerats i en ny resursgrupp som skapats för klienten.  Med hjälp av separata resursgrupper isolerar varje klient programresurser och gör att de kan hanteras oberoende av varandra. Varje programinstans har konfigurerats för att komma åt motsvarande databasen direkt i varje resursgrupp.  Den här anslutningen modellen skillnad från andra mönster som använder en katalog för broker anslutningar mellan appen och databasen.  Och eftersom det finns inga resursdelning, varje klientdatabas måste ha etablerats med tillräckligt med resurser för att hantera dess hög belastning. Det här mönstret tenderar att användas för SaaS-program med färre klienter, där det finns en större betoning på klient isolering och mindre betoning på resurskostnader.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Med hjälp av en klientkatalog med program per klient mönster

Varje klient app- och är helt isolerade, fungerar olika hanterings- och analysscenarier över klienter.  Till exempel kräver att använda en schemaändring för en ny version av programmet ändringar i schemat för varje klientdatabas. Scenarier för rapportering och analys kan också behöva åtkomst till alla klientdatabaser oavsett där de har distribuerats.

   ![mönster för App-per-klient](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Klient-katalogen innehåller en mappning mellan en klient-ID och en klientdatabas, vilket gör att en identifierare som kan matchas till en server och databas.  I Wingtip SaaS-app beräknas klient-ID som en hash av klientnamnet, även om andra scheman skulle kunna användas.  Men du fristående program inte behöver katalogen om du vill hantera anslutningar, kan du använda katalogen för att omfatta andra åtgärder till en uppsättning klientdatabaser. Elastisk fråga kan till exempel använda katalogen för att bestämma vilken uppsättning av databaser som distribueras för rapportering mellan klientorganisationer.

## <a name="elastic-database-client-library"></a>Klientbibliotek för Elastic Database

I exempelprogrammet Wingtip katalogen implementeras av fragment hanteringsfunktionerna i den [klientbibliotek för elastiska databaser](sql-database-elastic-database-client-library.md) (EDCL).  Biblioteket kan ett program för att skapa, hantera och använda en skärvkarta som lagras i en databas. I exemplet Wingtip biljetter katalogen lagras i den *klientkatalog* databas.  En klientnyckel till fragment (databas) shardkartor i som den klienten data lagras.  EDCL functions hantera en *globala fragmentkartan* lagras i tabeller i den *klientkatalog* databas och en *lokala fragmentkartan* lagras i varje shard.

EDCL funktioner kan anropas från program eller PowerShell-skript för att skapa och hantera poster i fragmentkartan. Andra EDCL funktioner kan användas för att hämta de shards eller Anslut till rätt databas för den givna klientnyckel. 

> [!IMPORTANT]
> Redigera inte data i katalogdatabasen eller lokala fragmentkartan i klientdatabaserna direkt. Direct uppdateringar stöds inte på grund av hög risken för korrupta data. I stället redigera mappningsdata genom att endast använda EDCL API: er.

## <a name="tenant-provisioning"></a>Klientorganisationens Etableringsläge 

Varje klient kräver en ny Azure resursgrupp, som måste skapas innan resurser kan etableras i den. När resursgruppen finns kan en Azure Resource Manager-mall användas för att distribuera programkomponenterna och databasen och sedan konfigurera anslutningen till databasen. Mallen kan importera en bacpac-fil för att initiera databasschemat.  Databasen kan också skapas som en kopia av en ”mall”-databas.  Databasen sedan ytterligare uppdateras med data som första plats och registrerat i katalogen.

## <a name="tutorial"></a>Självstudier

I den här självstudiekursen får du lära du dig att:

* Etablera en katalog
* Registrera klientdatabaserna exemplet som du har distribuerat tidigare i katalogen
* Etablera en ytterligare klient och registrera den i katalogen

En Azure Resource Manager-mall används för att distribuera och konfigurera programmet, skapa klientdatabasen och sedan importera en bacpac-fil för att initiera den. Importera begäran köas i flera minuter innan den är actioned.

I slutet av den här självstudien har du en uppsättning fristående klient program med varje databas som har registrerats i katalogen.

## <a name="prerequisites"></a>Förutsättningar

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen: 

* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Tre klient exempelapparna distribueras. Om du vill distribuera de här apparna på mindre än fem minuter [distribuera och utforska Wingtip biljetter SaaS fristående program mönstret](saas-standaloneapp-get-started-deploy.md).

## <a name="provision-the-catalog"></a>Etablera katalogen

I den här uppgiften ska du lära dig hur du etablerar katalogen för att registrera alla klientdatabaser. Du kommer att: 

* **Etablera katalogdatabasen** med en Azure resource management-mall. Databasen har initierats genom att importera en bacpac-fil.  
* **Registrera klient exempelappar** som du distribuerade tidigare.  Varje klient är registrerad med hjälp av en nyckel som skapas från en hash av innehavarens namn.  Klientnamnet lagras också i en tabell med tillägget i katalogen.

1. I PowerShell ISE öppnar *...\Learning Modules\UserConfig.psm* och uppdatera den **\<användaren\>** värdet till det värdet som du använde när du distribuerar tre exempelprogram.  **Spara filen**.  
1. I PowerShell ISE öppnar *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ange **$Scenario = 1**. Distribuera klientkatalog och registrera de fördefinierade innehavarna.

1. Lägg till en brytpunkt genom att placera markören var som helst på raden med texten `& $PSScriptRoot\New-Catalog.ps1`, och tryck sedan på **F9**.

    ![Ange en brytpunkt för spårning](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Kör skriptet genom att trycka på **F5**. 
1.  När skriptkörningen stannar vid brytpunkten, trycker du **F11** till steg i New-Catalog.ps1-skript.
1.  Spåra skriptkörningen med menyalternativen Debug F10 och F11, för att gå över eller in anropade funktionerna.
    *   Mer information om hur du felsöker PowerShell-skript finns i [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

När skriptet har körts kommer att finnas i katalogen och alla exempelklienter ska registreras. 

Nu vill titta på de resurser som du skapade.

1. Öppna den [Azure-portalen](https://portal.azure.com/) och bläddra resursgrupper.  Öppna den **wingtip-sa-catalog -\<användaren\>**  resource gruppen och notera katalogserver och databasen.
1. Öppna databasen i portalen och välj *datautforskaren* på den vänstra menyn.  Klicka på kommandot för inloggning och ange sedan lösenordet = **P@ssword1**.


1. Utforska schemat för den *tenantcatalog* databas.  
   * Objekten i den `__ShardManagement` schemat tillhandahålls av klientbiblioteket för elastiska databaser.
   * Den `Tenants` tabell och `TenantsExtended` visa är tillägg har lagts till i det här exemplet och som visar hur du kan utöka katalogen för att ge ytterligare värde.
1. Kör frågan, `SELECT * FROM dbo.TenantsExtended`.          

   ![datautforskaren](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Som ett alternativ till med Datautforskaren kan du ansluta till databasen från SQL Server Management Studio. Gör detta genom att ansluta till servern wingtip- 

    
    Observera att du inte bör redigera data direkt i katalogen – Använd alltid fragmenthanterings API: er. 

## <a name="provision-a-new-tenant-application"></a>Etablera ett nytt program för klienter

I den här uppgiften ska du lära dig hur du etablerar en enda klient-program. Du kommer att:  

* **Skapa en ny resursgrupp** för klienten. 
* **Tillhandahåll program- och databasen** till den nya resursgruppen med en Azure resource management-mall.  Den här åtgärden innehåller initiering av databasen med gemensamma schemat och referensdata genom att importera en bacpac-fil. 
* **Initiera databasen med grundläggande klientinformation**. Den här åtgärden inkluderar att du anger den typ av plats som bestämmer foto som används som bakgrund på webbplatsen händelser. 
* **Registrera databasen i katalogdatabasen**. 

1. I PowerShell ISE öppnar *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ange **$Scenario = 2**. Distribuera klientkatalog och registrera de fördefinierade innehavarna

1. Lägg till en brytpunkt i skriptet genom att placera markören var som helst på rad 49 med texten `& $PSScriptRoot\New-TenantApp.ps1`, och tryck sedan på **F9**.
1. Kör skriptet genom att trycka på **F5**. 
1.  När skriptkörningen stannar vid brytpunkten, trycker du **F11** till steg i New-Catalog.ps1-skript.
1.  Spåra skriptkörningen med menyalternativen Debug F10 och F11, för att gå över eller in anropade funktionerna.

När klienten har etablerats, öppnas den nya innehavaren händelser webbplats.

   ![Red maple racing](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Du kan sedan granska de nya resurserna som skapats i Azure-portalen. 

   ![Red maple racing resurser](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Ta bort resursgrupper om du vill stoppa faktureringen

Ta bort alla resursgrupper som du skapade för att stoppa den associerade faktureringen när du är klar med att utforska exemplet.

## <a name="additional-resources"></a>Ytterligare resurser

- Läs mer om delade SaaS-program i databasen i [designmönster för SaaS-program för flera innehavare](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar Wingtip biljetter SaaS fristående program.
> * Om servrar och databaser som ingår i appen.
> * Hur du tar bort exempelresurser för att stoppa relaterad fakturering.

Du kan utforska hur katalogen används för att ge stöd för olika mellan klientorganisationer scenarier med databas-per-klient-versionen av den [Wingtip biljetter SaaS-program](saas-dbpertenant-wingtip-app-overview.md).  
