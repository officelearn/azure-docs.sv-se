---
title: Använda klass rums labb för utbildning – Azure Lab Services
description: I den här artikeln beskrivs hur du använder Azure DevTest Labs för att skapa labb i Azure för inlärnings scenarier.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
ms.assetid: 57ff4e30-7e33-453f-9867-e19b3fdb9fe2
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/08/2020
ms.author: spelluru
ms.openlocfilehash: 54fd8caef51a17148714d9cf90328572433c59db
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/12/2020
ms.locfileid: "83115123"
---
# <a name="use-classroom-labs-for-trainings"></a>Använda klass rums labb för utbildningar
Med Azure Labs Services kan du snabbt och enkelt skapa ett online-labb för att etablera förkonfigurerade inlärnings miljöer för eleverna. Varje ombordpersonal skulle kunna använda identiska och isolerade miljöer för utbildningen. Principer kan användas för att säkerställa att inlärnings miljöerna endast är tillgängliga för varje ombordpersonal när de behöver dem och innehåller tillräckligt med resurser, till exempel virtuella datorer – krävs för utbildningen. 

![Klass rums labb](../media/classroom-labs-scenarios/classroom.png)

Klass rum Labs uppfyller följande krav som krävs för att utföra utbildning i alla virtuella miljöer: 

- Eleverna kan snabbt etablera sina utbildnings miljöer
- Varje utbildnings dator bör vara identisk
- Eleverna kan inte se virtuella datorer som skapats av andra eleverna
- Kontrol lera kostnaden genom att se till att eleverna inte kan hämta fler virtuella datorer än de behöver för utbildningen och Stäng virtuella datorer när de inte använder dem
- Dela utbildnings labbet enkelt med varje fordonsbaserad
- Återanvänd utbildnings labbet igen och återigen

I den här artikeln får du lära dig mer om olika Azure Lab Services funktioner som kan användas för att uppfylla de tidigare beskrivna utbildnings kraven och detaljerade steg som du kan följa för att ställa in ett labb för utbildning.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Skapa labb kontot som en labb konto administratör
Det första steget i att använda Azure Lab Services är att skapa ett labb konto i Azure Portal. När en labb konto administratör skapar labb kontot, lägger administratören till användare som vill skapa labb till rollen **labb skapare** . Läraren skapar labb med virtuella datorer för studenter för att göra övningar för den kurs som de lär sig. Mer information finns i [skapa och hantera labb konto](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Skapa och hantera klassrumslabb
En utbildare, som är medlem i rollen labb skapare i ett labb konto, kan skapa ett eller flera labb i labb kontot. Du skapar och konfigurerar en mall för virtuella datorer med all nödvändig program vara för att utföra övningar i kursen. Du väljer en färdig avbildning från de tillgängliga avbildningarna för att skapa ett klass rums labb och anpassar det genom att installera den program vara som krävs för labbet. Mer information finns i [skapa och hantera klass rums labb](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurera användnings inställningar och principer
Labb skaparen kan lägga till eller ta bort användare i labbet, Hämta registrerings länk för att skicka till labb användare, konfigurera principer, till exempel ställa in enskilda kvoter per användare, uppdatera antalet tillgängliga virtuella datorer i labbet och mycket mer. Mer information finns i [Konfigurera användnings inställningar och principer](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Skapa och hantera scheman
Med scheman kan du konfigurera ett klass rums labb så att virtuella datorer i labbet startar och stängs av automatiskt vid en viss tidpunkt. Du kan definiera ett eng ång slö schema eller ett återkommande schema. Mer information finns i [skapa och hantera scheman för klass rums labb](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurera och publicera en mall för virtuella datorer
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera mallen för den virtuella datorn så att den konfigureras med exakt det du vill ge till utbildnings deltagarna. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att skapa instanser av den virtuella mallen som är tillgänglig för dina labb användare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. Mer information finns i [Konfigurera och publicera mall för virtuella datorer](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Använda virtuella datorer i klass rums labbet
En student-eller tränings deltagare registrerar sig för labbet och ansluter till den virtuella datorn för att göra övningar för kursen. Mer information finns i [så här får du åtkomst till ett klass rums labb](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Nästa steg
Börja med att skapa ett labb konto i klass rum labbet genom att följa anvisningarna i artikeln: [självstudie: Konfigurera ett labb konto med Azure Lab Services](tutorial-setup-lab-account.md).