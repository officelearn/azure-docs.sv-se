---
title: Påskyndad installations guide för klass rummet för Azure Lab Services
description: Den här guiden hjälper labb skapare att snabbt konfigurera ett labb konto för användning i sin skola.
services: lab-services
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/19/2020
ms.author: spelluru
ms.openlocfilehash: 5c2ea38dfa799239adde158506e1359fb84adcc4
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/19/2020
ms.locfileid: "83650531"
---
# <a name="classroom-lab-setup-guide"></a>Installations guide för klass rums labb

Processen för att publicera ett labb till dina studenter kan ta upp till flera timmar, beroende på hur många virtuella datorer som kommer att skapas i labbet. Låt minst en dag konfigurera ett labb för att se till att det fungerar som det ska och för att tillåta tillräckligt med tid för att publicera studenters virtuella datorer.

## <a name="understand-the-lab-requirements-of-your-class"></a>Förstå labb kraven för klassen

Innan du skapar ett nytt labb bör du tänka på följande frågor.

### <a name="what-software-requirements-does-the-class-have"></a>Vilka program varu krav har klassen?

Utifrån klassens utbildnings mål bestämmer du vilka operativ system, program och verktyg som måste installeras på Labbets virtuella datorer. Om du vill konfigurera virtuella labb datorer har du tre alternativ:

- **Använd en Azure Marketplace-avbildning**: Azure Marketplace innehåller hundratals avbildningar som du kan använda när du skapar ett labb. För vissa klasser kan en av dessa avbildningar redan innehålla allt som du behöver för klassen.

- **Skapa en ny anpassad avbildning**: du kan skapa en egen anpassad avbildning genom att använda en Azure Marketplace-avbildning som en start punkt och anpassa den genom att installera ytterligare program vara och göra konfigurations ändringar.

- **Använd en befintlig anpassad avbildning**: du kan återanvända befintliga anpassade avbildningar som du skapat tidigare eller som har skapats av andra administratörer eller lärare i skolan. Detta kräver att dina administratörer har konfigurerat ett delat avbildnings Galleri, som är en lagrings plats för att spara anpassade avbildningar.

> [!NOTE]
> Dina administratörer ansvarar för att aktivera Azure Marketplace-avbildningar och anpassade avbildningar så att du kan använda dem. Koordinera med IT-avdelningen och se till att de bilder du behöver är aktiverade. Anpassade avbildningar som du skapar aktive ras automatiskt för användning inom labb som du äger.

### <a name="what-hardware-requirements-does-the-class-have"></a>Vilka maskin varu krav har klassen?

Det finns en mängd olika beräknings storlekar som du kan välja mellan:

- Kapslade virtualiseringslösningar så att du kan ge till gång till studenter till en virtuell dator som kan vara värd för flera, kapslade virtuella datorer. Du kan till exempel använda den här beräknings storleken för nätverks kurser.

- GPU-storlekar så att eleverna kan använda dator intensiva typer av program. Det här alternativet kan till exempel vara lämpligt för artificiell intelligens och Machine Learning.

Läs guiden om storlek på [virtuell dator](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) för att se en fullständig lista över tillgängliga beräknings storlekar.

> [!NOTE]
> Beroende på din labbs region kan du se färre beräknings storlekar som är tillgängliga, eftersom detta varierar beroende på region. I allmänhet bör du välja den minsta beräknings storlek som är närmast dina behov. Med Azure Lab Services kan du konfigurera ett nytt labb med en annan beräknings kapacitet senare, om det behövs.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Vilka beroenden har klassen på externa Azure-eller nätverks resurser?

Om dina virtuella labb datorer behöver använda externa resurser, till exempel en databas, fil resurs eller licensierings Server, koordinerar du med dina administratörer för att se till att ditt labb har åtkomst till dessa resurser.

För åtkomst till Azure-resurser som *inte* är säkrade av ett virtuellt nätverk behöver du inte söka ytterligare konfiguration av dina administratörer. Du kan komma åt dessa resurser via det offentliga Internet.

> [!NOTE]
> Du bör fundera över om du kan minska dina labb beroenden till externa resurser genom att tillhandahålla resursen direkt på den virtuella datorn. För att till exempel eliminera behovet av att läsa data från en extern databas kan du installera databasen direkt på den virtuella datorn.  

### <a name="how-will-costs-be-controlled"></a>Hur kommer kostnaderna att kontrol leras?

Labb tjänster använder en pris modell enligt principen betala per användning, vilket innebär att du bara betalar för den tid som en virtuell labb dator körs. För att kontrol lera kostnaderna har du tre alternativ som vanligt vis används tillsammans med varandra:

- **Schema**: ett schema gör att du automatiskt kan styra när dina labbs virtuella datorer startas och stängs av.
- **Kvot**: kvoten styr antalet timmar som eleverna får åtkomst till en virtuell dator utanför de schemalagda timmarna. Om kvoten uppnås när en student använder den, stängs den virtuella datorn automatiskt av. Studenten kan inte starta om den virtuella datorn om inte kvoten höjs.
- **Automatisk avstängning**: när den här inställningen är aktive rad gör inställningen för automatisk avstängning att virtuella Windows-datorer stängs av automatiskt efter en viss tids period, efter att en student har kopplats från en Remote Desktop Protocol-session (RDP). Som standard är denna inställning inaktiverad.  

    > [!NOTE]
    > Den här inställningen finns för närvarande bara för Windows.

### <a name="how-will-students-save-their-work"></a>Hur kommer eleverna att spara sitt arbete?

Eleverna tilldelas varje egen virtuell dator, som tilldelas dem under Labbets livs längd. De kan välja att:

- Spara direkt till den virtuella datorn.
- Spara till en extern plats, till exempel OneDrive eller GitHub.

Det är möjligt att konfigurera OneDrive automatiskt för studenter på sina virtuella labb datorer.

> [!NOTE]
> För att säkerställa att dina studenter har fortsatt åtkomst till sitt sparade arbete utanför labbet, och när klassen slutar, rekommenderar vi att eleverna sparar sitt arbete på en extern lagrings plats.

### <a name="how-will-students-connect-to-their-vm"></a>Hur ansluter eleverna till sin virtuella dator?

För RDP till virtuella Windows-datorer rekommenderar vi att eleverna använder [Microsoft fjärrskrivbord-klienten](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Fjärr skrivbords klienten stöder Mac, Chromebooks och Windows.

Studenter kan använda antingen SSH eller RDP för virtuella Linux-datorer. Om du vill att eleverna ska ansluta via RDP måste du installera och konfigurera de RDP-och GUI-paket som krävs.

## <a name="set-up-your-lab"></a>Skapa ett labb

När du har förstått kraven för klassens labb är du redo att konfigurera den. Följ länkarna i det här avsnittet för att se hur du konfigurerar labbet.

1. **Skapa ett labb.** Mer information hittar du i självstudien om hur du [skapar ett klass rums labb](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) .

    > [!NOTE]
    > Om din klass kräver kapslad virtualisering, se stegen i [Aktivera kapslad virtualisering](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Anpassa avbildningar och publicera virtuella labb datorer.** Anslut till en särskild virtuell dator som kallas för mallen VM. Se stegen i följande guider:
    - [Skapa och hantera en mall för virtuella datorer](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Använda ett delat bildgalleri](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Om du använder Windows bör du även se anvisningarna i [förbereda en virtuell Windows-mall](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). De här anvisningarna innehåller steg för att konfigurera OneDrive och Office för dina studenter att använda.

1. **Hantera VM-pool och kapacitet.** Du kan enkelt skala upp eller ned VM-kapaciteten efter behov av klassen. Tänk på att det kan ta flera timmar att öka VM-kapaciteten, eftersom det innebär att skapa nya virtuella datorer. Se stegen i [Konfigurera och hantera en VM-pool](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Lägg till och hantera labb användare.** Information om hur du lägger till användare i labbet finns i följande Självstudier:
   - [Lägga till användare i labbet](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Skicka inbjudningar till användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Information om vilka typer av konton som eleverna kan använda finns i [elev konton](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Ange kostnads kontroller.** För att kontrol lera kostnaderna för ditt labb, ange scheman, kvoter och automatisk avstängning. Se följande självstudiekurser:

   - [Ange ett schema](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Det kan ta flera minuter att starta en virtuell dator beroende på vilken typ av operativ system du har installerat. För att säkerställa att en virtuell labb dator är redo att användas under dina schemalagda timmar rekommenderar vi att du startar virtuella datorer 30 minuter i förväg.

   - [Ange kvoter för användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) och [Ange ytterligare kvot för en speciell användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Aktivera automatisk avstängning vid frånkoppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Scheman och kvoter gäller inte för mallen VM, men inställningarna för automatisk avstängning gäller. 
        > 
        > När du skapar ett labb skapas mallen VM, men den är inte igång. Du kan starta den, ansluta till den och installera all nödvändig program vara för labbet och sedan publicera den. När du publicerar mallen VM stängs den automatiskt av för dig om du inte har gjort det. 
        > 
        > Mall VM: ar **kostar** att köra, så se till att den virtuella datorns mall stängs av när du inte behöver den för att köras. 


1. **Använd instrument panelen.** Anvisningar finns i [använda Labbets instrument panel](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Den uppskattade kostnaden som visas på instrument panelen är den högsta kostnad som du kan förväntar dig för studenters användning av labbet. Till exempel debiteras du *inte* för oanvända kvot timmar av dina studenter. De uppskattade kostnaderna återspeglar *inte* några avgifter för att använda mallen VM eller det delade avbildnings galleriet.

## <a name="next-steps"></a>Nästa steg

- [Spåra användning av ett klassrumslabb](tutorial-track-usage.md)
  
- [Åtkomst till ett klassrumslabb](tutorial-connect-virtual-machine-classroom-lab.md)