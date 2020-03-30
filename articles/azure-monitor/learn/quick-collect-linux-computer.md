---
title: 'Snabbstart: Samla in data från en hybrid-Linux-dator med Azure Monitor'
description: I den här snabbstarten får du lära dig hur du distribuerar Log Analytics-agenten för Linux-datorer som körs utanför Azure och aktiverar datainsamling med Azure Monitor Logs.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 12/24/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-september2019, seo-javascript-october2019
ms.openlocfilehash: 0cf171da5f473a0d78fc5f37139c94b0e519bcfb
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79241264"
---
# <a name="quickstart-collect-data-from-a-linux-computer-in-a-hybrid-environment-with-azure-monitor"></a>Snabbstart: Samla in data från en Linux-dator i en hybridmiljö med Azure Monitor

[Azure Monitor](../overview.md) kan samla in data direkt från dina fysiska eller virtuella Linux-datorer i din miljö till en Log Analytics-arbetsyta för detaljerad analys och korrelation. Genom att installera [Log Analytics-agenten](../platform/log-analytics-agent.md) kan Azure Monitor samla in data från ett datacenter eller annan molnmiljö. Den här snabbstarten visar hur du konfigurerar och samlar in data från din Linux-server med några enkla steg. Information om virtuella Azure Linux-datorer finns [i Samla in data om virtuella Azure-datorer](../../azure-monitor/learn/quick-collect-azurevm.md).  

Information om hur du förstår konfigurationen som stöds finns i [Windows-operativsystem](../../azure-monitor/platform/log-analytics-agent.md#supported-linux-operating-systems) som stöds och [konfigurationen för nätverksbrandväggen](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-the-azure-portal"></a>Logga in på Azure Portal

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.

    ![Hitta Log Analytics-arbetsytan i Azure-portalen](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Välj **Skapa**och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*.  
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **Resursgrupp** väljer du en befintlig resursgrupp som innehåller en eller flera virtuella datorer i Azure.  
   * Välj den **plats** där dina virtuella datorer distribueras.  Mer information finns i avsnittet om [tillgängliga regioner för Log Analytics](https://azure.microsoft.com/regions/services/).
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 april eller en prenumeration som var bunden till en befintlig EA-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Skapa en Log Analytics-arbetsyta i Azure-portalen](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. När du har lämnat den information som krävs i **arbetsytan Log Analytics** väljer du **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 

## <a name="obtain-workspace-id-and-key"></a>Hämta arbetsytans id och nyckel

Innan du installerar Log Analytics-agenten för Linux behöver du arbetsytans id och nyckel för Log Analytics-arbetsytan. Den här informationen krävs av agentomslagsskriptet för att korrekt konfigurera agenten och se till att den kan kommunicera med Azure Monitor.

[!INCLUDE [log-analytics-agent-note](../../../includes/log-analytics-agent-note.md)]  

1. I det övre vänstra hörnet av Azure-portalen väljer du **Alla tjänster**. Skriv **Logganalys**i sökrutan . När du skriver filtrerar listan baserat på dina indata. Välj **Log Analytics-arbetsytor**.

2. Välj arbetsytan som du skapade tidigare i listan över Log Analytics-arbetsytor. (Du kanske har döpt den **till DefaultLAWorkspace**.)

3. Välj **Avancerade inställningar:**

    ![Menyn Avancerade inställningar för Logganalys i Azure-portalen](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png) 
 
4. Välj **Anslutna källor** och välj sedan **Linux-servrar**.

5. Värdet till höger om **Id för arbetsyta** och **Primär nyckel**. Kopiera och klistra in båda två i det redigeringsprogram du föredrar.

## <a name="install-the-agent-for-linux"></a>Installera agenten för Linux

Med följande steg konfigurerar du installationen av agenten för Log Analytics i Azure- och Azure Government-molnet.  

>[!NOTE]
>Log Analytics-agenten för Linux kan inte konfigureras att rapportera till fler än en Log Analytics-arbetsyta.  

Om Linux-datorn behöver kommunicera via en proxyserver till Log Analytics kan du ange proxykonfigurationen på kommandoraden genom att inkludera `-p [protocol://][user:password@]proxyhost[:port]`.  Egenskapen *proxyhost* accepterar ett fullständigt domännamn eller en fullständig IP-adress för proxyservern. 

Exempel: `https://user01:password@proxy01.contoso.com:30443`

1. Om du vill konfigurera Linux-datorn för att ansluta till en Log Analytics-arbetsyta kör du följande kommando som ger arbetsyte-ID och primärnyckeln som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 
    
    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

    Följande kommando innehåller `-p` proxyparametern och exempelsyntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY>
    ```

2. Om du vill konfigurera Linux-datorn för att ansluta till Log Analytics-arbetsytan i Azure Government-molnet kör du följande kommando som ger arbetsytans ID och primärnyckeln som kopierats tidigare. Med följande kommando laddar du ned agenten, verifierar dess kontrollsumma och installerar den. 

    ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ``` 

    Följande kommando innehåller `-p` proxyparametern och exempelsyntaxen när autentisering krävs av proxyservern:

   ```
    wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh -p [protocol://][user:password@]proxyhost[:port] -w <YOUR WORKSPACE ID> -s <YOUR WORKSPACE PRIMARY KEY> -d opinsights.azure.us
    ```

3. Starta om agenten genom att köra följande kommando: 

    ```
    sudo /opt/microsoft/omsagent/bin/service_control restart [<workspace id>]
    ``` 

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda

Azure Monitor kan samla in händelser från Linux Syslog och prestandaräknare som du anger för långsiktig analys och rapportering. Det kan också vidta åtgärder när den upptäcker ett visst villkor. Följ dessa steg om du vill konfigurera insamling av händelser från Linux Syslog och flera vanliga prestandaräknare till att börja med.  

1. I det nedre vänstra hörnet av Azure-portalen väljer du **Fler tjänster**. Skriv **Logganalys**i sökrutan . När du skriver filtrerar listan baserat på dina indata. Välj **Log Analytics-arbetsytor**.

2. Välj **Data**och välj sedan **Syslog**.  

3. Du lägger till syslog genom att skriva in loggens namn. Ange **Syslog** och välj **+** sedan plustecknet .  

4. Avmarkera allvarlighetsgraderna **Info**, **Meddelande** och **Felsök** i tabellen. 

5. Välj **Spara** högst upp på sidan för att spara konfigurationen.

6. Välj **Linux-prestandadata** för att aktivera insamling av prestandaräknare på en Linux-dator. 

7. När du först konfigurerar Linux-prestandaräknare för en ny Log Analytics-arbetsyta har du möjlighet att snabbt skapa flera vanliga räknare. De listas med en kryssruta bredvid varje.

    ![Standardräknare för Linux-prestanda som valts i Azure Monitor](media/quick-collect-azurevm/linux-perfcounters-azure-monitor.png)

    Välj **Använd under konfiguration till mina datorer** och välj sedan Lägg till de valda **prestandaräknarna**. De läggs till med en förinställning av provintervall på tio sekunder.  

8. Välj **Spara** högst upp på sidan för att spara konfigurationen.

## <a name="view-data-collected"></a>Visa data som samlas in

Nu när du har aktiverat insamling av data kan du köra en enkel loggsökning för att se vissa data från måldatorn.  

1. Välj **Loggar**i den valda arbetsytan i den vänstra rutan .

2. Skriv `Perf` in frågeredigeraren på frågesidan loggar och välj **Kör**.
 
    ![Logganalysloggsökning](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Frågan i följande bild returnerade till exempel 10 000 prestandaposter. Ditt resultatet blir mycket mindre.

    ![Resultat av loggsökning i Log Analytics](media/quick-collect-linux-computer/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser

När den inte längre behövs kan du ta bort agenten från Linux-datorn och ta bort Log Analytics-arbetsytan.  

Kör följande kommando för att ta bort agenten på Linux-datorn. Argumentet *--purge* tar bort agenten och dess konfiguration fullständigt.

   `wget https://raw.githubusercontent.com/Microsoft/OMS-Agent-for-Linux/master/installer/scripts/onboard_agent.sh && sh onboard_agent.sh --purge`

Om du vill ta bort arbetsytan markerar du arbetsytan Log Analytics som du skapade tidigare och väljer **Ta bort**på resurssidan .

![Ta bort Log Analytics-resurs](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg

Nu när du kan samla in funktions- och prestandadata från din lokala Linux-dator kan du enkelt kan börja utforska, analysera och vidta åtgärder på data som du samlar in *utan kostnad*.  

Om du vill lära dig hur du visar och analyserar data kan du fortsätta till självstudiekursen.

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](../../azure-monitor/learn/tutorial-viewdata.md)
