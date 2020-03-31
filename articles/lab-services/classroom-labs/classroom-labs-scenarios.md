---
title: Använda Classroom Labs för utbildningar - Azure Lab Services
description: I den här artikeln beskrivs hur du använder Azure DevTest Labs för att skapa labb på Azure för utbildningsscenarier.
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
ms.date: 01/23/2020
ms.author: spelluru
ms.openlocfilehash: 155806222f9e11fec177487b7147d81054ac06ed
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76717994"
---
# <a name="use-classroom-labs-for-trainings"></a>Använd Classroom Labs för utbildningar
Du kan skapa ett labb för utbildningar. Classroom Labs of Azure Lab Services gör att du kan skapa ett labb för din utbildning där varje praktikant använder identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildningsmiljöer endast är tillgängliga för varje praktikant när de behöver dem och innehåller tillräckligt med resurser , till exempel virtuella datorer, som krävs för utbildningen. 

![Labb i klassrummet](../media/classroom-labs-scenarios/classroom.png)

Classroom Labs uppfyller följande krav som krävs för att genomföra utbildning i alla virtuella miljöer: 

- Praktikanter kan snabbt tillhandahålla sina utbildningsmiljöer
- Varje träningsmaskin ska vara identisk
- Praktikanter kan inte se virtuella datorer som skapats av andra praktikanter
- Kontrollera kostnaderna genom att se till att praktikanter inte kan få fler virtuella datorer än de behöver för utbildning och även avstängning virtuella datorer när de inte använder dem
- Dela enkelt utbildningslabbet med varje praktikant
- Återanvända träningslabbet om och om igen

I den här artikeln får du lära dig mer om olika Azure Lab Services-funktioner som kan användas för att uppfylla de tidigare beskrivna utbildningskraven och detaljerade steg som du kan följa för att konfigurera ett labb för utbildning.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Skapa labbkontot som administratör för labbkonto
Det första steget i att använda Azure Lab Services är att skapa ett labbkonto i Azure-portalen. När en labbkontoadministratör har skapat labbkontot lägger administratören till användare som vill skapa labb i rollen **Labbskapare.** Utbildarna skapar labb med virtuella maskiner för studenter att göra övningar för kursen de undervisar. Mer information finns i [Skapa och hantera labbkonto](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Skapa och hantera klassrumslabb
En tränare som är medlem i rollen Lab Creator i ett labbkonto kan skapa ett eller flera labb i labbkontot. Du skapar och konfigurerar en virtuell mall med all nödvändig programvara för att göra övningar i din kurs. Du väljer en färdig avbildning från tillgängliga bilder för att skapa ett klassrumslabb och sedan anpassa det genom att installera den programvara som krävs för labbet. Mer information finns i [Skapa och hantera klassrumslabb](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurera användningsinställningar och principer
Labbskaparen kan lägga till eller ta bort användare i labbet, få registreringslänk för att skicka till labbanvändare, ställa in principer som att ange enskilda kvoter per användare, uppdatera antalet virtuella datorer som är tillgängliga i labbet med mera. Mer information finns i [Konfigurera användningsinställningar och principer](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Skapa och hantera scheman
Scheman kan du konfigurera ett klassrum lab så att virtuella datorer i labbet automatiskt starta och stängas av vid en viss tidpunkt. Du kan definiera ett engångsschema eller ett återkommande schema. Mer information finns i [Skapa och hantera scheman för klassrumslabb](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurera och publicera en virtuell mall
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Ställ in mallen VM så att den är konfigurerad med exakt vad du vill ge till utbildningsdeltagarna. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan publicerar du mallen för att göra instanser av mallen VM tillgängliga för dina labbanvändare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. Mer information finns i [Konfigurera och publicera virtuella malldatorer](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Använda virtuella datorer i klassrumslabbet
En deltagare eller utbildningsdeltagare registrerar sig till labbet och ansluter till den virtuella datorn för att göra övningar för kursen. Mer information finns i [Så här kommer du åt ett klassrumslabb](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Nästa steg
Börja med att skapa ett labbkonto i Classroom Labs genom att följa instruktionerna i artikeln: [Handledning: Konfigurera ett labbkonto med Azure Lab Services](tutorial-setup-lab-account.md).