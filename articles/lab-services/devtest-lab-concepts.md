---
title: DevTest Labs-koncept | Microsoft Docs
description: Lär dig de grundläggande principerna för DevTest Labs och hur det kan göra det enkelt att skapa, hantera och övervaka virtuella Azure-datorer
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
ms.openlocfilehash: ce0c51a3b97c57095a336128919c5e27f860905b
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2018
ms.locfileid: "38676478"
---
# <a name="devtest-labs-concepts"></a>DevTest Labs-koncept
## <a name="overview"></a>Översikt
I följande lista innehåller viktiga DevTest Labs-koncept och definitioner:

## <a name="labs"></a>Labbar
Ett labb är den infrastruktur som omfattar en grupp med resurser, till exempel virtuella datorer (VM), som låter dig hantera resurserna genom att ange gränser och kvoter.

## <a name="virtual-machine"></a>Virtuell dator
En Azure-dator är en av flera typer av [behovsbaserade och skalbara datorresurser](https://docs.microsoft.com/azure/app-service/choose-web-site-cloud-service-vm) som Azure erbjuder. Azure virtuella datorer ger dig virtualiseringsflexibilitet utan att behöva köpa och underhålla den fysiska maskinvara som den körs, även om du behöver fortfarande underhålla den virtuella datorn genom att utföra vissa åtgärder, till exempel konfigurera, korrigera och underhålla programvaran som körs på den.

[Översikt över Windows-datorer i Azure](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-overview) ger information om vad du bör tänka på innan du skapar en virtuell dator, hur du skapar den och hur du hanterar den.

## <a name="claimable-vm"></a>Virtuell dator
En virtuell dator i Azure är en virtuell dator som är tillgänglig för användning av någon lab-användare med behörigheter. En labb-administratör kan förbereda virtuella datorer med specifik Källavbildningen och artefakter och spara dem till en delad pool. En lab-användare kan sedan göra anspråk en aktiv virtuell dator från poolen när de behöver en med den specifika konfigurationen.

En virtuell dator som är tillgängliga tilldelas inte inledningsvis till en viss användare, men visas på varje användares listan under ”tillgängliga virtuella datorer”. När en virtuell dator ägs av en användare, den flyttas upp till deras området ”Mina virtuella datorer” och är inte längre tillgängliga av någon annan användare.

## <a name="environment"></a>Miljö
En miljö refererar till en samling Azure-resurser i ett labb i DevTest Labs. [Det här blogginlägget](https://blogs.msdn.microsoft.com/devtestlab/2016/11/16/connect-2016-news-for-azure-devtest-labs-azure-resource-manager-template-based-environments-vm-auto-shutdown-and-more/) beskrivs hur du skapar Konsekvens miljöer från dina Azure Resource Manager-mallar.

## <a name="base-images"></a>Basavbildningar
Grundläggande avbildningar är avbildningar av Virtuella datorer med de verktyg och inställningar förinstallerat och har konfigurerats för att snabbt skapa en virtuell dator. Du kan etablera en virtuell dator genom att välja en befintlig base och lägger till en artefakt om du vill installera test-agenten. Du kan spara den etablerade virtuella datorn som bas så att grundläggande kan användas utan att behöva installera om agenten test för varje etablering av den virtuella datorn.

## <a name="artifacts"></a>Artefakter
Artefakter används för att distribuera och konfigurera ditt program när en virtuell dator har etablerats. Artefakter kan vara:

* Verktyg som du vill installera på den virtuella datorn – t.ex agenter, Fiddler och Visual Studio.
* Åtgärder som du vill köra på den virtuella datorn – till exempel kloning av en lagringsplats.
* Program som du vill testa.

Artefakter är [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) JSON-filer som innehåller anvisningar för att distribuera och tillämpa konfigurationen.

## <a name="artifact-repositories"></a>Artefakten databaser
Artefakten databaser är git-lagringsplatser där artefakter checkas in. Artefakten databaser kan läggas till flera labs i din organisation att aktivera återanvändning och delning.

## <a name="formulas"></a>Formler
Formler, förutom grundläggande avbildningar tillhandahåller en mekanism för snabb etablering av virtuella datorer. En formel i DevTest Labs är en lista över standardegenskapsvärden som används för att skapa ett labb VM.
Med formler, virtuella datorer med samma uppsättning egenskaper – till exempel basavbildningen, VM-storlek, virtuellt nätverk och artefakter - kan skapas utan att behöva ange dessa egenskaper varje gång. När du skapar en virtuell dator från en formel, standardvärdena kan användas som – är eller ändras.

## <a name="policies"></a>Principer
Med hjälp av principer i kontrollkostnader i labbet. Du kan till exempel skapa en princip för att automatiskt stänga ned virtuella datorer baserat på ett definierat schema.

## <a name="caps"></a>CAPS
CAPS är en mekanism för att minimera slöseri i labbet. Du kan till exempel ange ett tak för att begränsa antalet virtuella datorer som kan skapas per användare eller i ett labb.

## <a name="security-levels"></a>Säkerhetsnivåer
Säkerhetsåtkomst bestäms av rollbaserad åtkomstkontroll (RBAC). Information om hur åtkomst fungerar finns det hjälper dig för att förstå skillnaderna mellan en behörighet, en roll och ett omfång som definieras av RBAC.

* Behörigheten - behörigheter är en definierad åtkomst till en specifik åtgärd (t.ex. Läs-åtkomst till alla virtuella datorer).
* Roll – en roll är en uppsättning behörigheter som kan grupperas och tilldelas till en användare. Till exempel den *prenumerationsägaren* rollen har åtkomst till alla resurser inom en prenumeration.
* Scope - ett scope är en nivå i hierarkin för en Azure-resurs, till exempel en resursgrupp, ett enda labb eller hela prenumerationen.

Det finns två typer av roller för att definiera behörigheter inom omfånget för DevTest Labs: lab ägar- och labb.

* Labbägare - labbägare har åtkomst till alla resurser i labbet. Därför kan labbägare ändra principer, läsa och skriva alla virtuella datorer, ändra det virtuella nätverket och så vidare.
* Lab användare – en lab-användare kan visa alla labbresurser, till exempel virtuella datorer, principer och virtuella nätverk, men det går inte att ändra principer eller alla virtuella datorer som skapats av andra användare.

Om du vill se hur du skapar anpassade roller i DevTest Labs finns i artikeln [bevilja användarbehörighet att specifika labbprinciper](devtest-lab-grant-user-permissions-to-specific-lab-policies.md).

Eftersom scope är hierarkiska, när en användare har behörighet för ett visst omfång kan beviljas de automatiskt dessa behörighet för definitionsområdet varje på lägre nivå databasloggen. Till exempel om en användare har tilldelats rollen som prenumerationsägare, har sedan de åtkomst till alla resurser i en prenumeration som omfattar alla virtuella datorer, alla virtuella nätverk och alla labs. Därför ärver prenumerationsägare automatiskt i rollen labbägare. Motsatsen är inte sant. Labbägare har åtkomst till ett labb, vilket är ett omfång som är lägre än prenumerationsnivån. Labbägare kommer därför inte att kunna se virtuella datorer eller virtuella nätverk eller resurser som är utanför labbet.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-mallar
Alla de begrepp som diskuteras i den här artikeln kan konfigureras med hjälp av Azure Resource Manager-mallar som låter dig Definiera infrastruktur/konfigurationen av din Azure-lösning och upprepade gånger distribuera i ett konsekvent tillstånd.

[Förstå strukturen och syntaxen för Azure Resource Manager-mallar](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates#template-format) beskriver strukturen för en Azure Resource Manager-mall och de egenskaper som är tillgängliga i de olika delarna i en mall.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Nästa steg
[Skapa ett labb i DevTest Labs](devtest-lab-create-lab.md)
