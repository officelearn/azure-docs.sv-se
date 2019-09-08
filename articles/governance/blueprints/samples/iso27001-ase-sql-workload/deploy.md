---
title: Exempel – ISO 27001 ASE/SQL arbets belastnings skiss – distribuera steg
description: Distribuera steg i exempel på arbets belastnings skiss för ISO 27001 App Service-miljön/SQL Database.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: fb68b2f78e48dabf5b1377b7c811840a85604f53
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802425"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Distribuera exempel på arbets belastnings skiss för ISO 27001 App Service-miljön/SQL Database

För att distribuera Azure-skisserna ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss, måste du vidta följande steg:

> [!div class="checklist"]
> - Distribuera skiss exemplet för [delade tjänster i ISO 27001](../iso27001-shared/index.md)
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera skiss exemplet för delade tjänster i ISO 27001

Innan detta skiss exempel kan distribueras måste du distribuera [ISO 27001](../iso27001-shared/index.md) -skissen för delade tjänster till mål prenumerationen. Utan en lyckad distribution av skiss exemplet för delade tjänster i ISO 27001 kommer det här skiss exemplet att sakna infrastruktur beroenden och Miss lyckas under distributionen.

> [!IMPORTANT]
> Det här skiss exemplet måste tilldelas i samma prenumeration som ISO 27001-skiss exemplet för [delade tjänster](../iso27001-shared/index.md) .

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. **Hitta ISO 27001: Skiss exempel för ASE** /SQL-arbetsbelastning under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet för arbets belastningen ISO 27001 ASE/SQL.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ högst upp på sidan eller **nästa: Artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

## <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men ändringen kan flyttas bort från ISO 27001-standarden.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** som "första versionen som publicerats från ISO 27001-skiss exemplet". Välj sedan **publicera** längst ned på sidan.

## <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundinställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: Namnet är ifyllt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [Hanterade identiteter för Azure-resurser](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skiss lås inställningen för din miljö. Mer information finns i [Låsa skissresurser](../../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelade_ hanterade identiteter.

   - Skissparametrar

     De parametrar som definieras i det här avsnittet används av många av artefakterna i skiss definitionen för att ge konsekvens.

     - **Organisations namn**: Ange ett kort namn för din organisation. Den här egenskapen används främst för att namnge resurser.
     - **Prenumerations-ID för delad tjänst**: Prenumerations-ID där ISO 27001-skiss exemplet för [delade tjänster](../iso27001-shared/index.md) har tilldelats.
     - Adressprefix för **standard under nätet**: CIDR-notering för det virtuella nätverkets standard under nät.
       Standardvärdet är _10.1.0.0/16_.
     - **Arbets plats**: Bestämmer vilken plats artefakterna distribueras till. Alla tjänster är inte tillgängliga på alla platser. Artefakter som distribuerar sådana tjänster tillhandahåller ett parameter alternativ för platsen som artefakten ska distribueras till.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

## <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Namn på artefakt|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|Log Analytics resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-log-rg` för att göra resurs gruppen unik.|
|Log Analytics resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Log Analytics-mall|Resource Manager-mall|Tjänstnivå|Anger nivån för Log Analytics arbets ytan. Standardvärdet är _PerNode_.|
|Log Analytics-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Log Analytics-mall|Resource Manager-mall|Location|Region som används för att skapa Log Analytics-arbetsytan. Standardvärdet är _USA, västra 2_.|
|Nätverks resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-net-rg` för att göra resurs gruppen unik.|
|Nätverks resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Nätverkssäkerhetsgrupp-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Privat IP för Azure Firewall|Konfigurerar den privata IP-adressen för [Azure-brandväggen](../../../../firewall/overview.md). Ska ingå i CIDR-notationen som definieras _i ISO 27001: Delade tjänster_ artefakt parameter **prefix för Azure Firewall-undernät**. Standardvärdet är _10.0.4.4_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Prenumerations-ID för delade tjänster|Värde som används för att aktivera VNET-peering mellan en arbets belastning och delade tjänster.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för virtuellt nätverk|CIDR-notering för det virtuella nätverket. Standardvärdet är _10.1.0.0/16_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för standardundernät|CIDR-notering för det virtuella nätverkets standard under nät. Standardvärdet är _10.1.0.0/16_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Lägger till IP-adress|IP-adressen för den första lägger till den virtuella datorn. Det här värdet används som anpassat VNET DNS.|
|Key Vault resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-kv-rg` för att göra resurs gruppen unik.|
|Key Vault resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Key Vault-mall|Resource Manager-mall|Objekt-ID för AAD|AAD-objektmodellen för det konto som kräver åtkomst till Key Vault-instansen. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ väljer du ikonen "Klicka för att kopiera" bredvid _objekt-ID_.|
|Key Vault-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Key Vault-mall|Resource Manager-mall|Key Vault-SKU|Anger SKU: n för den Key Vault som skapas. Standardvärdet är _Premium_.|
|Key Vault-mall|Resource Manager-mall|Azure SQL Server-administratörens användarnamn|Det användar namn som används för att få åtkomst till Azure-SQL Server. Måste matcha samma egenskaps värde i **Azure SQL Database-mallen**. Standardvärdet är _SQL-admin-user_.|
|Azure SQL Database resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-azsql-rg` för att göra resurs gruppen unik.|
|Azure SQL Database resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Azure SQL Database-mall|Resource Manager-mall|Azure SQL Server-administratörens användarnamn|Användar namn för Azure-SQL Server. Måste matcha samma egenskaps värde i **Key Vault-mallen**. Standardvärdet är _SQL-admin-user_.|
|Azure SQL Database-mall|Resource Manager-mall|Azure SQL Server admin-lösenord (Key Vault resurs-ID)|Resurs-ID för Key Vault. Använd "/Subscription/{subscriptionId}/resourceGroups/{orgName}-Workload-kv/providers/Microsoft.KeyVault/Vaults/{orgName}-Workload-kv" och Ersätt `{subscriptionId}` med ditt prenumerations- `{orgName}` ID och med **organisationens namn** skiss parameter.|
|Azure SQL Database-mall|Resource Manager-mall|Azure SQL Server admin-lösenord (Key Vault hemligt namn)|SQL Server administratörens användar namn. Måste överensstämma med värdet i **Key Vault Template** -egenskapen **Azure SQL Server Admin användar namn**.|
|Azure SQL Database-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Azure SQL Database-mall|Resource Manager-mall|AAD admin-objekt-ID|AAD-objekt-ID för den användare som ska tilldelas som Active Directory administratör. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ väljer du ikonen "Klicka för att kopiera" bredvid _objekt-ID_.|
|Azure SQL Database-mall|Resource Manager-mall|AAD admin-inloggning|För närvarande kan Microsoft-konton (t. ex. live.com eller outlook.com) inte anges som administratör. Endast användare och säkerhets grupper inom organisationen kan anges som administratör. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ kopierar du _användar namnet_.|
|App Service-miljön resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-workload-ase-rg` för att göra resurs gruppen unik.|
|App Service-miljön resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Mall för apptjänstmiljö|Resource Manager-mall|Domännamn|Namnet på Active Directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Mall för apptjänstmiljö|Resource Manager-mall|ASE-plats|App Service-miljön plats. Standardvärdet är _USA, västra 2_.|
|Mall för apptjänstmiljö|Resource Manager-mall|Application Gateway logg kvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss kan du gå till följande artiklar om du vill veta mer om arkitektur och kontroll mappning:

> [!div class="nextstepaction"]
> [ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss – översikt](./index.md)
> [ISO 27001 App Service-miljön/SQL Database arbets belastnings skiss-kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).