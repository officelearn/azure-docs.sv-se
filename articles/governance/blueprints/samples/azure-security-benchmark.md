---
title: Översikt över Azure Security Benchmark-skissexempel
description: Översikt över Azure Security Benchmark-skissexempel. Det här skissexemplet hjälper kunderna att utvärdera specifika kontroller.
ms.date: 06/02/2020
ms.topic: sample
ms.openlocfilehash: 4113f350c21087dbda5f69d9c3981e2d169ffc00
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/10/2020
ms.locfileid: "89651909"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Exempel på Azure Security benchmark-skiss

Azure Security Benchmark-skissexemplet tillhandahåller styrningsriktlinjer som använder [Azure Policy](../../policy/overview.md) och som hjälper dig att utvärdera specifika [Azure Security Benchmark](../../../security/benchmarks/overview.md)-kontroller. Den här skissen hjälper kunder att distribuera en central uppsättning principer för en Azure-distribuerad arkitektur där de planerar att implementera Azures Security Benchmark-kontroller.

## <a name="control-mapping"></a>Kontrollmappning

Den [Azure policy kontroll mappningen](../../policy/samples/azure-security-benchmark.md) innehåller information om princip definitioner som ingår i den här skissen och hur dessa princip definitioner mappas till **domänerna för regelefterlevnad** och **kontrollerna** i Azures säkerhets benchmark. När den är tilldelad till en arkitektur utvärderas resurserna av Azure Policy för inkompatibilitet med tilldelade princip definitioner. Mer information finns i [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Distribuera

Följande steg måste vidtas för att distribuera Azure-skisser om plan för säkerhet för Azure-skisser:

> [!div class="checklist"]
> - Skapa en ny skiss från exemplet
> - Markera din kopia av exemplet som **publicerad**
> - Tilldela din kopia av skissen till en befintlig prenumeration

Om du inte har någon Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free) innan du börjar.

### <a name="create-blueprint-from-sample"></a>Skapa skiss från exempel

Börja med att implementera skiss exemplet genom att skapa en ny skiss i din miljö med hjälp av exemplet som ett start verktyg.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Från sidan **komma igång** till vänster väljer du knappen **skapa** under _skapa en skiss_.

1. Leta upp utkastet för **Azure Security benchmark** -exempel under _andra exempel_ och välj namnet för att välja det här exemplet.

1. Ange _grunderna_ för skiss exemplet:

   - **Skiss namn**: Ange ett namn för din kopia av exempel på Azure Security benchmark-skissen.
   - **Definitions plats**: Använd ellipsen och välj den hanterings grupp där du vill spara din kopia av exemplet.

1. Välj fliken _artefakter_ överst på sidan eller **Nästa: artefakter** längst ned på sidan.

1. Granska listan över artefakter som utgör skiss exemplet. Många av artefakterna har parametrar som vi definierar senare. Välj **Spara utkast** när du är klar med att granska skiss exemplet.

### <a name="publish-the-sample-copy"></a>Publicera exempel kopian

Din kopia av skiss exemplet har nu skapats i din miljö. Den skapas i **utkast** läge och måste **publiceras** innan den kan tilldelas och distribueras. Kopian av skiss exemplet kan anpassas efter din miljö och dina behov, men den ändringen kan flytta den bort från anpassningen med hjälp av rekommendationer för Azures säkerhets benchmark.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **publicera skiss** överst på sidan. På den nya sidan till höger anger du en **version** för din kopia av skiss exemplet. Den här egenskapen är användbar för om du gör en ändring senare. Ange **ändrings anteckningar** , till exempel "första versionen som publicerats från exempel på Azure Security benchmark-skiss". Välj sedan **publicera** längst ned på sidan.

### <a name="assign-the-sample-copy"></a>Tilldela exempel kopian

När kopieringen av skiss exemplet har **publicerats**kan den tilldelas en prenumeration i hanterings gruppen som den sparades till. Det här steget är den plats där parametrar tillhandahålls för att göra varje distribution av kopian av skiss exemplet unik.

1. Välj **Alla tjänster** i den vänstra rutan. Sök efter och välj **Skisser**.

1. Välj sidan **skiss definitioner** till vänster. Använd filtren för att hitta din kopia av skiss exemplet och markera det.

1. Välj **tilldela skiss** överst på skiss definitions sidan.

1. Ange parameter värden för skiss tilldelningen:

   - Grundläggande inställningar

     - **Prenumerationer**: Välj en eller flera av de prenumerationer som finns i hanterings gruppen som du sparade din kopia av skiss exemplet till. Om du väljer fler än en prenumeration skapas en tilldelning för varje användning av de angivna parametrarna.
     - **Tilldelnings namn**: namnet fylls i automatiskt för dig baserat på namnet på skissen.
       Ändra vid behov eller lämna det som är.
     - **Plats**: Välj en region som den hanterade identiteten ska skapas i. Azure Blueprint använder den här hanterade identiteten för att distribuera alla artefakter i den tilldelade skissen. Mer information finns i [hanterade identiteter för Azure-resurser](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Ritnings definitions version**: Välj en **publicerad** version av din kopia av skiss exemplet.

   - Lås tilldelning

     Välj skiss lås inställningen för din miljö. Mer information finns i [Låsa skissresurser](../concepts/resource-locking.md).

   - Hanterad identitet

     Lämna alternativet standard _tilldelade_ hanterade identiteter.

   - Artefakt parametrar

     De parametrar som definieras i det här avsnittet gäller för den artefakt som den definieras under. Dessa parametrar är [dynamiska parametrar](../concepts/parameters.md#dynamic-parameters) eftersom de definieras när skissen tilldelas. En fullständig lista eller artefakt parametrar och deras beskrivningar finns i [tabellen artefakt parametrar](#artifact-parameters-table).

1. När alla parametrar har angetts väljer du **tilldela** längst ned på sidan. Skiss tilldelningen skapas och artefakt distributionen påbörjas. Distributionen tar ungefär en timme. Du kan kontrol lera statusen för distributionen genom att öppna skiss tilldelningen.

> [!WARNING]
> Tjänsten Azure-ritningar och de inbyggda skiss exemplen är **kostnads fria**. Azure-resurser [priss ätts per produkt](https://azure.microsoft.com/pricing/). Använd [pris kalkylatorn](https://azure.microsoft.com/pricing/calculator/) för att beräkna kostnaden för att köra resurser som distribuerats i skiss exemplet.

### <a name="artifact-parameters-table"></a>Tabellen artefakt parametrar

I följande tabell visas en lista över Skissernas artefakt parametrar:

|Artefakt namn|Artefakt typ|Parameternamn|Beskrivning|
|-|-|-|-|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över användare som uteslutits från administratörs gruppen för Windows-datorer|En semikolonavgränsad lista med medlemmar som ska uteslutas i den lokala gruppen Administratörer. T. ex. administratör; Mina Användare1; myUser2|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över användare som måste ingå i administratörs gruppen för en virtuell Windows-dator|En semikolonavgränsad lista med medlemmar som ska ingå i den lokala gruppen Administratörer. T. ex. administratör; Mina Användare1; myUser2|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över användare som administratörs gruppen för Windows VM-administratörer får *bara* innehålla|En semikolonavgränsad lista över alla förväntade medlemmar i den lokala gruppen Administratörer. T. ex. administratör; Mina Användare1; myUser2|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över regioner där Network Watcher ska aktive ras|Om du vill se en fullständig lista över regioner använder du get-AzLocation|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Virtuellt nätverk där virtuella datorer ska anslutas|Exempel:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Nätverksgateway som virtuella nätverk ska använda|Exempel:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över arbetsyte-ID: n där Log Analytics agenter bör ansluta|En semikolonavgränsad lista med de arbetsyte-ID: n som Log Analytics agenten ska vara ansluten till|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Lista över resurs typer som ska ha diagnostiska loggar aktiverade|Granska diagnostikinställningar för valda resurs typer|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Senaste PHP-version|Den senaste PHP-versionen som stöds för App Services|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Senaste Java-version|Senaste Java-version som stöds för App Services|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Senaste Windows python-versionen|Senaste python-version som stöds för App Services|
|Granska Azures säkerhets benchmark-rekommendationer och distribuera vissa stödda VM-tillägg|Principtilldelning|Senaste versionen av Linux python|Senaste python-version som stöds för App Services|

## <a name="next-steps"></a>Nästa steg

Ytterligare artiklar om skisser och hur de används:

- Mer information om [livscykeln för en skiss](../concepts/lifecycle.md).
- Förstå hur du använder [statiska och dynamiska parametrar](../concepts/parameters.md).
- Lär dig hur du anpassar [sekvensordningen för en skiss](../concepts/sequencing-order.md).
- Lär dig hur du använder [resurslåsning för en skiss](../concepts/resource-locking.md).
- Lär dig hur du [uppdaterar befintliga tilldelningar](../how-to/update-existing-assignments.md).