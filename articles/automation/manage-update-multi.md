---
title: "Hantera uppdateringar för flera virtuella Azure-datorer | Microsoft Docs"
description: "Publicera virtuella Azure-datorer för att hantera uppdateringar."
services: operations-management-suite
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: operations-management-suite
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 09/25/2017
ms.author: eslesar
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 89bf87f27fdf276068cba261fc6ae1660307e0b7
ms.contentlocale: sv-se
ms.lasthandoff: 09/25/2017

---

# <a name="manage-updates-for-multiple-azure-virtual-machines"></a>Hantera uppdateringar för flera virtuella Azure-datorer

Med Uppdateringshantering kan du hantera uppdateringar och korrigeringar för dina virtuella Azure-datorer.
Från ditt [Azure Automation](automation-offering-get-started.md)-konto kan du snabbt publicera virtuella datorer, se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att kontrollera att uppdateringarna har tillämpats för alla virtuella datorer som uppdateringshantering har aktiverats för.

## <a name="prerequisites"></a>Krav

För att slutföra stegen i den här guiden behöver du:

* Ett Azure Automation-konto. Instruktioner om hur du skapar ett Kör som-konto för Azure Automation finns i [Azure Kör som-konto](automation-sec-configure-azure-runas-account.md).
* En virtuell dator med Azure Resource Manager (inte klassisk). Instruktioner om hur du skapar en virtuell dator finns i [Skapa din första virtuella Windows-dator i Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Hantering av uppdateringar** till vänster på skärmen.
3. Klicka på **Lägg till virtuell Azure-dator** överst på skärmen.
    ![Publicera virtuella datorer](./media/manage-update-multi/update-onboard-vm.png)
4. Välj en virtuell dator som du vill publicera. Skärmen **Aktivera hantering av uppdateringar** visas.
5. Klicka på **Aktivera**.

   ![Aktivera uppdateringshantering](./media/manage-update-multi/update-enable.png)

Uppdateringshantering har aktiverats för den virtuella datorn.

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. Du kan se en lista med uppdateringar som saknas på fliken ** 
Uppdateringar som saknas**.

## <a name="schedule-an-update-deployment"></a>Schemalägga en uppdateringsdistribution

För att installera uppdateringar schemalägger du en distribution som passar ditt schema och servicefönster.
Du kan välja vilka uppdateringstyper som ska tas med i distributionen. Du kan till exempel ta med kritiska uppdateringar eller säkerhetsuppdateringar och exkludera samlade uppdateringar.

Schemalägg en ny uppdateringsdistribution för en eller flera virtuella datorer genom att klicka på **Distribution av schemauppdatering** längst upp på skärmen **Hantering av uppdateringar**. På skärmen **Ny uppdateringsdistribution** anger du följande:

* **Namn** – Ett unikt namn som identifierar uppdateringsdistributionen.
* **OS-typ** – Välj Windows eller Linux.
* **Datorer att uppdatera** – Välj de virtuella datorer som du vill uppdatera.

  ![Välja vilka virtuella datorer som ska uppdateras](./media/manage-update-multi/update-select-computers.png)

* **Uppdatera klassificering** – Välj vilka typer av programvara som ska tas med i uppdateringsdistributionen. Klassificeringstyper:
  * Kritiska uppdateringar
  * Säkerhetsuppdateringar
  * Samlade uppdateringar
  * Funktionspaket
  * Service pack
  * Definitionsuppdateringar
  * Verktyg
  * Uppdateringar
* **Schemainställningar** – Du kan antingen godkänna standarddatumet och -tiden, d.v.s. 30 minuter efter aktuell tid, eller så kan du ange en annan tid.
   Du kan också ange om distributionen ska ske en gång eller ange ett schema med återkommande tider. Klicka på alternativet Återkommande under Återkommande för att ange ett återkommande schema.

   ![Inställningsskärmen för uppdateringsschema](./media/manage-update-multi/update-set-schedule.png)

* **Underhållsperiod (minuter)** – Ange den tidsperiod som uppdateringsdistributionen ska utföras inom.  På så sätt försäkrar du dig om att ändringarna utförs inom ditt definierade servicefönster.

När du har konfigurerat schemat klickar du på **Skapa**. Därmed återgår du till statusinstrumentpanelen.
Observera att tabellen **Schemalagt** visar det distributionsschema som du precis skapade.

> [!WARNING]
> Om en uppdatering kräver omstart, startas den virtuella datorn om automatiskt.

## <a name="view-results-of-an-update-deployment"></a>Visa resultat för en uppdateringsdistribution

När den schemalagda distributionen har påbörjats kan du se status för distributionen på fliken **Uppdateringsdistributioner** på skärmen **Hantering av uppdateringar**.
Om distributionen körs visas status **Pågår**. När distributionen har slutförts ändras status till **Lyckades**.
Om det uppstod något fel med en eller flera uppdateringar i distributionen visas status **Misslyckades delvis**.

![Status för uppdateringsdistributioner ](./media/manage-update-multi/update-view-results.png)

Klicka på den slutförda uppdateringsdistributionen för att visa instrumentpanelen för distributionen.

På panelen för **uppdateringsresultat** visas en sammanfattning av det totala antalet uppdateringar och distributionsresultat för den virtuella datorn.
I tabellen till höger visas detaljer för varje uppdatering och installationsresultaten, som kan ha ett av följande värden:

* Inget försök har gjorts – Uppdateringen installerades inte eftersom det inte fanns tillräckligt med tid utifrån det underhållsfönster som definierats.
* Lyckades – Uppdateringen lyckades
* Misslyckades – Uppdateringen misslyckades

Klicka på **Alla loggar** om du vill se alla loggposter som har skapats för distributionen.

Klicka på panelen **Utdata** om du vill se jobbströmmen för den runbook som ansvarar för att hantera uppdateringsdistributionen på den virtuella måldatorn.

Klicka på **Fel** om du vill se detaljerad information om fel som uppstått vid distributionen.

## <a name="next-steps"></a>Nästa steg

* Mer information om uppdateringshantering finns i [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md).
