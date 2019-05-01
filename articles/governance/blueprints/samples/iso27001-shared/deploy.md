---
title: Exempel – ISO 27001 delade tjänster skiss – distribuera steg
description: Distribuera stegen för delade tjänster för ISO 27001 skissen exemplet.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 88d5cfbbcb29cacc2e8c1c6a226367c5f23e8231
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/30/2019
ms.locfileid: "64926308"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Distribuera exemplet ISO 27001 delade tjänster skiss

Följande steg måste vidtas för att distribuera Azure skisser ISO 27001 delade tjänster skissen exemplet:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

## <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Först måste implementera skissen exemplet genom att skapa en ny skiss i din miljö med exemplet som en starter.

1. Välj **alla tjänster** och Sök efter och välj **princip** i den vänstra rutan. På den **princip** väljer **skisser**.

1. Från den **komma igång** sidan till vänster, Välj den **skapa** knappen _skapa en skiss_.

1. Hitta den **ISO 27001: Delade tjänster** skissen exemplet under _andra exempel_ och välj **använda det här exemplet**.

1. Ange den _grunderna_ av skiss exemplet:

   - **Namn på skiss**: Ange ett namn för din kopia av exemplet för delade tjänster för ISO 27001-skissen.
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
     - **Adressprefix för delade tjänster undernät**: Ange värdet för CIDR-notation för nätverk distribuerade resurser tillsammans.
     - **Delade tjänster plats**: Anger vilken plats som artefakterna distribueras till. Inte alla tjänster som är tillgängliga på alla platser. Artefakter som distribuerar tjänster erbjuder alternativ för parametern att distribuera den artefakten som plats.
     - **Tillåtna plats (princip: Skiss initiativ för ISO 27001)**: Värde som anger tillåtna platser för resursgrupper och resurser.
     - **Log Analytics-arbetsyta för VM-agenter (princip: Skiss initiativ för ISO 27001)**: Anger resurs-ID för en arbetsyta. Den här parametern använder en `concat` funktionen för att konstruera resurs-ID.

   - Artefaktparametrar

     De parametrar som definierats i det här avsnittet gäller för artefakten som den definieras. De här parametrarna kan [dynamiska parametrar](../../concepts/parameters.md#dynamic-parameters) eftersom de definierades vid tilldelning av skissen. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [artefakt parametrar tabell](#artifact-parameters-table).

1. När alla parametrar har angetts, väljer **tilldela** längst ned på sidan. Skisstilldelningen har skapats och distribution av artefakt påbörjas. Distributionen tar ungefär en timme. Öppna skisstilldelningen för att kontrollera statusen för distributionen.

> [!WARNING]
> Tjänsten Azure skisser och inbyggda skissen exemplen är **kostar**. Azure-resurser är [prissätts per produkt](https://azure.microsoft.com/pricing/). Använd den [priskalkylator](https://azure.microsoft.com/pricing/calculator/) att uppskatta kostnaden för att köra resurser som har distribuerats av den här skissen-exemplet.

## <a name="artifact-parameters-table"></a>Artefakten parametrar tabell

Följande tabell innehåller en lista över skissen artefakt parametrar:

|Namn på artefakt|Artefakttyp|Parameternamn|Beskrivning|
|-|-|-|-|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Linux-operativsystem att lägga till i omfånget|(Valfritt) Standardvärdet är _[”Ingen”]_.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Linux-datorer|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Linux-operativsystem att lägga till i omfånget|(Valfritt) Standardvärdet är _[”Ingen”]_.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows VM Scale Sets (VMSS)|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Windows-operativsystem för att lägga till omfånget|(Valfritt) Standardvärdet är _[”Ingen”]_.|
|\[Förhandsversion av\]: Distribuera Log Analytics-agenten för Windows-datorer|Principtilldelning|Valfritt: Lista över avbildningar av Virtuella datorer som har stöd för Windows-operativsystem för att lägga till omfånget|(Valfritt) Standardvärdet är _[”Ingen”]_.|
|Tillåtna resurstyper|Principtilldelning|Tillåtna resurstyper|Lista över resurstyper som kan distribueras. Den här listan består av alla resurstyper som distribueras i delade tjänster.|
|Tillåtna SKU:er för lagringskonto|Principtilldelning|Tillåtna lagringsutrymmet SKU: er|Lista över diagnostik loggar storage konto SKU: er tillåts. Standardvärdet är _[”Standard_LRS”]_.|
|Tillåtna SKU:er för virtuella datorer|Principtilldelning|Lista över SKU: er för virtuell dator har tillåtelse för att distribueras. Default value is _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Skissinitiativ för ISO 27001|Principtilldelning|Resurstyper granska diagnostikloggar|Lista över resurstyper granska om diagnostiklogg inställningen inte är aktiverad. Godkända värden finns på [scheman för diagnostikloggar för Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Resursgrupp för log Analytics|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-sharedsvsc-log-rg` så att resursgruppen som är unikt.|
|Resursgrupp för log Analytics|Resursgrupp|Location|**Låst** -parametern skissen.|
|Log Analytics-mall|Resource Manager-mall|Tjänstenivå|Anger nivån av Log Analytics-arbetsytan. Standardvärdet är _PerNode_.|
|Log Analytics-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Log Analytics-mall|Resource Manager-mall|Location|Regionen som används för att skapa Log Analytics-arbetsytan. Standardvärdet är _USA, västra 2_.|
|Resursgrupp för nätverk|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-sharedsvcs-net-rg` så att resursgruppen som är unikt.|
|Resursgrupp för nätverk|Resursgrupp|Location|**Låst** -parametern skissen.|
|Azure Firewall-mall|Resource Manager-mall|Privat IP för Azure Firewall|Konfigurerar privata IP-Adressen för den [Azure brandväggen](../../../../firewall/overview.md). Det här värdet används också som standardvägtabellen på undernät för delade tjänster. Bör vara en del i CIDR-format som definierats i **adressprefix för Azure-brandvägg undernät**. Standardvärdet är _10.0.4.4_.|
|Azure Firewall-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Nätverkssäkerhetsgrupp-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Adressprefix för virtuellt nätverk|CIDR-format för det virtuella nätverket. Standardvärdet är _10.0.0.0/16_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Aktivera DDoS-skydd för virtuellt nätverk|Konfigurerar DDoS-skydd för det virtuella nätverket. Standardvärdet är _SANT_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Delade tjänster adressprefix för undernät|CIDR-notation för delade tjänster-undernätet. Standardvärdet är _10.0.0.0/24_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Adressprefix för DMZ-undernät|CIDR-notation för DMZ-undernätet. Standardvärdet är _10.0.1.0/24_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Undernätsadressprefix för programgateway|CIDR-notation för application gateway-undernätet. Standardvärdet är _10.0.2.0/24_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Undernätsadressprefix för virtuell nätverksgateway|CIDR-format för virtuella nätverkets gateway-undernät. Standardvärdet är _10.0.3.0/24_.|
|Mall för virtuellt nätverk och routningstabell|Resource Manager-mall|Adressprefix för Azure Firewall-undernät|CIDR-notation för den [Azure brandväggen](../../../../firewall/overview.md) undernät. Bör innehålla den **Azure brandväggs-IP för privat** parametern.|
|Key Vault-resursgrupp|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-sharedsvcs-kv-rg` så att resursgruppen som är unikt.|
|Key Vault-resursgrupp|Resursgrupp|Location|**Låst** -parametern skissen.|
|Key Vault-mall|Resource Manager-mall|Jumpbox-administratörens användarnamn|Användarnamnet för jumpboxen. Måste matcha samma egenskapsvärdet i **Jumpbox mallen**. Standardvärdet är _jb administratörsanvändare_.|
|Key Vault-mall|Resource Manager-mall|Jumpbox-administratörens SSH-nyckel eller lösenord|Nyckeln eller lösenordet för kontot för jumpboxen. Måste matcha samma egenskapsvärdet i **Jumpbox mallen**. Inget standardvärde värde och får inte vara tomt.|
|Key Vault-mall|Resource Manager-mall|Domänadministratörens användarnamn|Användarnamnet som används för att få åtkomst till Active Directory-VM och ansluta till andra virtuella datorer till en domän. Måste matcha **Domain admin-användare** egenskapsvärdet i **Active Directory Domain Services mall**. Standardvärdet är _domän administratörsanvändare_.|
|Key Vault-mall|Resource Manager-mall|Domänens administratörslösenord|Domain admin användarens lösenord. Inget standardvärde värde och får inte vara tomt.|
|Key Vault-mall|Resource Manager-mall|Objekt-ID för AAD|AAD-objektidentifierare för det konto som kräver åtkomst till Key Vault-instansen. Inget standardvärde värde och får inte vara tomt. Om du vill hitta det här värdet från Azure-portalen, Sök efter och Välj ”användare” _Services_. Använd den _namn_ om du vill filtrera på kontonamnet och välja det kontot. På den _användarprofil_ väljer ”Klicka för att kopiera”-ikonen bredvid den _objekt-ID_.  |
|Key Vault-mall|Resource Manager-mall|Loggkvarhållning i dagar|Datalagring i dagar. Standardvärdet är _365_.|
|Key Vault-mall|Resource Manager-mall|Key Vault-SKU|Anger SKU: N för Nyckelvalvet som har skapats. Standardvärdet är _Premium_.|
|Jumpbox resursgrupp|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-sharedsvcs-jb-rg` så att resursgruppen som är unikt.|
|Jumpbox resursgrupp|Resursgrupp|Location|**Låst** -parametern skissen.|
|Jumpbox-mall|Resource Manager-mall|Jumpbox-administratörens användarnamn|Användarnamnet som används för att få åtkomst till jumpboxen virtuella datorer. Måste matcha samma egenskapsvärdet i **Key Vault mallen**. Standardvärdet är _jb administratörsanvändare_.|
|Jumpbox-mall|Resource Manager-mall|Administratörslösenord för Jumpbox (resurs-ID för Key Vault)|Resurs-ID för Nyckelvalvet. Använd ”/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv” och Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med den  **Organisationsnamn** skiss parametern.|
|Jumpbox-mall|Resource Manager-mall|Administratörslösenord för Jumpbox (hemligt namn på Key Vault)|Användarnamnet för jumpbox-administratören. Måste matcha värdet i **Key Vault mallen** egenskapen **Jumpbox administratörsanvändarnamnet**.|
|Jumpbox-mall|Resource Manager-mall|Jumpbox-operativsystem|Fastställer operativsystemet på den virtuella jumpboxen-datorn. Standardvärdet är _Windows_.|
|Active Directory Domain Services-resursgrupp|Resursgrupp|Namn|**Låst** -sammanfogar den **organisationsnamn** med `-sharedsvcs-adds-rg` så att resursgruppen som är unikt.|
|Active Directory Domain Services-resursgrupp|Resursgrupp|Location|**Låst** -parametern skissen.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domänadministratörens användarnamn|Användarnamn för ADDS jumpbox. Måste matcha samma egenskapsvärdet i **Key Vault mallen**. Standardvärdet är _lägger till administratörsanvändare_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Administratörslösenord för domänen (resurs-ID för Key Vault)|Resurs-ID för Nyckelvalvet. Använd ”/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv” och Ersätt `{subscriptionId}` med ditt prenumerations-ID och `{orgName}` med den  **Organisationsnamn** skiss parametern.|
|Active Directory Domain Services-mall|Resource Manager-mall|Administratörslösenord för domänen (hemligt namn på Key Vault)|Användarnamnet för domänen administratören. Måste matcha värdet i **Key Vault mallen** egenskapen **domän administratörsanvändarnamnet**.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domännamn|Namnet på den Active Directory som skapats av exemplet. Standardvärdet är _contoso.com_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domain admin-användare|Användarnamnet för administratörskontot AD och för att ansluta enheter till AD-domänen. Måste matcha **AD administratörsanvändarnamnet** egenskapsvärdet i **Key Vault mallen**. Standardvärdet är _domän administratörsanvändare_.|
|Active Directory Domain Services-mall|Resource Manager-mall|Domänens administratörslösenord|Ställa in Key Vault-information för att lagra lösenordet. Inget standardvärde värde och får inte vara tomt.|

## <a name="next-steps"></a>Nästa steg

Nu när du har gått igenom stegen för att distribuera delade tjänster för ISO 27001 skissen exemplet finns i följande artiklar om du vill veta mer om arkitekturen och kontroll mappning:

> [!div class="nextstepaction"]
> [Delade tjänster för ISO 27001 skissen - översikt](./index.md)
> [ISO 27001 delade tjänster skissen - kontroll mappning](./control-mapping.md)

Ytterligare artiklar om skisser och hur de används:

- Lär dig mer om [livscykeln för en skiss](../../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../../how-to/update-existing-assignments.md).
