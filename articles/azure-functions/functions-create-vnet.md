---
title: Integrera Azure Functions med Azure-nätverk
description: En stegvis självstudie som visar hur du ansluter en funktion till ett Azure-nätverk
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 4/11/2019
ms.author: alkarche
ms.openlocfilehash: 96ab479d3373eb6e575a00898f7007a4df252e39
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "62125687"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrera en funktionsapp med en Azure-nätverk

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i Azure-nätverk.

Den här självstudien ska vi distribuera en WordPress-webbplats på en virtuell dator i ett virtuellt nätverk som inte är tillgänglig från internet. Sedan ska vi distribuera en funktion med åtkomst till både internet och det virtuella nätverket. Vi använder funktionen åtkomst till resurser från WordPress-webbplats som är distribuerat i det virtuella nätverket.

Mer information om hur systemet fungerar, felsökning och avancerad konfiguration finns i [integrera din app med Azure-nätverk](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Azure-funktioner i Premium-prenumerationen har samma värd funktioner som web apps, så alla funktioner och begränsningar i den här artikeln gäller för functions.

## <a name="topology"></a>Topologi

 ![Användargränssnittet för virtual network-integration][1]

## <a name="create-a-vm-inside-a-virtual-network"></a>Skapa en virtuell dator i ett virtuellt nätverk

Om du vill starta, skapar vi en förkonfigurerad virtuell dator som kör WordPress i ett virtuellt nätverk. 

Vi valde WordPress på en virtuell dator eftersom det är en av de billigaste resurser som kan distribueras i ett virtuellt nätverk. Observera att det här scenariot kan också fungera med alla resurser i ett virtuellt nätverk, till exempel REST API: er, App Service-miljöer och andra Azure-tjänster.

1. Gå till Azure Portal.
2. Lägg till en ny resurs genom att öppna den **skapa en resurs** bladet.
3. Sök efter ”[WordPress LEMP7 Max Performance på CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)” och öppna dess bladet för att skapa. 
4. På den **grunderna** konfigurerar den virtuella datorn med följande information:
    1. Skapa en ny resursgrupp för den här virtuella datorn att rensa resurserna som är enklare i slutet av självstudien. Här kan använder vi ”-funktion-VNET-Tutorial” som exempel.
    1. Ge ett unikt namn för den virtuella datorn. Vi använder ”virtuellt nätverk – Wordpress” som exempel.
    1. Välj regionen som är närmast dig.
    1. Välj storleken som B1s (1 vCPU, 1 GB minne).
    1. Välj lösenordsautentisering och ange ett unikt användarnamn och lösenord för administratörskontot. I den här självstudien behöver du inte logga in på den virtuella datorn om du inte behöver felsöka.
    
        ![Fliken grunderna för att skapa en virtuell dator](./media/functions-create-vnet/create-vm-1.png)

1. Flytta till den **nätverk** fliken och ange följande information:
    1.  Skapa ett nytt virtuellt nätverk.
    1.  Ange en privat adressintervall och ett undernät i det adressintervallet. Storleken på undernätet avgör hur många virtuella datorer som du kan använda i App Service-planen. Om IP-adresser och undernät som är nya för dig, det finns en [dokument som täcker grunderna](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). IP-adresser och undernät är viktiga i det här scenariot, så vi rekommenderar att du läser några artiklar och titta på några videor online tills det vara bra. 
    
        I det här exemplet vi väljer för att använda 10.10.0.0/16-nätverk med ett undernät för 10.10.1.0/24. Vi skaffa och använda en /16 undernätet eftersom det är enkelt att beräkna vilka undernät är tillgängliga i 10.10.0.0/16-nätverk.
        
        <img src="./media/functions-create-vnet/create-vm-2.png" width="700">

1. Gå tillbaka till den **nätverk** fliken genom att ange den offentliga IP-Adressen **ingen**. Det här steget distribuerar den virtuella datorn med åtkomst till endast det virtuella nätverket.
       
    <img src="./media/functions-create-vnet/create-vm-2-1.png" width="700">

7. Skapa den virtuella datorn. Processen tar cirka 5 minuter.
8. När den virtuella datorn har skapats går du till dess **nätverk** fliken och anteckna den privata IP-adressen för senare. Den virtuella datorn ska inte ha en offentlig IP-adress.

    ![14]

Nu har du en WordPress-webbplats som har distribuerats helt inom ditt virtuella nätverk. Den här platsen kan inte nås från det offentliga internet.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en funktionsapp i en premiumplan

Nästa steg är att skapa en funktionsapp i en premiumplan. En premiumplan ger serverlös skala med alla fördelar med en dedikerad App Service-plan. Funktionsappar som har skapats via förbrukningsplanen stöder inte virtual network-integration.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

## <a name="connect-your-function-app-to-your-virtual-network"></a>Ansluta appen till ditt virtuella nätverk

Du kan nu ansluta funktionsappen till det virtuella nätverket med en WordPress-webbplats som är värd för filer från ditt virtuella nätverk.

1.  I portalen för funktionsappen från föregående steg, Välj **plattformsfunktioner**. Välj sedan **nätverk**.

    <img src="./media/functions-create-vnet/networking-0.png" width="850">

1.  Välj **Klicka här om du vill konfigurera** under **VNet-integrering**.

    ![Status för att konfigurera en funktion för nätverk](./media/functions-create-vnet/Networking-1.png)

1. På sidan virtuellt nätverk integration väljer **Lägg till virtuellt nätverk (förhandsversion)**.

    <img src="./media/functions-create-vnet/networking-2.png" width="600"> 
    
1.  Skapa ett nytt undernät för funktionen och App Service plan som ska användas. Observera att storleken på undernätet kommer att begränsa det totala antalet virtuella datorer som du kan lägga till App Service-planen. Det virtuella nätverket dirigerar automatiskt trafik mellan undernät i det virtuella nätverket, så det ingen spelar roll att funktionen är i ett annat undernät från den virtuella datorn. 
    
    <img src="./media/functions-create-vnet/networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-virtual-network"></a>Skapa en funktion som ansluter till en resurs i ditt virtuella nätverk

Funktionsappen kan nu komma åt det virtuella nätverket med vår WordPress-webbplats. Vi kommer att använda funktionen för att komma åt filen och använda den tillbaka till användaren. I det här exemplet använder vi en WordPress-webbplats som API: et och en proxy som den anropande funktionen eftersom de är både enkelt att konfigurera och visualisera. 

Du kan lika enkelt att använda andra API som distribueras inom ett virtuellt nätverk. Du kan också använda en annan funktion med kod som gör API-anrop till API som distribueras i det virtuella nätverket. En SQL Server-instans som har distribuerats i det virtuella nätverket är ett bra exempel.

1. Öppna appen från föregående steg i portalen.
1. Skapa en proxy genom att välja **proxyservrar** > **+**.

    <img src="./media/functions-create-vnet/new-proxy.png" width="250">

1. Konfigurera proxynamn och väg. Det här exemplet använder ”/ planerar” som en väg.
1. Fyll i din WordPress-webbplats IP från tidigare och ange **Backend-URL** till `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/create-proxy.png" width="900">

Om du försöker att besöka din backend-URL direkt genom att klistra in den i en ny webbläsarflik bör nu sidan timeout. Det beror på att din WordPress-webbplats som är ansluten till endast det virtuella nätverket och inte via internet. Om du klistrar in proxy-URL i webbläsaren, ser du en anläggning bild (som hämtats från din WordPress-webbplats) i ditt virtuella nätverk. 

Funktionsappen är ansluten till både internet och det virtuella nätverket. Proxyn är tog emot en begäran via det offentliga internet och sedan fungerar som en enkel HTTP-proxyserver som vidarebefordrar begäran längs till virtuella nätverk. Proxyn vidarebefordrar sedan svaret tillbaka till det offentliga Internet. 

<img src="./media/functions-create-vnet/plant.png" width="900">

## <a name="next-steps"></a>Nästa steg

Funktioner som körs i en premiumplan delar samma underliggande App Service-infrastruktur som web apps på PremiumV2-planer. All dokumentation för web apps gäller för dina funktioner för Premium-plan.

* [Mer information om Nätverksalternativ i funktioner](./functions-networking-options.md)
* [Läs de funktioner som nätverk vanliga frågor och svar](./functions-networking-faq.md)
* [Mer information om virtuella nätverk i Azure](../virtual-network/virtual-networks-overview.md)
* [Aktivera flera nätverk funktioner och kontroll med App Service-miljöer](../app-service/environment/intro.md)
* [Ansluta till enskilda lokala resurser utan ändringar av brandvägg med hjälp av Hybridanslutningar](../app-service/app-service-hybrid-connections.md)
* [Mer information om Functions Proxies](./functions-proxies.md)

<!--Image references -->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/create-function-app.png
[3]: ./media/functions-create-vnet/create-app-service-plan.png
[4]: ./media/functions-create-vnet/configure-vnet.png
[5]: ./media/functions-create-vnet/create-vm-1.png
[6]: ./media/functions-create-vnet/create-vm-2.png
[7]: ./media/functions-create-vnet/create-vm-2-1.png
[8]: ./media/functions-create-vnet/networking-1.png
[9]: ./media/functions-create-vnet/networking-2.png
[10]: ./media/functions-create-vnet/networking-3.png
[11]: ./media/functions-create-vnet/new-proxy.png
[12]: ./media/functions-create-vnet/create-proxy.png
[14]: ./media/functions-create-vnet/vm-networking.png
