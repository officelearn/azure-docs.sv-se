---
title: Övervaka prestanda för en fragmenterad databas med flera innehavare
description: Övervaka och hantera prestanda för fragmenterad Azure SQL-databas med flera innehavare i en SaaS-app med flera innehavare
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 0af476b69f2effd836fe76d62059259076c16f53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79214163"
---
# <a name="monitor-and-manage-performance-of-sharded-multi-tenant-azure-sql-database-in-a-multi-tenant-saas-app"></a>Övervaka och hantera prestanda för fragmenterad Azure SQL-databas med flera innehavare i en SaaS-app med flera innehavare

I den här självstudien utforskas flera viktiga prestandahanteringsscenarier som används i SaaS-program. Med hjälp av en belastningsgenerator för att simulera aktivitet över fragmenterade databaser med flera innehavare demonstreras de inbyggda övervaknings- och varningsfunktionerna i SQL Database.

Wingtip-klientprogrammet SaaS Multi-tenant Database använder en fragmenterad datamodell för flera innehavare, där platsdata (klienten) distribueras av klient-ID över potentiellt flera databaser. Precis som för flera SaaS-program så är de förväntade belastningsmönstren för klienterna oberäkneliga och sporadiska. Biljettförsäljningar kan med andra ord ske när som helst. För att dra nytta av det här typiska databasanvändningsmönstret kan databaser skalas upp och ned för att optimera kostnaden för en lösning. Med den här typen av mönster är det viktigt att övervaka databasresursanvändningen för att säkerställa att belastningarna är rimligt balanserade mellan potentiellt flera databaser. Du måste också se till att enskilda databaser har tillräckliga resurser och inte når sina [DTU-gränser.](sql-database-purchase-models.md#dtu-based-purchasing-model) Den här självstudien undersöker olika sätt att övervaka och hantera databaser och hur du vidtar korrigerande åtgärder som svar på variationer i arbetsbelastningen.

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> 
> * Simulera användning på en fragmenterad databas med flera innehavare genom att köra en medföljande lastgenerator
> * Övervaka databasen när den svarar på den ökade belastningen
> * Skala upp databasen som svar på den ökade databasbelastningen
> * Etablera en klient i en databas med en enda klient

Följande krav måste uppfyllas för att kunna köra den här självstudiekursen:

* Wingtip-biljetterna SaaS Databasapp för flera innehavare distribueras. Information om hur du distribuerar på mindre än fem minuter finns i [Distribuera och utforska Wingtip Tickets SaaS-databasprogrammet för flera innehavare](saas-multitenantdb-get-started-deploy.md)
* Azure PowerShell ska ha installerats. Mer information finns i [Kom igång med Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps)

## <a name="introduction-to-saas-performance-management-patterns"></a>Introduktion till SaaS prestandahanteringsmönster

Hantering av databasprestanda innebär kompilering och analys av prestandadata för att sedan reagera på den genom att justera parametrarna för att bibehålla en acceptabel svarstid för programmet. 

### <a name="performance-management-strategies"></a>Strategier för prestandahantering

* För att undvika att behöva övervaka prestanda manuellt är det mest effektivt att **ställa in aviseringar som utlöses när databaser avviker från normala intervall**.
* För att svara på kortsiktiga fluktuationer i beräkningsstorleken för en databas **kan DTU-nivån skalas upp eller ned**. Om denna fluktuation inträffar regelbundet eller förutsägbart **kan skala databasen schemaläggas så att den sker automatiskt**. Du kan exempelvis skala ned när du vet att din arbetsbelastning är lätt, på nätter eller helger till exempel.
* Om du vill svara på långsiktiga fluktuationer, eller ändringar i klienterna, **kan enskilda klienter flyttas till andra databaser**.
* För att svara på kortsiktiga ökningar i *enskilda* klientbelastning, **kan enskilda klienter tas ut ur en databas och tilldelas en enskild beräkningsstorlek**. När belastningen har minskat kan klienten sedan returneras till databasen för flera innehavare. När detta är känt i förväg kan klienter flyttas förebyggande för att säkerställa att databasen alltid har de resurser den behöver och för att undvika påverkan på andra klienter i databasen för flera innehavare. Om det här kravet är förutsägbart, till exempel en plats där ett populärt evenemang skapar en rusning efter biljetter, kan det här hanteringsbeteendet integreras i programmet.

[Azure-portalen](https://portal.azure.com) tillhandahåller inbyggd övervakning och avisering för de flesta resurser. För SQL Database är övervakning och avisering tillgänglig på databaser. Den här inbyggda övervakningen och aviseringen är resursspecifik, så det är praktiskt att använda för ett litet antal resurser, men det är inte praktiskt när du arbetar med många resurser.

För scenarier med stora volymer, där du arbetar med många resurser, kan [Azure Monitor-loggar](https://azure.microsoft.com/services/log-analytics/) användas. Det här är en separat Azure-tjänst som tillhandahåller analyser över utgivna loggar som samlats in i en Log Analytics-arbetsyta. Azure Monitor-loggar kan samla in telemetri från många tjänster och användas för att fråga och ställa in aviseringar.

## <a name="get-the-wingtip-tickets-saas-multi-tenant-database-application-source-code-and-scripts"></a>Hämta källkoden och skripten för Wingtip-biljetterna SaaS Multi-tenant Database

Wingtip-biljetterna SaaS-databasskript för flera innehavare och programkällakod finns i [WingtipTicketsSAaS-MultitenantDB](https://github.com/microsoft/WingtipTicketsSaaS-MultiTenantDB) GitHub-repo. Kolla in den [allmänna vägledningen](saas-tenancy-wingtip-app-guidance-tips.md) för steg för att ladda ner och låsa upp Wingtip Tickets SaaS skript.

## <a name="provision-additional-tenants"></a>Etablera ytterligare klienter

För att få en god förståelse för hur prestandaövervakning och hantering fungerar i stor skala kräver den här självstudien att du har flera klienter i en fragmenterad databas med flera innehavare.

Om du redan har etablerat en batch med klienter i en föregående självstudiekurs går du till avsnittet [Simulera användning i alla klientdatabaser.](#simulate-usage-on-all-tenant-databases)

1. I **PowerShell ISE**, öppna ... \\Lärande Moduler\\Prestanda övervakning\\och förvaltning*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ange **$DemoScenario** = **1**, _Etablera en batch med klienter_
1. Tryck **F5** för att köra skriptet.

Skriptet distribuerar 17 klienter till databasen med flera innehavare på några minuter. 

Skriptet *New-TenantBatch* skapar nya klienter med unika klientnycklar i den fragmenterade databasen för flera innehavare och initierar dem med klientnamn och platstyp. Detta är förenligt med hur appen etablerar en ny klient. 

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulera användning på alla klientdatabaser

*Skriptet Demo-PerformanceMonitoringAndManagement.ps1* tillhandahålls som simulerar en arbetsbelastning som körs mot databasen med flera innehavare. Belastningen genereras med hjälp av ett av de tillgängliga belastningsscenarierna:

| Demo | Scenario |
|:--|:--|
| 2 | Generera normal intensitetsbelastning (ca 30 DTU) |
| 3 | Generera belastning med längre skurar per klient|
| 4 | Generera belastning med högre DTU-skurar per klient (cirka 70 DTU)|
| 5 | Generera en hög intensitet (cirka 90 DTU) på en enda klient plus en normal intensitet belastning på alla andra klienter |

Belastningsgeneratorn tillämpar en *syntetisk* enbart-CPU-belastning på varje klientdatabas. Generatorn startar ett jobb för varje klientdatabas som anropar en lagrad procedur med jämna mellanrum, vilket genererar belastningen. Belastningsnivåerna (i DU: er), varaktighet och intervall varierar mellan alla databaser, simulera oförutsägbara klientaktivitet.

1. I **PowerShell ISE**, öppna ... \\Lärande Moduler\\Prestanda övervakning\\och förvaltning*Demo-PerformanceMonitoringAndManagement.ps1*. Ha det här skriptet öppet medan du kör scenarierna i den här guiden.
1. Ställ in **$DemoScenario** = **2**, _Generera normal intensitet belastning_
1. Tryck på **F5** för att använda en belastning på alla dina klienter.

Wingtip Biljetter SaaS Multi-tenant Database är en SaaS-app, och den verkliga belastningen på en SaaS-app är vanligtvis sporadisk och oförutsägbar. För att simulera det så skapar belastningsgeneratorn en slumpmässig belastning som distribueras över alla klienterna. Flera minuter behövs för att belastningsmönstret ska dyka upp, så kör lastgeneratorn i 3-5 minuter innan du försöker övervaka belastningen i följande avsnitt.

> [!IMPORTANT]
> Belastningsgeneratorn körs som en serie jobb i ett nytt PowerShell-fönster. Om du stänger sessionen stannar lastgeneratorn. Lastgeneratorn förblir i ett *jobbansnat* tillstånd där den genererar belastning på alla nya klienter som etableras när generatorn har startats. Använd *Ctrl-C* för att sluta anropa nya jobb och avsluta skriptet. Lastgeneratorn fortsätter att köras, men bara på befintliga klienter.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Övervaka resursanvändning med Hjälp av Azure-portalen

Om du vill övervaka resursanvändningen som uppstår från den belastning som används öppnar du portalen till databasen med flera innehavare, **klienter1**, som innehåller klienterna:

1. Öppna [Azure-portalen](https://portal.azure.com) och bläddra till servern *tenants1-mt-USER&lt;&gt;*.
1. Bläddra nedåt och leta upp databaser och klicka på **klienter1**. Den här fragmenterade databasen för flera innehavare innehåller alla klienter som har skapats hittills.

![databasdiagram](./media/saas-multitenantdb-performance-monitoring/multitenantdb.png)

Observera **DTU-diagrammet.**

## <a name="set-performance-alerts-on-the-database"></a>Ange prestandavarningar i databasen

Ställ in en avisering på \>databasen som utlöser 75 % användning enligt följande:

1. Öppna *databasen tenants1* (på *&lt;&gt; klienten1-mt-USER-servern)* i [Azure-portalen](https://portal.azure.com).
1. Klicka på **aviseringsregler** och därefter på **+ lägg till avisering**:

   ![lägg till avisering](media/saas-multitenantdb-performance-monitoring/add-alert.png)

1. Ange ett namn, exempelvis **hög DTU**,
1. Ställ in följande värden:
   * **Mått = DTU-procent**
   * **Villkor = större än**
   * **Tröskelvärdet = 75**.
   * **Period = Under de senaste 30 minuterna**
1. Lägg till en e-postadress i rutan *Ytterligare administratörs-e-post* och klicka på **OK**.

   ![ange varning](media/saas-multitenantdb-performance-monitoring/set-alert.png)

## <a name="scale-up-a-busy-database"></a>Skala upp en upptagen databas

Om inläsningsnivån ökar i en databas till den grad att den maxar databasen och når 100 % DTU-användning, påverkas databasprestanda, vilket kan göra frågesvarstiderna långsammare.

**På kort sikt**bör du överväga att skala upp databasen för att tillhandahålla ytterligare resurser eller ta bort klienter från databasen med flera innehavare (flytta dem från databasen med flera innehavare till en fristående databas).

**Längre sikt**bör du överväga att optimera frågor eller indexanvändning för att förbättra databasens prestanda. Beroende på programmets känslighet för prestandaproblem är det bästa praxis att skala upp en databas innan den når 100 % DTU-användning. Använd en avisering för att varna dig i förväg.

Du kan simulera en upptagen databas genom att öka belastningen som produceras av generatorn. Vilket gör att klienterna spricker oftare och längre ökar belastningen på databasen med flera innehavare utan att ändra kraven för de enskilda klienterna. Att skala upp databasen görs enkelt i portalen eller från PowerShell. Den här övningen använder portalen.

1. Ange *$DemoScenario* = **3**, _Generera belastning med längre och mer frekventa skurar per databas_ för att öka intensiteten för den sammanlagda belastningen på databasen utan att ändra den högsta belastning som krävs av varje klient.
1. Tryck på **F5** för att tillämpa en belastning på alla dina klientdatabaser.
1. Gå till **klienten1-databasen** i Azure-portalen.

Övervaka den ökade databas-DTU-användningen i det övre diagrammet. Det tar några minuter för den nya högre belastningen att sparka in, men du bör snabbt se databasen börjar slå max utnyttjande, och som belastningen stadigt i det nya mönstret, överbelastar den snabbt databasen.

1. Om du vill skala upp databasen klickar du på **Prisnivå (skala DU:er)** i inställningsbladet.
1. Justera **DTU-inställningen** till **100**. 
1. Klicka på **Använd** om du vill skicka begäran om att skala databasen.

Gå tillbaka till **tenants1** > **Översikt** för att visa övervakningsdiagrammen. Övervaka effekten av att förse databasen med fler resurser (även om det med få klienter och en randomiserad belastning inte alltid är lätt att se slutgiltigt tills du kör under en tid). Medan du tittar på diagrammen komma ihåg att 100% på det övre diagrammet nu representerar 100 D RU, medan på det nedre diagrammet 100% är fortfarande 50 D RU: er.

Databaserna är online och fullt tillgängliga under hela processen. Programkod ska alltid skrivas för att försöka ta bort ignorerade anslutningar igen och kommer därför att återansluta till databasen.

## <a name="provision-a-new-tenant-in-its-own-database"></a>Etablera en ny klient i sin egen databas 

Med den fragmenterade modellen med flera innehavare kan du välja om du vill etablera en ny klient i en databas med flera innehavare tillsammans med andra klienter eller etablera klienten i en egen databas. Genom att etablera en klient i sin egen databas, drar det nytta av isolering inneboende i den separata databasen, så att du kan hantera prestanda för den klienten oberoende av andra, återställa den klienten oberoende av andra, etc. Du kan till exempel välja att placera kostnadsfria prov- eller stamkunder i en databas med flera innehavare och premiumkunder i enskilda databaser.  Om isolerade databaser med en klient har skapats kan de fortfarande hanteras kollektivt i en elastisk pool för att optimera resurskostnaderna.

Om du redan har etablerat en ny klient i en egen databas hoppar du över de närmaste stegen.

1. I **PowerShell ISE**, öppna ... \\Utbildningsmoduler\\ProvisionTenants\\*Demo-ProvisionTenants.ps1*. 
1. Ändra **$TenantName = "Salix Salsa"** och **$VenueType = "dans"**
1. Ange **$Scenario** = **2**, Etablera en klient i en ny databas med en enda _klientorganisation_
1. Tryck **F5** för att köra skriptet.

Skriptet etablerar den här klienten i en separat databas, registrerar databasen och klienten med katalogen och öppnar sedan klientens händelsesida i webbläsaren. Uppdatera sidan Events Hub och du kommer att se "Salix Salsa" har lagts till som en plats.

## <a name="manage-performance-of-an-individual-database"></a>Hantera prestanda för en enskild databas

Om en enskild klient i en databas med flera innehavare upplever en ihållande hög belastning kan den tenderar att dominera databasresurserna och påverka andra klienter i samma databas. Om aktiviteten sannolikt kommer att fortsätta under en tid kan klienten tillfälligt flyttas från databasen och till sin egen databas med en klient. Detta gör det möjligt för klienten att ha de extra resurser den behöver och helt isolerar den från de andra klienterna.

Denna övning simulerar effekten av Salix Salsa upplever en hög belastning när biljetter går till försäljning för ett populärt evenemang.

1. Öppna ... \\ *Demo-PerformanceMonitoringAndManagement.ps1* script.
1. Ställ in **$DemoScenario = 5**, _Generera en normal belastning plus en hög belastning på en enda klient (ca 90 DTU)._
1. Ställ **in $SingleTenantName = Salix Salsa**
1. Kör skriptet med **F5**.

Gå till portalen och navigera till **salixsalsa** > **Översikt** för att visa övervakningsdiagrammen. 

## <a name="other-performance-management-patterns"></a>Andra mönster för prestandahantering

**Skalning av självbetjäning för klienter**

Eftersom skalning är en uppgift som enkelt anropas via hanterings-API:et kan du enkelt skapa möjligheten att skala klientdatabaser till ditt klientinriktade program och erbjuda den som en funktion i din SaaS-tjänst. Exempelvis kan du låta klienter självhantera upp- och nedskalning, kanske kopplat direkt till sin fakturering!

**Skala en databas uppåt och nedåt enligt ett schema för att matcha användningsmönster**

Där aggregerad klientanvändning följer förutsägbara användningsmönster kan du använda Azure Automation för att skala en databas upp och ned enligt ett schema. Skala till exempel en databas efter 18:00 och upp igen före 06:00 på vardagar när du vet att det finns en minskning av resurskraven.

## <a name="next-steps"></a>Nästa steg

I den här guiden får du lära du dig hur man:

> [!div class="checklist"]
> * Simulera användning på en fragmenterad databas med flera innehavare genom att köra en medföljande lastgenerator
> * Övervaka databasen när den svarar på den ökade belastningen
> * Skala upp databasen som svar på den ökade databasbelastningen
> * Etablera en klient i en databas med en enda klient

## <a name="additional-resources"></a>Ytterligare resurser

<!--* [Additional tutorials that build upon the Wingtip Tickets SaaS Multi-tenant Database application deployment](saas-multitenantdb-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)-->
* [Azure-automatisering](../automation/automation-intro.md)