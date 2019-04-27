---
title: Exempel - ISO 27001 ASE/SQL arbetsbelastning skissen - distributionsstegen
description: Distribuera stegen för ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen exemplet.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 78f608aedd53aa1071eaf88864f5a63f8f9e6072
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60875999"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuera arbetsbelastningen skissen-exempel för ISO 27001 App Service Environment/SQL-databas

Följande steg måste vidtas för att distribuera Azure skisser ISO 27001 App Service Environment/SQL Database arbetsbelastning skissen exemplet:

> [!div class="checklist"]
> - Distribuera den [ISO 27001 delade tjänster](../iso27001-shared/index.md) skissen exemplet
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera exemplet ISO 27001 delade tjänster skiss

Innan det här exemplet skissen kan distribueras, den [ISO 27001 delade tjänster](../iso27001-shared/index.md) skissen exemplet måste distribueras till målprenumerationen. Utan en lyckad distribution av ISO 27001 delade tjänster skissen exemplet kommer det här exemplet skissen saknar beroenden för infrastruktur och misslyckas under distributionen.

> [!IMPORTANT]
> Det här exemplet skissen måste tilldelas i samma prenumeration som den [ISO 27001 delade tjänster](../iso27001-shared/index.md) skissen exemplet.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Först måste implementera skissen exemplet genom att skapa en ny skiss i din miljö med exemplet som en starter.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **ISO 27001: ASE/SQL-arbetsbelastningen** skissen exemplet under _andra exempel_ och välj **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av ISO 27001 ASE/SQL arbetsbelastning skissen exemplet.
   - **Definitionens plats**: Använd de tre punkterna och välj hanteringsgruppen för att spara din kopia av exemplet för att.

1. Välj den _artefakter_ fliken högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skissen exemplet. Många av artefakter har parametrar som ska vi definiera senare. Välj **spara utkast** när du har granskat skissen exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel-kopia

Din kopia av exemplet skissen har skapats i din miljö. Den skapas i **Draft** läge och måste vara **publicerad** innan den kan tilldelas och distribueras. Kopia av exemplet skissen kan anpassas till din miljö och behov, utan att ändringar kan flytta dem från standarden ISO 27001.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta din kopia av exemplet skissen och markera den.

1. Välj **publicera skiss** överst på sidan. I den nya sidan till höger, tillhandahåller en **Version** för din kopia av exemplet skissen. Den här egenskapen är användbar för om du gör en ändring av senare. Ange **ändra anteckningar** som ”första versionen publicerade från ISO 27001 skissen provet”. Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel-kopia

När kopian av exemplet skissen har installerats utan **publicerad**, så kan de tilldelas en prenumeration inom den sparades till hanteringsgruppen. Det här steget är där parametrar har angetts så att varje distribution av kopian av exemplet skissen unikt.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Välj den **skiss definitioner** sidan till vänster. Använd filtren för att hitta din kopia av exemplet skissen och markera den.

1. Välj **tilldela skissen** överst på sidan för skissen definition.

1. Ange parametervärden för skisstilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som är i hanteringsgruppen som du sparade din kopia av exemplet skissen till. Om du väljer mer än en prenumeration skapas en tilldelning för att använda de parametrar som anges.
     - **Tilldelningsnamn**: Namnet är förifyllda baserat på namnet på skissen.
       Ändra vid behov eller lämna skick.
     - **Plats**: Välj en region för den hanterade identitet som ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Definitionsversion för skissen**: Välj en **publicerad** version av din kopia av exemplet skissen.

   - Lås tilldelning

     Markera skiss Lås för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Låt standardvärdet _systemtilldelad_ hanterad identitet alternativet.

   - Skissparametrar

     De parametrar som definierats i det här avsnittet används av många av artefakter i skissdefinitionen för att tillhandahålla konsekvens.

     - **Organisationsnamn**: Ange ett kort-namn för din organisation. Den här egenskapen används främst för namngivning av resurser.
     - **Delad tjänst prenumerations-ID**: Prenumerations-ID där den [ISO 27001 delade tjänster](../iso27001-shared/index.md) skissen exemplet tilldelas.
     - **Standard undernätet adressprefix**: CIDR-notation för standard-undernät för virtuellt nätverk.
       Standardvärdet är _10.1.0.0/16_.
     - **Arbetsbelastningen plats**: Anger vilken plats som artefakterna distribueras till. Inte alla tjänster som är tillgängliga på alla platser. Artefakter som distribuerar tjänster erbjuder alternativ för parametern att distribuera den artefakten som plats.

   - Artefaktparametrar

     De parametrar som definierats i det här avsnittet gäller för artefakten som den definieras. De här parametrarna kan [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [artefakt parametrar tabell](#artifact-parameters-table).

1. När alla parametrar har angetts, väljer **tilldela** längst ned på sidan. Skisstilldelningen har skapats och distribution av artefakt påbörjas. Distributionen tar ungefär en timme. Öppna skisstilldelningen för att kontrollera statusen för distributionen.

> [!WARNING]
> Tjänsten Azure skisser och inbyggda skissen exemplen är **kostar**. Azure-resurser är [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd den [priskalkylator](https://azure.microsoft.com/pricing/calculator/) att uppskatta kostnaden för att köra resurser som har distribuerats av den här skissen-exemplet.

## <a name="artifact-parameters-table"></a>Artefakten parametrar tabell

Följande tabell innehåller en lista över skissen artefakt parametrar:

|Namn på artefakt|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Resursgrupp för log Analytics|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-workload-log-rg` så att resursgruppen som är unikt.|
|Resursgrupp för log Analytics|Resursgrupp|Location|**Låst** -parametern skissen.|
|Log Analytics-mall|Resource Manager-mall|Tjänstenivå|Anger nivån av Log Analytics-arbetsytan. Standardvärdet är _PerNode_.|
|Log Analytics-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Log Analytics-mall|Resource Manager-mall|Location|Regionen som används för att skapa Log Analytics-arbetsytan. Standardvärdet är _USA, västra 2_.|
|Resursgrupp för nätverk|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-workload-net-rg` så att resursgruppen som är unikt.|
|Resursgrupp för nätverk|Resursgrupp|Location|**Låst** -parametern skissen.|
|Nätverkssäkerhetsgrupp-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Privat IP för Azure Firewall|Konfigurerar privata IP-Adressen för den [Azure brandväggen](../../../../firewall/overview.md). Bör vara en del i CIDR-format som definierats i _ISO 27001: Delade tjänster_ artefakt parametern **adressprefix för Azure-brandvägg undernät**. Standardvärdet är _10.0.4.4_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Prenumerations-ID för delade tjänster|Värde som används för att aktivera VNET-peering mellan en arbetsbelastning och delade tjänster.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Adressprefix för virtuellt nätverk|CIDR-format för det virtuella nätverket. Standardvärdet är _10.1.0.0/16_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Adressprefix för standardundernät|CIDR-notation för standard-undernät för virtuellt nätverk. Standardvärdet är _10.1.0.0/16_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Lägger till IP-adress|IP-adressen för den första lägger till Virtuella. Det här värdet används som anpassad VNET DNS.|
|Key Vault-resursgrupp|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-workload-kv-rg` så att resursgruppen som är unikt.|
|Key Vault-resursgrupp|Resursgrupp|Location|**Låst** -parametern skissen.|
|Key Vault-mall|Resource Manager-mall|Objekt-ID för AAD|AAD-objektidentifierare för det konto som kräver åtkomst till Key Vault-instansen. Inget standardvärde värde och får inte vara tomt. Om du vill hitta det här värdet från Azure-portalen, Sök efter och Välj ”användare” _Services_. Använd den _namn_ om du vill filtrera på kontonamnet och välja det kontot. På den _användarprofil_ väljer ”Klicka för att kopiera”-ikonen bredvid den _objekt-ID_.|
|Key Vault-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Key Vault-mall|Resource Manager-mall|Key Vault-SKU|Anger SKU: N för Nyckelvalvet som har skapats. Standardvärdet är _Premium_.|
|Key Vault-mall|Resource Manager-mall|Azure SQL Server-administratörens användarnamn|Användarnamnet som används för att få åtkomst till Azure SQL Server. Måste matcha samma egenskapsvärdet i **mall för Azure SQL Database**. Standardvärdet är _sql-administratörsanvändare_.|
|Azure SQL Database-resursgrupp|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-workload-azsql-rg` så att resursgruppen som är unikt.|
|Azure SQL Database-resursgrupp|Resursgrupp|Location|**Låst** -parametern skissen.|
|Azure SQL Database-mall|Resource Manager-mall|Azure SQL Server-administratörens användarnamn|Användarnamn för Azure SQL-servern. Måste matcha samma egenskapsvärdet i **Key Vault mallen**. Standardvärdet är _sql-administratörsanvändare_.|
|Azure SQL Database-mall|Resource Manager-mall|Administratörslösenord för Azure SQL-servern (resurs-ID för Key Vault)|Resurs-ID för Nyckelvalvet. Använd ”/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv” och Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med den  **Organisationsnamn** skiss parametern.|
|Azure SQL Database-mall|Resource Manager-mall|Administratörslösenord för Azure SQL-servern (hemligt namn på Key Vault)|Användarnamnet för SQL Server-administratören. Måste matcha värdet i **Key Vault mallen** egenskapen **Azure SQL Server-administratörsanvändarnamn**.|
|Azure SQL Database-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Azure SQL Database-mall|Resource Manager-mall|Objekt-ID för AAD-administratör|AAD objekt-ID för den användare som ska tilldelas som en Active Directory-administratör. Inget standardvärde värde och får inte vara tomt. Om du vill hitta det här värdet från Azure-portalen, Sök efter och Välj ”användare” _Services_. Använd den _namn_ om du vill filtrera på kontonamnet och välja det kontot. På den _användarprofil_ väljer ”Klicka för att kopiera”-ikonen bredvid den _objekt-ID_.|
|Azure SQL Database-mall|Resource Manager-mall|Administratörsinloggning för AAD|För närvarande kan Microsoft-konton (t.ex live.com eller outlook.com) inte anges som administratör. Endast användare och säkerhetsgrupper inom organisationen kan ställas in som administratör. Inget standardvärde värde och får inte vara tomt. Om du vill hitta det här värdet från Azure-portalen, Sök efter och Välj ”användare” _Services_. Använd den _namn_ om du vill filtrera på kontonamnet och välja det kontot. På den _användarprofil_ sidan, kopiera den _användarnamn_.|
|Resursgrupp för App Service Environment|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-workload-ase-rg` så att resursgruppen som är unikt.|
|Resursgrupp för App Service Environment|Resursgrupp|Location|**Låst** -parametern skissen.|
|Mall för apptjänstmiljö|Resource Manager-mall|Domännamn|Namnet på den Active Directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Mall för apptjänstmiljö|Resource Manager-mall|ASE-plats|Plats för App Service Environment. Standardvärdet är _USA, västra 2_.|
|Mall för apptjänstmiljö|Resource Manager-mall|Kvarhållning av Application Gateway-logg i dagar|Datalagring i dagar. Standardvärdet är _365_.|

## <a name="next-steps"></a>Nästa steg

Nu när du har gått igenom stegen för att distribuera arbetsbelastningen skissen-exempel för ISO 27001 App Service Environment/SQL-databas finns i följande artiklar om du vill veta mer om arkitekturen och kontroll mappning:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen - översikt](./index.md)
> [ISO 27001 App Service Environment/SQL-databas arbetsbelastning skissen - kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).