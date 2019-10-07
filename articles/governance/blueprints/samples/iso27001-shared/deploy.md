---
title: Exempel – ISO 27001-skiss för delade tjänster – distribuera steg
description: Distribuera steg av skiss exemplet för delade tjänster i ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
ms.openlocfilehash: a55fccd8942e9bbdee4611120ffcab48fe4a8c62
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/06/2019
ms.locfileid: "71978298"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera skiss exemplet för delade tjänster i ISO 27001

För att distribuera Azure-skisser ISO 27001-skissen för delade tjänster måste följande steg vidtas:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Hitta **ISO 27001: Delade tjänster @ no__t – 0 skiss exempel under _andra exempel_ och välj **Använd det här exemplet**.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av skiss exemplet för delade tjänster i ISO 27001.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Next: Artefakter @ no__t-0 längst ned på sidan.

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
     - **Adressprefix för Shared Services-undernät**: Ange värdet för CIDR-notering för nätverk de distribuerade resurserna tillsammans.
     - **Plats för delade tjänster**: Bestämmer vilken plats artefakterna distribueras till. Alla tjänster är inte tillgängliga på alla platser. Artefakter som distribuerar sådana tjänster tillhandahåller ett parameter alternativ för platsen som artefakten ska distribueras till.
     - **Allowed plats (princip: Skiss initiativ för ISO 27001)** : Värde som anger tillåtna platser för resurs grupper och resurser.
     - **Log Analytics-arbetsyta för VM-agenter (princip: Skiss initiativ för ISO 27001)** : Anger resurs-ID för en arbets yta. Den här parametern använder en `concat`-funktion för att skapa resurs-ID.

   - Artefaktparametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

## <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Namn på artefakt|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Preview @ no__t-1: Distribuera Log Analytics agent för Linux VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfång|Valfritt Standardvärdet är _["none"]_ .|
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Linux-datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Linux-operativsystem som ska läggas till i omfång|Valfritt Standardvärdet är _["none"]_ .|
|\[Preview @ no__t-1: Distribuera Log Analytics agent för Windows VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Windows OS som ska läggas till i omfånget|Valfritt Standardvärdet är _["none"]_ .|
|\[Preview @ no__t-1: Distribuera Log Analytics agent för virtuella Windows-datorer|Principtilldelning|Valfritt: Lista över VM-avbildningar som har stöd för Windows OS som ska läggas till i omfånget|Valfritt Standardvärdet är _["none"]_ .|
|Tillåtna resurstyper|Principtilldelning|Tillåtna resurstyper|Lista över resurs typer som får distribueras. Den här listan består av alla resurs typer som distribueras i delade tjänster.|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Tillåtna lagrings-SKU: er|Lista över diagnostiska loggar lagrings konto SKU: er tillåts. Standardvärdet är _["Standard_LRS"]_ .|
|Tillåtna SKU:er för virtuella datorer|Principtilldelning|Lista över virtuella dator-SKU: er som kan distribueras. Standardvärdet är _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Skissinitiativ för ISO 27001|Principtilldelning|Resurs typer för att granska diagnostikloggar|Lista över resurs typer som ska granskas om diagnostisk logg inställning inte är aktive rad. Du hittar giltiga värden i [Azure Monitor-diagnostiska loggar scheman](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-sharedsvsc-log-rg` för att göra resurs gruppen unik.|
|Log Analytics resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Log Analytics-mall|Resource Manager-mall|Tjänstnivå|Anger nivån för Log Analytics arbets ytan. Standardvärdet är _PerNode_.|
|Log Analytics-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Log Analytics-mall|Resource Manager-mall|Location|Region som används för att skapa Log Analytics-arbetsytan. Standardvärdet är _USA, västra 2_.|
|Nätverks resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-sharedsvcs-net-rg` för att göra resurs gruppen unik.|
|Nätverks resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Azure Firewall-mall|Resource Manager-mall|Privat IP för Azure Firewall|Konfigurerar den privata IP-adressen för [Azure-brandväggen](../../../../firewall/overview.md). Det här värdet används också som standard väg tabell på under nätet för delade tjänster. Ska ingå i CIDR-notationen som definieras i adressprefix för **Azure Firewall-undernätet**. Standardvärdet är _10.0.4.4_.|
|Azure Firewall-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Nätverkssäkerhetsgrupp-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för virtuellt nätverk|CIDR-notering för det virtuella nätverket. Standardvärdet är _10.0.0.0/16_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Aktivera DDoS-skydd för virtuellt nätverk|Konfigurerar DDoS-skydd för det virtuella nätverket. Standardvärdet är _True_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för Shared Services-undernät|CIDR-notering för under nätet för delade tjänster. Standardvärdet är _10.0.0.0/24_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för DMZ-undernät|CIDR-notering för DMZ-undernätet. Standardvärdet är _10.0.1.0/24_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Undernätsadressprefix för programgateway|CIDR-notering för Application Gateway-undernätet. Standardvärdet är _10.0.2.0/24_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för Virtual Network Gateway-undernät|CIDR-notering för det virtuella nätverkets gateway-undernät. Standardvärdet är _10.0.3.0/24_.|
|Mall för Virtual Network och route-tabell|Resource Manager-mall|Adressprefix för Azure Firewall-undernät|CIDR-notering för [Azure Firewall](../../../../firewall/overview.md) -undernätet. Ska inkludera **Azure firewalls privata IP-** parameter.|
|Key Vault resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-sharedsvcs-kv-rg` för att göra resurs gruppen unik.|
|Key Vault resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Key Vault-mall|Resource Manager-mall|Jumpbox-administratörens användarnamn|Användar namn för hopp rutan. Måste matcha samma egenskaps värde i **hopp mal len**. Standardvärdet är _JB-admin-user_.|
|Key Vault-mall|Resource Manager-mall|SSH-nyckel eller lösen ord för bygel-administratör|Nyckel eller lösen ord för kontot i hoppet. Måste matcha samma egenskaps värde i **hopp mal len**. Inget standardvärde och får inte vara tomt.|
|Key Vault-mall|Resource Manager-mall|Domänadministratörens användarnamn|Användar namn som används för att komma åt Active Directory VM och för att ansluta till andra virtuella datorer till en domän. Måste matcha användar egenskap svärdet för **domän administratör** i **Active Directory Domain Services mall**. Standardvärdet är _domän administratör – användare_.|
|Key Vault-mall|Resource Manager-mall|Domänens administratörslösenord|Användarens lösen ord för domän administratör. Inget standardvärde och får inte vara tomt.|
|Key Vault-mall|Resource Manager-mall|Objekt-ID för AAD|AAD-objektmodellen för det konto som kräver åtkomst till Key Vault-instansen. Inget standardvärde och får inte vara tomt. Du hittar det här värdet från Azure Portal genom att söka efter och välja "användare" under _tjänster_. Använd rutan _namn_ om du vill filtrera efter konto namnet och välj det kontot. På sidan _användar profil_ väljer du ikonen "Klicka för att kopiera" bredvid _objekt-ID_.  |
|Key Vault-mall|Resource Manager-mall|Loggkvarhållning i dagar|Data kvarhållning i dagar. Standardvärdet är _365_.|
|Key Vault-mall|Resource Manager-mall|Key Vault-SKU|Anger SKU: n för den Key Vault som skapas. Standardvärdet är _Premium_.|
|Resurs grupp för hopp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-sharedsvcs-jb-rg` för att göra resurs gruppen unik.|
|Resurs grupp för hopp|Resource group|Location|**Locked** – använder skiss parametern.|
|Jumpbox-mall|Resource Manager-mall|Jumpbox-administratörens användarnamn|Det användar namn som används för att komma åt de virtuella datorerna i bygel. Måste matcha samma egenskaps värde i **Key Vault-mallen**. Standardvärdet är _JB-admin-user_.|
|Jumpbox-mall|Resource Manager-mall|Lösen ord för hopp i administratör (Key Vault resurs-ID)|Resurs-ID för Key Vault. Använd "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" och ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med **organisationens namn** skiss parameter.|
|Jumpbox-mall|Resource Manager-mall|Lösen ord för bygel (Key Vault hemligt namn)|Användar namn för hopp administratören. Måste matcha värdet i **Key Vault Template** - **administratörens användar namn**.|
|Jumpbox-mall|Resource Manager-mall|Jumpbox-operativsystem|Fastställer operativ systemet för den virtuella datorn i hoppet. Standardvärdet är _Windows_.|
|Active Directory Domain Services resurs grupp|Resource group|Name|**Locked** -sammanfogar **organisations namnet** med `-sharedsvcs-adds-rg` för att göra resurs gruppen unik.|
|Active Directory Domain Services resurs grupp|Resource group|Location|**Locked** – använder skiss parametern.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domänadministratörens användarnamn|Användar namn för Lägg till hopp. Måste matcha samma egenskaps värde i **Key Vault-mallen**. Standardvärdet _läggs till-admin-user_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Lösen ord för domän administratör (Key Vault resurs-ID)|Resurs-ID för Key Vault. Använd "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" och ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med **organisationens namn** skiss parameter.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domän administratörs lösen ord (Key Vault hemligt namn)|Användar namnet för domän administratören. Måste matcha värdet i **Key Vault mall** **domän administratör användar namn**.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domännamn|Namnet på Active Directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domän administratörs användare|Användar namn för admin AD-konto och för att ansluta enheter till AD-domänen. Måste matcha egenskap svärdet för **AD admin-användarnamn** i **Key Vault-mallen**. Standardvärdet är _domän administratör – användare_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domänens administratörslösenord|Ange Key Vault information för att lagra lösen ordet. Inget standardvärde och får inte vara tomt.|

## <a name="next-steps"></a>Nästa steg

Nu när du har granskat stegen för att distribuera ISO 27001-skissen för delade tjänster kan du gå till följande artiklar om du vill veta mer om arkitektur och kontroll mappning:

> [!div class="nextstepaction"]
> [Iso 27001 delade tjänster skiss – översikt](./index.md)
> [ISO 27001 delade tjänster skiss-kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
