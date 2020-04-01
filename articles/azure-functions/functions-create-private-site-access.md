---
title: Aktivera privat platsåtkomst till Azure-funktioner
description: Lär dig att konfigurera privat azure-nätverksplatsåtkomst för Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/24/2020
ms.locfileid: "78851296"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Självstudiekurs: Upprätta privat åtkomst till Azure Functions

Den här självstudien visar hur du aktiverar [privat platsåtkomst](./functions-networking-options.md#private-site-access) med Azure Functions. Med hjälp av privat platsåtkomst kan du kräva att funktionskoden endast utlöses från ett visst virtuellt nätverk.

Privat platsåtkomst är användbar i scenarier när åtkomsten till funktionsappen måste begränsas till ett visst virtuellt nätverk. Funktionsappen kan till exempel endast vara tillämplig på anställda i en viss organisation eller tjänster som finns inom det angivna virtuella nätverket (till exempel en annan Azure-funktion, Azure Virtual Machine eller ett AKS-kluster).

Om en functions-app behöver komma åt Azure-resurser i det virtuella nätverket eller ansluten via [tjänstslutpunkter,](../virtual-network/virtual-network-service-endpoints-overview.md)behövs [integrering av virtuella nätverk.](./functions-create-vnet.md)

I den här självstudien får du lära dig hur du konfigurerar privat webbplatsåtkomst för din funktionsapp:

> [!div class="checklist"]
> * Skapa en virtuell dator
> * Skapa en Azure Bastion-tjänst
> * Skapa en Azure-funktionsapp
> * Konfigurera en slutpunkt för en virtuell nätverkstjänst
> * Skapa och distribuera en Azure-funktion
> * Anropa funktionen utifrån och inom det virtuella nätverket

Om du inte har en Azure-prenumeration skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för den lösning som ska skapas:

![Arkitekturdiagram på hög nivå för privat lösning för platsåtkomst](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Krav

För den här självstudien är det viktigt att du förstår IP-adressering och undervikt. Du kan börja med [den här artikeln som täcker grunderna för adressering och undertextning](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor finns tillgängliga online.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure-portalen](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Det första steget i den här självstudien är att skapa en ny virtuell dator i ett virtuellt nätverk.  Den virtuella datorn kommer att användas för att komma åt din funktion när du har begränsat dess åtkomst till endast vara tillgänglig från det virtuella nätverket.

1. Välj knappen **Skapa en resurs.**

2. Skriv `Windows Server`och välj **Windows Server** i sökresultaten i sökfältet.

3. Välj **Windows Server 2019 Datacenter** i listan över Windows Server-alternativ och tryck på knappen **Skapa.**

4. På fliken **Grunderna** använder du de vm-inställningar som anges i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![Fliken Grunderna för en ny Windows-virtuell dator](./media/functions-create-private-site-access/create-vm-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Prenumerationen som dina resurser skapas under. |
    | [**Resursgrupp**](../azure-resource-manager/management/overview.md) | myResourceGroup | Välj resursgruppen om du vill innehålla alla resurser för den här självstudien.  Om du använder samma resursgrupp blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på virtuell dator** | myVM (på) | VM-namnet måste vara unikt i resursgruppen |
    | [**Regionen**](https://azure.microsoft.com/regions/) | Jag är mycket bra på att vi måste ta tid på Norra centrala USA | Välj en region nära dig eller i närheten av de funktioner som ska nås. |
    | **Offentliga inkommande portar** | Inget | Välj **Ingen** om du vill vara säkra på att det inte finns någon inkommande anslutning till den virtuella datorn från Internet. Fjärråtkomst till den virtuella datorn konfigureras via Azure Bastion-tjänsten. |

5. Välj fliken **Nätverk** och välj **Skapa ny** för att konfigurera ett nytt virtuellt nätverk.

    >[!div class="mx-imgBorder"]
    >![Skapa ett nytt virtuellt nätverk för den nya virtuella datorn](./media/functions-create-private-site-access/create-vm-networking.png)

6. I **Skapa virtuellt nätverk**använder du inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![Skapa ett nytt virtuellt nätverk för den nya virtuella datorn](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myResourceGroup-vnet | Du kan använda standardnamnet som genereras för det virtuella nätverket. |
    | **Adressintervall** | 10.10.0.0/16 | Använd ett enda adressintervall för det virtuella nätverket. |
    | **Namn på undernät** | Självstudier | Namnet på undernätet. |
    | **Adressintervall** (undernät) | 10.10.1.0/24 | Undernätsstorleken definierar hur många gränssnitt som kan läggas till i undernätet. Det här undernätet används av den virtuella datorn. Ett `/24` undernät innehåller 254 värdadresser. |

7. Välj **OK** om du vill skapa det virtuella nätverket.
8. Tillbaka på fliken **Nätverk,** **se** ingen är vald för **offentlig IP**.
9. Välj fliken **Hantering** och välj sedan **Skapa nytt** i **Diagnostiklagringskonto**för att skapa ett nytt lagringskonto.
10. Lämna standardvärdena för avsnitten **Identitet,** **Automatisk avstängning**och **Säkerhetskopiering.**
11. Välj **Granska + skapa**. När valideringen är klar väljer du **Skapa**. Det tar några minuter att skapa den virtuella datorn.

## <a name="configure-azure-bastion"></a>Konfigurera Azure Bastion

[Azure Bastion](https://azure.microsoft.com/services/azure-bastion/) är en fullständigt hanterad Azure-tjänst som ger säker RDP- och SSH-åtkomst till virtuella datorer direkt från Azure-portalen. Med hjälp av Azure Bastion-tjänsten tas behovet av att konfigurera nätverksinställningar relaterade till RDP-åtkomst.

1. Välj **Lägg till** högst upp i resursgruppvyn i portalen.
2. Skriv "Bastion" i sökfältet.  Välj "Bastion".
3. Välj **Skapa** om du vill påbörja processen med att skapa en ny Azure Bastion-resurs. Du kommer att märka ett felmeddelande i avsnittet **Virtuellt nätverk** eftersom det ännu inte finns något `AzureBastionSubnet` undernät. Undernätet skapas i följande steg. Använd inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![Börja skapa Azure Bastion](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myBastion myBastion myBastion myBas | Namnet på den nya Bastion-resursen |
    | **Regionen** | USA, norra centrala | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **Virtuellt nätverk** | myResourceGroup-vnet | Det virtuella nätverk där Bastion-resursen kommer att skapas i |
    | **Undernät** | AzureBastionSubnet | Undernätet i det virtuella nätverket som den nya bastion-värdresursen ska distribueras till. Du måste skapa ett undernät `AzureBastionSubnet`med namnvärdet . Det här värdet gör att Azure vet vilket undernät som bastionresurserna ska distribueras till. Du måste använda ett undernät på minst `/27` eller större (`/27`, `/26`och så vidare). |

    > [!NOTE]
    > En detaljerad steg-för-steg-guide för att skapa en Azure Bastion-resurs finns i självstudien [Skapa en Azure Bastion-värd.](../bastion/bastion-create-host-portal.md)

4. Skapa ett undernät där Azure kan etablera Azure Bastion-värden. Om du väljer **Hantera undernätskonfiguration** öppnas en ny ruta där du kan definiera ett nytt undernät.  Välj **+ Undernät** om du vill skapa ett nytt undernät.
5. Undernätet måste vara av `AzureBastionSubnet` namnet och undernätsprefixet måste vara minst `/27`.  Välj **OK** om du vill skapa undernätet.

    >[!div class="mx-imgBorder"]
    >![Skapa undernät för Azure Bastion-värd](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. På sidan **Skapa en bastion** väljer `AzureBastionSubnet` du den nyskapade i listan över tillgängliga undernät.

    >[!div class="mx-imgBorder"]
    >![Skapa en Azure Bastion-värd med ett visst undernät](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Välj **Granska & Skapa**. När valideringen är klar väljer du **Skapa**. Det tar några minuter innan Azure Bastion-resursen skapas.

## <a name="create-an-azure-functions-app"></a>Skapa en Azure-funktionsapp

Nästa steg är att skapa en funktionsapp i Azure med hjälp av [förbrukningsplanen](functions-scale.md#consumption-plan). Du distribuerar din funktionskod till den här resursen senare i självstudien.

1. Välj **Lägg till** högst upp i resursgruppvyn i portalen.
2. Välj **app för > funktion**
3. I avsnittet Grunderna använder du **funktionsappinställningarna** enligt tabellen nedan.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Resursgrupp** | myResourceGroup | Välj resursgruppen om du vill innehålla alla resurser för den här självstudien.  Om du använder samma resursgrupp för funktionsappen och den virtuella datorn blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på funktionsapp** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är a-z (skiftlägesokänslig), 0-9 och -. |
    | **Publicera** | Kod | Alternativ för att publicera kodfiler eller en Docker-container. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. |
    | **Regionen** | USA, norra centrala | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    Välj **knappen Nästa: Värd >.**
4. För avsnittet **Värd** väljer du rätt **lagringskonto,** **operativsystem**och **planera** en beskriven i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Lagringskonto** | Globalt unikt namn | Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagringskonto](./functions-scale.md#storage-account-requirements). |
    | **Operativsystem** | Önskat operativsystem | Ett operativsystem är förvalt för dig baserat på din runtime stack val, men du kan ändra inställningen om det behövs. |
    | **Planera** | Förbrukning | [Värdplanen](./functions-scale.md) avgör hur funktionsappen skalas och resurser som är tillgängliga för varje instans. |
5. Välj **Granska + Skapa** om du vill granska appkonfigurationsvalen.
6. Välj **Skapa** för att etablera och distribuera funktionsappen.

## <a name="configure-access-restrictions"></a>Konfigurera åtkomstbegränsningar

Nästa steg är att konfigurera [åtkomstbegränsningar](../app-service/app-service-ip-restrictions.md) för att säkerställa att endast resurser i det virtuella nätverket kan anropa funktionen.

[Privat platsåtkomst](functions-networking-options.md#private-site-access) aktiveras genom att skapa en Azure Virtual [Network-tjänstslutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) mellan funktionsappen och det angivna virtuella nätverket. Åtkomstbegränsningar implementeras via tjänstslutpunkter. Tjänstslutpunkter säkerställer att endast trafik som kommer inifrån det angivna virtuella nätverket kan komma åt den angivna resursen. I det här fallet är den angivna resursen Azure-funktionen.

1. Öppna avsnittet Status för nätverksfunktion i **funktionsappen.** **Networking** *Networking*
2. Sidan **Status för nätverksfunktion** är utgångspunkten för att konfigurera Azure Front Door, Azure CDN och även åtkomstbegränsningar. Välj **Konfigurera åtkomstbegränsningar** för att konfigurera privat platsåtkomst.
3. På sidan **Åtkomstbegränsningar** visas bara standardbegränsningen på plats. Standardinställningen begränsar inte åtkomsten till funktionsappen.  Välj **Lägg till regel** om du vill skapa en privat konfiguration för begränsning av platsåtkomst.
4. I fönstret **Lägg till åtkomstbegränsning** väljer du **Virtuellt nätverk** i listrutan **Typ** och väljer sedan det tidigare skapade virtuella nätverket och undernätet.
5. Sidan **Åtkomstbegränsningar** visar nu att det finns en ny begränsning. Det kan ta några sekunder innan **slutpunktsstatusen** ändras från `Disabled` till `Provisioning` `Enabled`.

    >[!IMPORTANT]
    > Varje funktionsapp har en [Kudu-webbplats (Advanced Tool)](../app-service/app-service-ip-restrictions.md#scm-site) som används för att hantera funktionsappdistributioner. Den här webbplatsen nås från `<FUNCTION_APP_NAME>.scm.azurewebsites.net`en webbadress som: . Eftersom åtkomstbegränsningar inte har aktiverats på den här distributionsplatsen kan du fortfarande distribuera projektkoden från en lokal utvecklararbetsstation eller byggtjänst utan att behöva etablera en agent i det virtuella nätverket.

## <a name="access-the-functions-app"></a>Få tillgång till funktionsappen

1. Gå tillbaka till den tidigare skapade funktionsappen.  Kopiera WEBBADRESSEN i avsnittet **Översikt.**

    >[!div class="mx-imgBorder"]
    >![Hämta url:en till funktionsappen](./media/functions-create-private-site-access/access-function-overview.png)

2. Om du försöker komma åt funktionsappen nu från datorn utanför det virtuella nätverket får du en HTTP 403-sida som anger att appen har stoppats.  Appen stoppas inte. Svaret är faktiskt en HTTP 403 IP Forbidden status.
3. Nu kommer du åt din funktion från den tidigare skapade virtuella datorn, som är ansluten till ditt virtuella nätverk. För att komma åt webbplatsen från den virtuella datorn måste du ansluta till den virtuella datorn via Azure Bastion-tjänsten.  Välj först **Anslut** och välj sedan **Bastion**.
4. Ange det användarnamn och lösenord som krävs för att logga in på den virtuella datorn.  Välj **Anslut**. Ett nytt webbläsarfönster kommer att dyka upp så att du kan interagera med den virtuella datorn.
5. Eftersom den här virtuella datorn har åtkomst till funktionen via det virtuella nätverket är det möjligt att komma åt webbplatsen från webbläsaren på den virtuella datorn.  Det är viktigt att notera att även om funktionsappen endast är tillgänglig från det angivna virtuella nätverket, kvarstår en offentlig DNS-post. Som visas ovan kommer försök att komma åt webbplatsen att resultera i ett HTTP 403-svar.

## <a name="create-a-function"></a>Skapa en funktion

Nästa steg i den här självstudien är att skapa en HTTP-utlöst Azure-funktion. Om du anropar funktionen via en HTTP GET eller POST bör det resultera i ett svar på "Hej, {name}".  

1. Följ en av följande snabbstarter för att skapa och distribuera din Azure Functions-app.

    * [Visual Studio-koden](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Kommandoraden](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. När du publicerar ditt Azure Functions-projekt väljer du den funktionsappresurs som du skapade tidigare i den här självstudien.
3. Kontrollera att funktionen har distribuerats.

    >[!div class="mx-imgBorder"]
    >![Distribuerad funktion i listan över funktioner](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Anropa funktionen direkt

1. För att testa åtkomsten till funktionen måste du kopiera funktions-URL:en. Välj den distribuerade funktionen och välj sedan **</> Hämta funktions-URL**. Klicka sedan på knappen **Kopiera** om du vill kopiera WEBBADRESSEN till Urklipp.

    >[!div class="mx-imgBorder"]
    >![Kopiera funktionens URL](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > När funktionen körs visas ett körningsfel i portalen som anger att funktionens körning inte kan starta. Trots meddelandetexten körs funktionsappen faktiskt. Felet är ett resultat av de nya åtkomstbegränsningarna, som förhindrar att portalen frågar för att kontrollera körningen.

2. Klistra in webbadressen i en webbläsare. När du nu försöker komma åt funktionsappen från en dator utanför det virtuella nätverket får du ett HTTP 403-svar som anger att appen har stoppats.

## <a name="invoke-the-function-from-the-virtual-network"></a>Anropa funktionen från det virtuella nätverket

Åtkomst till funktionen via en webbläsare (med hjälp av Azure Bastion-tjänsten) på den konfigurerade virtuella datorn i det virtuella nätverket resulterar i framgång!

>[!div class="mx-imgBorder"]
>![Få tillgång till Azure-funktionen via Azure Bastion](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg


> [!div class="nextstepaction"]
> [Läs mer om nätverksalternativen i Funktioner](./functions-networking-options.md)
