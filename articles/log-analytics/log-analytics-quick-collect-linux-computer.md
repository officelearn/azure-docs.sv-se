---
title: "Samla in data från lokala Linux-datorer med Azure Log Analytics | Microsoft Docs"
description: "Lär dig hur du distribuerar Log Analytics-agenten för Linux och aktiverar insamling av data från det OS med Log Analytics."
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
ms.date: 12/14/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: fae88e44ee763a0c59b4ad2c731d77db379aa1ee
ms.sourcegitcommit: 3fca41d1c978d4b9165666bb2a9a1fe2a13aabb6
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/15/2017
---
# <a name="collect-data-from-linux-computers-hosted-in-your-environment"></a>Samla in data från Linux-datorer i din miljö
Med [Azure Log Analytics](log-analytics-overview.md) kan du samla in data direkt från fysiska eller virtuella Linux-datorer och andra resurser i din miljö till en enda lagringsplats för detaljerad analys och korrelation.  Den här snabbstarten visar hur du konfigurerar och samlar in data från Linux-datorer med några enkla steg.  För virtuella Linux-datorer, se avsnittet [Samla in data om virtuella datorer i Azure](log-analytics-quick-collect-azurevm.md).  

Information om nätverks- och systemkraven för att distribuera Linux-agenten finns i [Collect data from your environment with Azure Log Analytics](log-analytics-concept-hybrid.md#prerequisites) (Samla in data från din miljö med Azure Log Analytics).

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="log-in-to-azure-portal"></a>Logga in på Azure Portal
Logga in på Azure Portal på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta
1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.<br><br> ![Azure-portalen](media/log-analytics-quick-collect-azurevm/azure-portal-01.png)<br><br>  
2. Klicka på **Skapa** och välj sedan alternativ för följande objekt:

  * Ange ett namn för den nya **OMS-arbetsytan**, som *DefaultLAWorkspace*. 
  * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
  * För **Resursgrupp** väljer du en befintlig resursgrupp som innehåller en eller flera virtuella datorer i Azure.  
  * Välj den **plats** där dina virtuella datorer distribueras.  Mer information finns i avsnittet om [tillgängliga regioner för Log Analytics](https://azure.microsoft.com/regions/services/).
  * Du kan välja mellan tre olika **prisnivåer** i Log Analytics, men för den här snabbstarten ska du välja den **kostnadsfria** nivån.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).

        ![Create Log Analytics resource blade](./media/log-analytics-quick-collect-azurevm/create-loganalytics-workspace-01.png)<br>  
3. När du har angett den nödvändiga informationen i fönsterrutan **OMS-arbetsyta** klickar du på **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans ID och nyckel
Innan du installerar OMS-agenten för Linux behöver du arbetsytans id och nyckel för Log Analytics-arbetsytan.  Den här informationen krävs av agentens adapterskript för att agenten ska konfigureras ordentligt och kunna kommunicera med Log Analytics.  

1. I Azure Portal klickar du på knappen **Fler tjänster** längst upp till vänster. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics**.
2. Välj *DefaultLAWorkspace* som du skapade tidigare i listan med Log Analytics-arbetsytor.
3. Välj **Avancerade inställningar**.<br><br> ![Avancerade inställningar i Log Analytics](media/log-analytics-quick-collect-azurevm/log-analytics-advanced-settings-01.png)<br><br>  
4. Välj **Anslutna källor** och välj sedan **Linux-servrar**.   
5. Värdet till höger om **Id för arbetsyta** och **Primär nyckel**. Kopiera och klistra in båda två i ditt favoritredigeringsprogram.   

## <a name="install-the-agent-for-linux"></a>Installera agenten för Linux
Med följande steg konfigurerar du installationen av agenten för Log Analytics i Azure- och Azure Government-molnet.  

>[!NOTE]
>OMS-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics--arbetsyta.  

1. Konfigurera Linux-datorn att ansluta till Log Analytics genom att köra följande kommando och ange arbetsytans id och den primära nyckeln som du kopierade tidigare.  Med det här kommandot laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn att ansluta till Log Analytics i Azure Government-molnet ska du köra följande kommando och ange arbetsytans id och den primära nyckeln som du kopierade tidigare.  Med det här kommandot laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

## <a name="configure-agent-to-communicate-with-a-proxy-server"></a>Konfigurera agenten att kommunicera med en proxyserver

Utför följande steg om Linux-datorerna måste kommunicera med Log Analytics via en proxyserver.  Konfigurationsvärdet för proxyn har följande syntax `[protocol://][user:password@]proxyhost[:port]`.

1. Redigera filen `/etc/opt/microsoft/omsagent/proxy.conf` genom att köra följande kommandon och ändra värdena enligt dina specifika inställningar.

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
Log Analytics kan samla in händelser från Linux Syslog och prestandaräknare som du anger för analys och rapportering på längre sikt samt vidta åtgärder när ett visst villkor har identifierats.  Följ dessa steg om du vill konfigurera insamling av händelser från Linux Syslog och flera vanliga prestandaräknare till att börja med.  

1. Välj **Syslog**.  
2. Du kan lägga till en händelselogg genom att skriva namnet på loggen.  Skriv **Syslog** och klicka sedan på plustecknet **+**.  
3. Avmarkera allvarlighetsgraderna **Info**, **Meddelande** och **Felsök** i tabellen. 
4. Klicka på **Spara** överst på sidan för att spara konfigurationen.
5. Välj **Linux-prestandadata** för att aktivera insamling av prestandaräknare på en Windows-dator. 
6. När du först konfigurerar Linux-prestandaräknare för en ny Log Analytics-arbetsyta har du möjlighet att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.<br><br> ![Standardalternativen för Windows-prestandaräknare markerade](media/log-analytics-quick-collect-azurevm/linux-perfcounters-default.png).<br><br> Klicka på **Lägg till valda prestandaräknare**.  De läggs till med en förinställning av provintervall på tio sekunder.  
7. Klicka på **Spara** överst på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in
Nu när du har aktiverat insamling av data kan du köra en enkel loggsökning för att se vissa data från måldatorn.  

1. Gå till Azure Portal, navigera till Log Analytics och välj den arbetsyta du skapade tidigare.
2. Klicka på panelen **Loggsökning** och i fönsterrutan Loggsökning skriver du sedan `Perf` i frågefältet och trycker på retur eller klickar på sökknappen till höger om fältet.<br><br> ![Exempel på loggsökningsfråga i Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-portal-queryexample.png)<br><br> Frågan i följande bild returnerar till exempel 735 prestandaposter.<br><br> ![Resultat av loggsökning i Log Analytics](media/log-analytics-quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser
När den inte längre behövs kan du ta bort agenten från Linux-datorn och ta bort Log Analytics-arbetsytan.  

Utför följande steg för att ta bort agenten.

1. Ladda ned Linux-agenten [universal script](https://github.com/Microsoft/OMS-Agent-for-Linux/releases) till datorn.
2. Kör paketets SH-fil med argumentet *--purge* på datorn, som fullständigt tar bort agenten och dess konfiguration.

    `sudo sh ./omsagent-<version>.universal.x64.sh --purge`

Om du vill ta bort arbetsytan väljer du den Log Analytics-arbetsyta som du skapade tidigare och på resurssidan klickar du på **Ta bort**.<br><br> ![Ta bort Log Analytics-resurs](media/log-analytics-quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg
Nu när du kan samla in funktions- och prestandadata från din lokala Linux-dator kan du enkelt kan börja utforska, analysera och vidta åtgärder på data som du samlar in *utan kostnad*.  

Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.   

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](log-analytics-tutorial-viewdata.md)
