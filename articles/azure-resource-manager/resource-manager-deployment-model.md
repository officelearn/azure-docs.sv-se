---
title: Resource Manager och klassisk distribution | Microsoft Docs
description: Här beskrivs skillnaderna mellan distributionsmodell Resource Manager och klassiskt (eller Service Management) distributionsmodell.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: tomfitz
ms.openlocfilehash: 06da24babd470e81bed9c45a32c59ad9cfd153fe
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2018
---
# <a name="azure-resource-manager-vs-classic-deployment-understand-deployment-models-and-the-state-of-your-resources"></a>Azure Resource Manager och klassisk distribution: Förstå distributionsmodeller och tillståndet för dina resurser
I den här artikeln får information du om Azure Resource Manager och klassiska distributionsmodeller. Resource Manager och klassiska distributionsmodeller representerar två olika sätt att distribuera och hantera dina Azure-lösningar. Du arbetar med dem via två uppsättningar API och distribuerade resurser kan innehålla viktiga skillnader. De två modellerna är inte kompatibla med varandra. Den här artikeln beskriver dessa skillnader.

Microsoft rekommenderar att du använder Resource Manager för alla nya resurser för att förenkla distribution och hantering av resurser. Om möjligt rekommenderar Microsoft att du distribuerar befintliga resurser via Resource Manager.

Om du är nybörjare till Resource Manager kan du kanske vill läsa de termer som definierats i den [översikt över Azure Resource Manager](resource-group-overview.md).

## <a name="history-of-the-deployment-models"></a>Historik över distributionsmodellerna
Azure som ursprungligen den klassiska distributionsmodellen. I den här modellen fanns varje resurs självständigt. Det gick inte att gruppera relaterade resurser. I stället var du tvungen att manuellt spåra vilka resurser som består av din lösning eller ditt program och Kom ihåg att hantera dem på ett samordnat sätt. Om du vill distribuera en lösning var du tvungen att skapa varje resurs individuellt via portalen eller skapa ett skript som distribuerats alla resurser i rätt ordning. Om du vill ta bort en lösning var du tvungen att ta bort varje resurs individuellt. Du kan inte enkelt tillämpa och uppdatera principer för åtkomstkontroll för relaterade resurser. Slutligen kan du inte använda taggar till resurser för att märka dem med villkor som hjälper dig övervaka dina resurser och hantera fakturering.

I 2014 introducerade Azure Resource Manager som läggs till konceptet för en resursgrupp. En resursgrupp är en behållare för resurser som delar en gemensam livscykel. Resource Manager-distributionsmodellen ger många fördelar:

* Du kan distribuera, hantera och övervaka alla tjänster för din lösning som en grupp i stället för att hantera dessa tjänster individuellt.
* Du kan upprepade gånger distribuera lösningen under hela dess livscykel och vara säker på att dina resurser distribueras i ett konsekvent tillstånd.
* Du kan använda åtkomstkontroll för alla resurser i resursgruppen och dessa principer tillämpas automatiskt när nya resurser läggs till i resursgruppen.
* Du kan lägga till taggar för resurser och organisera alla logiskt i din prenumeration.
* Du kan använda JavaScript Object Notation (JSON) för att definiera infrastrukturen för lösningen. JSON-filen kallas en Resource Manager-mall.
* Du kan definiera beroenden mellan resurser så att de distribueras i rätt ordning.

När Resource Manager lades till lades retroaktivt alla resurser till standard resursgrupper. Om du skapar en resurs via nu klassisk distribution skapas automatiskt resursen i en standard resursgrupp för tjänsten, även om du inte anger den resursgruppen i distributionen. Dock innebär bara befintliga inom en resursgrupp inte att resursen har konverterats till Resource Manager-modellen.

## <a name="understand-support-for-the-models"></a>Förstå stöd för modeller
Det finns tre scenarier för att vara medveten om:

1. Cloud Services stöder inte Resource Manager-distributionsmodellen.
2. Virtuella datorer, lagringskonton och virtuella nätverk stöder både Resource Manager och klassiska distributionsmodeller.
3. Alla andra Azure-tjänster stöder Resource Manager.

För virtuella datorer, lagringskonton och virtuella nätverk, om resursen har skapats via klassisk distribution fortsätta du att använda via klassiska åtgärder. Om den virtuella datorn, storage-konto eller ett virtuellt nätverk har skapats via Resource Manager-distribution, måste du fortsätta att använda Resource Manager-åtgärder. Denna skillnad kan få förvirrande när prenumerationen innehåller en blandning av resurser som skapats via Resource Manager och klassisk distribution. Den här kombinationen av resurser kan skapa oväntade resultat eftersom resurserna inte har stöd för samma åtgärder.

I vissa fall kan en Resource Manager-kommandot kan hämta information om en resurs som skapats via klassisk distribution eller kan utföra administrativa åtgärder, till exempel flytta en klassisk resurs till en annan resursgrupp. Men dessa fall inte ska ge intryck av att typen har stöd för Resource Manager-åtgärder. Anta att du har en resursgrupp som innehåller en virtuell dator som har skapats med klassisk distribution. Om du kör följande Resource Manager PowerShell-kommando:

```powershell
Get-AzureRmResource -ResourceGroupName ExampleGroup -ResourceType Microsoft.ClassicCompute/virtualMachines
```

Returnerar den virtuella datorn:

```powershell
Name              : ExampleClassicVM
ResourceId        : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.ClassicCompute/virtualMachines/ExampleClassicVM
ResourceName      : ExampleClassicVM
ResourceType      : Microsoft.ClassicCompute/virtualMachines
ResourceGroupName : ExampleGroup
Location          : westus
SubscriptionId    : {guid}
```

Dock cmdleten Resource Manager **Get-AzureRmVM** returnerar endast virtuella datorer distribueras via Resource Manager. Följande kommando returnerar inte den virtuella datorn skapas via klassisk distribution.

```powershell
Get-AzureRmVM -ResourceGroupName ExampleGroup
```

Endast resurser som skapats via Hanteraren för filserverresurser stöd taggar. Du kan inte använda taggar klassiska resurser.

## <a name="changes-for-compute-network-and-storage"></a>Ändringar för beräkning, nätverk och lagring
Följande diagram visar beräkning, nätverk och lagring resurser har distribuerats via Resource Manager.

![Resurshanterarens arkitektur](./media/resource-manager-deployment-model/arm_arch3.png)

Observera följande relationerna mellan resurser:

* Alla resurser som finns inom en resursgrupp.
* Den virtuella datorn är beroende av en specifik storage-konto som är definierad i Storage resource provider för lagring av diskarna i blob storage (krävs).
* Den virtuella datorn refererar till ett specifikt nätverkskort som definierats i nätverksresursprovidern (krävs) och en tillgänglighetsuppsättning definierade i Compute-resursprovidern (valfritt).
* Nätverkskortet refererar till den virtuella datorn är tilldelad IP-adress (obligatoriskt), undernät för det virtuella nätverket för den virtuella datorn (krävs) och en Nätverkssäkerhetsgrupp (valfritt).
* Undernät inom ett virtuellt nätverk refererar till en Nätverkssäkerhetsgrupp (valfritt).
* Belastningen belastningsutjämnaren instans refererar till serverdelspoolen av IP-adresser med nätverkskort för en virtuell dator (valfritt) som refererar till en offentlig eller privat IP-belastningsutjämnaradress (valfritt).

Här följer komponenterna och deras relationer för klassisk distribution:

![klassiska arkitektur](./media/resource-manager-deployment-model/arm_arch1.png)

Den klassiska lösningen för värd för en virtuell dator innehåller:

* En nödvändig molntjänst som fungerar som en behållare för värd för virtuella datorer (beräkning). Virtuella datorer tillhandahålls automatiskt med ett nätverkskort (NIC) och en IP-adress som tilldelats av Azure. Molntjänsten innehåller dessutom en instans för externa belastningsutjämnaren, en offentlig IP-adress och standardslutpunkterna för att tillåta fjärråtkomst fjärrskrivbord och PowerShell för Windows-baserade virtuella datorer och SSH (Secure Shell) trafik för Linux-baserade virtuella datorer.
* Ett obligatoriskt lagringskonto som lagrar de virtuella hårddiskarna för en virtuell dator, inklusive operativsystem, tillfällig och ytterligare datadiskar (lagring).
* Ett valfritt virtuellt nätverk som fungerar som en ytterligare behållare som du kan skapa en uppdelade struktur och ange undernätet där den virtuella datorn finns (nätverk).

I följande tabell beskrivs ändringar i hur beräknings-, nätverks- och Storage-resursprovidrar samverkar:

| Objekt | Klassisk | Resource Manager |
| --- | --- | --- |
| Molntjänst för Virtual Machines |Molntjänst var en behållare för virtuella datorer som krävde tillgänglighet från plattformen och belastningsutjämning. |Molntjänst är inte längre ett objekt som krävs för att skapa en virtuell dator med den nya modellen. |
| Virtuella nätverk |Ett virtuellt nätverk är valfri för den virtuella datorn. Om det virtuella nätverket kan inte distribueras med Resource Manager. |Virtuell dator kräver ett virtuellt nätverk som har distribuerats med Resource Manager. |
| Lagringskonton |Den virtuella datorn kräver ett lagringskonto som lagrar de virtuella hårddiskarna för operativsystemet, tillfällig och ytterligare datadiskar. |Den virtuella datorn kräver ett lagringskonto för att lagra diskarna i blob storage. |
| Tillgänglighetsuppsättningar |Tillgänglighet till plattformen indikerades genom att konfigurera samma "AvailabilitySetName" på Virtual Machines. Det maximala antalet feldomäner var 2. |Tillgänglighetsuppsättning är en resurs som exponeras av Microsoft.Compute-providern. Virtuella datorer som kräver hög tillgänglighet måste inkluderas i tillgänglighetsuppsättningen. Det maximala antalet feldomäner är nu 3. |
| Tillhörighetsgrupper |Tillhörighetsgrupper krävdes för virtuella nätverk. Men med introduktionen av regionala virtuella nätverk, krävs det inte längre. |Lite enklare sagt så existerar inte konceptet tillhörighetsgrupper i de API:er som exponeras via Azure Resource Manager. |
| Belastningsutjämning |Skapandet av en molntjänst ger en implicit belastningsutjämnare för de virtuella datorer som distribueras. |Load Balancer är en resurs som exponeras av Microsoft.Network-providern. Det primära nätverksgränssnittet för Virtual Machines som måste belastningsutjämnas ska referera till belastningsutjämnaren. Belastningsutjämnare kan vara interna eller externa. En belastningen belastningsutjämnaren instans refererar till serverdelspoolen av IP-adresser med nätverkskort för en virtuell dator (valfritt) som refererar till en offentlig eller privat IP-belastningsutjämnaradress (valfritt). |
| Virtuell IP-adress |Cloud Services får en standard-VIP (virtuell IP-adress) när en virtuell dator läggs till en tjänst i molnet. Den virtuella IP-adressen är den adress som är associerad med den implicita belastningsutjämnaren. |En offentlig IP-adress är en resurs som exponeras av Microsoft.Network-providern. Offentliga IP-adressen kan vara statisk (reserverad) eller dynamisk. Dynamiska offentliga IP-adresser kan tilldelas till en belastningsutjämnare. Offentliga IP-adresser kan skyddas med hjälp av säkerhetsgrupper. |
| Reserverad IP-adress |Du kan reservera en IP-adress i Azure och koppla den till en molntjänst för att kontrollera att IP-adressen är fäst. |Offentlig IP-adress kan skapas i statiskt läge och den har samma funktion som en reserverad IP-adress. |
| Offentlig IP-adress (PIP) per VM |Offentliga IP-adresser kan också vara kopplad till en virtuell dator direkt. |En offentlig IP-adress är en resurs som exponeras av Microsoft.Network-providern. Offentliga IP-adressen kan vara statisk (reserverad) eller dynamisk. |
| Slutpunkter |Inkommande slutpunkter behöver konfigureras på en virtuell dator för att öppna upp anslutningar på vissa portar. Ett vanligt sätt att ansluta till virtuella datorer är genom att konfigurera inkommande slutpunkter. |Inkommande NAT-regler kan konfigureras på belastningsutjämnare för att uppnå samma funktion som vid aktivering av inkommande slutpunkter på specifika portar för att ansluta till VM:ar. |
| DNS-namn |En molntjänst får ett implicit, globalt-unikt DNS-namn. Till exempel: `mycoffeeshop.cloudapp.net`. |DNS-namn är valfria parametrar som kan anges för en offentlig IP-adressresurs. FQDN är i följande format: `<domainlabel>.<region>.cloudapp.azure.com`. |
| Nätverksgränssnitt |Primära och sekundära nätverksgränssnitt och dess egenskaper har definierats som nätverkskonfigurationen för en virtuell dator. |Nätverksgränssnittet är en resurs som exponeras av Microsoft.Network-providern. Livscykeln för nätverksgränssnittet är inte kopplat till en virtuell dator. Den refererar till den virtuella datorn är tilldelad IP-adress (obligatoriskt), undernät för det virtuella nätverket för den virtuella datorn (krävs) och en Nätverkssäkerhetsgrupp (valfritt). |

Läs om hur du ansluter virtuella nätverk från olika distributionsmodeller i [ansluta virtuella nätverk från olika distributionsmodeller i portalen](../vpn-gateway/vpn-gateway-connect-different-deployment-models-portal.md).

## <a name="migrate-from-classic-to-resource-manager"></a>Migrera från classic till Resource Manager
Om du är redo att migrera dina resurser från klassiska distributionen till Resource Manager-distribution, se:

1. [Tekniska ingående om plattformen stöds från klassiska till Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-deep-dive.md)
2. [Migrering av plattform som stöds av IaaS-resurser från klassiska till Azure Resource Manager](../virtual-machines/windows/migration-classic-resource-manager-overview.md)
3. [Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure PowerShell](../virtual-machines/windows/migration-classic-resource-manager-ps.md)
4. [Migrera IaaS-resurser från klassiska till Azure Resource Manager med hjälp av Azure CLI](../virtual-machines/virtual-machines-linux-cli-migration-classic-resource-manager.md)

## <a name="frequently-asked-questions"></a>Vanliga frågor och svar
**Kan jag skapa en virtuell dator med Resource Manager för distribution i ett virtuellt nätverk som skapats med hjälp av klassisk distribution?**

Den här konfigurationen stöds inte. Du kan inte använda Resource Manager för att distribuera en virtuell dator till ett virtuellt nätverk som skapades med klassisk distribution.

**Kan jag skapa en virtuell dator med Resource Manager från en användaravbildning som skapades med den klassiska distributionsmodellen?**

Den här konfigurationen stöds inte. Du kan dock kopiera VHD-filer från ett lagringskonto som har skapats med den klassiska distributionsmodellen och lägga till dem i ett nytt konto via Resource Manager.

**Hur påverkas kvoten för min prenumeration?**

Kvoter för virtuella datorer, virtuella nätverk och lagringskonton som skapats via Azure Resource Manager skiljer sig från andra kvoter. Varje prenumeration får kvoter att skapa resurser med de nya API: er. Du kan läsa mer om de utökade kvoterna [här](../azure-subscription-service-limits.md).

**Kan jag fortsätta att använda Mina automatiserade skript för etablering av virtuella datorer, virtuella nätverk och lagringskonton via Resource Manager-API: er?**

All automatisering och skript som du har skapat fortsätta fungera för befintliga virtuella datorer, virtuella nätverk som skapats under Azure Service Management-läge. Skripten måste dock uppdateras för att använda det nya schemat för att skapa samma resurser i Resource Manager-läget.

**Var hittar jag exempel på Azure Resource Manager-mallar**

En omfattande uppsättning startmallar finns på [Azure Resource Manager-Snabbstartsmallar](https://azure.microsoft.com/documentation/templates/).

## <a name="next-steps"></a>Nästa steg
* Om du vill gå igenom skapandet av mallen som definierar en virtuell dator, storage-konto och virtuella nätverk finns [genomgång av Resource Manager-mall](resource-manager-template-walkthrough.md).
* Kommandon för att distribuera en mall finns i [distribuera ett program med Azure Resource Manager-mall](resource-group-template-deploy.md).

