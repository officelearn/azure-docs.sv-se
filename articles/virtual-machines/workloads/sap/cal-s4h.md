---
title: "Distribuera SAP S/4HANA eller BW/4HANA på en virtuell dator i Azure | Microsoft Docs"
description: "Distribuera SAP S/4HANA eller BW/4HANA på en virtuell dator i Azure"
services: virtual-machines-linux
documentationcenter: 
author: hermanndms
manager: timlt
editor: 
tags: azure-resource-manager
keywords: 
ms.assetid: 44bbd2b6-a376-4b5c-b824-e76917117fa9
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/15/2016
ms.author: hermannd
ms.openlocfilehash: 4788fa14a6c49d39b5a3096a69b6738f4a5d8cca
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="deploy-sap-s4hana-or-bw4hana-on-azure"></a>Distribuera SAP S/4HANA eller BW/4HANA på Azure
Den här artikeln beskriver hur du distribuerar S/4HANA på Azure med hjälp av SAP-installation Molnbibliotek (SAP CAL) 3.0. Följ samma steg för att distribuera andra SAP HANA-baserade lösningar, till exempel BW/4HANA.

> [!NOTE]
Mer information om SAP-CAL går du till den [SAP installation Molnbibliotek](https://cal.sap.com/) webbplats. SAP har också en blogg om den [SAP molnet installation biblioteket 3.0](http://scn.sap.com/community/cloud-appliance-library/blog/2016/05/27/sap-cloud-appliance-library-30-came-with-a-new-user-experience).

> [!NOTE]
Du kan använda Azure Resource Manager-distributionsmodellen utöver den önskade mindre klassiska distributionsmodellen för att distribuera SAP-CAL från och med den 29 maj 2017. Vi rekommenderar att du använder den nya Resource Manager-distributionsmodellen och bortse från den klassiska distributionsmodellen.

## <a name="step-by-step-process-to-deploy-the-solution"></a>Steg för steg hur du distribuerar lösningen

Följande sekvens av skärmdumpar visar hur du distribuerar S/4HANA på Azure med hjälp av SAP-CAL. Processen fungerar på samma sätt som andra lösningar, till exempel BW/4HANA.

Den **lösningar** sidan visar några av de tillgängliga SAP HANA för CAL-baserade lösningarna på Azure. **SAP S/4HANA 1610 FPS01, Fully-Activated installation** i mitten rad:

![SAP CAL-lösningar](./media/cal-s4h/s4h-pic-1c.png)

### <a name="create-an-account-in-the-sap-cal"></a>Skapa ett konto i SAP-CAL
1. Om du vill logga in till SAP-CAL för första gången använder din SAP-S-användare eller en annan användare har registrerat hos SAP. Definiera ett SAP CAL-konto som används av SAP-CAL för att distribuera installationer på Azure. I definitionen konto måste du:

    a. Välj distributionsmodell i Azure (Resource Manager eller klassisk).

    b. Ange din Azure-prenumeration. En SAP CAL-konto kan tilldelas till en prenumeration. Om du behöver mer än en prenumeration måste du skapa en annan SAP CAL-konto.

    c. Ge SAP CAL-behörighet att distribuera till din Azure-prenumeration.

    > [!NOTE]
    Nästa steg visar hur du skapar ett SAP CAL-konto för Resource Manager distributioner. Om du redan har ett SAP CAL-konto som är länkad till den klassiska distributionsmodellen du *måste* att följa dessa steg om du vill skapa ett nytt SAP CAL-konto. Det nya SAP CAL-kontot måste distribueras i Resource Manager-modellen.

2. Skapa ett nytt SAP CAL-konto. Den **konton** sidan visas tre alternativ för Azure: 

    a. **Microsoft Azure (klassisk)** är den klassiska distributionsmodellen och är inte längre önskade.

    b. **Microsoft Azure** är den nya Resource Manager-distributionsmodellen.

    c. **Windows Azure som drivs av 21Vianet** är ett alternativ i Kina som använder den klassiska distributionsmodellen.

    Om du vill distribuera i Resource Manager-modellen, Välj **Microsoft Azure**.

    ![Information om SAP CAL-konto](./media/cal-s4h/s4h-pic-2a.png)

3. Ange Azure **prenumerations-ID** som finns på Azure-portalen.

   ![SAP CAL-konton](./media/cal-s4h/s4h-pic3c.png)

4. För att godkänna SAP-CAL att distribuera till Azure-prenumerationen du definierat klickar **auktorisera**. Följande sida visas i fliken i webbläsaren:

   ![Internet Explorer cloud services-inloggning](./media/cal-s4h/s4h-pic4c.png)

5. Om fler än en användare visas väljer du Microsoft-kontot som är kopplad till att coadministrator av Azure-prenumerationen du valt. Följande sida visas i fliken i webbläsaren:

   ![Bekräftelse för Internet Explorer cloud services](./media/cal-s4h/s4h-pic5a.png)

6. Klicka på **acceptera**. Om tillståndet lyckas visar definition för SAP CAL-konto igen. Efter en kort tid ett meddelande som bekräftar att Auktoriseringen lyckades.

7. Om du vill tilldela användaren det nyligen skapade SAP CAL-kontot, ange din **användar-ID** i rutan till höger och klicka på **Lägg till**.

   ![Kontot till användarassociationen](./media/cal-s4h/s4h-pic8a.png)

8. Om du vill koppla ditt konto till den användare som du använder för att logga in på SAP-CAL, klickar du på **granska**. 
 
9. Om du vill skapa kopplingen mellan användaren och det nyligen skapade SAP CAL-kontot, klickar du på **skapa**.

   ![Användaren till SAP CAL-kontokoppling](./media/cal-s4h/s4h-pic9b.png)

Du har skapat ett SAP CAL-konto som kan:

- Använd Resource Manager-distributionsmodellen.
- Distribuera SAP-system i din Azure-prenumeration.

Nu kan du börja distribuera S/4HANA i din prenumeration för användare i Azure.

> [!NOTE]
Innan du fortsätter kan du avgöra om du har Azure core kvoter för virtuella datorer i Azure H-serien. För tillfället använder SAP-CAL H-serien virtuella datorer i Azure för att distribuera vissa SAP HANA-baserade lösningar. Din Azure-prenumeration kanske inte H-serien core kvoter för H-serien. I så fall, kan du behöva kontakta Azure-supporten för att få en kvot på minst 16 H-serien kärnor.

> [!NOTE]
När du distribuerar en lösning på Azure i SAP-CAL kanske du upptäcker att du kan välja endast en Azure-region. Om du vill distribuera till Azure-regioner än den föreslagna av SAP-CAL, måste du köpa en prenumeration för Fjärrskrivbordstjänster från SAP. Du kan behöva öppna ett meddelande med SAP till ditt CAL-konto som är aktiverad för att leverera i Azure-regioner än de som ursprungligen förslag.

### <a name="deploy-a-solution"></a>Distribuera en lösning

Nu ska vi distribuera en lösning från den **lösningar** sidan i SAP-klientåtkomstlicens. SAP-CAL har två sekvenser att distribuera:

- En grundläggande sekvens som använder en sida för att definiera system som ska distribueras
- En avancerad sekvens som ger dig vissa alternativ på VM-storlekar 

Vi visar grundläggande sökvägen till den här distributionen.

1. På den **kontoinformation** sidan som du behöver:

    a. Välj ett SAP CAL-konto. (Använda ett konto som är kopplad till distribuera med Resource Manager-distributionsmodellen.)

    b. Ange en instans **namn**.

    c. Välj en Azure **Region**. SAP-CAL föreslår en region. Om du behöver en annan Azure-region och du inte har en SAP CAL-prenumeration kan behöver du ordna en CAL-prenumeration med SAP.

    d. Ange en **lösenord** för lösning av åtta eller nio tecken. Lösenordet som används för administratörerna för de olika komponenterna.

   ![SAP CAL grundläggande läge: Skapa en instans](./media/cal-s4h/s4h-pic10a.png)

2. Klicka på **skapa**, och i meddelanderutan som visas på **OK**.

   ![SAP CAL VM-storlekar som stöds](./media/cal-s4h/s4h-pic10b.png)

3. I den **privat nyckel** dialogrutan klickar du på **lagra** att lagra den privata nyckeln i SAP-klientåtkomstlicens. Om du vill använda lösenordsskydd för den privata nyckeln **hämta**. 

   ![SAP CAL privat nyckel](./media/cal-s4h/s4h-pic10c.png)

4. Läsa SAP-CAL **varning** meddelande och på **OK**.

   ![SAP CAL-varning](./media/cal-s4h/s4h-pic10d.png)

    Nu sker distributionen. Efter en stund, beroende på storleken och komplexiteten för lösning (SAP-CAL ger en uppskattning), visas status som aktiv och redo för användning.

5. Gå till Azure-portalen för att hitta de virtuella datorerna som samlats in med andra associerade resurser i en resursgrupp: 

   ![SAP CAL-objekt som distribuerats i den nya portalen](./media/cal-s4h/sapcaldeplyment_portalview.png)

6. På portalen SAP CAL status visas som **Active**. Anslut till lösningen **Anslut**. Olika alternativ för att ansluta till de olika komponenterna distribueras i den här lösningen.

   ![SAP CAL-instanser](./media/cal-s4h/active_solution.png)

7. Innan du kan använda något av alternativen för att ansluta till distribuerade system, klickar du på **komma igång**. 

   ![Anslut till instansen](./media/cal-s4h/connect_to_solution.png)

    I dokumentationen för att användarna för varje anslutningsmetoder. Lösenorden för dessa användare har angetts till master lösenordet som du angav i början av distributionsprocessen. I dokumentationen för visas andra mer funktionell användare med sina lösenord, som du kan använda för att logga in på det distribuerade systemet. 

    Till exempel om du använder SAP-Användargränssnittet som är förinstallerat på datorn i Windows Remote Desktop S/4 system kan se ut så här:

   ![SM50 i förinstallerade SAP-Gränssnittet](./media/cal-s4h/gui_sm50.png)

    Eller om du använder DBACockpit instansen kan se ut så här:

   ![SM50 i det grafiska Användargränssnittet SAP DBACockpit](./media/cal-s4h/dbacockpit.png)

En felfri SAP S/4 installation distribueras inom några timmar i Azure.

Om du har köpt en prenumeration för SAP CAL stöd SAP fullständigt för distribution via SAP-CAL på Azure. Stöd för kön är BC-VCM-klientåtkomstlicens.







