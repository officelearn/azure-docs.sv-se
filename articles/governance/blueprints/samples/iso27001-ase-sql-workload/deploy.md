---
title: Distribuera skissexemplar för ISO 27001 ASE/SQL-arbetsbelastning
description: Distribuera steg i arbetsbelastningen ISO 27001 App Service Environment/SQL Database-arbetsbelastningskopempel med hjälp av parameterinformation om skissartefakt.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922583"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuera arbetsbelastningsritningsprovet för ISO 27001 App Service Environment/SQL Database

Om du vill distribuera azure blueprints ISO 27001 App Service Environment/SQL Database arbetsbelastningsritningsprov måste följande åtgärder vidtas:

> [!div class="checklist"]
> - Distribuera skissexemplet [för delade tjänster i ISO 27001](../iso27001-shared/index.md)
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera skissexemplet för delade tjänster i ISO 27001

Innan det här skissexemplet kan distribueras måste skissexemplet [ISO 27001 Shared Services](../iso27001-shared/index.md) distribueras till målprenumerationen. Utan en lyckad distribution av skissexemplet ISO 27001 Shared Services saknas infrastrukturberoenden och misslyckas under distributionen.

> [!IMPORTANT]
> Det här skissexemplet måste tilldelas i samma prenumeration som skissexemplet [ISO 27001 Shared Services.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på skissexemplet **ISO 27001: ASE/SQL Workload** under _Andra exempel_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn**: Ange ett namn för ditt exemplar av skissexemplet ISO 27001 ASE/SQL-arbetsbelastning.
   - **Definitionsplats**: Använd ellipsen och välj hanteringsgruppen för att spara din kopia av exemplet.

1. Välj fliken _Artefakter_ högst upp på sidan eller **Nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissexemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du har granskat skissexemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempelkopian

Ditt exemplar av skissexemplet har nu skapats i din miljö. Den skapas i **utkastläge** och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skissprovet kan anpassas till din miljö och dina behov, men den ändringen kan flytta den bort från ISO 27001-standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Publicera skiss** högst upp på sidan. På den nya sidan till höger anger du en **version** för ditt exemplar av skissexemplet. Den här egenskapen är användbar om du gör en ändring senare. Ange **ändringsanteckningar** som "Första versionen som publiceras från skissexemplet ISO 27001". Välj sedan **Publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempelkopian

När kopian av skissexemplet har **publicerats**kan den tilldelas en prenumeration inom den hanteringsgrupp som den sparades till. Det här steget är där parametrar tillhandahålls för att göra varje distribution av kopian av skissexemplet unikt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **Skissdefinitioner** till vänster. Använd filtren för att hitta kopian av skissexemplet och välj det sedan.

1. Välj **Tilldela skiss** högst upp på skissdefinitionssidan.

1. Ange parametervärdena för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av prenumerationerna i hanteringsgruppen som du sparade din kopia av skissexemplet till. Om du väljer mer än en prenumeration skapas en tilldelning för var och en med de angivna parametrarna.
     - **Tilldelningsnamn**: Namnet är förifyllt för dig baserat på namnet på skissen.
       Ändra efter behov eller lämna som det är.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Blueprint definition version**: Välj en **publicerad** version av ditt exemplar av skissen provet.

   - Lås tilldelning

     Välj inställningen för skisslås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna _standardalternativet för tilldelade_ hanterade identiteter.

   - Skissparametrar

     De parametrar som definieras i det här avsnittet används av många av artefakterna i skissdefinitionen för att ge konsekvens.

     - **Organisationsnamn**: Ange ett kortnamn för din organisation. Den här egenskapen används främst för att namnge resurser.
     - **Prenumerations-ID för delade**tjänster: Prenumerations-ID där skissexemplet [ISO 27001 Shared Services](../iso27001-shared/index.md) har tilldelats.
     - **Standardprefix för undernätsadress:** CIDR-notationen för det virtuella nätverksstandardundernätet.
       Standardvärdet är _10.1.0.0/16_.
     - **Arbetsbelastningsplats**: Bestämmer vilken plats artefakterna distribueras till. Alla tjänster är inte tillgängliga på alla platser. Artefakter som distribuerar sådana tjänster är ett parameteralternativ för platsen att distribuera artefakten till.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [tabellen Artefaktparametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen börjar. Distributionen tar ungefär en timme. Om du vill kontrollera status för distributionen öppnar du skisstilldelningen.

> [!WARNING]
> Azure Blueprints-tjänsten och de inbyggda skissexemplen är **kostnadsfria**. Azure-resurser [prissätts efter produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för att köra resurser som distribueras av det här skissexemplet.

## <a name="artifact-parameters-table"></a>Tabellen Artefaktparametrar

Följande tabell innehåller en lista över parametrarna för skissartefakt:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Resursgrupp för Logganalys|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-workload-log-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Logganalys|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för Logganalys|Resource Manager-mall|Tjänstenivå|Anger nivån på log analytics-arbetsytan. Standardvärdet är _PerNode_.|
|Mall för Logganalys|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för Logganalys|Resource Manager-mall|Location|Region som används för att skapa log analytics-arbetsytan. Standardvärdet är _Västra US 2_.|
|Resursgrupp för nätverk|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-workload-net-rg` med för att göra resursgruppen unik.|
|Resursgrupp för nätverk|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för nätverkssäkerhetsgrupp|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Privat IP-adress i Azure-brandväggen|Konfigurerar den privata IP-adressen för [Azure-brandväggen](../../../../firewall/overview.md). Bör vara en del av CIDR-notationen som definieras i _ISO 27001: Shared Services_ artefaktparameter Azure Firewall **undernätsadressprefix**. Standardvärdet är _10.0.4.4_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prenumerations-ID för delade tjänster|Värde som används för att aktivera VNET-peering mellan en arbetsbelastning och delade tjänster.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för virtuell nätverksadress|CIDR-notationen för det virtuella nätverket. Standardvärdet är _10.1.0.0/16_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Standardprefix för undernätsadress|CIDR-notationen för det virtuella nätverksstandardundernätet. Standardvärdet är _10.1.0.0/16_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|LÄGGER TILL IP-adress|IP-adressen för den första TILLÄGGSDA VM. Det här värdet används som anpassad VNET DNS.|
|Resursgrupp för Nyckelvalv|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-workload-kv-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Nyckelvalv|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mallen Nyckelvalv|Resource Manager-mall|AAD-objekt-ID|AAD-objektidentifieraren för kontot som kräver åtkomst till Key Vault-instansen. Inget standardvärde och kan inte lämnas tomt. Om du vill hitta det här värdet från Azure-portalen söker du efter och väljer "Användare" under _Tjänster_. Använd rutan _Namn_ för att filtrera efter kontonamnet och välj det kontot. På sidan _Användarprofil_ väljer du ikonen "Klicka för att kopiera" _bredvid objekt-ID._|
|Mallen Nyckelvalv|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mallen Nyckelvalv|Resource Manager-mall|SKU för nyckelvalv|Anger SKU för nyckelvalvet som skapas. Standardvärdet är _Premium_.|
|Mallen Nyckelvalv|Resource Manager-mall|Användarnamn för Azure SQL Server-administratör|Användarnamnet som används för att komma åt Azure SQL Server. Måste matcha samma egenskapsvärde i **Azure SQL Database-mallen**. Standardvärdet är _sql-admin-user_.|
|Resursgrupp för Azure SQL Database|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-workload-azsql-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Azure SQL Database|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för Azure SQL-databas|Resource Manager-mall|Användarnamn för Azure SQL Server-administratör|Användarnamn för Azure SQL Server. Måste matcha samma egenskapsvärde i **Mallen Key Vault**. Standardvärdet är _sql-admin-user_.|
|Mall för Azure SQL-databas|Resource Manager-mall|Azure SQL Server-administratörslösenord (Resurs-ID för Key Vault)|Resurs-ID för nyckelvalvet. Använd "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" `{subscriptionId}` och ersätt med `{orgName}` ditt prenumerations-ID och med skissparametern **Organisationsnamn.**|
|Mall för Azure SQL-databas|Resource Manager-mall|Azure SQL Server admin lösenord (Key Vault hemligt namn)|Användarnamn för SQL Server-administratören. Måste matcha värdet i **egenskapen Key Vault-mall** **Azure SQL Server admin username**.|
|Mall för Azure SQL-databas|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för Azure SQL-databas|Resource Manager-mall|AAD-administratörsobjekt-ID|AAD-objekt-ID för den användare som ska tilldelas som Active Directory-administratör. Inget standardvärde och kan inte lämnas tomt. Om du vill hitta det här värdet från Azure-portalen söker du efter och väljer "Användare" under _Tjänster_. Använd rutan _Namn_ för att filtrera efter kontonamnet och välj det kontot. På sidan _Användarprofil_ väljer du ikonen "Klicka för att kopiera" _bredvid objekt-ID._|
|Mall för Azure SQL-databas|Resource Manager-mall|AAD-administratörsinloggning|För närvarande kan Microsoft-konton (till exempel live.com eller outlook.com) inte anges som administratör. Endast användare och säkerhetsgrupper inom organisationen kan anges som administratör. Inget standardvärde och kan inte lämnas tomt. Om du vill hitta det här värdet från Azure-portalen söker du efter och väljer "Användare" under _Tjänster_. Använd rutan _Namn_ för att filtrera efter kontonamnet och välj det kontot. Kopiera _användarnamnet på_sidan _Användarprofil_ .|
|Resursgrupp för apptjänstmiljö|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-workload-ase-rg` med för att göra resursgruppen unik.|
|Resursgrupp för apptjänstmiljö|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för apptjänstmiljö|Resource Manager-mall|Domännamn|Namnet på active directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Mall för apptjänstmiljö|Resource Manager-mall|ASE-plats|Plats för apptjänstmiljö. Standardvärdet är _Västra US 2_.|
|Mall för apptjänstmiljö|Resource Manager-mall|Application Gateway logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera arbetsbelastningskopempelet ISO 27001 App Service Environment/SQL Database kan du läsa följande artiklar om arkitekturen och kontrollmappningen:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database arbetsbelastning skiss - Översikt](./index.md)
> [ISO 27001 App Service Miljö / SQL Database arbetsbelastning skiss - Kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).