---
title: "Etablera en djup Learning datavetenskap virtuell dator på Azure | Microsoft Docs"
description: "Konfigurera och skapa en djup Learning datavetenskap virtuell dator i Azure för analys och maskininlärning."
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.workload: data-services
ms.devlang: na
ms.topic: article
ms.date: 09/10/2017
ms.author: gokuma;bradsev
ms.openlocfilehash: db1360fa54d82c50adc04194697d994925338296
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Etablera en djup Learning virtuell dator på Azure 

Djupgående Learning virtuell dator (DLVM) är en speciellt konfigurerade variant av den populära [datavetenskap virtuella](http://aka.ms/dsvm) (DSVM) för att göra det enklare att använda GPU-baserade Virtuella instanser för snabbt utbildning djup learning-modeller. Stöds med Windows 2016 eller Ubuntu DSVM som bas. DLVM delar samma core VM-avbildningar och därför omfattande verktygsuppsättningen som är tillgängligt på DSVM. 

DLVM innehåller flera verktyg för AI inklusive GPU-versionerna av populära djup learning ramverk som Microsoft kognitiva Toolkit, TensorFlow, Keras, Caffe2, klientprocess; Verktyg för att hämta och före processer bild, textinformation, verktyg för datavetenskap modellering och utveckling aktiviteter, till exempel Microsoft R Server Developer Edition, Anaconda Python Jupyter-anteckningsböcker för Python och R, IDEs för Python och R, SQL-databaser och många andra datavetenskap och ML verktyg. 

## <a name="create-your-deep-learning-virtual-machine"></a>Skapa din djup Learning virtuell dator
Här följer stegen för att skapa en instans av djup Learning virtuell dator: 

1. Navigera till den virtuella datorn visas i en lista på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Välj den **skapa** längst ned till tas med i en guide.![ Skapa dlvm](./media/dlvm-provision-wizard.PNG)
3. Guiden som används för att skapa DLVM kräver **indata** för var och en av de **fyra steg** räknas upp till höger på denna bild. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   1. **Grundläggande inställningar**
      
      1. **Namnet**: namnet på din server för vetenskap av data som du skapar.
      2. **Välj typ av operativsystem för djup Learning VM**: välja Windows eller Linux (för Windows 2016 och Ubuntu Linux grundläggande DSVM)
      2. **Användarnamnet**: Admin inloggnings-id för kontot.
      3. **Lösenordet**: Admin kontolösenord.
      4. **Prenumerationen**: Om du har mer än en prenumeration väljer du en som datorn ska skapas och debiteras.
      5. **Resursgruppen**: du kan skapa en ny eller Använd en **tom** befintliga Azure-resursgrupp i din prenumeration.
      6. **Plats**: Välj datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast den fysiska platsen för snabbaste nätverksåtkomst. 
      
> [!NOTE]
> Eftersom DLVM har etablerats på Azure NC-serien GPU VM-instanser måste du välja någon av platser i Azure som har GPU-kort. För närvarande platser som har GPU virtuella datorer är: **östra USA, norra centrala USA, södra centrala USA, västra USA 2, Nordeuropa, Västeuropa**. Den senaste listan Kontrollera den [Azure produkter efter Region sidan](https://azure.microsoft.com/en-us/regions/services/) och leta efter **NC-serien** under **Compute**. 

   2. **Inställningar för**: Välj en storlek för NC-serien GPU virtuell dator som uppfyller dina krav på funktionsnivå och kostnaden begränsningar. Skapa ett lagringskonto för den virtuella datorn.  ![dlvm-inställningar](./media/dlvm-provision-step-2.PNG)
   
   3. **Sammanfattning**: Kontrollera att all information du angett är korrekt.
   5. **Köpa**: Klicka på **köpa** att starta etableringen. En länk som villkoren i transaktionen. Den virtuella datorn inte har några ytterligare kostnader utöver beräkning för server-storlek som du har valt i den **storlek** steg. 

> [!NOTE]
> Etableringen bör ta ungefär 10 20 minuter. Status för etablering av visas på Azure-portalen.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Hur du kommer åt den djupgående Learning virtuella datorn

### <a name="windows-edition"></a>Windows-version
När den virtuella datorn har skapats kan du fjärrskrivbord till den med administratörsautentiseringsuppgifter för kontot som du konfigurerade i föregående **grunderna** avsnitt. 

### <a name="linux-edition"></a>Linux-version

När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använd de kontoautentiseringsuppgifter som du skapade i den **grunderna** avsnitt i steg 3 för gränssnittet text shell. På en startdiskett inifrån Windows-klient kan du ladda ned en SSH-klientverktyg som [Putty](http://www.putty.org). Du kan använda X11 vidarebefordran på Putty eller installera X2Go klienten om du föredrar en grafisk skrivbord (X Windows System).

> [!NOTE]
> Klienten X2Go presterade bättre än X11 vidarebefordran i våra tester. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installera och konfigurera X2Go klienten
Linux DLVM är redan etablerade med X2Go server och redo att acceptera anslutningar. Slutför följande procedur på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Hämta och installera klienten för din klientplattform från X2Go [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Kör klienten X2Go och välj **ny Session**. Öppnar ett fönster för konfiguration med flera flikar. Ange följande konfigurationsparametrar:
   * **Sessionen fliken**:
     * **Värden**: värdnamn eller IP-adressen för din Linux datavetenskap VM.
     * **Inloggningen**: användarnamn på Linux-VM.
     * **SSH-Port**: lämna på 22, standardvärdet.
     * **Sessionstyp**: ändra värdet till **XFCE**. Linux DSVM stöder för närvarande endast XFCE skrivbordet.
   * **Fliken Media**: du kan inaktivera stöd för ljud och skriva ut om du inte behöver använda dem-klienten.
   * **Delade mappar**: Om du vill kataloger från dina klientdatorer monteras på Linux VM, lägger du till klienten datorn kataloger som du vill dela med den virtuella datorn på den här fliken.

När du loggar in på den virtuella datorn med hjälp av SSH-klienten eller XFCE grafiska via X2Go klienten, är du redo att börja använda verktygen som installeras och konfigureras på den virtuella datorn. På XFCE, kan du se program menyn genvägar och ikoner på skrivbordet för många av verktyg.

När den virtuella datorn skapas och etableras, är du redo att börja använda verktygen som installeras och konfigureras på den. Det finns start-menyn paneler och ikoner på skrivbordet för många av verktyg. 
