---
title: Skapa en virtuell dator för datavetenskap för Djupinlärning
titleSuffix: Azure
description: Konfigurera och skapa en Deep Learning Data Science Virtual Machine på Azure för analys och maskininlärning.
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: e1467c0f-497b-48f7-96a0-7f806a7bec0b
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.date: 03/16/2018
ms.author: gokuma
ms.openlocfilehash: 53ddea5426d2adfa7b0ddfcbda3375efae8d0859
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/30/2019
ms.locfileid: "55250814"
---
# <a name="provision-a-deep-learning-virtual-machine-on-azure"></a>Etablera en virtuell dator på Azure för Djupinlärning 

Deep Learning Virtual Machine (DLVM) är en speciellt konfigurerad variant av populära [Data Science Virtual Machine](https://aka.ms/dsvm) (DSVM) för att göra det enklare att använda GPU-baserad VM-instanser för att snabbt träna modeller för djup maskininlärning. Den stöds med Windows 2016 eller Ubuntu DSVM som bas. DLVM delar samma grundläggande VM-avbildningarna och därför alla omfattande uppsättning verktyg som är tillgänglig på DSVM. 

DLVM innehåller flera verktyg för AI inklusive GPU-versionerna av populära deep learning-ramverk som Microsoft Cognitive Toolkit, TensorFlow, Keras, Caffe2, Chainer; Verktyg för att hämta och förprocess bild, text, verktyg för datavetenskap modellering och utveckling, till exempel Microsoft R Server Developer Edition, Anaconda Python, Jupyter-anteckningsböcker för Python och R IDE: er för Python och R, SQL-databaser och många andra datavetenskap och ML-verktyg. 

## <a name="create-your-deep-learning-virtual-machine"></a>Skapa virtuell dator för Djupinlärning
Här följer stegen för att skapa en instans av Deep Learning Virtual Machine: 

1. Navigera till den virtuella datorn på [Azure-portalen](https://portal.azure.com/#create/microsoft-ads.dsvm-deep-learningtoolkit
).
2. Välj den **skapa** längst ned för att gå till en guide.![ Skapa dlvm](./media/dlvm-provision-wizard.PNG)
3. I guiden som används för att skapa DLVM kräver **indata** för var och en av de **fyra steg** räknas upp till höger i den här bilden. Här följer de indata som behövs för att konfigurera var och en av de här stegen:
   
   1. **Grundläggande inställningar**
      
      1. **Namn**: Namnet på din data science-server som du skapar.
      2. **Välj OS-typ för den Djupinlärning**: Välj Windows eller Linux (för Windows 2016 och Ubuntu Linux grundläggande DSVM)
      2. **Användarnamn**: Admin-konto inloggnings-id.
      3. **Lösenord**: Lösenord för administratörskonto.
      4. **Prenumeration**: Om du har mer än en prenumeration väljer du det som datorn är skapas och faktureras.
      5. **Resursgrupp**: Du kan skapa en ny eller Använd en **tom** befintliga Azure-resursgrupp i din prenumeration.
      6. **Plats**: Välj det datacenter som är mest lämplig. Vanligtvis är det datacenter som har de flesta av dina data eller som är närmast dina fysiska platsen för snabbaste nätverksåtkomst. 
      
> [!NOTE]
> DLVM stöder alla NC- och ND-serien GPU VM-instanser. När du etablerar DLVM, måste du välja någon av platser i Azure med GPU: er. Kontrollera den [Azure-produkter per Region sidan](https://azure.microsoft.com/regions/services/) för de tillgängliga platserna och leta efter **NC-serien**, **NCv2-serien**, **NCv3-serien** , eller **ND-serien** under **Compute**. 

   2. **Inställningar för**: Välj en av NC-serien (NC, NCv2, NCv3) eller ND-serien GPU-storlekar för virtuella datorer som uppfyller dina funktionella krav och begränsningar för kostnad. Skapa ett lagringskonto för den virtuella datorn.  ![dlvm-inställningar](./media/dlvm-provision-step-2.PNG)
   
   3. **Sammanfattning av**: Kontrollera att all information du angett är korrekt.
   5. **Köpa**: Klicka på **köpa** att starta etableringen. Det finns en länk till villkoren för transaktionen. Den virtuella datorn har inte några ytterligare avgifter utöver beräkning för serverstorlek som du valde i den **storlek** steg. 

> [!NOTE]
> Etableringen tar cirka 10-20 minuter. Status för etableringen visas på Azure portal.
> 


## <a name="how-to-access-the-deep-learning-virtual-machine"></a>Hur du kommer åt Deep Learning Virtual Machine

### <a name="windows-edition"></a>Windows-version
När den virtuella datorn har skapats kan du fjärrskrivbord till den med hjälp av autentiseringsuppgifter som administratör-konto som du konfigurerade i föregående **grunderna** avsnittet. 

### <a name="linux-edition"></a>Linux-version

När den virtuella datorn har skapats kan logga du in till den med hjälp av SSH. Använda de autentiseringsuppgifter som du skapade i den **grunderna** avsnittet i steg 3 för text shell-gränssnittet. På en startdiskett inifrån Windows-klienten, kan du ladda ned ett SSH-klientverktyg som [Putty](http://www.putty.org). Du kan använda X11 vidarebefordran på Putty eller installera X2Go klienten om du föredrar en grafisk desktop (X Windows System).

> [!NOTE]
> Klienten X2Go presterade bättre än X11 vidarebefordran i våra tester. Vi rekommenderar att du använder X2Go-klienten för ett grafiskt gränssnitt för fjärrskrivbord.
> 
> 

#### <a name="installing-and-configuring-x2go-client"></a>Installera och konfigurera X2Go klienten
Linux DLVM är redan upprättad med X2Go server och redo att ta emot klientanslutningar. Slutför följande procedur på klienten för att ansluta till Linux VM grafiska skrivbordet:

1. Ladda ned och installera klienten för din klientplattform från X2Go [X2Go](http://wiki.x2go.org/doku.php/doc:installation:x2goclient).    
2. Kör X2Go-klienten och välj **ny Session**. Det öppnar en konfiguration med flera flikar. Ange följande konfigurationsparametrar:
   * **Sessionen fliken**:
     * **Värden**: Värdnamn eller IP-adressen för din Linux virtuell dator för datavetenskap.
     * **logga in**: Användarnamn på Linux-VM.
     * **SSH Port**: Lämna det på 22, standardvärdet.
     * **Sessionstyp**: Ändra värdet till **XFCE**. Linux DSVM stöder för närvarande endast XFCE desktop.
   * **Media-flik**: Du kan inaktivera stöd för ljud och skriva ut om du inte behöver använda dem-klienten.
   * **Delade mappar**: Om du vill kataloger från dina klientdatorer som är monterad på Linux-VM, lägger du till de klienten datorn kataloger som du vill dela med den virtuella datorn på den här fliken.

När du loggar in till den virtuella datorn med hjälp av SSH-klient eller XFCE grafiska desktop via X2Go klienten, är du redo att börja använda de verktyg som är installerade och konfigurerade på den virtuella datorn. På XFCE, kan du se genvägar på Program-menyn och ikoner på skrivbordet för många av verktygen.

När den virtuella datorn skapas och etablerats, är du redo att börja använda de verktyg som är installerade och konfigurerade på den. Det finns paneler för start-menyn och ikoner på skrivbordet för många av verktygen. 
