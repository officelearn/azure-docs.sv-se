---
title: DevTest Labs koncept | Microsoft-dokument
description: Lär dig de grundläggande begreppen i DevTest Labs och hur det kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: 105919e8-3617-4ce3-a29f-a289fa608fb2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/05/2018
ms.author: spelluru
ms.openlocfilehash: 22fd78ccd58be1790fcd167da396600e8b876564
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75428919"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-koncept
## <a name="overview"></a>Översikt
Följande lista innehåller viktiga devtest labs-begrepp och definitioner:

## <a name="labs"></a>Labbar
Ett labb är den infrastruktur som omfattar en grupp resurser, till exempel virtuella datorer ( virtuella datorer), som gör att du bättre kan hantera dessa resurser genom att ange gränser och kvoter.

## <a name="virtual-machine"></a>Virtuell dator
En Virtuell Azure-dator är en av flera typer av [skalbara datorresurser på begäran](/azure/architecture/guide/technology-choices/compute-decision-tree) som Azure erbjuder. Virtuella Azure-datorer ger dig flexibiliteten i virtualisering utan att behöva köpa och underhålla den fysiska maskinvaran som kör den, även om du fortfarande behöver underhålla den virtuella datorn genom att utföra vissa uppgifter, till exempel konfigurera, korrigera och installera programvaran som körs på Det.

[Översikt över virtuella Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) ger dig information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

## <a name="claimable-vm"></a>Dator som kan begäras på begäran
En virtuell azure-anspråksbar virtuell dator är en virtuell dator som är tillgänglig för användning av alla labbanvändare med behörigheter. En labbadministratör kan förbereda virtuella datorer med specifika basavbildningar och artefakter och spara dem i en delad pool. En labbanvändare kan sedan göra anspråk på en fungerande virtuell dator från poolen när de behöver en med den specifika konfigurationen.

En virtuell dator som kan anspråksbara tilldelas inte initialt till en viss användare, men visas i varje användares lista under "Claimable virtual machines". När en virtuell dator har begärts av en användare flyttas den upp till deras område "Mina virtuella datorer" och kan inte längre anspråksbara av någon annan användare.

## <a name="environment"></a>Miljö
I DevTest Labs refererar en miljö till en samling Azure-resurser i ett labb. [I det här blogginlägget](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) beskrivs hur du skapar miljöer med flera virtuella datorer från dina Azure Resource Manager-mallar.

## <a name="base-images"></a>Basavbildningar
Basavbildningar är VM-avbildningar med alla verktyg och inställningar förinstallerade och konfigurerade för att snabbt skapa en virtuell dator. Du kan etablera en virtuell dator genom att välja en befintlig bas och lägga till en artefakt för att installera testagenten. Du kan sedan spara den etablerade virtuella datorn som en bas så att basen kan användas utan att behöva installera om testagenten för varje etablering av den virtuella datorn.

## <a name="artifacts"></a>Artefakter
Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

* Verktyg som du vill installera på den virtuella datorn – till exempel agenter, Spelman och Visual Studio.
* Åtgärder som du vill köra på den virtuella datorn – till exempel kloning av en repo.
* Program som du vill testa.

Artefakter är [Azure Resource Manager](../azure-resource-manager/management/overview.md) JSON-filer som innehåller instruktioner för att utföra distribution och tillämpa konfiguration.

## <a name="artifact-repositories"></a>Artefaktdatabaser
Artefaktdatabaser är git-databaser där artefakter checkas in. Artefaktdatabaser kan läggas till i flera labb i organisationen som aktiverar återanvändning och delning.

## <a name="formulas"></a>Formler
Formler, förutom basavbildningar, ger en mekanism för snabb vm-etablering. En formel i DevTest Labs är en lista över standardegenskapsvärden som används för att skapa en virtuell labb-dator.
Med formler kan virtuella datorer med samma uppsättning egenskaper – till exempel basavbildning, vm-storlek, virtuellt nätverk och artefakter – skapas utan att behöva ange dessa egenskaper varje gång. När du skapar en virtuell dator från en formel kan standardvärdena användas som är eller ändras.

## <a name="policies"></a>Principer
Principer hjälper till att kontrollera kostnaderna i labbet. Du kan till exempel skapa en princip för att automatiskt stänga av virtuella datorer baserat på ett definierat schema.

## <a name="caps"></a>Kepsar
Caps är en mekanism för att minimera avfall i ditt labb. Du kan till exempel ange ett tak för att begränsa antalet virtuella datorer som kan skapas per användare eller i ett labb.

## <a name="security-levels"></a>Säkerhetsnivåer
Säkerhetsåtkomst bestäms av Azure Role-Based Access Control (RBAC). För att förstå hur åtkomst fungerar hjälper det att förstå skillnaderna mellan en behörighet, en roll och ett scope som definieras av RBAC.

* Behörighet - En behörighet är en definierad åtkomst till en viss åtgärd (t.ex. läsåtkomst till alla virtuella datorer).
* Roll - En roll är en uppsättning behörigheter som kan grupperas och tilldelas en användare. Rollen *prenumerationsägare* har till exempel åtkomst till alla resurser i en prenumeration.
* Scope - Ett scope är en nivå inom hierarkin för en Azure-resurs, till exempel en resursgrupp, ett enda labb eller hela prenumerationen.

Inom ramen för DevTest Labs finns det två typer av roller för att definiera användarbehörigheter: labbägare och labbanvändare.

* Lab Ägare - En labbägare har tillgång till alla resurser i labbet. Därför kan en labbägare ändra principer, läsa och skriva alla virtuella datorer, ändra det virtuella nätverket och så vidare.
* Labbanvändare – En labbanvändare kan visa alla labbresurser, till exempel virtuella datorer, principer och virtuella nätverk, men kan inte ändra principer eller virtuella datorer som skapats av andra användare.

Information om hur du skapar anpassade roller i DevTest Labs finns i artikeln [Bevilja användarbehörigheter till specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörigheter i ett visst omfång, beviljas de automatiskt dessa behörigheter på varje begränsad omfattning på lägre nivå. Till exempel om en användare tilldelas rollen som prenumerationsägare, har de åtkomst till alla resurser i en prenumeration, som omfattar alla virtuella datorer, alla virtuella nätverk och alla labb. Därför ärver en prenumerationsägare automatiskt rollen som labbägare. Det motsatta är dock inte sant. En labbägare har åtkomst till ett labb, vilket är ett lägre omfång än prenumerationsnivån. Därför kan en labbägare inte se virtuella datorer eller virtuella nätverk eller resurser som finns utanför labbet.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Alla begrepp som beskrivs i den här artikeln kan konfigureras med hjälp av Azure Resource Manager-mallar, som låter dig definiera infrastrukturen/konfigurationen av din Azure-lösning och upprepade gånger distribuera den i ett konsekvent tillstånd.

[Förstå strukturen och syntaxen i Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) beskriver strukturen för en Azure Resource Manager-mall och de egenskaper som är tillgängliga i de olika avsnitten i en mall.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
[Skapa ett labb i DevTest Labs](devtest-lab-create-lab.md)
