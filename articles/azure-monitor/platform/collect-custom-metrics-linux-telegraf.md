---
title: Samla in anpassade mått för virtuella Linux-datorer med InfluxData Telegraf-agenten
description: Instruktioner om hur du distribuerar InfluxData Telegraf-agenten på en Virtuell Linux-dator i Azure och konfigurerar agenten för att publicera mått till Azure Monitor.
author: anirudhcavale
services: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 0ed9144116c1d716124025ef0aae39e7783c5934
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655471"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Samla in anpassade mått för en virtuell Linux-dator med Agenten InfluxData Telegraf

Genom att använda Azure Monitor kan du samla in anpassade mått via programtelemetrin, en agent som körs på dina Azure-resurser eller till och med övervakningssystem utanför in. Sedan kan du skicka dem direkt till Azure Monitor. Den här artikeln innehåller instruktioner om hur du distribuerar [InfluxData](https://www.influxdata.com/) Telegraf-agenten på en Virtuell Linux-dator i Azure och konfigurerar agenten för att publicera mått till Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>TillströmningData Telegraf agent 

[Telegraf](https://docs.influxdata.com/telegraf/) är en plug-in-driven agent som möjliggör insamling av mätvärden från över 150 olika källor. Beroende på vilka arbetsbelastningar som körs på den virtuella datorn kan du konfigurera agenten så att den använder specialiserade plugin-plugin-program för att samla in mått. Exempel är MySQL, NGINX och Apache. Genom att använda plugin-program för utdata kan agenten sedan skriva till destinationer som du väljer. Telegraf-agenten har integrerats direkt med Azure Monitor anpassade mått REST API. Den stöder ett plugin-program för Azure Monitor-utdata. Genom att använda det här plugin-programmet kan agenten samla in arbetsbelastningsspecifika mått på din Virtuella Linux-dator och skicka dem som anpassade mått till Azure Monitor. 

 ![Telegraph agent översikt](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Skicka anpassade mått 

För den här självstudien distribuerar vi en Virtuell Linux-dator som kör Ubuntu 16.04 LTS-operativsystemet. Telegraf-agenten stöds för de flesta Linux-operativsystem. Både Debian- och RPM-paket finns tillgängliga tillsammans med oförpackade Linux-binärfiler på [downloadportalen InfluxData](https://portal.influxdata.com/downloads). Mer information och alternativ finns i [installationsguiden för Telegraf.](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) 

Logga in på [Azure-portalen](https://portal.azure.com).

Skapa en ny Virtuell Linux-dator: 

1. Välj alternativet **Skapa en resurs** i det vänstra navigeringsfönstret. 
1. Sök efter **virtuell dator**.  
1. Välj **Ubuntu 16.04 LTS** och välj **Skapa**. 
1. Ange ett VM-namn som **MyTelegrafVM**.  
1. Lämna disktypen som **SSD**. Ange sedan ett **användarnamn**, till exempel **azureuser**. 
1. För **autentiseringstyp**väljer du **Lösenord**. Ange sedan ett lösenord som du ska använda senare till SSH i den här virtuella datorn. 
1. Välj att **skapa en ny resursgrupp**. Ange sedan ett namn, till exempel **myResourceGroup**. Välj **plats**. Välj sedan **OK**. 

    ![Skapa en virtuell Ubuntu-dator](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Välj en storlek för den virtuella datorn. Du kan till exempel filtrera efter **Beräkningstyp** eller **Disktyp**. 

    ![Översikt över Telegraph-agent för virtuell maskin](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. På sidan **Inställningar** i**Säkerhetsgrupp** > För **nätverk** > **Välj offentliga inkommande portar**väljer du **HTTP** och **SSH (22)**. Lämna resten av standardinställningarna och välj **OK**. 

1. På sammanfattningssidan klickar du på **Skapa** för att starta distributionen av den virtuella datorn. 

1. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen av den virtuella datorn automatiskt. 

1. I fönstret Virtuell dator navigerar du till fliken **Identitet.** Se till att den virtuella datorn har en systemtilldelad identitet inställd **på På**. 
 
    ![Förhandsgranskning av telegraf-VM-identitet](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Anslut till VM:en 

Skapa en SSH-anslutning med den virtuella datorn. Välj **Anslut**-knappen på översiktssidan för din virtuella dator. 

![Översiktssida för Telegraf-vm](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

På sidan **Anslut till den virtuella datorn** behåller du standardalternativen för att ansluta med DNS-namn via port 22. I **Logga in med vm-lokalt konto**visas ett anslutningskommando. Markera knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Klistra in kommandot SSH-anslutning i ett skal, till exempel Azure Cloud Shell eller Bash på Ubuntu i Windows, eller använd en SSH-klient som du väljer för att skapa anslutningen. 

## <a name="install-and-configure-telegraf"></a>Installera och konfigurera Telegraf 

Om du vill installera Telegrafs Debianpaket på den virtuella datorn kör du följande kommandon från din SSH-session: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegrafs konfigurationsfil definierar Telegrafs verksamhet. Som standard installeras en exempelkonfigurationsfil på sökvägen **/etc/telegraf/telegraf.conf**. Exempelkonfigurationsfilen visar alla möjliga plugin-program för in- och utdata. Vi skapar dock en anpassad konfigurationsfil och låter agenten använda den genom att köra följande kommandon: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Den föregående koden gör det bara två ingångsplug-ins: **cpu** och **mem**. Du kan lägga till fler plugin-program för indata, beroende på vilken arbetsbelastning som körs på datorn. Exempel är Docker, MySQL och NGINX. En fullständig lista över plugin-program för indata finns i avsnittet **Ytterligare konfiguration.** 

Slutligen, för att få agenten att börja använda den nya konfigurationen, tvingar vi agenten att stoppa och börja med att köra följande kommandon: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Nu samlar agenten in mått från vart och ett av de angivna plugin-programmen för indata och skickar dem till Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Rita dina Telegraf-mått i Azure-portalen 

1. Öppna [Azure-portalen](https://portal.azure.com). 

1. Navigera till den nya fliken **Bildskärm.** Välj sedan **Mått**.  

     ![Övervaka - Mått (förhandsgranskning)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Välj din virtuella dator i resursväljaren.

     ![Måttdiagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Välj namnområdet **Telegraf/CPU** och välj **usage_system** mått. Du kan välja att filtrera efter dimensionerna på det här måttet eller dela på dem.  

     ![Välj namnområde och mått](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Ytterligare konfiguration 

Den föregående genomgången innehåller information om hur du konfigurerar Telegraf-agenten för att samla in mått från några grundläggande plugin-program för indata. Telegraf-agenten har stöd för över 150 plugin-program för ingång, med några stöd för ytterligare konfigurationsalternativ. InfluxData har publicerat en [lista över plugins](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) och instruktioner som stöds om [hur du konfigurerar dem](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Dessutom, i den här genomgången, du använde Telegraf agenten för att avge mått om den virtuella datorn agenten distribueras på. Telegraf-agenten kan också användas som insamlare och vidarebefordrare av mått för andra resurser. Mer information om hur du konfigurerar agenten för att avge mått för andra Azure-resurser finns i [Azure Monitor Custom Metric Output for Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Rensa resurser 

När de inte längre behövs kan du ta bort resursgruppen, den virtuella datorn och alla relaterade resurser. Om du vill göra det markerar du resursgruppen för den virtuella datorn och väljer **Ta bort**. Bekräfta sedan namnet på resursgruppen som ska tas bort. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).



