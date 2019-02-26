---
title: Använda labb för klassrum för utbildningar – Azure Lab Services | Microsoft Docs
description: Lär dig hur du använder Azure DevTest Labs för utbildning-scenarier.
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
ms.date: 02/22/2019
ms.author: spelluru
ms.openlocfilehash: 4d2ba11181977f1976b5ae933e8b93a92424fa96
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/25/2019
ms.locfileid: "56808735"
---
# <a name="use-classroom-labs-for-trainings"></a>Använda labb för klassrum för utbildningar
Du kan ställa in ett labb för utbildningar. Klassrum Labs av Azure Lab Services kan du skapa ett labb för utbildning där varje praktikanten använder identiska och isolerade miljöer för utbildning. Du kan tillämpa principer för att säkerställa att utbildning miljöer är tillgängliga för varje ges endast när de behöver dem och innehåller tillräckligt med resurser – till exempel virtuella datorer – krävs för utbildning. 

![Labbet klassrum](../media/classroom-labs-scenarios/classroom.png)

Labb för klassrum uppfyller följande krav som krävs för att genomföra utbildningar i alla virtuella miljöer: 

- Eleverna kan snabbt etablera sina miljöer för utbildning
- Alla datorer för utbildning ska vara identiska
- Eleverna kan inte se virtuella datorer som skapats av andra eleverna
- Styra kostnaderna genom att se till att eleverna inte kan hämta flera virtuella datorer än de behöver för utbildning och även stänga av virtuella datorer när de inte använder dem
- Enkelt dela labb för utbildning med varje äga rum
- Återanvända labb för utbildning och om igen

I den här artikeln får du lära dig om olika Azure Lab Services-funktioner som kan användas för att uppfylla de tidigare beskrivna utbildningskrav och detaljerade steg som du kan följa om du vill konfigurera ett labb för utbildning.  

## <a name="create-the-lab-account-as-a-lab-account-administrator"></a>Skapa labb-kontot som en lab kontoadministratör
Det första steget i att använda Azure Lab Services är att skapa ett labbkonto i Azure-portalen. När en lab kontoadministratör skapar labb-kontot kan administratören lägger till användare som vill skapa labb att den **Labbskaparen** roll. Av lärare skapa labb med virtuella datorer för studenter att göra övningarna för kursen de undervisa. Mer information finns i [skapa och hantera labbkonto](how-to-manage-lab-accounts.md).

## <a name="create-and-manage-classroom-labs"></a>Skapa och hantera labb för klassrum
En utbildare som är medlem i rollen Labbskaparen i ett labbkonto kan skapa en eller flera labs i labbkonto. Du skapar och konfigurerar en mall för virtuell dator med all nödvändig programvara för att göra övningarna i kursen. Du väljer en färdig avbildning från de tillgängliga avbildningarna för att skapa ett klassrumslabb och sedan anpassa den genom att installera den programvara som krävs för övningen. Mer information finns i [skapa och hantera labb för klassrum](how-to-manage-classroom-labs.md).

## <a name="configure-usage-settings-and-policies"></a>Konfigurera inställningar och principer
Labbskaparen kan lägga till eller ta bort användare i labbet, hämta registreringslänk ska skickas till labbanvändare, konfigurera principer som inställningen enskilda kvoter per användare, uppdatera antalet virtuella datorer i labbet och mycket mer. Mer information finns i [konfigurera inställningar och principer](how-to-configure-student-usage.md).

## <a name="create-and-manage-schedules"></a>Skapa och hantera scheman
Scheman kan du konfigurera ett klassrumslabb så att virtuella datorer i labbet automatiskt starta och stänga av en viss tid. Du kan definiera en engångsschema eller ett återkommande schema. Mer information finns i [skapa och hantera scheman för labb för klassrum](how-to-create-schedules.md).

## <a name="set-up-and-publish-a-template-vm"></a>Konfigurera och publicera en mall för virtuell dator
En mall i ett labb är en basavbildning av en virtuell dator som alla användares virtuella datorer skapas från. Konfigurera VM-mallen så att den är konfigurerad med exakt vad du vill ge till utbildning deltagare. Du kan ange ett namn och en beskrivning av mallen som visas för labbanvändarna. Sedan kan publicera du mallen för att tillgängliggöra instanser av VM-mallen för dina labbanvändare. När du publicerar en mall skapar Azure Lab Services virtuella datorer i labbet med hjälp av mallen. Antalet virtuella datorer som skapas i den här processen är samma som det högsta antalet användare som tillåts i labbet. Du kan definiera antalet i användningsprincipen för labbet. Alla virtuella datorer har samma konfiguration som mallen. Mer information finns i [ange upp och publicera en mall för virtuella datorer](how-to-create-manage-template.md). 

## <a name="use-vms-in-the-classroom-lab"></a>Använd virtuella datorer i labbet klassrum
En student eller utbildning deltagare registrerar till labbet och ansluter till den virtuella datorn ska göra övningarna för kursen. Mer information finns i [hur du kommer åt ett klassrumslabb](how-to-use-classroom-lab.md).

## <a name="next-steps"></a>Nästa steg
Börja med att skapa ett labbkonto i klassrum Labs genom att följa instruktionerna i artikeln: [Självstudier: Konfigurera ett labbkonto med Azure Lab Services](tutorial-setup-lab-account.md).