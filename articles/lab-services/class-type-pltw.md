---
title: Konfigurera projekt Skapa på ett sätt labb med Azure Lab Services
description: Lär dig hur du konfigurerar labb för att lära projektet att leda till att klasser.
ms.topic: article
ms.date: 10/28/2020
ms.openlocfilehash: e3783ae4fa07bf783841022903c4bcf3ab6fbe23
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2020
ms.locfileid: "94648012"
---
# <a name="set-up-labs-for-project-lead-the-way-classes"></a>Konfigurera labb för projekt som leder till att klasser

[Projektet leder dig (PLTW)](https://www.pltw.org/) är en ideell organisation som tillhandahåller PreK-12-kurs hantering i den USA i dator vetenskap, teknik och biomedicinsk vetenskap.  I varje PLTW-klass använder studenter en mängd olika program vara som en del av deras praktiska inlärnings upplevelser.  Många av programmen kräver en snabb processor eller i vissa fall en GPU.  Den här artikeln visar hur du konfigurerar labb för följande PLTW-klasser som vanligt vis erbjuds studenter i betyg 9-12:

- **Introduktion till teknisk design**

    Studenter introduceras i processen för konstruktions design, vilket innefattar att använda Autodesks inventering av program vara från en CAD-programvara [(Computer-Aided Design)](https://www.autodesk.com/products/inventor/new-features) för 3D-modellering.

- **Principer för teknik**
    
    Studenter lär sig mer om tekniska mekanismer, structural\materials styrka och automatisering.  Den här klassen använder program vara som [MD-solider](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/MD+Solids/MD+Solids+Software+Installation+Guide.pdf), [västra Point Bridge designer](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/West+Point+Bridge+Builder/Installation+Guide+for+West+Point+Bridge+Designer.pdf)och [America Army-simulering](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/America's+Army/Installation+Guide+for+Americas+Army+Simulation+17-18.pdf).

- **Byggnads teknik och arkitektur**

    Eleverna är inbygga på att skapa och utforma och utveckla och utveckla med [Autodesks Revit](https://www.autodesk.com/products/revit/overview) arkitektur design program för BIM (3D Building Information Modeling).

- **Dator integrerad tillverkning**

    Eleverna utforskar moderna tillverknings processer som omfattar Robotics och automatisering.   I den här klassen använder eleverna [Autodesks lager CAD](https://www.autodesk.com/products/inventor/new-features) och [Autodesk Inventory Inventory Computer-Aided Manufacturing (Cam)](https://www.autodesk.com/products/inventor-cam/overview) . 

- **Digital elektronik**

    Studenter kan studera elektroniska logik kretsar och enheter med hjälp av [National Instruments Multisim](https://www.ni.com/en-us/shop/electronic-test-instrumentation/application-software-for-electronic-test-and-instrumentation-category/what-is-multisim.html) -simulering och krets design program.

- **Konstruktion och utveckling av teknik**

    Studenter bidrar till en komplett lösning som kombinerar forskning, utformning och testning som finns på en panel med tekniker.  I den här klassen använder eleverna [Autodesks lager CAD](https://www.autodesk.com/products/inventor/new-features) -programvara.

- **Viktig information för dator vetenskap**

    Studenter introduceras för beräknings koncept och verktyg.  De startar med block-baserad programmering och flyttar sedan till Använd textbaserade kodning med hjälp av kodnings miljöer som [VEXcode V5-block](https://s3.amazonaws.com/support-downloads.pltw.org/2020-21/VEXcode+V5+Blocks/VexCode+V5+Blocks+Installation+Guide.pdf).

- **Principer för dator vetenskap**
    
    Eleverna kan utveckla sina kunskaper om programmering med [python](https://www.python.org/) med hjälp av [Microsofts kod utvecklings miljö i Visual Studio](https://code.visualstudio.com/). 

- **Dator vetenskap A**

    Studenter ökar sin programmerings expertis i den här klassen genom att lära oss utveckla mobilappar.  I den här klassen får de lära sig [Java](https://www.java.com/) med [Microsofts kod utvecklings miljö i Visual Studio](https://code.visualstudio.com/).  Studenter använder också en emulator som gör det möjligt för dem att köra och testa sin kod för mobilapp.  Om du vill ha mer information om hur du konfigurerar en emulator i Azure Labs kan du kontakta oss på azlabspilot@microsoft.com .

Se PLTW-webbplatsen för en [fullständig lista över program vara](https://www.pltw.org/pltw-software) för varje klass.

## <a name="lab-configuration"></a>Labb konfiguration
För att kunna konfigurera labb för PLTW behöver du ett Azure-prenumerations-och labb konto för att komma igång. Om du inte har någon Azure-prenumeration kan du [skapa ett kostnadsfritt konto](https://azure.microsoft.com/free/) innan du börjar. När du har skaffat en Azure-prenumeration kan du skapa ett nytt labb konto i Azure Lab Services. Mer information om hur du skapar ett nytt labb konto finns i självstudien om [hur du ställer in ett labb konto](./tutorial-setup-lab-account.md). Du kan också använda ett befintligt labb konto.

När du har ett labb konto bör du skapa separata labb för varje session av en PLTW-klass som din skola erbjuder.  Vi rekommenderar också att du skapar separata avbildningar för varje typ av PLTW-klass.  Läs blogg inlägget om du vill ha mer information om hur du strukturerar dina labb och bilder: [flytta från ett fysiskt labb till Azure Lab Services](https://techcommunity.microsoft.com/t5/azure-lab-services/moving-from-a-physical-lab-to-azure-lab-services/ba-p/1654931).

### <a name="lab-account-settings"></a>Labb konto inställningar
Aktivera inställningarna som beskrivs i tabellen nedan för labb kontot. Mer information om hur du aktiverar Marketplace-avbildningar finns i artikeln om [hur du anger Marketplace-avbildningar som är tillgängliga för labb skapare](./specify-marketplace-images.md).

| Inställning för labb konto | Instruktioner |
| -------------------- | ----- |
| Marketplace-avbildning | Aktivera Windows 10 Pro-avbildningen för användning i ditt labb konto. |

### <a name="lab-settings"></a>Labb inställningar
Storleken på den virtuella dator som vi rekommenderar att du använder för PLTW-klasser beror på vilka typer av arbets belastningar som dina studenter gör i klassen.  För ovanstående klasser rekommenderar vi att du använder stora och små GPU (visualisering) VM-storlekar.  Läs anvisningarna i tabellen nedan när du konfigurerar labb för dina PLTW-klasser.

| Labb inställning | Värde/anvisningar |
| ------------ | ------------------ |
|Storlek på virtuell dator| **Liten GPU (visualisering)**.  Den här virtuella datorn passar bäst för fjärrvisualisering, strömning, spel, kodning med hjälp av ramverk som OpenGL och DirectX.  Vi rekommenderar att du använder den här storleken för följande PLTW-klasser: byggnads konstruktion och arkitektur, digital elektronik, dator integrerad tillverkning. och teknisk utformning och utveckling.
|Storlek på virtuell dator| **Stor**.  Den här storleken lämpar sig bäst för program som behöver snabbare processorer, bättre prestanda för lokala diskar, stora databaser, stora cacheminnen.  Vi rekommenderar att du använder den här storleken för följande PLTW-klasser: Introduktion till teknisk design, principer för teknik, viktiga data för dator vetenskap, principer för dator vetenskap och dator vetenskap A.

### <a name="licensing-server"></a>Licensierings Server
Merparten av program varan som används i ovanstående PLTW-klasser är **_inte_* _ Kräv åtkomst till en licens Server.  Du måste dock ha åtkomst till en licensierings server om du planerar att använda Autodesks nätverks licensierings modell för följande program vara:
-   Revit
-   Hållning
-   Inventerings kamera

För att kunna använda nätverks licensiering med Autodesks program vara, [innehåller PLTW detaljerade anvisningar](https://www.pltw.org/pltw-software) för att installera Autodesks licens hanterare på din licensierings Server.  Den här licensierings servern finns vanligt vis antingen i ditt lokala nätverk eller på en virtuell Azure-dator (VM) i Azure Virtual Network (VNet).

När licens servern har kon figurer ATS måste du koppla [VNet](./how-to-connect-peer-virtual-network.md) till ditt [labb konto](./tutorial-setup-lab-account.md). Nätverks-peering måste vara klar _before * skapa labbet så att virtuella labb datorer kan komma åt licens servern och det andra sättet.

Autodesks genererade licensfiler bäddar in MAC-adressen för licens servern.  Om du väljer att vara värd för licensierings servern med hjälp av en virtuell Azure-dator är det viktigt att se till att licens serverns MAC-adress inte ändras.   Annars måste dina licensfiler återskapas när MAC-adressen ändras.  Följ de här tipsen för att förhindra att MAC-adressen ändras:

- [Ange en statisk privat IP-adress och Mac-adress](./how-to-create-a-lab-with-shared-resource.md#static-private-ip-and-mac-address) för den virtuella Azure-dator som är värd för din licensierings Server.
- Se till att du konfigurerar både ditt labb konto och licensierings serverns VNet i en region\location som har tillräcklig VM-kapacitet så att du inte behöver flytta resurserna till en ny region\location senare.

Läs även artikeln om [hur du konfigurerar en licensierings server som en delad resurs](./how-to-create-a-lab-with-shared-resource.md) för mer information.

### <a name="template-machine"></a>Mall dator
Några av de installationsfiler som du behöver för PLTW är stora och tar lång tid att kopiera när du laddar ned dem till Labbets mall.

I stället för att ladda ned installationsfilerna till mallen och installera allt där, rekommenderar vi att du skapar dina PLTW-avbildningar i din fysiska miljö.  Sedan kan du importera bilderna till det delade bild galleriet så att du kan använda dessa anpassade avbildningar för att skapa dina labb.  Läs följande artikel om du vill ha mer information: [Ladda upp en anpassad avbildning till den delade avbildnings galleriet](./upload-custom-image-shared-image-gallery.md).

Följande rekommendationer är viktiga för att konfigurera ett labb:

1. Skapa avbildningen för klassen i den fysiska miljön.

    a.  Använd PLTW: s detaljerade steg för att ladda ned installationsfiler och installera nödvändig program vara.

    > [!NOTE]    
    > När du installerar Autodesks program måste datorn som du installerar Autodesk på kunna kommunicera med licens servern (Autodesks installations guide uppmanas att ange dator namnet för den dator som licens servern finns på).  Om du är värd för licensierings servern på en virtuell Azure-dator kan du behöva vänta på att installera Autodesk på Labbets mall, så att Autodesks installations guide kan komma åt din licensierings Server

    b.  [Installera och konfigurera OneDrive](./how-to-prepare-windows-template.md#install-and-configure-onedrive) (eller andra säkerhets kopierings alternativ som din skola kan använda).
    
    c.  [Installera och konfigurera Windows-uppdateringar](./how-to-prepare-windows-template.md#install-and-configure-updates).

1.  Överför den anpassade avbildningen till det [delade avbildnings galleriet som är kopplat till ditt labb konto](./how-to-attach-detach-shared-image-gallery.md).

1.  Skapa ett labb och välj den anpassade avbildning som du laddade upp i föregående steg.

1.  När labbet har skapats startar du och ansluter till mallen för att verifiera att avbildningen fungerar som förväntat.

1.  Publicera sedan mallen Machine för att skapa studenternas virtuella datorer.

## <a name="student-devices"></a>Elev enheter
Dina studenter kan ansluta till sina virtuella labb datorer från Windows\Mac-datorer och Chromebooks.  Här följer länkar till anvisningar för var och en av dessa alternativ:

- [Anslut från Windows](./how-to-use-classroom-lab.md#connect-to-the-vm)
- [Anslut från Mac](./connect-virtual-machine-mac-remote-desktop.md)
- [Anslut från Chromebook](./connect-virtual-machine-chromebook-remote-desktop.md)

## <a name="cost"></a>Cost (Kostnad)
Vi ska se en möjlig kostnads uppskattning för ovanstående PLTW-klasser.  Den här uppskattningen omfattar inte kostnaden för att köra en licens Server eller för att använda delade avbildnings galleriet.  Vi använder en klass av 25 studenter.  Det finns 20 timmar med den schemalagda klass tiden.  Dessutom får varje student en kvot på 10 timmar för läxor eller tilldelningar utanför den schemalagda klass tiden.  Läs följande kostnads uppskattningar för både **stor** och **liten GPU-storlek (visualisering)** .

- **Stor virtuell dator**

    25 studenter x (20 schemalagda timmar + 10 kvot timmar) x 70 labb enheter x 0,01 USD per timme = 525,00 USD

- **Liten GPU (visualisering)**

    25 studenter x (20 schemalagda timmar + 10 kvot timmar) x 160 labb enheter x 0,01 USD per timme = 1200,00 USD

> [!IMPORTANT] 
> Kostnads beräkningen är endast till exempel syfte.  Aktuell information om priser finns i [Azure Lab Services priser](https://azure.microsoft.com/pricing/details/lab-services/).

> [!NOTE] 
> Många av PLTW-klasserna använder program som nås via en webbläsare, till exempel MIT app Inventory.  Dessa webbläsarbaserade program kräver ingen snabb CPU eller GPU och kan nås från alla enheter som har en Internet anslutning.  När eleverna använder dessa typer av program rekommenderar vi att de använder webbläsaren på sina fysiska enheter i stället för att använda webbläsaren på sina virtuella labb datorer.  Detta hjälper till att hålla kostnaderna nere genom att bara använda virtuella labb datorer för program som kräver en snabb processor eller GPU.

## <a name="next-steps"></a>Nästa steg
Nästa steg är vanliga för att ställa in labb:

- [Lägg till användare](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ange kvot](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ange ett schema](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab) 
- [E-postregistrering länkar till studenter](how-to-configure-student-usage.md#send-invitations-to-users).