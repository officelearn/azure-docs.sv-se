---
title: Installationsguide för labb i klassrummet för Azure Lab Services
description: Den här guiden hjälper labbskapare att snabbt skapa ett labbkonto för användning inom sin skola.
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
ms.date: 3/18/2020
ms.author: spelluru
ms.openlocfilehash: e00b6dd5af1cb489aee9e8b4c9f3337eb02e4b14
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80878110"
---
# <a name="classroom-lab-setup-guide"></a>Installationsguide för klassrumslabb

Processen för att publicera ett labb till dina elever kan ta upp till flera timmar, beroende på antalet virtuella datorer som kommer att skapas i labbet. Tillåt minst en dag att konfigurera ett labb, för att säkerställa att det fungerar korrekt och ge tillräckligt med tid för att publicera elevernas virtuella datorer.

## <a name="understand-the-lab-requirements-of-your-class"></a>Förstå labbkraven för din klass

Innan du konfigurerar ett nytt labb bör du överväga följande frågor.

### <a name="what-software-requirements-does-the-class-have"></a>Vilka programvarukrav har klassen?

Baserat på klassens inlärningsmål bestämmer du vilket operativsystem, program och verktyg som behöver installeras på labbets virtuella datorer. Om du vill konfigurera virtuella labb-datorer har du tre alternativ:

- **Använd en Azure Marketplace-avbildning:** Azure Marketplace innehåller hundratals avbildningar som du kan använda när du skapar ett labb. För vissa klasser kanske en av dessa bilder redan innehåller allt du behöver för din klass.

- **Skapa en ny anpassad avbildning:** Du kan skapa en egen anpassad avbildning genom att använda en Azure Marketplace-avbildning som utgångspunkt och anpassa den genom att installera ytterligare programvara och göra konfigurationsändringar.

- **Använd en befintlig anpassad avbildning:** Du kan återanvända befintliga anpassade bilder som du tidigare har skapat eller som har skapats av andra administratörer eller lärare på din skola. Detta kräver att administratörerna har konfigurerat ett delat bildgalleri, som är en databas för att spara anpassade bilder.

> [!NOTE]
> Administratörerna ansvarar för att aktivera Azure Marketplace-avbildningar och anpassade avbildningar så att du kan använda dem. Samordna med IT-avdelningen för att säkerställa att bilder som du behöver är aktiverade. Anpassade bilder som du skapar aktiveras automatiskt för användning i labb som du äger.

### <a name="what-hardware-requirements-does-the-class-have"></a>Vilka maskinvarukrav har klassen?

Det finns en mängd olika beräkningsstorlekar som du kan välja mellan:

- Kapslade virtualiseringsstorlekar, så att du kan ge åtkomst till deltagare till en virtuell dator som kan vara värd för flera kapslade virtuella datorer. Du kan till exempel använda den här beräkningsstorleken för nätverkskurser.

- GPU-storlekar, så att dina elever kan använda datorintensiva typer av program. Det här valet kan till exempel vara lämpligt för artificiell intelligens och maskininlärning.

Se guiden om [vm-storlek](https://docs.microsoft.com/azure/lab-services/classroom-labs/administrator-guide#vm-sizing) för att se hela listan över tillgängliga beräkningsstorlekar.

> [!NOTE]
> Beroende på regionen i ditt labb kan du se färre beräkningsstorlekar tillgängliga, eftersom detta varierar beroende på region. I allmänhet bör du välja den minsta beräkningsstorlek som är närmast dina behov. Med Azure Lab Services kan du konfigurera ett nytt labb med en annan beräkningskapacitet senare, om det behövs.

### <a name="what-dependencies-does-the-class-have-on-external-azure-or-network-resources"></a>Vilka beroenden har klassen på externa Azure- eller nätverksresurser?

Om dina virtuella labbresurser behöver använda externa resurser, till exempel en databas, filresurs eller licensieringsserver, samordnar du med administratörerna för att säkerställa att labbet har åtkomst till dessa resurser.

För åtkomst till Azure-resurser som *inte* skyddas av ett virtuellt nätverk behöver du inte söka ytterligare konfiguration av administratörerna. Du kan komma åt dessa resurser via det offentliga internet.

> [!NOTE]
> Du bör överväga om du kan minska labbets beroenden till externa resurser genom att tillhandahålla resursen direkt på den virtuella datorn. Om du till exempel vill eliminera behovet av att läsa data från en extern databas kan du installera databasen direkt på den virtuella datorn.  

### <a name="how-will-costs-be-controlled"></a>Hur kommer kostnaderna att kontrolleras?

Lab Services använder en prismodell för användningsbaserad betalning, vilket innebär att du bara betalar för den tid som en virtuell labbdator körs. Du kan kontrollera kostnaderna genom att tre alternativ som vanligtvis används tillsammans med varandra:

- **Schema:** Ett schema kan du automatiskt styra när dina labs virtuella datorer startas och stängs av.
- **Kvot**: Kvoten styr antalet timmar som deltagarna har åtkomst till en virtuell dator utanför de schemalagda timmarna. Om kvoten nås medan en deltagare använder den stängs den virtuella datorn automatiskt av. Deltagaren kan inte starta om den virtuella datorn om inte kvoten ökas.
- **Automatisk avstängning**: När den är aktiverad gör inställningen för automatisk avstängning att Windows virtuella datorer stängs av automatiskt efter en viss tid, efter att en elev har kopplat från en RDP-session (Remote Desktop Protocol). Som standard är denna inställning inaktiverad.  

    > [!NOTE]
    > Den här inställningen finns för närvarande bara för Windows.

### <a name="how-will-students-save-their-work"></a>Hur ska eleverna spara sitt arbete?

Deltagarna tilldelas var sin egen virtuella dator, som tilldelas dem under labbets livstid. De kan välja att:

- Spara direkt till den virtuella datorn.
- Spara på en extern plats, till exempel OneDrive eller GitHub.

Det är möjligt att konfigurera OneDrive automatiskt för studenter på sina virtuella labbdatorer.

> [!NOTE]
> För att säkerställa att dina elever har fortsatt åtkomst till sitt sparade arbete utanför labbet, och när klassen är, rekommenderar vi att eleverna sparar sitt arbete i en extern databas.

### <a name="how-will-students-connect-to-their-vm"></a>Hur kommer eleverna att ansluta till sin virtuella dator?

För RDP till Windows virtuella datorer rekommenderar vi att eleverna använder [Microsoft Remote Desktop-klienten](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/clients/remote-desktop-clients). Klienten för fjärrskrivbord stöder Mac-datorer, Chromebook och Windows.

För virtuella Linux-datorer kan eleverna använda antingen SSH eller RDP. Om du vill att deltagarna ska ansluta med RDP måste du installera och konfigurera nödvändiga RDP- och GUI-paket.

## <a name="set-up-your-lab"></a>Skapa ett labb

När du har förstått kraven för klassens labb är du redo att konfigurera det. Följ länkarna i det här avsnittet om du vill se hur du konfigurerar labbet.

1. **Skapa ett labb.** Se handledningen om [hur du skapar ett klassrumslabb](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#create-a-classroom-lab) för instruktioner.

    > [!NOTE]
    > Om klassen kräver kapslad virtualisering läser du stegen för [att aktivera kapslad virtualisering](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-nested-virtualization-template-vm).

1. **Anpassa bilder och publicera virtuella labb-datorer.** Anslut till en särskild virtuell dator som kallas mallen VM. Se stegen i följande guider:
    - [Skapa och hantera en virtuell mall](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#publish-the-template-vm)
    - [Använda ett delat bildgalleri](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-use-shared-image-gallery)

    > [!NOTE]
    > Om du använder Windows bör du också se instruktionerna för [att förbereda en virtuell Windows-mall](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-prepare-windows-template). De här anvisningarna innehåller steg för att konfigurera OneDrive och Office som dina elever kan använda.

1. **Hantera VM-pool och kapacitet.** Du kan enkelt skala upp eller ned vm-kapacitet, efter behov av din klass. Tänk på att det kan ta flera timmar att öka den virtuella datorns kapacitet, eftersom det innebär att du ställer in nya virtuella datorer. Se stegen för [att konfigurera och hantera en VM-pool](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-set-virtual-machine-passwords).

1. **Lägg till och hantera labbanvändare.** Information om hur du lägger till användare i labbet finns i följande självstudier:
   - [Lägga till användare i labbet](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#add-users-to-the-lab)
   - [Skicka inbjudningar till användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#send-invitation-emails-to-users)

    Information om vilka typer av konton som deltagarna kan använda finns i [Studentkonton](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#student-accounts).
  
1. **Ställ in kostnadskontroller.** Om du vill styra kostnaderna för ditt labb anger du scheman, kvoter och automatisk avstängning. Se följande självstudiekurser:

   - [Ange ett schema](https://docs.microsoft.com/azure/lab-services/classroom-labs/tutorial-setup-classroom-lab#set-a-schedule-for-the-lab)
        > [!NOTE]
        > Beroende på vilken typ av operativsystem du har installerat kan det ta flera minuter att starta en virtuell dator. För att säkerställa att en virtuell labbdator är klar för användning under dina schemalagda timmar rekommenderar vi att du startar virtuella datorer 30 minuter i förväg.

   - [Ange kvoter för användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-quotas-for-users) och [ange ytterligare kvoter för en viss användare](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-configure-student-usage#set-additional-quotas-for-specific-users)
  
   - [Aktivera automatisk avstängning vid frånkoppling](https://docs.microsoft.com/azure/lab-services/classroom-labs/how-to-enable-shutdown-disconnect)

        > [!NOTE]
        > Scheman, kvoter och automatisk avstängning gäller inte för mallen VM. Därför måste du se till att du stänger av mallen VM när den inte används. Annars fortsätter det att medföra kostnader. Som standard startas mallen VM som standard automatiskt när du skapar ett labb. Se till att du omedelbart slutför inrättandet av labbet och stäng av mallen VM.

1. **Använd instrumentpanelen.** Instruktioner finns [i hur du använder labbets instrumentpanel](https://docs.microsoft.com/azure/lab-services/classroom-labs/use-dashboard).

    > [!NOTE]
    > Den uppskattade kostnaden som visas i instrumentpanelen är den maximala kostnaden som du kan förvänta dig för studenter användning av labbet. Du debiteras till exempel *inte* för oanvända kvottimmar av dina deltagare. De uppskattade kostnaderna återspeglar *inte* några avgifter för att använda mallen VM eller det delade bildgalleriet.

## <a name="next-steps"></a>Nästa steg

- [Spåra användning av ett klassrumslabb](tutorial-track-usage.md)
  
- [Åtkomst till ett klassrumslabb](tutorial-connect-virtual-machine-classroom-lab.md)