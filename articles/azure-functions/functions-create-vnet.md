---
title: Integrera Azure-funktioner med ett virtuellt Azure-nätverk
description: En steg-för-steg-självstudie som visar hur du ansluter en funktion till ett virtuellt Azure-nätverk
author: alexkarcher-msft
ms.topic: article
ms.date: 5/03/2019
ms.author: alkarche
ms.reviewer: glenga
ms.openlocfilehash: 0c70c69f547405eb8ebdcf6dcc6ae597db151e53
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75433219"
---
# <a name="tutorial-integrate-functions-with-an-azure-virtual-network"></a>Självstudie: integrera Functions med ett virtuellt Azure-nätverk

Den här självstudien visar hur du använder Azure Functions för att ansluta till resurser i ett virtuellt Azure-nätverk. du skapar en funktion som har tillgång till både Internet och till en virtuell dator som kör WordPress i virtuellt nätverk.

> [!div class="checklist"]
> * Skapa en funktionsapp i Premium-planen
> * Distribuera en WordPress-plats till vm i ett virtuellt nätverk
> * Ansluta funktionsappen till det virtuella nätverket
> * Skapa en funktionsproxy för att komma åt WordPress-resurser
> * Begär en WordPress-fil inifrån det virtuella nätverket

## <a name="topology"></a>Topologi

I följande diagram visas arkitekturen för den lösning som du skapar:

 ![Användargränssnitt för integrering av virtuella nätverk](./media/functions-create-vnet/topology.png)

Funktioner som körs i Premium-planen har samma värdfunktioner som webbappar i Azure App Service, som innehåller funktionen VNet-integrering. Mer information om VNet-integrering, inklusive felsökning och avancerad konfiguration, finns i [Integrera din app med ett virtuellt Azure-nätverk](../app-service/web-sites-integrate-with-vnet.md).

## <a name="prerequisites"></a>Krav

För den här självstudien är det viktigt att du förstår IP-adressering och undervikt. Du kan börja med [den här artikeln som täcker grunderna för adressering och undertextning](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor finns tillgängliga online.

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="create-a-function-app-in-a-premium-plan"></a>Skapa en funktionsapp i en Premium-plan

Först skapar du en funktionsapp i [Premium-planen]. Den här planen ger serverlös skala samtidigt som den stöder integrering av virtuella nätverk.

[!INCLUDE [functions-premium-create](../../includes/functions-premium-create.md)]  

Du kan fästa funktionsappen på instrumentpanelen genom att välja pinikonen i det övre högra hörnet. Pinning gör det enklare att återgå till den här funktionsappen när du har skapat den virtuella datorn.

## <a name="create-a-vm-inside-a-virtual-network"></a>Skapa en virtuell dator i ett virtuellt nätverk

Skapa sedan en förkonfigurerad virtuell dator som kör WordPress i ett virtuellt nätverk[(WordPress LEMP7 Max Performance](https://jetware.io/appliances/jetware/wordpress4_lemp7-170526/profile?us=azure) av Jetware). En WordPress VM används på grund av dess låga kostnad och bekvämlighet. Samma scenario fungerar med alla resurser i ett virtuellt nätverk, till exempel REST-API:er, App Service-miljöer och andra Azure-tjänster. 

1. I portalen väljer du **+ Skapa en resurs** i det `WordPress LEMP7 Max Performance`vänstra navigeringsfönstret, i sökfältstypen och trycker på Retur.

1. Välj **Wordpress LEMP Max Performance** i sökresultaten. Välj en programvaruplan för **Wordpress LEMP Max Performance för CentOS** som **programvaruplan** och välj **Skapa**.

1. På fliken **Grunderna** använder du de vm-inställningar som anges i tabellen under bilden:

    ![Fliken Grunderna för att skapa en virtuell dator](./media/functions-create-vnet/create-vm-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som dina resurser skapas under. | 
    | **[Resursgrupp](../azure-resource-manager/management/overview.md)**  | myResourceGroup | Välj `myResourceGroup`eller resursgruppen som du skapade med funktionsappen. Använda samma resursgrupp för funktionsappen, WordPress VM och hostingplan gör det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på virtuell dator** | VNET-Wordpress | VM-namnet måste vara unikt i resursgruppen |
    | **[Regionen](https://azure.microsoft.com/regions/)** | (Europa) Västeuropa | Välj en region nära dig eller i närheten av de funktioner som kommer åt den virtuella datorn. |
    | **Storlek** | B1s (på) | Välj **Ändra storlek** och välj sedan B1s standardbild, som har 1 vCPU och 1 GB minne. |
    | **Autentiseringstyp** | lösenord | Om du vill använda lösenordsautentisering måste du också ange ett **användarnamn**, ett säkert **lösenord**och sedan **bekräfta lösenord**. För den här självstudien behöver du inte logga in på den virtuella datorn om du inte behöver felsöka. |

1. Välj fliken **Nätverk** och välj **Skapa nya**under Konfigurera virtuella nätverk .

1. I **Skapa virtuellt nätverk**använder du inställningarna i tabellen under bilden:

    ![Fliken Nätverk för skapa virtuell dator](./media/functions-create-vnet/create-vm-2.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myResourceGroup-vnet | Du kan använda standardnamnet som genereras för det virtuella nätverket. |
    | **Adressintervall** | 10.10.0.0/16 | Använd ett enda adressintervall för det virtuella nätverket. |
    | **Namn på undernät** | Självstudie-Net | Namnet på undernätet. |
    | **Adressintervall** (undernät) | 10.10.1.0/24   | Undernätsstorleken definierar hur många gränssnitt som kan läggas till i undernätet. Detta undernät används av WordPress webbplats.  Ett `/24` undernät innehåller 254 värdadresser. |

1. Välj **OK** om du vill skapa det virtuella nätverket.

1. Tillbaka på fliken **Nätverk** väljer du **Ingen** för **offentlig IP**.

1. Välj fliken **Hantering** och välj sedan det **lagringskonto**för Diagnostik som du skapade med funktionsappen i Diagnostikkontot.

1. Välj **Granska + skapa**. När valideringen är klar väljer du **Skapa**. Det tar några minuter att skapa den virtuella datorn. Den skapade virtuella datorn kan bara komma åt det virtuella nätverket.

1. När den virtuella datorn har skapats väljer du **Gå till resurs** för att visa sidan för den nya virtuella datorn och väljer sedan **Nätverk** under **Inställningar**.

1. Kontrollera att det inte finns någon **offentlig IP**. Anteckna den **privata IP-**, som du använder för att ansluta till den virtuella datorn från din funktionsapp.

    ![Nätverksinställningar i den virtuella datorn](./media/functions-create-vnet/vm-networking.png)

Du har nu en WordPress-plats som distribueras helt inom ditt virtuella nätverk. Denna webbplats är inte tillgänglig från det offentliga internet.

## <a name="connect-your-function-app-to-the-virtual-network"></a>Ansluta funktionsappen till det virtuella nätverket

Med en WordPress-webbplats som körs i en virtuell dator i ett virtuellt nätverk kan du nu ansluta din funktionsapp till det virtuella nätverket.

1. I din nya funktionsapp väljer du **Plattformsfunktioner** > **Nätverk.**

    ![Välj nätverk i funktionsappen](./media/functions-create-vnet/networking-0.png)

1. Under **VNet-integrering**väljer du **Klicka här för att konfigurera**.

    ![Status för att konfigurera en nätverksfunktion](./media/functions-create-vnet/Networking-1.png)

1. På sidan för integrering av virtuellt nätverk väljer du **Lägg till virtuellt nätverk (förhandsgranskning)**.

    ![Lägga till förhandsversionen av VNet-integrering](./media/functions-create-vnet/networking-2.png)

1. I Status för **nätverksfunktion**använder du inställningarna i tabellen under bilden:

    ![Definiera det virtuella nätverket för funktionsapp](./media/functions-create-vnet/networking-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Virtuellt nätverk** | MyResourceGroup-vnet | Det här virtuella nätverket är det som du skapade tidigare. |
    | **Undernät** | Skapa nytt undernät | Skapa ett undernät i det virtuella nätverket som funktionsappen kan använda. VNet-integrering måste konfigureras för att använda ett tomt undernät. Det spelar ingen roll att dina funktioner använder ett annat undernät än den virtuella datorn. Det virtuella nätverket dirigerar automatiskt trafik mellan de två undernäten. |
    | **Namn på undernät** | Funktion-Netto | Namnet på det nya undernätet. |
    | **Adressblock för virtuellt nätverk** | 10.10.0.0/16 | Välj samma adressblock som används av WordPress-webbplatsen. Du bör bara ha ett adressblock definierat. |
    | **Adressintervall** | 10.10.2.0/24   | Undernätsstorleken begränsar det totala antalet instanser som din Premium-planfunktionsapp kan skalas ut till. I det `/24` här exemplet används ett undernät med 254 tillgängliga värdadresser. Det här undernätet är överetablerade, men lätt att beräkna. |

1. Välj **OK** om du vill lägga till undernätet. Stäng sidorna för VNet-integrering och nätverksfunktion för att återgå till funktionsappsidan.

Funktionsappen kan nu komma åt det virtuella nätverket där WordPress-webbplatsen körs. Därefter använder du [Azure Functions Proxies](functions-proxies.md) för att returnera en fil från WordPress-webbplatsen.

## <a name="create-a-proxy-to-access-vm-resources"></a>Skapa en proxy för att komma åt VM-resurser

Med VNet-integrering aktiverat kan du skapa en proxy i funktionsappen för att vidarebefordra begäranden till den virtuella datorn som körs i det virtuella nätverket.

1. I funktionsappen väljer du **Proxyservrar** > **+** och använder sedan proxyinställningarna i tabellen under bilden:

    ![Definiera proxyinställningarna](./media/functions-create-vnet/create-proxy.png)

    | Inställning  | Föreslaget värde  | Beskrivning      |
    | -------- | ---------------- | ---------------- |
    | **Namn** | Anläggning | Namnet kan vara vilket värde som helst. Den används för att identifiera proxyn. |
    | **Flödesmall** | /växt | Rutt som mappar till en VM-resurs. |
    | **Webbadress för serverdel** | http://<YOUR_VM_IP>/wp-content/themes/twentyseventeen/assets/images/header.jpg | Ersätt `<YOUR_VM_IP>` med IP-adressen för din WordPress VM som du skapade tidigare. Den här mappningen returnerar en enskild fil från platsen. |

1. Välj **Skapa** om du vill lägga till proxyn i funktionsappen.

## <a name="try-it-out"></a>Prova det

1. Försök att komma åt webbadressen som du använde som **backend-URL**i webbläsaren . Som väntat time time outs begäran. En timeout uppstår eftersom din WordPress-webbplats endast är ansluten till ditt virtuella nätverk och inte internet.

1. Kopiera **proxy-URL-värdet** från den nya proxyn och klistra in det i webbläsarens adressfält. Den returnerade bilden kommer från WordPress-webbplatsen som körs i ditt virtuella nätverk.

    ![Plant bildfil tillbaka från WordPress webbplats](./media/functions-create-vnet/plant.png)

Din funktionsapp är ansluten till både internet och ditt virtuella nätverk. Proxyn tar emot en begäran via det offentliga internet och fungerar sedan som en enkel HTTP-proxy för att vidarebefordra den begäran till det anslutna virtuella nätverket. Proxyn vidarebefordrar sedan svaret tillbaka till dig offentligt över Internet.

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg

I den här guiden fungerar WordPress-webbplatsen som ett API som anropas med hjälp av en proxy i funktionsappen. Det här scenariot gör en bra självstudie eftersom det är enkelt att konfigurera och visualisera. Du kan använda vilket annat API som helst som distribueras i ett virtuellt nätverk. Du kan också ha skapat en funktion med kod som anropar API:er som distribueras i det virtuella nätverket. Ett mer realistiskt scenario är en funktion som använder dataklient-API:er för att anropa en SQL Server-instans som distribueras i det virtuella nätverket.

Funktioner som körs i en Premium-abonnemang delar samma underliggande App Service-infrastruktur som webbappar i PremiumV2-abonnemang. All dokumentation för [webbappar i Azure App Service](../app-service/overview.md) gäller för dina Premium-abonnemangsfunktioner.

> [!div class="nextstepaction"]
> [Läs mer om nätverksalternativen i Funktioner](./functions-networking-options.md)

[Premiumplan]: functions-scale.md#premium-plan
