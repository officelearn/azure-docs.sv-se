---
title: Integrera Azure Functions med Azure-nätverk
description: En stegvis självstudie som visar hur du ansluter en funktion till ett Azure-nätverk
services: functions
author: alexkarcher-msft
manager: jehollan
ms.service: azure-functions
ms.topic: article
ms.date: 12/03/2018
ms.author: alkarche
ms.openlocfilehash: 1140c23a0aa5344119c35434316ec73cc3918f90
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/10/2019
ms.locfileid: "54198379"
---
# <a name="integrate-a-function-app-with-an-azure-virtual-network"></a>Integrera en Funktionsapp med en Azure-nätverk
Den här stegvisa självstudien visar hur du använder Azure Functions för att ansluta till resurser i ett virtuellt Azure nätverk. 

Den här självstudien ska vi distribuera en wordpress-webbplats på en virtuell dator i en privat, icke-åtkomlig via internet, virtuellt nätverk. Vi sedan distribuerar en funktion med åtkomst till både internet och det virtuella nätverket. Vi använder funktionen för att komma åt resurser från wordpress-webbplats som är distribuerat i det virtuella nätverket.

Mer information om hur systemet fungerar, felsökning och avancerad konfiguration finns i dokumentet [integrera din app med Azure Virtual Network](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet). Azure Functions i dedikerade planen har samma värd funktioner som web apps, så alla funktioner och begränsningar i detta dokument gäller för Functions även.

## <a name="topology"></a>Topologi
 ![VNet-integrering UI][1]

## <a name="creating-a-vm-inside-of-a-vnet"></a>Skapa en virtuell dator i ett virtuellt nätverk

Om du vill börja, skapar vi en förkonfigurerad virtuell dator med Wordpress i ett virtuellt nätverk. 

WordPress på en virtuell dator har valts eftersom det är en av de billigaste resurser som kan distribueras i ett virtuellt nätverk. Observera att det här scenariot kan också arbeta med resurser i ett virtuellt nätverk, inklusive REST API: er, andra Azure-tjänster, App Service-miljöer och så vidare.

1.  Gå till Azure Portal
2.  Lägg till en ny resurs genom att öppna bladet ”skapa en resurs”
3.  Sök efter ”[Wordpress LEMP7 Max Performance på CentOS](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure)” och öppna dess bladet för att skapa 
4.  Konfigurera den virtuella datorn med följande information i bladet för att skapa:
    1.  Skapa en ny resursgrupp för den här virtuella datorn att rensa resurserna som är enklare i slutet av självstudien. Jag heter Min resursgrupp ”-funktion-VNET-Tutorial”
    1.  Ge ett unikt namn för den virtuella datorn. Jag heter utvinna data från ”virtuellt nätverk – Wordpress”
    1.  Välj regionen som är närmast dig
    1.  Välj storleken som B1s (1 vcpu, 1 GB minne)
    1.  Välj lösenordsautentisering och ange ett unikt användarnamn och lösenord för administratörskontot. Den här självstudien behöver du inte att logga in på den virtuella datorn om du inte behöver felsöka.
    
        <img src="./media/functions-create-vnet/create-VM-1.png" width="700">

1. Gå till fliken nätverk och ange följande information:
    1.  Skapa ett nytt virtuellt nätverk
    1.  Ange i dina önskade privat adressintervall och undernät i rätt adressintervall. Storleken på undernätet avgör hur många virtuella datorer som du kan använda i App Service-planen. Om IP-adresser och undernät är nya för dig, det finns en [dokument som täcker grunderna](https://support.microsoft.com/en-us/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). IP-adresser och undernät är viktiga i det här scenariot, så jag rekommenderar läsa några artiklar och titta på några video online tills det vara bra. 
        1. I det här exemplet har jag valt för att använda 10.10.0.0/16-nätverk med ett undernät för 10.10.1.0/24. Jag har valt att därför överetablerar och använda en /16 undernätet eftersom det är enkelt att beräkna vilka undernät är tillgängliga i 10.10.0.0/16-nätverk.
        
        <img src="./media/functions-create-vnet/create-VM-2.png" width="700">

1. Tillbaka i fliken nätverk anger du den offentliga IP-Adressen till ”ingen”. Detta distribuerar den virtuella datorn med åtkomst till endast det virtuella nätverket.
       
    <img src="./media/functions-create-vnet/create-VM-2.1.png" width="700">

7. Skapa den virtuella datorn. Detta tar cirka 5 minuter.
8. När den virtuella datorn har skapats besök fliken nätverk och anteckna privata IP-adressen för senare. Den virtuella datorn ska inte ha en offentlig IP-adress.

    ![14]

Du har nu en wordpress-webbplats som har distribuerats helt inom ditt virtuella nätverk. Den här platsen kan inte nås från det offentliga internet.

## <a name="create-a-dedicated-function-app"></a>Skapa en dedikerad Funktionsapp

Nästa steg är att skapa en Funktionsapp i en standard eller högre App Service-Plan. Observera att förbrukning plan funktionsappar inte har stöd för VNET-integrering.

1. Gå till Azure Portal
2. Lägg till en ny resurs genom att öppna bladet ”skapa en resurs”
3. Välj ”serverlös Funktionsapp”
4. Ange all normal information i bladet skapa med ett undantag:
    1. Välj en standard eller högre App Service-Plan-nivå.

        <img src="./media/functions-create-vnet/Create-App-Service-Plan.PNG" width="300">
    
1. Mina slutförda skapa bladet tittat som liknar följande.

    <img src="./media/functions-create-vnet/Create-Function-App.png" width="300">


## <a name="connect-your-function-app-to-your-vnet"></a>Ansluta appen till ditt virtuella nätverk

Nu vi har en wordpress-webbplats som är värd för många filer från inom ett virtuellt nätverk och nu behöver du ansluta appen till det virtuella nätverket.

1.  I portalen för Funktionsappen från föregående steg väljer **plattformsfunktioner**och välj sedan **nätverk**
1.  Välj **Klicka om du vill konfigurera** under VNet-integrering

    <img src="./media/functions-create-vnet/Networking-1.png" width="450">

1. VNET-integrering på sidan Välj **Lägg till virtuellt nätverk (förhandsversion)**

    <img src="./media/functions-create-vnet/Networking-2.png" width="600"> 
    
1.  Skapa ett nytt undernät för din funktion och App Service plan som ska användas. Observera att storleken på undernätet kommer att begränsa det totala antalet virtuella datorer som du kan lägga till app service-planen. Det virtuella nätverket dirigerar automatiskt trafik mellan undernät i ditt virtuella nätverk, så det ingen spelar roll att funktionen är i ett annat undernät från den virtuella datorn. 
    
    <img src="./media/functions-create-vnet/Networking-3.png" width="600">

## <a name="create-a-function-that-accesses-a-resource-in-your-vnet"></a>Skapa en funktion som ansluter till en resurs i ditt virtuella nätverk

Funktionsappen kan nu komma åt det virtuella nätverket med vår wordpress-webbplats, vi kommer att använda funktionen för att komma åt filen och använda den tillbaka till användaren. I det här exemplet använder vi en wordpress-webbplats som API: et och en Proxy för funktionen som de anropande funktionerna eftersom de är både enkelt att konfigurera och visualisera. Du kan lika enkelt att använda andra API som distribueras i ett virtuellt nätverk och en annan funktion med kod genom att API-anrop till den API som distribueras i ditt virtuella nätverk. En SQLServer distribueras inom ditt virtuella nätverk är ett bra exempel.

1. Öppna appen från föregående steg i portalen
1. Skapa en Proxy genom att välja **proxyservrar** > **+**

    <img src="./media/functions-create-vnet/New-Proxy.png" width="250">

1. Konfigurera Proxy-namn och väg. Jag har valt /plant som min väg.
1. Fyll i din wordpress-webbplats IP från tidigare och inställd på Backend-URL `http://{YOUR VM IP}/wp-content/themes/twentyseventeen/assets/images/header.jpg`
    
    <img src="./media/functions-create-vnet/Create-Proxy.png" width="900">

Om du försöker att besöka backend-URL direkt genom att klistra in den i en ny webbläsarflik bör nu sidan timeout. Detta är kan förväntas, eftersom din wordpress-webbplats som är ansluten till endast det virtuella nätverket och inte via internet. Om du klistrar in Proxy-URL i webbläsaren bör du se en snygg anläggning-bild som hämtats från din Wordpress-webbplats i det virtuella nätverket. 

Funktionsappen är ansluten till både Internet och det virtuella nätverket. Proxyn är tog emot en begäran via det offentliga internet och sedan fungerar som en enkel HTTP-proxyserver som vidarebefordrar begäran längs till virtuella nätverk. Proxyn vidarebefordrar sedan svaret tillbaka till det offentliga Internet. 

<img src="./media/functions-create-vnet/Plant.png" width="900">

## <a name="next-steps"></a>Nästa steg

Azure Functions som körs i App Service planer körs på samma tjänst som webbappar, så att alla i dokumentationen för Web Apps gäller för dedikerade funktioner.

1. [Läs mer om VNET-integrering med App Service / fungerar här](https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet)
1. [Mer information om virtuella nätverk i Azure](https://azure.microsoft.com/documentation/articles/virtual-networks-overview/)
1. [Aktivera för nätverksfunktioner och kontrollera med App Service-miljöer](https://docs.microsoft.com/azure/app-service/environment/intro)
1. [Ansluta till enskilda lokala resurser utan ändringar av brandvägg med hjälp av Hybridanslutningar](https://docs.microsoft.com/azure/app-service/app-service-hybrid-connections)
1. [Mer information om funktionen proxyservrar](https://review.docs.microsoft.com/azure/azure-functions/functions-proxies)

<!--Image references-->
[1]: ./media/functions-create-vnet/topology.png
[2]: ./media/functions-create-vnet/Create-Function-App.png
[3]: ./media/functions-create-vnet/Create-App-Service-Plan.PNG
[4]: ./media/functions-create-vnet/configure-VNET.png
[5]: ./media/functions-create-vnet/create-VM-1.png
[6]: ./media/functions-create-vnet/create-VM-2.png
[7]: ./media/functions-create-vnet/create-VM-2.1.png
[8]: ./media/functions-create-vnet/Networking-1.png
[9]: ./media/functions-create-vnet/Networking-2.png
[10]: ./media/functions-create-vnet/Networking-3.png
[11]: ./media/functions-create-vnet/New-Proxy.png
[12]: ./media/functions-create-vnet/Create-Proxy.png
[14]: ./media/functions-create-vnet/VM-Networking.png
