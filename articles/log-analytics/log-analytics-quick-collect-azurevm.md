---
title: Samla in data om virtuella datorer i Azure | Microsoft Docs
description: Lär dig hur du aktiverar OMS-agentens tillägg för virtuella datorer och hur du aktiverar insamling av data från virtuella datorer i Azure med Log Analytics.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 06/26/2018
ms.author: magoedte
ms.custom: mvc
ms.component: na
ms.openlocfilehash: a79679068b03103bd8ca63455dd2d1758751aa6f
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37131245"
---
# <a name="collect-data-about-azure-virtual-machines"></a>Samla in data om virtuella datorer i Azure
Med [Azure Log Analytics](log-analytics-overview.md) kan du samla in data direkt från virtuella datorer i Azure och från andra resurser i din miljö till en enda lagringsplats för detaljerad analys och korrelation.  Den här snabbstarten visar hur du konfigurerar och samlar in data från virtuella Linux- eller Windows-datorer i Azure med några enkla steg.  
 
För den här snabbstarten förutsätts det att du har en befintlig virtuell dator i Azure. Om du inte har det kan du [skapa en virtuell Windows-dator](../virtual-machines/windows/quick-create-portal.md) eller [skapa en virtuell Linux-dator](../virtual-machines/linux/quick-create-cli.md) med hjälp av våra snabbstarter för virtuella datorer.

## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. Klicka på **Alla tjänster** på Azure Portal. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.

    ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br>  

2. Klicka på **Skapa** och välj sedan alternativ för följande objekt:

  * Ange ett namn för den nya **OMS-arbetsytan**, som *DefaultLAWorkspace*. 
  * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
  * För **Resursgrupp** väljer du en befintlig resursgrupp som innehåller en eller flera virtuella datorer i Azure.  
  * Välj den **plats** där dina virtuella datorer distribueras.  Mer information finns i avsnittet om [tillgängliga regioner för Log Analytics](https://azure.microsoft.com/regions/services/).
  * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 april eller en prenumeration som var bunden till en befintlig EA-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-02.png) 

3. När du har angett den nödvändiga informationen i fönsterrutan **OMS-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="enable-the-log-analytics-vm-extension"></a>Aktivera Log Analytics-tillägget för virtuella datorer
För virtuella Windows- och Linux-datorer som redan har distribuerats i Azure installerar du Log Analytics-agenten med Log Analytics-tillägget för virtuella datorer.  Med hjälp av tillägget förenklas installationen och agenten konfigureras automatiskt att skicka data till den Log Analytics-arbetsyta som du anger. Agenten uppgraderas också automatiskt så att du alltid har de senaste funktionerna och korrigeringarna.

>[!NOTE]
>OMS-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics--arbetsyta. 

1. I Azure Portal klickar du på **Alla tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj *DefaultLAWorkspace* som du skapade tidigare i listan med Log Analytics-arbetsytor.
3. Gå till menyn till vänster och klicka under Datakällor för arbetsyta på **Virtuella datorer**.  
4. Från listan med **virtuella datorer** väljer du en virtuell dator där du vill installera agenten. Observera att **OMS-anslutningsstatus** för den virtuella datorn anger att den är **Inte ansluten**.
5. Gå till informationen om den virtuella datorn och välj **Anslut**. Agenten installeras och konfigureras för Log Analytics-arbetsytan automatiskt. Den här processen tar några minuter och under tiden står **Status** som **Ansluter**.
6. När du har installerat och anslutit agenten ska **OMS-anslutningsstatus** uppdateras till **Den här arbetsytan**.

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda
Log Analytics kan samla in händelser från Windows-händelseloggar eller Linux Syslog och prestandaräknare som du anger för analys och rapportering på längre sikt samt vidta åtgärder när ett visst villkor har identifierats.  Följ dessa steg om du vill konfigurera insamling av händelser från Windows systemlogg och Linux Syslog och flera vanliga prestandaräknare till att börja med.  

### <a name="data-collection-from-windows-vm"></a>Insamling av data från virtuella Windows-datorer
1. Välj **Avancerade inställningar**.

    ![Avancerade inställningar i Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)

3. Välj **Data** och sedan **Windows-händelseloggar**.  
4. Du kan lägga till en händelselogg genom att skriva namnet på loggen.  Skriv **System** och klicka sedan på plustecknet **+**.  
5. Kontrollera allvarlighetsgraderna **Fel** och **Varning** i tabellen.   
6. Klicka på **Spara** överst på sidan för att spara konfigurationen.
7. Välj **Windows-prestandadata** för att aktivera insamling av prestandaräknare på en Windows-dator. 
8. När du först konfigurerar Windows-prestandaräknare för en ny Log Analytics-arbetsyta har du möjlighet att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.

    ![Standardalternativen för Windows-prestandaräknare markerade](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).

    Klicka på **Lägg till valda prestandaräknare**.  De läggs till med en förinställning av provintervall på tio sekunder.
  
9. Klicka på **Spara** överst på sidan för att spara konfigurationen.

### <a name="data-collection-from-linux-vm"></a>Insamling av data från virtuella Linux-datorer

1. Välj **Syslog**.  
2. Du kan lägga till en händelselogg genom att skriva namnet på loggen.  Skriv **Syslog** och klicka sedan på plustecknet **+**.  
3. Avmarkera allvarlighetsgraderna **Info**, **Meddelande** och **Felsök** i tabellen. 
4. Klicka på **Spara** överst på sidan för att spara konfigurationen.
5. Välj **Linux-prestandadata** för att aktivera insamling av prestandaräknare på en Linux-dator. 
6. När du först konfigurerar Linux-prestandaräknare för en ny Log Analytics-arbetsyta har du möjlighet att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.

    ![Standardalternativen för Windows-prestandaräknare markerade](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).

    Klicka på **Lägg till valda prestandaräknare**.  De läggs till med en förinställning av provintervall på tio sekunder.  

7. Klicka på **Spara** överst på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in
Nu när du har aktiverat insamling av data kan du köra en enkel loggsökning för att se vissa data från de virtuella måldatorerna.  

1. Gå till Azure Portal, navigera till Log Analytics och välj den arbetsyta du skapade tidigare.
2. Klicka på panelen **Loggsökning** och i fönsterrutan Loggsökning skriver du sedan `Perf` i frågefältet och trycker på retur eller klickar på sökknappen till höger om fältet.

    ![Exempel på loggsökningsfråga i Log Analytics](./media/log-analytics-quick-collect-azurevm/log-analytics-portal-perf-query.png) 

Frågan i följande bild returnerar till exempel 735 prestandaposter.  Ditt resultatet blir mycket mindre. 

![Resultat av loggsökning i Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser
Ta bort Log Analytics-arbetsytan när den inte längre behövs. Markera i så fall den Log Analytics-arbetsyta du skapade tidigare och klicka på **Ta bort** på resurssidan.


![Ta bort Log Analytics-resurs](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan samla in funktions- och prestandadata från dina virtuella Windows- eller Linux-datorer kan du enkelt kan börja utforska, analysera och vidta åtgärder på data som du samlar in *utan kostnad*.  

Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.   

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](log-analytics-tutorial-viewdata.md)
