---
title: "Självstudier för flera innehavare SaaS - Azure SQL Database | Microsoft Docs"
description: "Etablera och katalogen nya klienter som använder fristående programmönster"
keywords: sql database tutorial
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: SaaS
ms.workload: Inactive
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/31/2018
ms.author: billgib
ms.openlocfilehash: a13eeb79320360da078ee19a61cc32a2e1f35354
ms.sourcegitcommit: d1f35f71e6b1cbeee79b06bfc3a7d0914ac57275
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/22/2018
---
# <a name="provision-and-catalog-new-tenants-using-the--application-per-tenant-saas-pattern"></a>Etablera och katalogen nya klienter som använder program per klient SaaS mönster

Den här artikeln beskriver etablering och katalogiserar av nya klienter som använder fristående app per klient SaaS-mönster.
Den här artikeln har två huvuddelar:
* Diskussion om etablering och katalogiserar nya klienter
* En självstudiekurs som visar exempelkod för PowerShell som genomför etablering och katalogiserar
    * I självstudiekursen används Wingtip biljetter SaaS exempelprogrammet anpassas till fristående app per klient mönster.

## <a name="standalone-application-per-tenant-pattern"></a>Fristående program per klient mönster
Fristående app per klient mönster är ett av flera mönster för flera innehavare SaaS-program.  I det här mönstret etableras en fristående app för varje klient. Programmet omfattar nivå programkomponenter och en SQL-databas.  Varje klient-app kan distribueras i leverantörens prenumerationen.  Du kan också Azure erbjuder en [hanterade program programmet](https://docs.microsoft.com/en-us/azure/managed-applications/overview) i som en app kan distribueras i en klients prenumerationen och hanteras av leverantören för klientens räkning. 

   ![mönstret App per klient](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern.png)

När du distribuerar ett program för en klient kan etableras appen och databasen i en ny resursgrupp som skapats för klienten.  Med hjälp av separata resursgrupper isolerar varje klient-resurser och gör att de kan hanteras oberoende av varandra. Varje instans av programmet har konfigurerats för att komma åt motsvarande databasen direkt i varje resursgrupp.  Den här anslutningen modellen står i kontrast mot andra mönster som använder en katalog på broker anslutningar mellan appen och databasen.  Och eftersom det inte finns några resursdelning, varje klient-databas måste etableras med tillräckligt med resurser för att hantera dess belastning. Det här mönstret tenderar att användas för SaaS-program med färre klienter, där det finns en stark betoning innehavaren isolering och mindre betoning på resurskostnader.  

## <a name="using-a-tenant-catalog-with-the-application-per-tenant-pattern"></a>Med hjälp av en klient-katalog med program per klient mönster
Varje klient app- och är helt isolerade, fungerar olika hantering och analytics scenarier över klienter.  Till exempel kräver använda en schemaändring för en ny version av programmet ändringar i schemat för varje klient-databas. Scenarier för rapportering och analys kan också behöva åtkomst till alla klient-databaser oavsett när de distribueras.

   ![mönstret App per klient](media/saas-standaloneapp-provision-and-catalog/standalone-app-pattern-with-catalog.png)

Klient-katalogen innehåller en mappning mellan en klient-ID och ett klient-databasen så att en identifierare som matchas till en server och databasnamnet.  I appen Wingtip SaaS beräknas klient-ID som en hash av innehavarens namn, även om andra system kan användas.  Medan fristående program inte behöver katalogen om du vill hantera anslutningar kan användas katalogen för att definiera omfattningen av andra åtgärder för en uppsättning klient-databaser. Elastisk frågan kan till exempel använda katalogen för att bestämma vilken uppsättning av databaser som distribueras frågor för flera innehavare rapportering.

## <a name="elastic-database-client-library"></a>Klientbibliotek för Elastic Database
I exempelprogrammet Wingtip katalogen implementeras av Fragmentera hanteringsfunktionerna i den [klientbibliotek för elastisk databas](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-elastic-database-client-library) (EDCL).  Biblioteket aktiverar ett program att skapa, hantera och använda en Fragmentera karta som lagras i en databas. I exemplet Wingtip biljetter lagras i katalogen i den *klient katalogen* databas.  Fragmentera mappar en klientnyckel till Fragmentera (databasen) i vilket den klientorganisationen data lagras.  Hantera EDCL funktioner en *globala Fragmentera kartan* lagras i tabeller i den *klient katalogen* databasen och en *lokala Fragmentera kartan* lagras i varje Fragmentera.

EDCL funktioner kan anropas från program eller PowerShell-skript för att skapa och hantera poster i kartan Fragmentera. Andra EDCL funktioner kan användas för att hämta de delar eller ansluta till rätt databas för klientnyckel. 
    
> [!IMPORTANT] 
> Redigera inte data i katalogdatabasen eller lokala Fragmentera kartan i klient-databaser direkt. Direkt uppdateringar stöds inte på grund av hög risk för skadade data. I stället redigera mappning med hjälp av EDCL API: er endast.

## <a name="tenant-provisioning"></a>Klientorganisationens Etableringsläge 
Varje klient kräver en ny Azure resursgrupp, som måste skapas innan resurser kan etableras i den. När resursgruppen finns kan en Azure Resource Manager-mall användas för att distribuera programkomponenterna och databasen och sedan konfigurera anslutningen till databasen. För att initiera databasschemat importera mallen en bacpac-fil.  Alternativt kan databasen skapas en kopia av en malldatabasen.  Databasen sedan ytterligare uppdateras med data som första plats och registrerad i katalogen.

## <a name="tutorial"></a>Självstudier

I den här självstudiekursen får du lära du dig att:
* Etablera en katalog
* Registrera klienten exempeldatabasen som du har distribuerat tidigare i katalogen
* Etablera en ytterligare klient och registrera den i katalogen

En Azure Resource Manager-mallen används för att distribuera och konfigurera programmet, skapar klient-databasen och sedan importera en bacpac-fil för att initiera den. Begäran om att importera köas i flera minuter innan den är åtgärdade.

I slutet av den här kursen har du en uppsättning fristående klient program med varje databas som har registrerats i katalogen.

## <a name="prerequisites"></a>Förutsättningar
Följande krav måste uppfyllas för att kunna köra den här självstudiekursen: 
* Azure PowerShell ska ha installerats. Mer information finns i [Komma igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)
* Tre klient exempelapparna distribueras. För att distribuera de här apparna på mindre än fem minuter finns [distribuera och utforska programmönster Wingtip biljetter SaaS fristående](https://docs.microsoft.com/en-us/azure/sql-database/saas-standaloneapp-get-started-deploy).

## <a name="provision-the-catalog"></a>Etablera katalogen
Lär dig hur du etablerar den katalog som används för att registrera alla klient-databaser i den här uppgiften. Du kommer att: 
* **Etablera katalogdatabasen** med en mall för hantering av Azure-resurs. Databasen har initierats genom att importera en bacpac-fil.  
* **Registrera klienten exempelappar** som du har distribuerat tidigare.  Varje klient har registrerats med en nyckel som konstrueras utifrån en hash av innehavarens namn.  Innehavarens namn lagras också i en tabell, tillägg i katalogen.

1. I PowerShell ISE öppnar *...\Learning Modules\UserConfig.psm* och uppdatera den  **\<användare\>**  värdet med värdet som du använde när du distribuerar tre exempelprogrammen.  **Spara filen**.  
1. I PowerShell ISE öppnar *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ange **$Scenario = 1**. Distribuera klient-katalogen och registrera de fördefinierade innehavarna.

1. Lägga till en brytpunkt genom att placera markören på raden, `& $PSScriptRoot\New-Catalog.ps1`, och tryck sedan på **F9**.

    ![Ange en brytpunkt för spårning](media/saas-standaloneapp-provision-and-catalog/breakpoint.png)

1. Kör skript genom att trycka på **F5**. 
1.  Skriptkörningen slutar vid brytpunkten, tryck på **F11** till steg i New-Catalog.ps1-skript.
1.  Spåra skriptkörningen med menyalternativen Debug F10 och F11, för att gå över eller till kallas för funktioner.
    *   Mer information om felsökning av PowerShell-skript finns [Tips om att arbeta med och felsöka PowerShell-skript](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

När skriptet har slutförts kommer att finnas i katalogen och alla exempel klienter ska registreras. 

Nu titta på de resurser som du skapade.

1. Öppna den [Azure-portalen](https://portal.azure.com/) och bläddra resursgrupper.  Öppna den **wingtip-sa-katalog -\<användare\>**  resurs gruppen och notera katalogserver och databasen.
1. Öppna databasen i portalen och välj *Data explorer* i den vänstra menyn.  Klicka på kommandot inloggning och ange sedan lösenordet =  **P@ssword1** .


1. Utforska schemat för den *tenantcatalog* databas.  
   * Objekten i den `__ShardManagement` schemat tillhandahålls av klientbiblioteket för elastisk databas.
   * Den `Tenants` tabell och `TenantsExtended` visa är tillägg som lagts till i exemplet som visar hur du kan utöka katalogen för att ange ytterligare värde.
1. Kör frågan, `SELECT * FROM dbo.TenantsExtended`.          

   ![data explorer](media/saas-standaloneapp-provision-and-catalog/data-explorer-tenantsextended.png)

    Som ett alternativ till att använda Data Explorer kan du ansluta till databasen från SQL Server Management Studio. Gör detta genom att ansluta till servern wingtip- 

    
    Observera att du inte bör ändra data direkt i katalogen - alltid använder Fragmentera management API: er. 

## <a name="provision-a-new-tenant-application"></a>Etablera ett nytt program för klient

Lär dig hur du etablerar en enda klient-program i den här uppgiften. Du kommer att:  
* **Skapa en ny resursgrupp** för klienten. 
* **Etablera program och databasen** till den nya resursgruppen med en mall för hantering av Azure-resurs.  Den här åtgärden innehåller vid initiering av databasen med gemensamma schemat och referensdata genom att importera en bacpac-fil. 
* **Initiera databasen med grundläggande klient information**. Den här åtgärden innehåller anger vilken plats som bestämmer foto som används som bakgrund på webbplatsen händelser. 
* **Registrera databasen i katalogdatabasen**. 

1. I PowerShell ISE öppnar *...\Learning Modules\ProvisionTenants\Demo-ProvisionAndCatalog.ps1* och ange **$Scenario = 2**. Distribuera katalogen klienten och registrera de fördefinierade innehavarna

1. Lägga till en brytpunkt i skriptet genom att placera markören på raden 49, `& $PSScriptRoot\New-TenantApp.ps1`, och tryck sedan på **F9**.
1. Kör skript genom att trycka på **F5**. 
1.  Skriptkörningen slutar vid brytpunkten, tryck på **F11** till steg i New-Catalog.ps1-skript.
1.  Spåra skriptkörningen med menyalternativen Debug F10 och F11, för att gå över eller till kallas för funktioner.

När klienten har etablerats, öppna den nya innehavaren händelser webbplats.

   ![Röd lönn tävling](media/saas-standaloneapp-provision-and-catalog/redmapleracing.png)

Sedan kan du granska de nya resurser som skapades i Azure-portalen. 

   ![Röd lönn kapplöpningar resurser](media/saas-standaloneapp-provision-and-catalog/redmapleracing-resources.png)


## <a name="to-stop-billing-delete-resource-groups"></a>Om du vill stoppa fakturering, tar du bort resursgrupper ##

Ta bort alla resursgrupper som du skapade för att stoppa den associerade faktureringen när du är klar med att utforska exemplet.

## <a name="additional-resources"></a>Ytterligare resurser

- Mer information om program med flera klienter SaaS-databasen finns [designmönster för SaaS-program med flera innehavare](saas-tenancy-app-design-patterns.md).

## <a name="next-steps"></a>Nästa steg

I den här guiden har du lärt dig:

> [!div class="checklist"]
> * Så här distribuerar du Wingtip biljetter SaaS fristående program.
> * Om servrar och databaser som ingår i appen.
> * Ta bort resurser för att stoppa relaterade fakturering.

Du kan utforska hur katalogen används för att stödja olika mellan klient-scenarier med hjälp av databasen per klient-versionen av den [Wingtip biljetter SaaS-program](https://docs.microsoft.com/en-us/azure/sql-database/saas-dbpertenant-wingtip-app-overview).  
