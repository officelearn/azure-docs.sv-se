---
title: "Samla in data från lokala Windows-datorer med Azure Log Analytics | Microsoft Docs"
description: "Lär dig att distribuera agenten för Windows som körs på datorer utanför Azure logganalys och aktivera insamling av data med logganalys."
services: log-analytics
documentationcenter: log-analytics
author: MGoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 10/16/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 6da36184baee921c828b037e1337df2d14c79462
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-from-windows-computers-hosted-in-your-environment"></a>Samla in data från Windows-datorer i din miljö
[Azure logganalys](log-analytics-overview.md) kan samla in data direkt från din fysiska eller virtuella Windows-datorer och andra resurser i din miljö till en databas för detaljerad analys och korrelation.  Den här snabbstarten visar hur du konfigurerar och samla in data från din Windows-dator med några enkla steg.  Virtuella Azure Windows-datorer finns i följande avsnitt [samla in data om Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  
 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure-portal"></a>Logga in på Azure-portalen
Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.<br><br> ![Azure Portal](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Klicka på **skapa**, och välj sedan alternativ för följande objekt:

  * Ange ett namn för den nya **OMS-arbetsytan**, som *DefaultLAWorkspace*. 
  * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
  * För **resursgruppen**, Välj en befintlig resursgrupp som innehåller en eller flera virtuella Azure-datorer.  
  * Välj den **plats** dina virtuella datorer distribueras till.  Mer information finns i som [regioner Log Analytics är tillgängligt i](https://azure.microsoft.com/regions/services/).
  * Du kan välja mellan tre olika **prisnivåer** i logganalys, men för denna Snabbstart som du ska välja den **ledigt** nivå.  Mer information om de specifika nivåerna finns [Log Analytics-prisinformation](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. När du har angett informationen som krävs på den **OMS-arbetsytan** rutan klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsyte-ID och nyckel
Innan du installerar Microsoft Monitoring Agent för Windows, måste det arbetsyte-ID och nyckel för logganalys-arbetsytan.  Den här informationen krävs av guiden Konfigurera agenten och se till att den kan kommunicera med logganalys korrekt.  

1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
2. Välj i listan över logganalys arbetsytor *DefaultLAWorkspace* skapade tidigare.
3. Välj **avancerade inställningar**.<br><br> ![Logganalys-avancerade inställningar](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **anslutna källor**, och välj sedan **Windows-servrar**.   
5. Värdet till höger om **arbetsyte-ID** och **primärnyckel**. Kopiera och klistra in både i din favorit-redigeraren.   

## <a name="install-the-agent-for-windows"></a>Installera agenten för Windows
Följande steg installera och konfigurera agenten för Log Analytics i Azure och Azure Government molnet med hjälp av installationsprogrammet för Microsoft Monitoring Agent på datorn.  

1. På den **Windows-servrar** väljer rätt **ladda ned Windows Agent** versionen för att hämta beroende på processorarkitektur av Windows-operativsystemet.
2. Kör installationsprogrammet för att installera agenten på datorn.
2. På den **Välkommen** klickar du på **nästa**.
3. På den **licensvillkoren** , Läs licensvillkoren och klickar sedan på **jag accepterar**.
4. På den **målmappen** , ändra eller behålla standardinstallationsmappen och klickar sedan på **nästa**.
5. På den **installationsalternativ för Agent** väljer Anslut agenten till Azure logganalys (OMS) och klicka sedan på **nästa**.   
6. På den **Azure logganalys** utför följande:
   1. Klistra in den **arbetsyte-ID** och **Arbetsytenyckel (primärnyckel)** som du kopierade tidigare.  Om datorn ska rapportera till logganalys-arbetsytan i Azure Government molnet, väljer **Azure som tillhör amerikanska myndigheter** från den **Azure-molnet** listrutan.  
   2. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten klickar du på **Avancerat** och ange en URL och portnummer för proxyservern.  Om proxyservern kräver autentisering, skriver du användarnamn och lösenord för att autentisera med proxyservern och klicka sedan på **nästa**.  
7. Klicka på **nästa** när du har slutfört att tillhandahålla de nödvändiga konfigurationsinställningarna.<br><br> ![Klistra in arbetsyte-ID och primärnyckel](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)<br><br>
8. På den **klar att installera** granskar du dina val och klicka sedan på **installera**.
9. På den **konfigurationen slutförts** klickar du på **Slutför**.

När du är färdig den **Microsoft Monitoring Agent** visas i **Kontrollpanelen**. Du kan granska din konfiguration och kontrollera att agenten är ansluten till logganalys. När du är ansluten till den **Azure logganalys (OMS)** fliken agenten visas ett meddelande om: **i Microsoft Monitoring Agent har lyckats ansluta till tjänsten Microsoft Operations Management Suite.**<br><br> ![MMA anslutningsstatusen till logganalys](media/log-analytics-quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda
Logganalys kan samla in händelser från Windows-händelseloggen och prestandaräknare som du anger för längre sikt analys och rapportering och vidta åtgärder när ett visst villkor har identifierats.  Följ dessa steg om du vill konfigurera insamling av händelser från Windows-händelseloggen och flera prestandaräknare från början.  

1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
2. Välj **avancerade inställningar**.<br><br> ![Logganalys-avancerade inställningar](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br> 
3. Välj **Data**, och välj sedan **Windows-händelseloggar**.  
4. Du kan lägga till en händelselogg genom att skriva namnet på loggen.  Typen **System** och sedan klicka på plustecknet  **+** .  
5. I tabellen, kontrollera allvarlighetsgraderna **fel** och **varning**.   
6. Klicka på **spara** längst upp på sidan för att spara konfigurationen.
7. Välj **Windows prestandadata** att aktivera insamling av prestandaräknare på en Windows-dator. 
8. När du först konfigurera Windows-prestandaräknare för en ny logganalys-arbetsyta möjlighet du att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.<br> ![Standard Windows prestandaräknare valt](media/log-analytics-quick-collect-azurevm/windows-perfcounters-default.png).<br> Klicka på **Lägg till valda prestandaräknare**.  De läggs till och förinställningen med provintervall tio andra samlingen.  
9. Klicka på **spara** längst upp på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in
Nu när du har aktiverat insamling av data, kan du köra en enkel logg Sök exemplet för att se vissa data från måldatorn.  

1. I Azure-portalen under den valda arbetsytan klickar du på den **loggen Sök** panelen.  
2. I fönstret loggen Sök i frågan fälttypen `Perf` och sedan trycker eller klicka på sökknappen till höger om fältet fråga.<br><br> ![Logganalys logga Sök frågan exempel](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Till exempel returnerade frågan i följande bild 735 uppgifter.<br><br> ![Logganalys logga sökresultat](media/log-analytics-quick-collect-windows-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser
När de inte längre behövs kan du ta bort agenten från Windows-datorn och ta bort logganalys-arbetsytan.  

Utför följande steg för att ta bort agenten.

1. Öppna **Kontrollpanelen**.
2. Öppna **program och funktioner**.
3. I **program och funktioner**väljer **Microsoft Monitoring Agent** och på **avinstallera**.

Om du vill ta bort arbetsytan, Välj logganalys-arbetsytan som du skapade tidigare och på sidan i resursen **ta bort**.<br><br> ![Ta bort logganalys-resurs](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg
Nu när du samlar in operativa och prestandadata från din lokala Linux-dator, kan du enkelt kan börja utforska, analysera och vidtar åtgärder på data som du samlar in för *ledigt*.  

Information om hur du visar och analyserar data, även i fortsättningen kursen.   

> [!div class="nextstepaction"]
> [Visa och analysera data i logganalys](log-analytics-tutorial-viewdata.md)
