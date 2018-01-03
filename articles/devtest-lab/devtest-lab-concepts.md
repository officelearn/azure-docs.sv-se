---
title: "Begrepp för DevTest Labs | Microsoft Docs"
description: "Lär dig grundläggande begrepp för DevTest Labs och hur det kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer"
services: devtest-lab,virtual-machines
documentationcenter: na
author: craigcaseyMSFT
manager: douge
editor: 
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/25/2016
ms.author: v-craic
ms.openlocfilehash: 46271c1122df852b37d4117f9d4008fd74f43d95
ms.sourcegitcommit: 85012dbead7879f1f6c2965daa61302eb78bd366
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2018
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-koncept
## <a name="overview"></a>Översikt
Följande lista innehåller huvudbegrepp DevTest Labs och definitioner:

## <a name="labs"></a>Labbar
Ett labb är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig bättre hantera resurserna genom att ange gränser och kvoter.

## <a name="virtual-machine"></a>Virtuell dator
En Azure VM är en av flera typer av [på begäran skalbara datorresurser](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) som Azure erbjuder. Virtuella Azure-datorer ger dig virtualiseringsflexibilitet utan att köpa och underhålla den fysiska maskinvara som kör det, även om du fortfarande behöver underhålla den virtuella datorn genom att utföra vissa åtgärder, till exempel konfigurera, korrigera och installera programvaran som körs på den.

[Översikt över Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) ger information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar och hur du hanterar.

## <a name="claimable-vm"></a>Claimable VM
En Claimable Azure VM är en virtuell dator som är tillgängliga för användning av alla lab-användare med behörighet. En labb-administratör kan förbereda virtuella datorer med vissa grundläggande bilder och artefakter och spara dem till en delad pool. Lab-användare kan sedan begära en aktiv virtuell dator från poolen när de behöver en med den specifika konfigurationen.

En virtuell dator som är claimable ursprungligen har tilldelats inte en viss användare, men kommer att visas i listan för varje användare under ”Claimable virtuella datorer”. När en virtuell dator ägs av en användare, flyttas upp till sina ”Mina virtuella datorer” området och inte längre claimable av någon annan användare.

## <a name="environment"></a>Miljö
En miljö refererar till en samling Azure-resurser i ett labb i DevTest Labs. [Det här blogginlägget](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) beskrivs hur du skapar flera Virtuella miljöer från Azure Resource Manager-mallar.

## <a name="base-images"></a>Grundläggande bilder
Grundläggande bilder är VM-avbildningar med de verktyg och inställningar förinstallerat och konfigurerade för att snabbt skapa en virtuell dator. Du kan etablera en virtuell dator genom att välja en befintlig base och lägger till en artefakt för installation av test-agent. Du kan spara den etablerade Virtuellt som bas så att grundläggande kan användas utan att behöva installera om agenten test för varje etableringen av den virtuella datorn.

## <a name="artifacts"></a>Artefakter
Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

* Verktyg som du vill installera på den virtuella datorn – t.ex agenter, Fiddler och Visual Studio.
* Åtgärder som du vill köra på den virtuella datorn – t.ex att klona en repo.
* Program som du vill testa.

Artefakter är [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) JSON-filer som innehåller instruktioner för att utföra distribution och tillämpa konfigurationen.

## <a name="artifact-repositories"></a>Artefakt databaser
Artefakt databaser är git-databaser där artefakter checkas in. Artefakt databaser kan läggas till flera övningar i din organisation att aktivera återanvändning och delning.

## <a name="formulas"></a>Formler
Formler, förutom grundläggande bilder, tillhandahåller en mekanism för snabb etablering av virtuell dator. En formel i DevTest Labs är en lista över egenskapen standardvärden används för att skapa ett labb VM.
Med formler, virtuella datorer med samma uppsättning egenskaper – till exempel basavbildning, VM-storlek, virtuella nätverk och artefakter - skapas utan att behöva ange dessa egenskaper varje gång. När du skapar en virtuell dator från en formel standardvärdena kan användas som-har eller ändrats.

## <a name="policies"></a>Principer
Med hjälp av principer kontrollerar kostnaden i labbet. Du kan till exempel skapa en princip för att automatiskt stänga av virtuella datorer baserat på ett definierat schema.

## <a name="caps"></a>Versaler
CAPS är en mekanism för att minimera skräp i labbet. Du kan till exempel ange ett tak för att begränsa antalet virtuella datorer som kan skapas per användare eller i ett labb.

## <a name="security-levels"></a>Säkerhetsnivåer
Säkerhetsåtkomst bestäms av rollbaserad åtkomstkontroll (RBAC). För att förstå hur åtkomst fungerar, hjälper det att du förstår skillnaderna mellan en behörighet, en roll och ett omfång som definieras av RBAC.

* Behörigheten - behörighet är en definierad åtkomst till en specifik åtgärd (t.ex. Läs-åtkomst till alla virtuella datorer).
* Roll - en roll är en uppsättning behörigheter som kan grupperas och tilldelats till en användare. Till exempel den *prenumerationsägaren* rollen har åtkomst till alla resurser inom en prenumeration.
* Scope - ett scope är en nivå i hierarkin för en Azure-resurs, till exempel en resursgrupp, en enda lab eller hela prenumerationen.

Det finns två typer av roller för att definiera behörigheter inom omfånget för DevTest Labs: labbägaren och lab-användare.

* Labbägaren - en labbägaren har åtkomst till resurser i labbet. Därför kan en labbägaren ändra principer, läsa och skriva alla virtuella datorer, ändra det virtuella nätverket och så vidare.
* Lab-användare - lab-användare kan visa alla lab resurser, till exempel virtuella datorer, principer och virtuella nätverk, men det går inte att ändra principer eller virtuella datorer som skapats av andra användare.

Om du vill se hur du skapar anpassade roller i DevTest Labs finns i artikel [bevilja användarbehörighet att principer för specifika labbet](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörighet för ett visst område kan beviljas de automatiskt dessa behörighet för definitionsområdet var lägre nivå finns. Till exempel om en användare har tilldelats rollen prenumerationsägaren, har sedan de åtkomst till alla resurser i en prenumeration som omfattar alla virtuella datorer, alla virtuella nätverk och alla övningar. Därför ärver en prenumerationsägaren automatiskt labbägaren roll. Motsatsen är inte sant. En labbägaren har åtkomst till ett labb som är en omfattning som är lägre än prenumerationsnivån. Därför kan en labbägaren inte finns virtuella datorer eller virtuella nätverk eller resurser som ligger utanför labbet.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Alla de begrepp som diskuteras i den här artikeln kan konfigureras med hjälp av Azure Resource Manager-mallar, vilket gör att du kan definiera infrastruktur/konfigurationen av din lösning för Azure och distribuera den flera gånger i ett konsekvent tillstånd.

[Förstå struktur och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) beskriver strukturen för en Azure Resource Manager-mall och egenskaper som är tillgängliga i de olika avsnitten i en mall.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
[Skapa ett labb i DevTest Labs](devtest-lab-create-lab.md)
