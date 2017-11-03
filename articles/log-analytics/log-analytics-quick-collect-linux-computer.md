---
title: "Samla in data från lokala Linux-datorer med Azure Log Analytics | Microsoft Docs"
description: "Lär dig hur du distribuerar logganalys-agent för Linux och aktivera insamling av data från den OS med logganalys."
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
ms.date: 10/13/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: d22fe6456c3bd886f8f8863d362c0084fbe03da3
ms.sourcegitcommit: e6029b2994fa5ba82d0ac72b264879c3484e3dd0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Samla in data från Linux-datorer i din miljö
[Azure logganalys](log-analytics-overview.md) kan samla in data direkt från din fysiska eller virtuella Linux-datorer och andra resurser i din miljö till en databas för detaljerad analys och korrelation.  Den här snabbstarten visar hur du konfigurerar och samla in data från ditt Linux-dator med några enkla steg.  Virtuella Azure Linux-datorer finns i följande avsnitt [samla in data om Azure Virtual Machines](log-analytics-quick-collect-azurevm.md).  
 
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

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. När du har angett informationen som krävs på den **OMS-arbetsytan** rutan klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsyte-ID och nyckel
Innan du installerar OMS-agent för Linux måste arbetsyte-ID och nyckel för logganalys-arbetsytan.  Den här informationen krävs av skriptet agent wrapper konfigurera agenten och se till att den kan kommunicera med logganalys korrekt.  

1. I Azure-portalen klickar du på **fler tjänster** hittades i det nedre vänstra hörnet. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **logga Analytics**.
2. Välj i listan över logganalys arbetsytor *DefaultLAWorkspace* skapade tidigare.
3. Välj **avancerade inställningar**.<br><br> ![Logganalys-avancerade inställningar](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **anslutna källor**, och välj sedan **Linux-servrar**.   
5. Värdet till höger om **arbetsyte-ID** och **primärnyckel**. Kopiera och klistra in både i din favorit-redigeraren.   

## <a name="install-the-agent-for-linux"></a>Installera agenten för Linux
Följande steg kan du konfigurera installationen av agenten för logganalys i Azure och Azure Government moln.  

1. Kör följande kommando för att ange arbetsyte-ID och primärnyckel kopierade tidigare för att konfigurera Linux-dator att ansluta till logganalys.  Detta kommando hämtar agenten, verifierar dess kontrollsumma och installerar den. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Kör följande kommando tillhandahåller arbetsyte-ID och primärnyckel kopierade tidigare för att konfigurera Linux-dator att ansluta till logganalys i Azure Government-molnet.  Detta kommando hämtar agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Konfigurera agenten kan kommunicera med en proxyserver

Utför följande steg om Linux-datorer som behöver kommunicera via en proxyserver till logganalys.  Konfigurationsvärdet proxy har följande syntax `[protocol://][user:password@]proxyhost[:port]`.

1. Redigera filen `/etc/opt/microsoft/omsagent/proxy.conf` genom att köra följande kommandon och ändra värdena för dina specifika inställningar.

    ```
    proxyconf="https://proxyuser:proxypassword@proxyserver01:30443"
    sudo echo $proxyconf >>/etc/opt/microsoft/omsagent/proxy.conf
    sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/proxy.conf 
    ```

2. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda
Logganalys kan samla in händelser från Linux Syslog och prestandaräknare som du anger för längre sikt analys och rapportering och vidta åtgärder när ett visst villkor har identifierats.  Följ dessa steg om du vill konfigurera insamling av händelser från Linux Syslog och flera prestandaräknare från början.  

1. Välj **Syslog**.  
2. Du kan lägga till en händelselogg genom att skriva namnet på loggen.  Typen **Syslog** och sedan klicka på plustecknet  **+** .  
3. I tabellen, avmarkera allvarlighetsgraderna **Info**, **meddelande** och **felsöka**. 
4. Klicka på **spara** längst upp på sidan för att spara konfigurationen.
5. Välj **Linux prestandadata** att aktivera insamling av prestandaräknare på en Windows-dator. 
6. När du först konfigurerar Linux prestandaräknare för en ny logganalys-arbetsyta möjlighet du att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.<br><br> ![Standard Windows prestandaräknare valt](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Klicka på **Lägg till valda prestandaräknare**.  De läggs till och förinställningen med provintervall tio andra samlingen.  
7. Klicka på **spara** längst upp på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in
Nu när du har aktiverat insamling av data, kan du köra en enkel logg Sök exemplet för att se vissa data från måldatorn.  

1. Gå till logganalys och markera arbetsytan skapade tidigare i Azure-portalen.
2. Klicka på den **loggen Sök** panelen och i fönstret loggen Sök i frågan fälttypen `Perf` och sedan trycker eller klicka på sökknappen till höger om fältet fråga.<br><br> ![Logganalys logga Sök frågan exempel](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Till exempel returnerade frågan i följande bild 735 uppgifter.<br><br> ![Logganalys logga sökresultat](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser
När de inte längre behövs kan du ta bort agenten från Linux-datorn och ta bort logganalys-arbetsytan.  

Utför följande steg för att ta bort agenten.

1. Hämta den Linux-agenten [universal skriptet](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) till datorn.
2. Kör paket .sh filen w den *--Rensa* argumentet på datorn som helt tar bort agenten och dess konfiguration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

Om du vill ta bort arbetsytan, Välj logganalys-arbetsytan som du skapade tidigare och på sidan i resursen **ta bort**.<br><br> ![Ta bort logganalys-resurs](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg
Nu när du samlar in operativa och prestandadata från din lokala Linux-dator, kan du enkelt kan börja utforska, analysera och vidtar åtgärder på data som du samlar in för *ledigt*.  

Information om hur du visar och analyserar data, även i fortsättningen kursen.   

> [!div class="nextstepaction"]
> [Visa och analysera data i logganalys](log-analytics-tutorial-viewdata.md)
