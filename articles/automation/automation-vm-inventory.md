---
title: Hantera en virtuell Azure-dator med inventeringssamling | Microsoft Docs
description: Hantera en virtuell dator med inventeringssamling
services: automation
ms.subservice: change-inventory-management
keywords: inventory, automation, change, tracking
ms.date: 01/28/2020
ms.topic: conceptual
ms.openlocfilehash: 0627d2daa70c276535dc43b722e22e1d73b0c8d2
ms.sourcegitcommit: eefb0f30426a138366a9d405dacdb61330df65e7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/17/2020
ms.locfileid: "81617370"
---
# <a name="manage-an-azure-virtual-machine-with-inventory-collection"></a>Hantera en virtuell Azure-dator med inventeringssamling

Du kan aktivera inventeringssamling för en virtuell Azure-dator via den virtuella datorns resurssida. Du kan samla in och visa följande inventeringsinformation på dina datorer:

- Windows-program (Windows-program och Windows-uppdateringar), tjänster, filer och registernycklar
- Linux-program (paket) demoner och filer

Den här metoden ger ett webbläsarbaserat användargränssnitt för att installera och konfigurera inventeringssamling.

## <a name="before-you-begin"></a>Innan du börjar

Om du inte har en Azure-prenumeration [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/).

Den här artikeln förutsätter att du har en virtuell dator för att konfigurera lösningen på. Om du inte har en virtuell Azure-dator ska du skapa en [virtuell dator](../virtual-machines/windows/quick-create-portal.md).

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på [Azure-portalen](https://portal.azure.com/).

## <a name="enable-inventory-collection-from-the-virtual-machine-resource-page"></a>Aktivera inventeringssamling via en virtuell dators resurssida

1. I rutan till vänster på Azure Portal väljer du **Virtuella datorer**.
2. Från listan med virtuella datorer väljer du en virtuell dator.
3. Välj **Lager**under **Operationer**på **Resurs-menyn.**
4. Välj en Log Analytics-arbetsyta för lagring av dataloggar.
    Om det inte finns några tillgängliga arbetsytor för dig för en regionen uppmanas du att skapa en standardarbetsyta och ett Automation-konto.
5. Klicka på **Aktivera** för att börjar integrera datorn.

   ![Visa integreringsalternativ](./media/automation-vm-inventory/inventory-onboarding-options.png)

    Ett statusfält meddelar dig att lösningen aktiveras. Processen kan ta upp till 15 minuter. Under den här tiden kan du stänga fönstret eller hålla det öppet och det meddelar dig när lösningen är aktiverad. Du kan övervaka distributionsstatusen via meddelandefönstret.

   ![Visa inventeringslösningen direkt efter integreringen](./media/automation-vm-inventory/inventory-onboarded.png)

När distributionen är klar försvinner statusfältet. Systemet samlar fortfarande in inventeringsdata och dessa data kanske inte visas än. En fullständig insamling av data kan ta 24 timmar.

## <a name="configure-your-inventory-settings"></a>Konfigurera inventeringsinställningarna

Som standard konfigureras programvara, Windows-tjänster och Linux-daemon för insamling. Om du vill inventera Windows-registret och filer kan du konfigurera inställningarna för inventeringsinsamling.

1. Klicka på Redigera **inställningar** högst upp på sidan på sidan Lager.
2. Om du vill lägga till en ny samlingsinställning går du till den inställningskategori som du vill lägga till genom att välja fliken **Windows-registret,** **Windows-filer**eller **Linux-filer.**
3. Markera lämplig kategori och klicka på **Lägg till** högst upp på sidan.

Följande tabeller innehåller information om varje egenskap som kan konfigureras för de olika kategorierna.

### <a name="windows-registry"></a>Windows-registret

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Fastställer om inställningen tillämpas        |
|Objektnamn     | Eget namn på filen som ska spåras        |
|Grupp     | Ett gruppnamn för att gruppera filer logiskt        |
|Windows-registernyckel   | Sökvägen för att söka efter filen Till exempel: ”HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders\Common Startup”      |

### <a name="windows-files"></a>Windows-filer

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Sant om inställningen tillämpas och Falskt annars.        |
|Objektnamn     | Det egna namnet på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppering av filer.       |
|Ange sökväg     | Sökvägen för att söka efter filen, till exempel **c:\temp\myfile.txt**.

### <a name="linux-files"></a>Linux-filer

|Egenskap  |Beskrivning  |
|---------|---------|
|Enabled     | Sant om inställningen tillämpas och Falskt annars.        |
|Objektnamn     | Det egna namnet på filen som ska spåras.        |
|Grupp     | Ett gruppnamn för logiskt gruppering av filer.        |
|Ange sökväg     | Sökvägen för att söka efter filen, till exempel **/etc/*.conf**.       |
|Sökvägstyp     | Den typ av objekt som ska spåras. Värden är Fil och katalog.        |
|Rekursion     | Sant om rekursion används när du letar efter objektet som ska spåras, och Falskt annars.        |
|Använda Sudo     | Sant om sudo används vid kontroll av objektet och Falskt annat.         |
|Länkar     | Värde som anger hur symboliska länkar hanteras när kataloger korsas. Möjliga värden: <br> Ignorera – Ignorerar symboliska länkar och inkluderar inte refererade filer/kataloger<br>Följ – Följer de symboliska länkarna under rekursion och inkluderar refererade filer/kataloger<br>Hantera – Följer de symboliska länkarna och tillåter ändring av behandling av returnerat innehåll      |

## <a name="manage-machine-groups"></a>Hantera maskingrupper

Med Inventory kan du skapa och visa datorgrupper i Azure Monitor-loggar. Maskingrupper är samlingar av datorer som definieras av en fråga i Azure Monitor-loggar.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

Om du vill visa dina maskingrupper väljer du fliken **Maskingrupper** på sidan Lager.

![Visa maskingrupper på lagersidan](./media/automation-vm-inventory/inventory-machine-groups.png)

Om du väljer en maskingrupp i listan öppnas sidan Maskingrupper. På den här sidan visas information om maskingruppen. Dessa information omfattar logganalysfrågan som används för att definiera gruppen. Längst ned på sidan finns en växlingslista över de datorer som ingår i den gruppen.

![Visa sidan för maskingrupp](./media/automation-vm-inventory/machine-group-page.png)

Klicka på **+ Klona** om du vill klona maskingruppen. Du måste ge gruppen ett nytt namn och alias för gruppen. Definitionen kan ändras just nu. När du har ändrat frågan klickar du på **Validera fråga** om du vill förhandsgranska de datorer som skulle väljas. När du är nöjd med gruppen klickar du på **Skapa** för att skapa maskingruppen.

Om du vill skapa en ny maskingrupp klickar du på **+ Skapa en maskingrupp**. Den här knappen öppnar sidan **Skapa en maskingrupp** där du kan definiera den nya gruppen. Skapa gruppen genom att klicka på **Skapa**.

![Skapa ny maskingrupp](./media/automation-vm-inventory/create-new-group.png)

## <a name="disconnect-your-virtual-machine-from-management"></a>Koppla bort den virtuella datorn från hantering

Ta bort den virtuella datorn från inventeringshantering:

1. Välj **Log Analytics** i den vänstra rutan på Azure Portal och välj sedan den arbetsyta du använde när du integrerade den virtuella datorn.
2. Öppna **resursmenyn** på sidan Logganalys.
3. Välj **virtuella datorer** under **Datakällor för arbetsyta**.
4. Välj den virtuella dator du vill koppla bort från listan. Den virtuella datorn har en grön bock bredvid texten **Den här arbetsytan** i kolumnen **OMS-anslutning**.

   >[!NOTE]
   >Operations Management Suite (OMS) kallas nu Azure Monitor-loggar.
   
5. Klicka på **Koppla från**högst upp på nästa sida .
6. Klicka på **Ja** i bekräftelsefönstret om du vill koppla bort datorn från hanteringen.

## <a name="next-steps"></a>Nästa steg

* Information om hur du hanterar ändringar i inställningarna för filer och registret finns i [Track software changes in your environment with the Change Tracking solution](../log-analytics/log-analytics-change-tracking.md) (Spåra programändringar i miljön med lösningen Ändringsspårning).
* Mer information om hur du hanterar Windows och paketuppdateringar på dina virtuella datorer finns [i Lösningen för uppdateringshantering i Azure](../operations-management-suite/oms-solution-update-management.md).