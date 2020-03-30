---
title: Samla in data från hybrid Windows-dator med Azure Monitor
description: I den här snabbstarten får du lära dig hur du distribuerar Log Analytics-agenten för Windows-datorer som körs utanför Azure och aktiverar datainsamling med Azure Monitor Logs.
services: azure-monitor
documentationcenter: azure-monitor
author: bwren
manager: carmonm
editor: ''
ms.assetid: ''
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: quickstart
ms.date: 08/22/2019
ms.author: bwren
ms.custom: mvc, seo-javascript-october2019
ms.openlocfilehash: 11b61612a261b32e2d15b5dc70005b18aa112ed4
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79240347"
---
# <a name="collect-data-from-a-windows-computer-in-a-hybrid-environment-with-azure-monitor"></a>Samla in data från en Windows-dator i en hybridmiljö med Azure Monitor

[Azure Monitor](../overview.md) kan samla in data direkt från dina fysiska eller virtuella Windows-datorer i din miljö till en Log Analytics-arbetsyta för detaljerad analys och korrelation. Genom att installera [Log Analytics-agenten](../platform/log-analytics-agent.md) kan Azure Monitor samla in data från ett datacenter eller annan molnmiljö. Den här snabbstarten visar hur du konfigurerar och samlar in data från Windows-datorer med några enkla steg. Information om virtuella Azure Windows-datorer finns [i Samla in data om virtuella Azure-datorer](../../azure-monitor/learn/quick-collect-azurevm.md).  

Information om hur du förstår konfigurationen som stöds finns i [Windows-operativsystem](../../azure-monitor/platform/log-analytics-agent.md#supported-windows-operating-systems) som stöds och [konfigurationen för nätverksbrandväggen](../../azure-monitor/platform/log-analytics-agent.md#network-firewall-requirements).
 
Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) konto innan du börjar.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på Azure-portalen på [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-workspace"></a>Skapa en arbetsyta

1. Välj **Alla tjänster** i Azure-portalen. I listan över resurser skriver du **Log Analytics**. När du börjar skriva filtreras listan baserat på det du skriver. Välj **Log Analytics-arbetsytor**.

    ![Azure Portal](media/quick-collect-azurevm/azure-portal-log-analytics-workspaces.png)<br>  

2. Välj **Skapa**och välj sedan alternativ för följande objekt:

   * Ange ett namn för den nya **Log Analytics-arbetsytan**, som *DefaultLAWorkspace*.  
   * Välj en **prenumeration** att länka till genom att välja från den listrutan om standardvalet inte är lämpligt.
   * För **Resursgrupp** väljer du en befintlig resursgrupp som innehåller en eller flera virtuella datorer i Azure.  
   * Välj den **plats** där dina virtuella datorer distribueras.  Mer information finns i avsnittet om [tillgängliga regioner för Log Analytics](https://azure.microsoft.com/regions/services/).
   * Om du skapar en arbetsyta i en ny prenumeration som skapats efter 2 april 2018 används prisplanen *Per GB* automatiskt och alternativet för att välja en prisnivå är inte tillgängligt.  Om du skapar en arbetsyta för en befintlig prenumeration som skapats före 2 april eller en prenumeration som var bunden till en befintlig EA-registrering, väljer du önskad prisnivå.  Mer information om de olika nivåerna finns i [prisinformation om Log Analytics](https://azure.microsoft.com/pricing/details/log-analytics/).
  
        ![Skapa resursblad för Logganalys](media/quick-collect-azurevm/create-log-analytics-workspace-azure-portal.png) 

3. När du har lämnat den information som krävs i **arbetsytan Log Analytics** väljer du **OK**.  

När informationen har verifierats och arbetsytan skapas, kan du spåra förloppet under **Meddelanden** på menyn. 


## <a name="get-the-workspace-id-and-key"></a>Hämta arbetsyte-ID och -tangenten

Innan du installerar Log Analytics-agenten för Windows (kallas även MICROSOFT Monitoring Agent (MMA) behöver du arbetsytans ID och nyckeln för logganalysarbetsytan. Installationsguiden behöver den här informationen för att korrekt konfigurera agenten och se till att den kan kommunicera med Azure Monitor.  

1. I det övre vänstra hörnet av Azure-portalen väljer du **Alla tjänster**. Skriv **Logganalys**i sökrutan . När du skriver filtrerar listan baserat på dina indata. Välj **Log Analytics-arbetsytor**.

2. Välj arbetsytan som du skapade tidigare i listan över Log Analytics-arbetsytor. (Du kanske har döpt den **till DefaultLAWorkspace**.)

3. Välj **Avancerade inställningar:**

    ![Avancerade inställningar för Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
  
4. Välj **Anslutna källor** och välj sedan **Windows-servrar**.

5. Kopiera värdena till höger om **arbetsyte-ID** och **Primärnyckel**. Klistra in dem i din favoritredigerare.

## <a name="install-the-agent-for-windows"></a>Installera agenten för Windows

Följande steg installerar och konfigurerar agenten för Logganalys i Azure och Azure Government. Du ska använda installationsprogrammet för Microsoft Monitoring Agent för att installera agenten på datorn.

1. Välj den **version hämta Windows Agent** som du vill hämta på sidan Windows-servrar på sidan **Windows-servrar.** Välj lämplig version för processorarkitekturen i operativsystemet Windows.

2. Kör installationsprogrammet för att installera agenten på datorn.

3. På sidan **Välkommen** klickar du på **Nästa**.

4. På sidan **Licensvillkor** läser du licensen och väljer **Jag accepterar**.

5. På sidan **Målmapp** ändrar du eller behåller standardinstallationsmappen och väljer **Nästa**.

6. På sidan **Alternativ för agentinställningar** ansluter du agenten till Azure Log Analytics och väljer sedan **Nästa**.

7. Gör så här på sidan **Azure Log Analytics:**

   1. Klistra in i **arbetsyte-ID** och **arbetsytenyckel (primärnyckel)** som du kopierade tidigare. Om datorn ska rapportera till en Log Analytics-arbetsyta i Azure Government väljer du **Azure US Government** i Azure **Cloud-listan.**  
   2. Om datorn behöver kommunicera via en proxyserver till Log Analytics-tjänsten väljer du **Avancerat** och anger URL och portnummer för proxyservern. Om proxyservern kräver autentisering anger du användarnamnet och lösenordet för autentisering med proxyservern och väljer sedan **Nästa**.  

8. Välj **Nästa** när du har lagt till konfigurationsinställningarna:

    ![Installationsprogrammet för Microsoft Monitoring Agent](media/quick-collect-windows-computer/log-analytics-mma-setup-laworkspace.png)

9. På sidan **Klar att installera** kontrollerar du valen och väljer sedan **Installera**.

10. På sidan **Konfiguration har slutförts** väljer du **Slutför**.

När installationen och installationen är klar visas Microsoft Monitoring Agent på Kontrollpanelen. Du kan granska konfigurationen och kontrollera att agenten är ansluten till log analytics-arbetsytan. När agenten är ansluten på fliken **Azure Log Analytics** visas det här meddelandet: Microsoft Monitoring Agent har anslutit till Microsoft Log **Analytics-tjänsten.**<br><br> ![MMA-anslutningsstatus](media/quick-collect-windows-computer/log-analytics-mma-laworkspace-status.png)

## <a name="collect-event-and-performance-data"></a>Samla in data om händelser och prestanda

Azure Monitor kan samla in händelser som du anger från Windows-händelseloggen och prestandaräknare för långsiktig analys och rapportering. Det kan också vidta åtgärder när den upptäcker ett visst villkor. Följ dessa steg om du vill konfigurera insamling av händelser från Windows-händelseloggen och flera vanliga prestandaräknare till att börja med.  

1. I det nedre vänstra hörnet av Azure-portalen väljer du **Fler tjänster**. Skriv **Logganalys**i sökrutan . När du skriver filtrerar listan baserat på dina indata. Välj **Log Analytics-arbetsytor**.

2. Välj **Avancerade inställningar:**

    ![Avancerade inställningar för Log Analytics](media/quick-collect-azurevm/log-analytics-advanced-settings-azure-portal.png)
 
3. Välj **Data** och sedan **Windows-händelseloggar**.  

4. Du lägger till en händelselogg genom att ange namnet på loggen. Ange **system**och välj sedan**+** plustecknet ( ).  

5. Välj allvarlighetsgraden **Fel** och **Varning** i tabellen.

6. Välj **Spara** högst upp på sidan.

7. Välj **Windows-prestandaräknare** för att aktivera insamling av prestandaräknare i en Windows-dator.

8. När du först konfigurerar Windows-prestandaräknare för en ny Log Analytics-arbetsyta får du möjlighet att snabbt skapa flera vanliga räknare. Varje alternativ visas med en kryssruta bredvid:

    ![Windows-prestandaräknare](media/quick-collect-windows-computer/windows-perfcounters-default.png).
    
    Välj **Lägg till de valda prestandaräknarna**. Räknarna läggs till och förinställs med ett urvalsintervall på tio sekunder.

9. Välj **Spara** högst upp på sidan.

## <a name="view-collected-data"></a>Visa insamlade data

Nu när du har aktiverat datainsamling ska vi köra en enkel loggsökning för att se vissa data från måldatorn.  

1. Välj **Loggar**i den valda arbetsytan i den vänstra rutan .

2. Skriv `Perf` in frågeredigeraren på frågesidan loggar och välj **Kör**.
 
    ![Logganalysloggsökning](media/quick-collect-windows-computer/log-analytics-portal-queryexample.png)

    Frågan i den här bilden returnerade till exempel 10 000 prestandaposter. Ditt resultatet blir mycket mindre.

    ![Resultat av loggsökning i Log Analytics](media/quick-collect-azurevm/log-analytics-search-perf.png)

## <a name="clean-up-resources"></a>Rensa resurser

Du kan ta bort agenten från datorn och ta bort log analytics-arbetsytan om du inte längre behöver dem.  

Så här tar du bort agenten:

1. Öppna Kontrollpanelen.

2. Öppna **Program och funktioner**.

3. I **Program och funktioner**väljer du Microsoft Monitoring **Agent** och väljer sedan **Avinstallera**.

Om du vill ta bort arbetsytan Logganalys som du skapade tidigare markerar du den och väljer **Ta bort**på resurssidan:

![Ta bort logganalysarbetsyta](media/quick-collect-azurevm/log-analytics-portal-delete-resource.png)

## <a name="next-steps"></a>Nästa steg

Nu när du samlar in drift- och prestandadata från din Windows-dator kan du enkelt börja utforska, analysera och agera på de data du samlar *in, gratis*.  

Om du vill veta hur du visar och analyserar data fortsätter du till självstudien:

> [!div class="nextstepaction"]
> [Visa eller analysera data i Log Analytics](tutorial-viewdata.md)
