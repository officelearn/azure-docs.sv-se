---
title: Samla in anpassade mått för en Linux VM med InfluxData Telegraf agenten
description: Samla in anpassade mått för en Linux VM med InfluxData Telegraf agenten
author: anirudhcavale
services: azure-monitor
ms.service: azure-monitor
ms.topic: howto
ms.date: 09/24/2018
ms.author: ancav
ms.component: metrics
ms.openlocfilehash: 651706b269cf24eca85e0601384ef63cb6ed06a2
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/24/2018
ms.locfileid: "46990713"
---
# <a name="collect-custom-metrics-for-a-linux-vm-with-the-influxdata-telegraf-agent"></a>Samla in anpassade mått för en Linux VM med InfluxData Telegraf agenten

Azure Monitor kan du samla in anpassade mått via din programtelemetri, en agent som körs på din Azure-resurser eller utsidan även i övervakningssystem och skicka dem direkt till Azure Monitor. Den här artikeln fokuserar på med anvisningar om hur du distribuerar den [InfluxData](https://www.influxdata.com/) Telegraf agenten på en Linux-VM i Azure och konfigurera agenten för att publicera mått i Azure Monitor. 

## <a name="influxdata-telegraf-agent"></a>InfluxData Telegraf Agent 

[Telegraf](https://docs.influxdata.com/telegraf/v1.7/) är ett plugin-programmet-drivna agent som möjliggör insamling av mätvärden via 150 olika källor. Beroende på vad arbetsbelastningar sedan körs på den virtuella datorn (t.ex. MySQL, NGINX, Apache, osv.) Du kan konfigurera agenten för att utnyttja specialiserade inkommande plugin-program för att samla in mått. Utdata plugin-program och aktivera sedan agenten att skriva till mål du väljer. Telegraf agenten har integrerats direkt med REST-API med Azure Monitor, anpassade mått och stöder ett ”Azure Monitor-utdata plugin-program”. På så sätt kan agenten samla in specifika mått för arbetsbelastning på Linux-VM och skicka dem som anpassade mått till Azure Monitor. 

 ![Översikt över Övervakningsagenten telegraf](./media/metrics-store-custom-linux-telegraf/telegraf-agent-overview.png)

## <a name="send-custom-metrics"></a>Skicka anpassade mått 

I självstudien distribuerar vi en Linux VM som kör operativsystemet Ubuntu 16.04 LTS. Telegraf agenten har stöd för de flesta Linux-operativsystem. Debian- och RPM-paket är tillgängliga tillsammans med oförpackat Linux-binärfiler i InfluxData download-portalen. Se den här installationsguiden för ytterligare Telegraf installationsinstruktioner och alternativ. 

Logga in på den [Azure-portalen](https://portal.azure.com)

Skapa en ny Linux-VM: 

1. Klicka på den **skapa en resurs** i det vänstra navigeringsfönstret. 
1. Sök efter *virtuell dator*  
1. Välj *Ubuntu 16.04 LTS* och klicka på **skapa** 
1. Ange ett namn som *MyTelegrafVM*.  
1. Lämna disktyp som **SSD**, ange sedan en **användarnamn**, till exempel *azureuser*. 
1. För *autentiseringstyp*väljer **lösenord**, skriver in ett lösenord som du använder senare för att SSH till den här virtuella datorn. 
1. Välja att **Skapa ny resursgrupp**, ange ett namn, till exempel *myResourceGroup*.  Välj en valfri plats och välj **OK**. 

     ![Skapa virtuell Ubuntu-dator](./media/metrics-store-custom-linux-telegraf/create-vm.png)

1. Välj en storlek för den virtuella datorn. Du kan filtrera efter beräkningstyp eller disktyp, till exempel. 

     ![VM-storlek Telegraph översikt över Övervakningsagenten](./media/metrics-store-custom-linux-telegraf/vm-size.png)

1. På den **inställningssidan**i **nätverk** > **Nätverkssäkerhetsgrupp**   >  ** Välj offentliga inkommande portar**väljer *HTTP* och *SSH (22)*. Lämna resten av standardinställningarna och välj **OK**. 

1. Välj Skapa för att starta distributionen av virtuella datorn på sidan Sammanfattning. 

1. Den virtuella datorn fästs på Azure Portals instrumentpanel. När distributionen är klar öppnas sammanfattningen för den virtuella datorn automatiskt. 

1. I bladet för virtuella datorn går du till den **identitet** fliken och se till att den virtuella datorn har en systemtilldelad identitet *på*. 
 
![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-to-VM.png)
 
## <a name="connect-to-the-vm"></a>Anslut till VM:en 

Skapa en SSH-anslutning med den virtuella datorn. Välj knappen Anslut på översiktssidan för den virtuella datorn. 

![FILLIN](./media/metrics-store-custom-linux-telegraf/connect-VM-button2.png)

I Anslut till virtuell dator att behålla standardalternativ för att ansluta med DNS-namn via port 22. Lokalt konto en anslutningskommandot visas med hjälp av virtuell dator när du loggar in. Klicka på knappen för att kopiera kommandot. Följande exempel visar hur SSH-anslutningskommandot ser ut: 

```cmd
ssh azureuser@XXXX.XX.XXX 
```

Klistra in SSH-anslutningen kommando i ett gränssnitt, till exempel Azure Cloud Shell, Bash i Ubuntu för Windows, eller använda en SSH-klient som du väljer för att skapa anslutningen. 

## <a name="install-and-configure-telegraf"></a>Installera och konfigurera Telegraf 

Om du vill installera Telegraf Debian-paket till den virtuella datorn kör du följande kommandon från SSH-session: 

```cmd
# download the package to the VM 
wget https://dl.influxdata.com/telegraf/releases/telegraf_1.8.0~rc1-1_amd64.deb 
# install the package 
sudo dpkg -i telegraf_1.8.0~rc1-1_amd64.deb
```
Telegrafs konfigurationsfilen definierar Telegrafs åtgärder. Som standard installeras en konfigurationsfil för exempel på sökvägen ”/ etc/telegraf/telegraf.conf”. Konfigurationsfilen exempel visar en lista över alla möjliga indata och utdata plugin-program. Men ska vi skapa en anpassad konfigurationsfil och agenten använder genom att köra följande kommandon 

```cmd
# generate the new Telegraf config file in the current directory 
telegraf --input-filter cpu:mem --output-filter azure_monitor config > azm-telegraf.conf 

# replace the example config with the new generated config 
sudo cp azm-telegraf.conf /etc/telegraf/telegraf.conf 
```

> [!NOTE]
> Ovanstående endast får två inkommande plugin-program ”processor” och ”mem” och passa på att lägga till fler inkommande plugin-program (Docker, MySQL, NGINX osv.) beroende på arbetsbelastningen som körs på din dator. En fullständig lista över inkommande plugin-program finns här. 

Slutligen, om du vill att agenten början med hjälp av den nya konfigurationen vi tvingar agenten att stoppa och starta genom att köra följande kommandon 

```cmd
# stop the telegraf agent on the VM 
sudo systemctl stop telegraf 
# start the telegraf agent on the VM to ensure it picks up the latest configuration 
sudo systemctl start telegraf 
```
Nu kommer agenten samla in mått från varje inkommande plugin-program som angetts och skickar dem till Azure Monitor. 

## <a name="plot-your-telegraf-metrics-in-the-azure-portal"></a>Rita dina Telegraf mått i Azure portal 

1. Öppna den [Azure-portalen](https://portal.azure.com) 

1. Gå till fliken Övervakare och välj sedan **mått**.  
     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metrics.png)

1. Välj den virtuella datorn i resursväljaren för

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/metric-chart.png)

1. Välj den *Telegraf/CPU* namnområdet, och välj den *usage_system* mått. Du kan välja filtret per dimension på det här måttet eller dela upp dem.  

     ![FILLIN](./media/metrics-store-custom-linux-telegraf/VM-resource-selector.png)

## <a name="additional-configuration"></a>Ytterligare konfiguration 

Den här genomgången ovan innehåller information om hur du konfigurerar Telegraf agenten för att samla in statistik från några grundläggande inkommande plugin-program. Telegraf agenten har stöd för över 150 indata-plugin-program, med några stödjande ytterligare konfigurationsalternativ. InfluxData har publicerat en [listan över stöds plugin-program](https://docs.influxdata.com/telegraf/v1.7/plugins/inputs/) och anvisningar om [hur du konfigurerar dem](https://docs.influxdata.com/telegraf/v1.7/administration/configuration/).  

Dessutom har den här genomgången får du använda Telegraf agenten för att generera mått om den virtuella datorn som agenten har distribuerats på. Telegraf-agenten kan också användas som en insamlare och vidarebefordrare av mått för andra resurser. Läs hur du konfigurerar agenten för att skapa mått för andra Azure-resurser i [Azure Monitor anpassade mått utdata för Telegraf](https://github.com/influxdata/telegraf/blob/fb704500386214655e2adb53b6eb6b15f7a6c694/plugins/outputs/azure_monitor/README.md).  

## <a name="clean-up-resources"></a>Rensa resurser 

Du kan ta bort resursgruppen, den virtuella datorn och alla relaterade resurser när de inte längre behövs. Om du vill göra det väljer du resursgruppen för den virtuella datorn, välj Ta bort och sedan bekräfta namnet på resursgruppen som ska ta bort. 

## <a name="next-steps"></a>Nästa steg
- Läs mer om [anpassade mått](metrics-custom-overview.md).


