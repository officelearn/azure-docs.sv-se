---
title: Integrera Azure Functions med Azure-nätverk
description: En stegvis självstudie som visar hur du ansluter en funktion till ett Azure-nätverk
services: functions
author: alexkarcher-msft
manager: jeconnoc
ms.service: azure-functions
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche, glenga
ms.openlocfilehash: 55cce60ab3d1cda3cb870afd2f6214f917a04189
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/13/2019
ms.locfileid: "67063273"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Självstudie: integrera funktioner med ett Azure-nätverk

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i Azure-nätverk. du ska skapa en funktion som har åtkomst till både internet och att en virtuell dator med WordPress i det virtuella nätverket.

> [!div class="checklist"]
> * Skapa en funktionsapp i Premium-plan
> * Distribuera en WordPress-webbplats till en virtuell dator i ett virtuellt nätverk
> * Ansluta appen till det virtuella nätverket
> * Skapa en funktion som proxy för WordPress-resurser
> * Begär en WordPress-fil i virtuella nätverk

> [!NOTE]  
> Den här självstudiekursen skapar en funktionsapp i Premium-prenumerationen. Den här värdplanen förhandsvisas just nu. Mer information finns i [Premium-abonnemang].

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för lösningen som du skapar:

 ![Användargränssnittet för virtual network-integration](./media/functions-create-vnet/topology.png)

Funktioner som körs i Premium-prenumerationen har samma värd funktioner som web apps i Azure App Service som innehåller funktionen VNet-integrering. Läs mer om VNet-integrering, inklusive felsökning och avancerad konfiguration i [integrera din app med Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Nödvändiga komponenter

Den här självstudien är det viktigt att du förstår IP-adresser och undernät. Du kan börja med [i den här artikeln som täcker grunderna för adresser och undernät](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor finns på Internet.

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en funktionsapp i en premiumplan

Först måste du skapa en funktionsapp i den [Premium-abonnemang]. Den här planen innehåller serverlös skala stöd för virtual network-integration.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Du kan fästa funktionsappen till instrumentpanelen genom att välja fästikonen i det övre högra hörnet. Fästa gör det enklare att återgå till den här funktionsappen när du har skapat den virtuella datorn.

## <a name="create-a-vm-inside-a-virtual-network"></a>Skapa en virtuell dator i ett virtuellt nätverk

Skapa sedan en förkonfigurerad virtuell dator som kör WordPress i ett virtuellt nätverk ([WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) av Jetware). En WordPress-VM används på grund av dess låg kostnad och bekvämlighet. Det här samma scenariot fungerar med alla resurser i ett virtuellt nätverk, till exempel REST API: er, App Service-miljöer och andra Azure-tjänster. 

1. I portalen, väljer **+ skapa en resurs** i det vänstra navigeringsfönstret i fältet söktyp `WordPress LEMP7 Max Performance`, och tryck på RETUR.

1. Välj **Wordpress LEMP Max Performance** i sökresultatet. Välj en plan för programvara för **Wordpress LEMP Max Performance för CentOS** som den **Programvaruplanen** och välj **skapa**.

1. I den **grunderna** fliken, Använd inställningarna för virtuella datorer som anges i tabellen nedanför bilden:

    ![Fliken grunderna för att skapa en virtuell dator](./media/functions-create-vnet/create-vm-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som har skapats för dina resurser. | 
    | **[Resursgrupp](../azure-resource-manager/resource-group-overview.md)**  | myResourceGroup | Välj `myResourceGroup`, eller resursgruppen som du skapade med din funktionsapp. Använder samma resursgrupp för funktionsappen, WordPress VM och värdplan gör det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på virtuell dator** | VNET-Wordpress | VM-namnet måste vara unikt i resursgruppen |
    | **[Region](https://azure.microsoft.com/regions/)** | (Europa) Västeuropa | Välj en region nära dig eller nära de funktioner som har åtkomst till den virtuella datorn. |
    | **Storlek** | B1s | Välj **ändra storleken på** och välj sedan B1s standard avbildningen, som har 1 virtuella processorer och 1 GB minne. |
    | **Autentiseringstyp** | Lösenord | Om du vill använda autentisering med lösenord, måste du även ange en **användarnamn**, en säker **lösenord**, och sedan **Bekräfta lösenord**. I den här självstudien behöver du inte logga in på den virtuella datorn om du inte behöver felsöka. |

1. Välj den **nätverk** fliken och markera under Konfigurera virtuella nätverk **Skapa ny**.

1. I **skapa virtuellt nätverk**, använder du inställningarna i tabellen nedanför bilden:

    ![Knappen Skapa virtuell dator](./media/functions-create-vnet/create-vm-2.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myResourceGroup-vnet | Du kan använda standardnamnet som genererats för det virtuella nätverket. |
    | **Adressintervall** | 10.10.0.0/16 | Använd ett enda adressintervall för det virtuella nätverket. |
    | **Namn på undernät** | Självstudie – Net | Namnet på undernätet. |
    | **Adressintervall** (undernät) | 10.10.1.0/24   | Storleken på undernätet definierar hur många gränssnitt kan läggas till undernätet. Det här undernätet används av WordPress-webbplats.  En `/24` undernätet innehåller 254 värdadresser. |

1. Välj **OK** att skapa det virtuella nätverket.

1. I den **nätverk** fliken **ingen** för **offentlig IP-adress**.

1. Välj den **Management** fliken i **diagnostiklagringskonto**, Välj det lagringskonto du skapade med din funktionsapp.

1. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Den virtuella datorn skapa processen tar några minuter. Den skapade virtuella datorn kan bara komma åt det virtuella nätverket.

1. När den virtuella datorn har skapats kan du välja **gå till resurs** för att visa sidan för din nya virtuella dator, därefter **nätverk** under **inställningar**.

1. Kontrollera att det finns inga **offentlig IP-adress**. Anteckna den **privat IP**, som du använder för att ansluta till den virtuella datorn från din funktionsapp.

    ![Nätverksinställningar på den virtuella datorn](./media/functions-create-vnet/vm-networking.png)

Nu har du en WordPress-webbplats som har distribuerats helt inom ditt virtuella nätverk. Den här platsen är inte tillgänglig från det offentliga internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ansluta appen till det virtuella nätverket

Du kan nu ansluta din funktionsapp till det virtuella nätverket med en WordPress-webbplats som körs i en virtuell dator i ett virtuellt nätverk.

1. Välj i den nya funktionsappen **plattformsfunktioner** > **nätverk**.

    ![Välj nätverk i funktionsappen](./media/functions-create-vnet/networking-0.png)

1. Under **VNet-integrering**väljer **Klicka här om du vill konfigurera**.

    ![Status för att konfigurera en funktion för nätverk](./media/functions-create-vnet/Networking-1.png)

1. På sidan virtuellt nätverk integration väljer **Lägg till virtuellt nätverk (förhandsversion)** .

    ![Lägg till förhandsversionen av VNet-integrering](./media/functions-create-vnet/networking-2.png)

1. I **Nätverksfunktionsstatus**, använder du inställningarna i tabellen nedanför bilden:

    ![Definiera funktion app virtuellt nätverk](./media/functions-create-vnet/networking-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Virtual Network** | MyResourceGroup-vnet | Det här virtuella nätverket är det som du skapade tidigare. |
    | **Undernät** | Skapa ett nytt undernät | Skapa ett undernät i det virtuella nätverket för din funktionsapp använda. VNet-integrering måste konfigureras för att använda ett tomt undernät. Det spelar ingen roll att dina funktioner använder ett annat undernät än den virtuella datorn. Det virtuella nätverket dirigerar automatiskt trafik mellan de två undernäten. |
    | **Namn på undernät** | Funktionen-Net | Namnet på det nya undernätet. |
    | **Adressblock för virtuellt nätverk** | 10.10.0.0/16 | Välj samma adressblocket som används av WordPress-webbplats. Du bör bara ha en Adressblock som definierats. |
    | **Adressintervall** | 10.10.2.0/24   | Storleken på undernätet begränsar det totala antalet instanser som funktionsappen Premium plan kan skala ut till. Det här exemplet används en `/24` undernätet med 254 tillgängliga värdadresser. Det här undernätet är överetablerade, men enkelt att beräkna. |

1. Välj **OK** att lägga till undernätet. Stäng sidorna för VNet-integrering och Nätverksfunktionsstatus att återgå till din funktion appsidan.

Funktionsappen kan nu komma åt det virtuella nätverket där WordPress-webbplatsen körs. Därefter kan du använda [Azure Functions Proxies](functions-proxies.md) att returnera en fil från WordPress-webbplats.

## <a name="create-a-proxy-to-access-vm-resources"></a>Skapa en proxy för åtkomst till VM-resurser

Du kan använda VNet-integrering aktiverat, för att skapa en proxy i din funktionsapp som vidarebefordrar begäranden till den virtuella datorn som körs i det virtuella nätverket.

1. I din funktionsapp väljer **proxyservrar** >  **+** , Använd proxyinställningarna i tabellen nedanför bilden:

    ![Ange proxyinställningar för](./media/functions-create-vnet/create-proxy.png)

    | Inställning  | Föreslaget värde  | Beskrivning      |
    | -------- | ---------------- | ---------------- |
    | **Namn** | Anläggningen | Namnet kan vara vilket värde. Den används för att identifiera proxyn. |
    | **Flödesmall** | /plant | Väg som mappar till en VM-resurs. |
    | **Backend-URL** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Ersätt `<YOUR_VM_IP>` med IP-adressen för din WordPress VM som du skapade tidigare. Den här mappningen returnerar en enskild fil från platsen. |

1. Välj **skapa** att lägga till proxyn i din funktionsapp.

## <a name="try-it-out"></a>Prova det

1. I webbläsaren försöker få åtkomst till URL: en som du använde som den **Backend-URL**. Som förväntat, tidsgränsen uppnås för begäran. En timeout uppstår eftersom din WordPress-webbplats som är anslutet endast till det virtuella nätverket och inte via internet.

1. Kopiera den **Proxy-URL: en** från din nya proxyserver och klistra in den i adressfältet i webbläsaren. Returnerade bilden är från WordPress-webbplats som körs i ditt virtuella nätverk.

    ![Anläggning bildfil som returnerades från WordPress-webbplats](./media/functions-create-vnet/plant.png)

Funktionsappen är ansluten till både internet och det virtuella nätverket. Proxyn är tog emot en begäran via det offentliga internet och sedan fungerar som en enkel HTTP-proxy för att vidarebefordra begäran till det virtuella nätverket som är anslutna. Proxyn sedan vidarebefordrar svaret tillbaka till offentligt över internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I de här självstudierna WordPress-webbplats som fungerar som ett API som ska anropas med hjälp av en proxy i funktionsappen. Det här scenariot är en bra genomgång eftersom det är enkelt att konfigurera och visualisera. Du kan använda andra API som distribueras inom ett virtuellt nätverk. Du kan också ha skapat en funktion med kod som anropar API: er som distribuerats i det virtuella nätverket. Ett mer realistiskt scenario är en funktion som använder data-klientens API: er för att anropa en SQL Server-instans som distribuerats i det virtuella nätverket.

Funktioner som körs i en premiumplan delar samma underliggande App Service-infrastruktur som web apps på PremiumV2-planer. All dokumentation för [webbappar i Azure App Service](../app-service/overview.md) gäller för dina funktioner för Premium-plan.

> [!div class="nextstepaction"]
> [Mer information om Nätverksalternativ i funktioner](./functions-networking-options.md)

[Premium-abonnemang]: functions-scale.md#premium-plan
