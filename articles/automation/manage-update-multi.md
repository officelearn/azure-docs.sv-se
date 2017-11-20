---
title: "Hantera uppdateringar för flera virtuella Azure-datorer | Microsoft Docs"
description: "Det här ämnet beskriver hur du hanterar uppdateringar för virtuella Azure-datorer."
services: automation
documentationcenter: 
author: eslesar
manager: carmonm
editor: 
ms.assetid: 
ms.service: automation
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/31/2017
ms.author: magoedte;eslesar
ms.openlocfilehash: f97b28d1588e959728163f7ab16d2550a79f610e
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/15/2017
---
# <a name="manage-updates-for-multiple-machines"></a>Hantera uppdateringar av flera datorer

Med Uppdateringshantering kan du hantera uppdateringar och korrigeringar av dina Windows- och Linux-datorer.
Från ditt [Azure Automation](automation-offering-get-started.md)-konto kan du snabbt publicera virtuella datorer, se status för tillgängliga uppdateringar, schemalägga installation av nödvändiga uppdateringar och granska distributionsresultat för att kontrollera att uppdateringarna har tillämpats för alla virtuella datorer som uppdateringshantering har aktiverats för.

## <a name="prerequisites"></a>Krav

Om du vill använda uppdateringshantering behöver du:

* Ett Azure Automation-konto. Se [Getting Started with Azure Automation](automation-offering-get-started.md) (Komma igång med Azure Automation) för instruktioner om hur du skapar ett Kör som-konto för Azure Automation.

* En virtuell dator eller en dator med ett operativsystem som stöds installerat.

## <a name="supported-operating-systems"></a>Operativsystem som stöds

Uppdateringshantering stöds på följande operativsystem.

### <a name="windows"></a>Windows

* Windows Server 2008 eller senare och uppdateringsdistributioner av Windows Server 2008 R2 SP1 och högre.  Installationsalternativ för Server Core och Nano Server stöds inte.

    > [!NOTE]
    > Stöd för att distribuera uppdateringar till Windows Server 2008 R2 SP1 kräver .NET Framework 4.5 och WMF 5.0 eller senare.
    > 
* Windows-klientoperativsystem stöds inte.

Windows-agenter måste antingen konfigureras för att kommunicera med en WSUS-server (Windows Server Update Services) eller ha åtkomst till Microsoft Update.

> [!NOTE]
> Windows-agenten kan inte hanteras samtidigt av System Center Configuration Manager.
>

### <a name="linux"></a>Linux

* CentOS 6 (x86/x64) och 7 (x64)  
* Red Hat Enterprise 6 (x86/x64) och 7 (x64)  
* SUSE Linux Enterprise Server 11 (x86/x64) och 12 (x64)  
* Ubuntu 12.04 LTS och senare x86/x64   

> [!NOTE]  
> Konfigurera om Unattended Upgrade-paketet om du vill inaktivera automatiska uppdateringar för att undvika att uppdateringar tillämpas utanför en underhållsperiod på Ubuntu. Mer information om hur du konfigurerar detta finns i avsnittet [automatiska uppdateringar i handboken för Ubuntu Server](https://help.ubuntu.com/lts/serverguide/automatic-updates.html).

Linux-agenter måste ha åtkomst till en uppdateringslagringsplats.

> [!NOTE]
> En OMS-agent för Linux som konfigurerats för att rapportera till flera OMS-arbetsytor stöds inte av den här lösningen.  
>

## <a name="enable-update-management-for-azure-virtual-machines"></a>Aktivera uppdateringshantering för virtuella Azure-datorer

1. Öppna ditt Automation-konto på Azure Portal.
2. Välj **Hantering av uppdateringar** till vänster på skärmen.
3. Klicka på **Lägg till virtuell Azure-dator** överst på skärmen.
    ![Publicera virtuella datorer](./media/manage-update-multi/update-onboard-vm.png)
4. Välj en virtuell dator som du vill publicera. Skärmen **Aktivera hantering av uppdateringar** visas.
5. Klicka på **Aktivera**.

   ![Aktivera uppdateringshantering](./media/manage-update-multi/update-enable.png)

Uppdateringshantering har aktiverats för den virtuella datorn.

## <a name="enable-update-management-for-non-azure-virtual-machines-and-computers"></a>Aktivera uppdateringshantering för virtuella datorer som inte använder Azure och datorer

Instruktioner om hur du aktiverar uppdateringshantering för virtuella datorer som inte använder Azure Windows och datorer finns i [Connect Windows computers to the Log Analytics service in Azure](../log-analytics/log-analytics-windows-agents.md) (Anslut Windows-datorer till Log Analytics-tjänsten i Azure).

Instruktioner om hur du aktiverar uppdateringshantering för virtuella datorer som inte använder Azure Linux och datorer finns i [Connect your Linux Computers to Operations Management Suite (OMS)](../log-analytics/log-analytics-agent-linux.md) (Anslut Linux-datorer till Operations Management Suite (OMS)).

## <a name="view-update-assessment"></a>Visa kontroll av uppdateringar

När **uppdateringshantering** är aktiverat visas skärmen **Hantering av uppdateringar**. Du kan se en lista med uppdateringar som saknas på fliken  **Uppdateringar som saknas**.

## <a name="data-collection"></a>Datainsamling

Agenter som installerats på virtuella datorer och datorer samlar in data om uppdateringar och skickar dem till Azures uppdateringshantering.

### <a name="supported-agents"></a>Agenter som stöds

I följande tabell beskrivs de anslutna källor som stöds av den här lösningen.

| Ansluten källa | Stöds | Beskrivning |
| --- | --- | --- |
| Windows-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Windows-agenter och initierar installationen av nödvändiga uppdateringar. |
| Linux-agenter |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från Linux-agenter och initierar installationen av nödvändiga uppdateringar för distributioner som stöds. |
| Operations Manager-hanteringsgrupp |Ja |Uppdateringshanteringen samlar in information om systemuppdateringar från agenter i en ansluten hanteringsgrupp. |
| Azure Storage-konto |Nej |Azure Storage inkluderar inte information om systemuppdateringar. |

### <a name="collection-frequency"></a>Insamlingsfrekvens

För varje hanterad Windows-dator utförs en genomsökning två gånger per dag. Var 15:e minut anropas Windows API för att fråga efter den senaste uppdateringstiden för att fastställa om statusen har ändrats, och i så fall om en fullständig genomsökning har initierats.  För varje hanterad Linux-dator utförs en sökning var tredje timme.

Det kan ta alltifrån 30 minuter upp till 6 timmar för instrumentpanelen att visa uppdaterade data från hanterade datorer.

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

Mer information om loggar, utdata och felinformation finns i [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md).

## <a name="next-steps"></a>Nästa steg

* Mer information om uppdateringshantering finns i [Uppdateringshantering](../operations-management-suite/oms-solution-update-management.md).
