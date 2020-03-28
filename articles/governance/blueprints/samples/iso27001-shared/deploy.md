---
title: Distribuera skissexempel för DELADE TJÄNSTER i ISO 27001
description: Distribuera steg för skissexemplet ISO 27001 Shared Services, inklusive information om skissartefaktparameter.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920680"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera skissexemplet för delade tjänster i ISO 27001

Om du vill distribuera skissprovet för Azure Blueprints ISO 27001 Shared Services måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera ditt exemplar av exemplet som **publicerat**
> - Tilldela ditt exemplar av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free) konto innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Implementera först skissprovet genom att skapa en ny skiss i din miljö med hjälp av exemplet som en förrätt.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. På sidan **Komma igång till** vänster väljer du knappen **Skapa** under Skapa _en skiss_.

1. Leta reda på skissexemplet **ISO 27001: Shared Services** under Andra _exempel_ och välj Använd det **här exemplet**.

1. Ange _grunderna_ i skissexemplet:

   - **Skissnamn**: Ange ett namn för ditt exemplar av skissexemplet ISO 27001 Shared Services.
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
     - **Undernätsprefix för delade tjänster**: Ange CIDR-notationsvärdet för nätverk av de distribuerade resurserna tillsammans.
     - **Plats för delade tjänster**: Bestämmer vilken plats artefakterna distribueras till. Alla tjänster är inte tillgängliga på alla platser. Artefakter som distribuerar sådana tjänster är ett parameteralternativ för platsen att distribuera artefakten till.
     - **Tillåten plats (Princip: Skissinitiativ för ISO 27001)**: Värde som anger tillåtna platser för resursgrupper och resurser.
     - **Log Analytics-arbetsyta för VM-agenter (Princip: Blueprint-initiativ för ISO 27001):** Anger resurs-ID för en arbetsyta. Den här `concat` parametern använder en funktion för att konstruera resurs-ID.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som det definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de har definierats under tilldelningen av skissen. En fullständig lista eller artefaktparametrar och deras beskrivningar finns i [tabellen Artefaktparametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **Tilldela** längst ned på sidan. Skisstilldelningen skapas och artefaktdistributionen börjar. Distributionen tar ungefär en timme. Om du vill kontrollera status för distributionen öppnar du skisstilldelningen.

> [!WARNING]
> Azure Blueprints-tjänsten och de inbyggda skissexemplen är **kostnadsfria**. Azure-resurser [prissätts efter produkt](https://azure.microsoft.com/pricing/). Använd [priskalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att uppskatta kostnaden för att köra resurser som distribueras av det här skissexemplet.

## <a name="artifact-parameters-table"></a>Tabellen Artefaktparametrar

Följande tabell innehåller en lista över parametrarna för skissartefakt:

|Artefaktnamn|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Linux VMSS)|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS för att lägga till i omfång|(Valfritt) Standardvärdet är _["none"]_.|
|\[Preview\]: Distribuera LoggAnalys agent för Linux virtuella datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux OS för att lägga till i omfång|(Valfritt) Standardvärdet är _["none"]_.|
|\[Förhandsversion:\]Distribuera logganalysagent för VMSS (Windows VMSS)|Principtilldelning|Valfritt: Lista över vm-avbildningar som har stöd för Windows OS att lägga till i omfång|(Valfritt) Standardvärdet är _["none"]_.|
|\[Förhandsgranskning:\]Distribuera logganalysagent för virtuella Windows-datorer|Principtilldelning|Valfritt: Lista över vm-avbildningar som har stöd för Windows OS att lägga till i omfång|(Valfritt) Standardvärdet är _["none"]_.|
|Tillåtna resurstyper|Principtilldelning|Tillåtna resurstyper|Lista över resurstyper som kan distribueras. Den här listan består av alla resurstyper som distribueras i Delade tjänster.|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Tillåtna lagringssscku:er|Lista över tillåtna lagringskonto för diagnostikloggar. Standardvärdet är _["Standard_LRS"]_.|
|Tillåtna SKU:er för virtuella datorer|Principtilldelning|Lista över SKU:er för virtuella datorer som kan distribueras. Standardvärdet är _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Plan initiativ för ISO 27001|Principtilldelning|Resurstyper för granskning av diagnostikloggar|Lista över resurstyper som ska granskas om diagnostiklogginställningen inte är aktiverad. Acceptabla värden finns i [Azure Monitor diagnostikloggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Resursgrupp för Logganalys|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-sharedsvsc-log-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Logganalys|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för Logganalys|Resource Manager-mall|Tjänstenivå|Anger nivån på log analytics-arbetsytan. Standardvärdet är _PerNode_.|
|Mall för Logganalys|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för Logganalys|Resource Manager-mall|Location|Region som används för att skapa log analytics-arbetsytan. Standardvärdet är _Västra US 2_.|
|Resursgrupp för nätverk|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-sharedsvcs-net-rg` med för att göra resursgruppen unik.|
|Resursgrupp för nätverk|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för Azure-brandvägg|Resource Manager-mall|Privat IP-adress i Azure-brandväggen|Konfigurerar den privata IP-adressen för [Azure-brandväggen](../../../../firewall/overview.md). Det här värdet används också som standardrutttabell i undernätet för delade tjänster. Bör vara en del av CIDR-notationen som definieras i **Azure Firewall-undernätsadressprefixet**. Standardvärdet är _10.0.4.4_.|
|Mall för Azure-brandvägg|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för nätverkssäkerhetsgrupp|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för virtuell nätverksadress|CIDR-notationen för det virtuella nätverket. Standardvärdet är _10.0.0.0/16_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Aktivera DDoS-skydd för virtuellt nätverk|Konfigurerar DDoS-skydd för det virtuella nätverket. Standardvärdet är _sant_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för delad tjänst-undernätadress|CIDR-notationen för undernätet Delade tjänster. Standardvärdet är _10.0.0.0/24_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för DMZ-nätadress|CIDR-notationen för DMZ-undernätet. Standardvärdet är _10.0.1.0/24_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för undernätsadress för Application Gateway|CIDR-notationen för undernätet för programgatewayen. Standardvärdet är _10.0.2.0/24_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för virtuell nätverksgateway|CIDR-notationen för det virtuella nätverksgatewayundernätet. Standardvärdet är _10.0.3.0/24_.|
|Mall för virtuellt nätverk och rutttabell|Resource Manager-mall|Prefix för Azure-brandväggens undernätadress|CIDR-notationen för undernätet för [Azure-brandväggen.](../../../../firewall/overview.md) Bör inkludera **azure-brandväggens privata IP-parameter.**|
|Resursgrupp för Nyckelvalv|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-sharedsvcs-kv-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Nyckelvalv|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mallen Nyckelvalv|Resource Manager-mall|Användarnamn för Jumpbox-administratör|Användarnamn för jumpboxen. Måste matcha samma egenskapsvärde i **Jumpbox-mallen**. Standardvärdet är _jb-admin-user_.|
|Mallen Nyckelvalv|Resource Manager-mall|Jumpbox admin ssh nyckel eller lösenord|Nyckel eller lösenord för kontot i jumpboxen. Måste matcha samma egenskapsvärde i **Jumpbox-mallen**. Inget standardvärde och kan inte lämnas tomt.|
|Mallen Nyckelvalv|Resource Manager-mall|Användarnamn för domänadministratör|Användarnamn som används för att komma åt Active Directory VM och för att ansluta andra virtuella datorer till en domän. Måste matcha **domänadministratörsanvändaregenskapsvärdet** i **Active Directory Domain Services-mallen**. Standardvärdet är _domän-admin-användare_.|
|Mallen Nyckelvalv|Resource Manager-mall|Lösenord för domänadministratör|Domänadministratörsanvändarens lösenord. Inget standardvärde och kan inte lämnas tomt.|
|Mallen Nyckelvalv|Resource Manager-mall|AAD-objekt-ID|AAD-objektidentifieraren för kontot som kräver åtkomst till Key Vault-instansen. Inget standardvärde och kan inte lämnas tomt. Om du vill hitta det här värdet från Azure-portalen söker du efter och väljer "Användare" under _Tjänster_. Använd rutan _Namn_ för att filtrera efter kontonamnet och välj det kontot. På sidan _Användarprofil_ väljer du ikonen "Klicka för att kopiera" _bredvid objekt-ID._  |
|Mallen Nyckelvalv|Resource Manager-mall|Logga kvarhållning i dagar|Lagring av data i dagar. Standardvärdet är _365_.|
|Mallen Nyckelvalv|Resource Manager-mall|SKU för nyckelvalv|Anger SKU för nyckelvalvet som skapas. Standardvärdet är _Premium_.|
|Resursgrupp för Jumpbox|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-sharedsvcs-jb-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Jumpbox|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mall för Jumpbox|Resource Manager-mall|Användarnamn för Jumpbox-administratör|Användarnamnet som används för att komma åt virtuella jumpbox-datorer. Måste matcha samma egenskapsvärde i **Mallen Key Vault**. Standardvärdet är _jb-admin-user_.|
|Mall för Jumpbox|Resource Manager-mall|Lösenordet för Jumpbox-administratör (Resurs-ID för Key Vault)|Resurs-ID för nyckelvalvet. Använd "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` och ersätt med `{orgName}` ditt prenumerations-ID och med skissparametern **Organisationsnamn.**|
|Mall för Jumpbox|Resource Manager-mall|Lösenordet för Jumpbox-administratör (Hemligt namn för Key Vault)|Användarnamn för jumpbox admin. Måste matcha värdet i **Key Vault-mallegenskapen** **Jumpbox admin username**.|
|Mall för Jumpbox|Resource Manager-mall|Operativsystemet Jumpbox|Bestämmer operativsystemet för den virtuella återställningsboxen. Standardvärdet är _Windows_.|
|Resursgrupp för Active Directory Domain Services|Resursgrupp|Namn|**Låst** - Sammanfogar **organisationsnamnet** `-sharedsvcs-adds-rg` med för att göra resursgruppen unik.|
|Resursgrupp för Active Directory Domain Services|Resursgrupp|Location|**Låst** - Använder skissparametern.|
|Mallen Active Directory Domain Services|Resource Manager-mall|Användarnamn för domänadministratör|Användarnamn för JUMPBOXEN ADDS. Måste matcha samma egenskapsvärde i **Mallen Key Vault**. Standardvärdet är _add-admin-user_.|
|Mallen Active Directory Domain Services|Resource Manager-mall|Lösenord för domänadministratör (resurs-ID för nyckelvalv)|Resurs-ID för nyckelvalvet. Använd "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` och ersätt med `{orgName}` ditt prenumerations-ID och med skissparametern **Organisationsnamn.**|
|Mallen Active Directory Domain Services|Resource Manager-mall|Lösenord för domänadministratör (Hemligt namn för Nyckelvalv)|Användarnamn för domänadministratören. Måste matcha värdet i egenskapen Domain admin username för **Key Vault-mall** . **Domain admin username**|
|Mallen Active Directory Domain Services|Resource Manager-mall|Domännamn|Namnet på active directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Mallen Active Directory Domain Services|Resource Manager-mall|Användare av domänadministratör|Användarnamn för admin AD-kontot och för att ansluta enheter till AD-domänen. Måste matcha **egenskapsvärdet för AD-administratörsanvändaregenskap** i **Mallen Key Vault**. Standardvärdet är _domän-admin-användare_.|
|Mallen Active Directory Domain Services|Resource Manager-mall|Lösenord för domänadministratör|Ange information om Nyckelvalv för lagring av lösenordet. Inget standardvärde och kan inte lämnas tomt.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera skissexemplet ISO 27001 Shared Services kan du läsa följande artiklar om arkitekturen och kontrollmappningen:

> [!div class="nextstepaction"]
> [ISO 27001 Delad Tjänst skiss - Översikt](./index.md)
> [ISO 27001 Shared Services skiss - Kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Läs mer om [skisslivscykeln](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig att anpassa [ordningsföljden för skisssekvensering](../../concepts/sequencing-order.md).
- Ta reda på hur du använder [skiss resurs låsning](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
