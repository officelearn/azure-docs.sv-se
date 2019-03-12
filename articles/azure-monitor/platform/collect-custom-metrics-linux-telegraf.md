---
title: Samla in anpassade mått för en Linux VM med InfluxData Telegraf-agent
description: Samla in anpassade mått för en Linux VM med InfluxData Telegraf-agent
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 14415b88cd6036642442ef9ae23e8dee301bb908
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/12/2019
ms.locfileid: "57775619"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Samla in anpassade mått för en Linux VM med InfluxData Telegraf-agent

Genom att använda Azure Monitor kan du samla in anpassade mått via din programtelemetri, en agent som körs på din Azure-resurser eller utsidan även i övervakningssystem. Du kan sedan skicka dem direkt till Azure Monitor. Den här artikeln innehåller instruktioner om hur du distribuerar den [InfluxData](https://www.influxdata.com/) Telegraf agenten på en Linux-VM i Azure och konfigurera agenten för att publicera mått i Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf agent 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) är en plug i-drivna agent som möjliggör insamling av mätvärden via 150 olika källor. Beroende på vad arbetsbelastningar körs på den virtuella datorn, kan du konfigurera agenten för att utnyttja specialiserade inkommande plugin-program för att samla in mått. Exempel är Apache, MySQL och NGINX. Agenten kan sedan skriva till mål som du väljer med hjälp av utdata plugin-program. Telegraf agenten har integrerats direkt med REST-API med Azure Monitor, anpassade mått. Det stöder plugin-programmet Azure Monitor utdata. Med den här plugin-programmet agenten kan samla in belastningsspecifikt mått på din Linux-VM och skicka dem som anpassade mått för att Azure Monitor. 

 ![Översikt över övervakningsagenten telegraf](./media/collect-custom-metrics-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Skicka anpassade mått 

I den här självstudien distribuerar vi en Linux VM som kör Ubuntu 16.04 LTS-operativsystem. Telegraf agenten har stöd för de flesta Linux-operativsystem. Både Debian och RPM-paket är tillgängliga tillsammans med oförpackat Linux-binärfiler i den [InfluxData download portal](https://portal.influxdata.com/downloads). Se den här [Telegraf installationsguide](https://docs.influxdata.com/telegraf/v1.8/introduction/installation/) ytterligare installationsinstruktioner och alternativ. 

Logga in på [Azure Portal](https://portal.azure.com).

Skapa en ny Linux-VM: 

1. Välj den **skapa en resurs** i det vänstra navigeringsfönstret. 
1. Sök efter **VM**.  
1. Välj **Ubuntu 16.04 LTS** och välj **skapa**. 
1. Ange ett namn som **MyTelegrafVM**.  
1. Lämna disktyp som **SSD**. Ange sedan en **användarnamn**, till exempel **azureuser**. 
1. För **autentiseringstyp**väljer **lösenord**. Ange ett lösenord använder du senare att SSH till den här virtuella datorn. 
1. Välja att **Skapa ny resursgrupp**. Ange ett namn, till exempel **myResourceGroup**. Välj din **plats**. Välj sedan **OK**. 

    ![Skapa en virtuell Ubuntu-dator](./media/collect-custom-metrics-linux-telegraf/create-vm.png)

1. Välj en storlek för den virtuella datorn. Du kan filtrera efter **typ av databehandling** eller **disktyp**, till exempel. 

    ![Översikt över virtuella datorer storlek telegraf agent](./media/collect-custom-metrics-linux-telegraf/vm-size.png)

1. På den **inställningar** sidan i **nätverk** > **Nätverkssäkerhetsgrupp**   >  ** Välj offentliga inkommande portar**väljer **HTTP** och **SSH (22)**. Lämna resten av standardinställningarna och välj **OK**. 

1. På sidan Sammanfattning väljer **skapa** att starta VM-distributionen. 

1. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för virtuella datorer automatiskt. 

1. I fönstret VM navigerar du till den **identitet** fliken. Kontrollera att den virtuella datorn har en systemtilldelad identitet som angetts till **på**. 
 
    ![Förhandsversion av Telegraf VM identitet](./media/collect-custom-metrics-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Anslut till VM:en 

Skapa en SSH-anslutning med den virtuella datorn. Välj den **Connect** på översiktssidan för den virtuella datorn. 

![Översiktssidan för Telegraf VM](./media/collect-custom-metrics-linux-telegraf/connect-VM-button2.png)

I den **Anslut till den virtuella datorn** behåller standardalternativen för att ansluta med DNS-namn via port 22. I **logga in med lokalt konto för virtuell dator**, visas en anslutningskommandot. Välj knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Klistra in kommandot SSH-anslutning i ett gränssnitt, till exempel Azure Cloud Shell eller Bash i Ubuntu för Windows, eller använda en SSH-klient väljer för att skapa anslutningen. 

## <a name="install-and-configure-telegraf"></a>Installera och konfigurera Telegraf 

Om du vill installera Telegraf Debian-paket till den virtuella datorn kör du följande kommandon från SSH-session: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegrafs konfigurationsfilen definierar Telegrafs åtgärder. Som standard installeras en konfigurationsfil för exempel på sökväg **/etc/telegraf/telegraf.conf**. Konfigurationsfilen exempel visar en lista över alla möjliga indata och utdata plugin-program. Men vi skapar en anpassad konfigurationsfil och agenten använder genom att köra följande kommandon: 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]  
> Föregående kod gör bara två ange plugin-program: **cpu** och **mem**. Du kan lägga till fler inkommande plugin-program, beroende på arbetsbelastningen som körs på din dator. Exempel är Docker, MySQL och NGINX. En fullständig lista över inkommande plugin-program finns i den **ytterligare konfiguration** avsnittet. 

Om du vill att agenten början med hjälp av den nya konfigurationen, kan vi slutligen tvingar agenten att stoppa och starta genom att köra följande kommandon: 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Nu kommer agenten samla in statistik från var och en av de inkommande pluginen som angetts och skickar dem till Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Rita dina Telegraf mått i Azure portal 

1. Öppna [Azure-portalen](https://portal.azure.com). 

1. Gå till den nya **övervakaren** fliken. Välj sedan **mått**.  

     ![Övervaka – mått (förhandsversion)](./media/collect-custom-metrics-linux-telegraf/metrics.png)

1. Välj den virtuella datorn i resursväljaren för.

     ![Måttdiagram](./media/collect-custom-metrics-linux-telegraf/metric-chart.png)

1. Välj den **Telegraf/CPU** namnområdet, och välj den **usage_system** mått. Du kan välja att filtrera efter dimensioner för den här mått eller dela dem.  

     ![Välj namnområde och mått](./media/collect-custom-metrics-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Ytterligare konfiguration 

Den föregående genomgången innehåller information om hur du konfigurerar Telegraf agenten för att samla in statistik från några grundläggande inkommande plugin-program. Telegraf agenten har stöd för över 150 inkommande plugin-program, med några stödjande ytterligare konfigurationsalternativ. InfluxData har publicerat en [listan över stöds plugin-program](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) och anvisningar om [hur du konfigurerar dem](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Dessutom i den här genomgången ska använt du Telegraf agenten för att skapa mått om den virtuella datorn som agenten har distribuerats på. Telegraf-agenten kan också användas som en insamlare och vidarebefordrare av mått för andra resurser. Läs hur du konfigurerar agenten för att skapa mått för andra Azure-resurser i [Azure Monitor anpassade mått utdata för Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Rensa resurser 

När de inte längre behövs kan du ta bort resursgruppen, virtuell dator och alla relaterade resurser. Om du vill göra det väljer du resursgruppen för den virtuella datorn och välj **ta bort**. Bekräfta sedan namnet på resursgruppen som ska ta bort. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).



