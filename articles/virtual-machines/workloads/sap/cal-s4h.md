---
title: Distribuera SAP S/4HANA eller BW/4HANA på en virtuell Azure-dator | Microsoft Docs
description: Distribuera SAP S/4HANA eller BW/4HANA på en Azure virtuell dator
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: c59fcf43cb4767f1d95d769dfce4d5c8755e45ee
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "57990484"
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Distribuera SAP S/4HANA eller BW/4HANA på Azure
Den här artikeln beskriver hur du distribuerar S/4HANA på Azure med SAP Cloud Appliance Library (SAP CAL) 3.0. Följ samma steg för att distribuera andra SAP HANA-baserade lösningar som BW/4HANA.

> [!NOTE]
> Mer information om SAP CAL går du till den [SAP Cloud Appliance Library](https://cal.sap.com/) webbplats. SAP har också en blogg om den [SAP Cloud Appliance Library 3.0](https://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).
> 
> [!NOTE]
> Du kan använda Azure Resource Manager-distributionsmodellen utöver den mindre prioriterad klassiska distributionsmodellen från och med den 29 maj 2017 för att distribuera SAP CAL. Vi rekommenderar att du använder den nya Resource Manager-distributionsmodellen och bortse från den klassiska distributionsmodellen.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Steg för steg hur du distribuerar lösningen

Följande sekvens med skärmbilder visar hur du distribuerar S/4HANA på Azure med hjälp av SAP CAL. Processen fungerar på samma sätt som andra lösningar som BW/4HANA.

Den **lösningar** visar några av de tillgängliga SAP CAL HANA-baserade lösningarna på Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated installation** är på mittenraden:

![SAP CAL-lösningar](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto på SAP CAL
1. Registrerad med SAP vill logga in på SAP CAL för första gången ska du använda din SAP-S-användare eller en annan användare. Definiera ett SAP CAL-konto som används av SAP CAL för att distribuera enheter på Azure. I definitionen av konto måste du:

    a. Välj distributionsmodell på Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. Ett SAP CAL-konto kan tilldelas till en prenumeration. Om du behöver mer än en prenumeration kan behöva du skapa ett annat SAP CAL-konto.

    c. Ge SAP CAL-behörighet för att distribuera till din Azure-prenumeration.

   > [!NOTE]
   >  Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager-distributioner. Om du redan har ett SAP CAL-konto som är länkad till den klassiska distributionsmodellen du *behöver* att följa stegen nedan för att skapa ett nytt SAP CAL-konto. Nytt SAP CAL-konto måste du distribuerar i Resource Manager-modellen.

1. Skapa ett nytt SAP CAL-konto. Den **konton** sidan visas tre alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre att föredra.

    b. **Microsoft Azure** är den nya Resource Manager-distributionsmodellen.

    c. **Windows Azure som drivs av 21Vianet** är ett alternativ i Kina som använder den klassiska distributionsmodellen.

    Om du vill distribuera i Resource Manager-modellen, Välj **Microsoft Azure**.

    ![Kontoinformation för SAP CAL](./media/cal-s4h/s4h-pic-2a.png)

1. Ange Azure **prenumerations-ID** som finns på Azure portal.

   ![SAP CAL-konton](./media/cal-s4h/s4h-pic3c.png)

1. För att auktorisera SAP CAL att distribuera till Azure-prenumeration du har definierat, klickar du på **auktorisera**. Följande sida visas i fliken i webbläsaren:

   ![Logga in för Internet Explorer cloud services](./media/cal-s4h/s4h-pic4c.png)

1. Om fler än en användare visas i listan, väljer du Microsoft-konto som är länkad till vara medadministratör för Azure-prenumeration du valde. Följande sida visas i fliken i webbläsaren:

   ![Bekräftelse för Internet Explorer cloud services](./media/cal-s4h/s4h-pic5a.png)

1. Klicka på **acceptera**. Om auktoriseringen är klar, visar SAP CAL-konto definitionen igen. Om en stund bekräftar ett meddelande att auktoriseringsprocessen lyckades.

1. Om du vill tilldela det nyligen skapade SAP CAL-kontot till dina användare, ange din **användar-ID** i rutan till höger och klicka på **Lägg till**.

   ![Kontot till användarassociationen](./media/cal-s4h/s4h-pic8a.png)

1. Om du vill koppla ditt konto med användaren som används för att logga in på SAP CAL, klickar du på **granska**. 
 
1. Om du vill skapa associationen mellan användaren och det nyligen skapade SAP CAL-kontot, klickar du på **skapa**.

   ![Användaren att associationen med SAP CAL](./media/cal-s4h/s4h-pic9b.png)

Du skapat har ett SAP CAL-konto som kan:

- Använda Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

Nu kan du börja distribuera S/4HANA i din användarprenumeration i Azure.

> [!NOTE]
> Innan du fortsätter kan du avgöra om du har Azure vCPU-kvoter för virtuella datorer i Azure H-serien. För tillfället använder SAP CAL H-serien virtuella datorer i Azure för att distribuera några SAP HANA-baserade lösningar. Din Azure-prenumeration kanske inte har någon H-serien vCPU-kvoter för H-serien. I så fall kan du behöva kontakta Azure-supporten om du vill ha en kvot på minst 16 virtuella processorer H-serien.
> 
> [!NOTE]
> När du distribuerar en lösning på Azure i SAP CAL, kanske du upptäcker att du kan välja endast en Azure-region. Du måste köpa en prenumeration på CAL från SAP för att distribuera till Azure-regioner än den som föreslås av SAP CAL. Du även behöva öppna ett meddelande med SAP har din CAL-konto som är aktiverade för att leverera i Azure-regioner än de som ursprungligen föreslås.

### <a name="deploy-a-solution"></a>Distribuera en lösning

Nu ska vi distribuera en lösning från den **lösningar** för SAP CAL. SAP CAL har två sekvenser att distribuera:

- En grundläggande sekvens som använder en sida för att definiera system som ska distribueras
- En avancerad sekvens som ger dig vissa alternativ på VM-storlekar 

Vi visar grundläggande sökvägen till den här distributionen.

1. På den **kontoinformation** sidan som du behöver:

    a. Välj ett SAP CAL-konto. (Använd ett konto som är kopplad till att distribuera med Resource Manager-distributionsmodellen).

    b. Ange en instans **namn**.

    c. Välj en Azure **Region**. SAP CAL föreslår en region. Om du behöver en annan Azure-region och du inte har en SAP CAL-prenumeration kan behöva du beställa en CAL-prenumeration med SAP.

    d. Ange en **lösenord** för lösning av åtta eller nio tecken. Lösenordet används för administratörerna av de olika komponenterna.

   ![SAP CAL Basic-läge: Skapa instans](./media/cal-s4h/s4h-pic10a.png)

1. Klicka på **skapa**, och i meddelanderutan som visas på **OK**.

   ![VM-storlekar som stöds av SAP CAL](./media/cal-s4h/s4h-pic10b.png)

1. I den **privat nyckel** dialogrutan klickar du på **Store** att lagra den privata nyckeln i SAP CAL. Om du vill använda lösenordsskydd för den privata nyckeln, klickar du på **hämta**. 

   ![SAP CAL privat nyckel](./media/cal-s4h/s4h-pic10c.png)

1. Läsa SAP CAL **varning** meddelande och på **OK**.

   ![SAP CAL-varning](./media/cal-s4h/s4h-pic10d.png)

    Nu distributionen äger rum. Om en stund, beroende på storleken och komplexiteten för lösningen (SAP CAL ger en beräkning) och visas status som aktiv och redo för användning.

1. Gå till Azure-portalen för att hitta de virtuella datorerna som samlas in med andra associerade resurser i en resursgrupp: 

   ![SAP CAL-objekt som distribuerats i den nya portalen](./media/cal-s4h/sapcaldeplyment_portalview.png)

1. I SAP CAL-portal visas statusen som **Active**. Anslut till lösningen genom att klicka på **Connect**. Olika alternativ för att ansluta till de olika komponenterna har distribuerats i den här lösningen.

   ![SAP CAL-instanser](./media/cal-s4h/active_solution.png)

1. Innan du kan använda något av alternativen för att ansluta till de distribuerade system, klickar du på **Kom igång-guiden**. 

   ![Anslut till instansen](./media/cal-s4h/connect_to_solution.png)

    Dokumentationen namn användarna för var och en av metoderna anslutning. Lösenorden för dessa användare är inställda på master lösenordet som du angav i början av distributionsprocessen. I dokumentationen listas andra mer funktionell användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet. 

    Till exempel om du använder SAP-Gränssnittet som är förinstallerade på Windows Remote Desktop-datorn, s/4-system kan se ut så här:

   ![SM50 i förinstallerade SAP-Gränssnittet](./media/cal-s4h/gui_sm50.png)

    Eller om du använder DBACockpit instansen kan se ut så här:

   ![SM50 i DBACockpit SAP-Gränssnittet](./media/cal-s4h/dbacockpit.png)

Inom några timmar distribueras en felfri SAP s/4-installation i Azure.

Om du har köpt en prenumeration på SAP CAL stöd SAP fullständigt för distribution via SAP CAL på Azure. Stöd för kön är BC-VCM-CAL.







