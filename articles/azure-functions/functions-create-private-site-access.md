---
title: Aktivera åtkomst till privat plats till Azure Functions
description: Lär dig hur du konfigurerar åtkomst till Azure Virtual Network-webbplatser för Azure Functions.
author: mcollier
ms.author: mcollier
ms.service: azure-functions
ms.topic: tutorial
ms.date: 02/15/2020
ms.openlocfilehash: ada08de182791c6ecb2b83ef3b924bf40975e1ee
ms.sourcegitcommit: f5e4d0466b417fa511b942fd3bd206aeae0055bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2020
ms.locfileid: "78851296"
---
# <a name="tutorial-establish-azure-functions-private-site-access"></a>Självstudie: upprätta Azure Functions åtkomst till privat webbplats

I den här självstudien får du se hur du aktiverar [åtkomst till privata platser](./functions-networking-options.md#private-site-access) med Azure Functions. Genom att använda privat plats åtkomst kan du kräva att funktions koden bara utlöses från ett speciellt virtuellt nätverk.

Åtkomst till privata webbplatser är användbart i scenarier när åtkomst till Function-appen måste begränsas till ett visst virtuellt nätverk. Till exempel kan Function-appen endast användas för anställda i en viss organisation eller tjänster som finns i det angivna virtuella nätverket (till exempel en annan Azure-funktion, en virtuell Azure-dator eller ett AKS-kluster).

Om en Functions-app behöver åtkomst till Azure-resurser i det virtuella nätverket, eller är ansluten via [tjänst slut punkter](../virtual-network/virtual-network-service-endpoints-overview.md), krävs [integrering av virtuella nätverk](./functions-create-vnet.md) .

I den här självstudien får du lära dig hur du konfigurerar privat plats åtkomst för din Function-app:

> [!div class="checklist"]
> * Skapa en virtuell dator
> * Skapa en Azure skydds-tjänst
> * Skapa en Azure Functions-app
> * Konfigurera en tjänst slut punkt för virtuellt nätverk
> * Skapa och distribuera en Azure-funktion
> * Anropa funktionen från utsidan och inom det virtuella nätverket

Om du inte har en Azure-prenumeration kan du skapa ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

## <a name="topology"></a>Topologi

Följande diagram visar arkitekturen för den lösning som ska skapas:

![Högnivå arkitektur diagram för åtkomst lösning för privat webbplats](./media/functions-create-private-site-access/topology.png)

## <a name="prerequisites"></a>Förutsättningar

I den här självstudien är det viktigt att du förstår IP-adressering och undernät. Du kan börja med [den här artikeln som beskriver grunderna för adressering och undernät](https://support.microsoft.com/help/164015/understanding-tcp-ip-addressing-and-subnetting-basics). Många fler artiklar och videor är tillgängliga online.

## <a name="sign-in-to-azure-portal"></a>Logga in på Azure-portalen

Logga in på [Azure Portal](https://portal.azure.com).

## <a name="create-a-virtual-machine"></a>Skapa en virtuell dator

Det första steget i den här självstudien är att skapa en ny virtuell dator i ett virtuellt nätverk.  Den virtuella datorn kommer att användas för att få åtkomst till din funktion när du har begränsat åtkomsten till den som bara är tillgänglig i det virtuella nätverket.

1. Välj knappen **skapa en resurs** .

2. I Sök fältet skriver du `Windows Server`och väljer **Windows Server** i Sök resultatet.

3. Välj **Windows server 2019 Data Center** i listan över Windows Server-alternativ och klicka på knappen **skapa** .

4. På fliken **grundläggande** använder du de VM-inställningar som anges i tabellen nedanför bilden:

    >[!div class="mx-imgBorder"]
    >fliken ![grunder för en ny virtuell Windows-dator](./media/functions-create-private-site-access/create-vm-3.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Prenumeration** | Din prenumeration | Den prenumeration som dina resurser skapas under. |
    | [**Resurs grupp**](../azure-resource-manager/management/overview.md) | myResourceGroup | Välj den resurs grupp som innehåller alla resurser för den här självstudien.  Med samma resurs grupp blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Namn på virtuell dator** | myVM | Det virtuella dator namnet måste vara unikt i resurs gruppen |
    | [**Nationella**](https://azure.microsoft.com/regions/) | USA Norra centrala USA | Välj en region nära dig eller nära de funktioner som ska nås. |
    | **Offentliga inkommande portar** | Ingen | Välj **ingen** för att se till att det inte finns någon inkommande anslutning till den virtuella datorn från Internet. Fjärråtkomst till den virtuella datorn kommer att konfigureras via Azure skydds-tjänsten. |

5. Välj fliken **nätverk** och välj **Skapa ny** för att konfigurera ett nytt virtuellt nätverk.

    >[!div class="mx-imgBorder"]
    >![skapa ett nytt virtuellt nätverk för den nya virtuella datorn](./media/functions-create-private-site-access/create-vm-networking.png)

6. I **Skapa virtuellt nätverk**använder du inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![skapa ett nytt virtuellt nätverk för den nya virtuella datorn](./media/functions-create-private-site-access/create-vm-vnet-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myResourceGroup-vnet | Du kan använda standard namnet som genereras för det virtuella nätverket. |
    | **Adressintervall** | 10.10.0.0/16 | Använd ett enda adress intervall för det virtuella nätverket. |
    | **Namn på undernät** | Självstudier | Namnet på under nätet. |
    | **Adress intervall** (undernät) | 10.10.1.0/24 | Under näts storleken definierar hur många gränssnitt som kan läggas till i under nätet. Det här under nätet används av den virtuella datorn. Ett `/24` undernät tillhandahåller 254-värd adresser. |

7. Välj **OK** för att skapa det virtuella nätverket.
8. Gå tillbaka till fliken **nätverk** , se till att **ingen** är markerad för **offentlig IP**.
9. Välj fliken **hantering** och sedan **Skapa nytt** lagrings konto i **diagnostik Storage-konto**.
10. Lämna standardvärdena för avsnitten **identitet**, **Automatisk avstängning**och **säkerhets kopiering** .
11. Välj **Granska + skapa**. När verifieringen är klar väljer du **skapa**. Processen för att skapa virtuella datorer tar några minuter.

## <a name="configure-azure-bastion"></a>Konfigurera Azure-skydds

[Azure skydds](https://azure.microsoft.com/services/azure-bastion/) är en helt hanterad Azure-tjänst som ger säker RDP-och SSH-åtkomst till virtuella datorer direkt från Azure Portal. Genom att använda Azure skydds-tjänsten tar du bort behovet av att konfigurera nätverks inställningar som rör RDP-åtkomst.

1. I portalen väljer du **Lägg till** överst i vyn resurs grupp.
2. Skriv "skydds" i Sök fältet.  Välj "skydds".
3. Välj **skapa** för att påbörja processen med att skapa en ny Azure skydds-resurs. Du kommer att märka ett fel meddelande i det **virtuella nätverket** -avsnittet eftersom det inte finns något `AzureBastionSubnet` under nätet än. Under nätet skapas i följande steg. Använd inställningarna i tabellen under bilden:

    >[!div class="mx-imgBorder"]
    >![början av att skapa Azure-skydds](./media/functions-create-private-site-access/create-bastion-basics-1.png)

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Namn** | myBastion | Namnet på den nya skydds-resursen |
    | **Region** | USA, norra centrala | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |
    | **Virtuellt nätverk** | myResourceGroup-vnet | Det virtuella nätverk där skydds-resursen ska skapas i |
    | **Undernät** | AzureBastionSubnet | Under nätet i det virtuella nätverket som den nya skydds-värd resursen ska distribueras till. Du måste skapa ett undernät med namnet Value `AzureBastionSubnet`. Med det här värdet kan Azure veta vilket undernät som skydds-resurserna ska distribueras till. Du måste använda ett undernät på minst `/27` eller större (`/27`, `/26`och så vidare). |

    > [!NOTE]
    > En detaljerad steg-för-steg-guide om hur du skapar en Azure skydds-resurs finns i själv studie kursen [skapa en Azure skydds-värd](../bastion/bastion-create-host-portal.md) .

4. Skapa ett undernät där Azure kan etablera Azure skydds-värden. Om du väljer **Hantera under näts konfiguration** öppnas ett nytt fönster där du kan definiera ett nytt undernät.  Välj **+ undernät** för att skapa ett nytt undernät.
5. Under nätet måste ha namnet `AzureBastionSubnet` och under nätets prefix måste vara minst `/27`.  Välj **OK** för att skapa under nätet.

    >[!div class="mx-imgBorder"]
    >![skapa undernät för Azure skydds-värd](./media/functions-create-private-site-access/create-bastion-subnet-2.png)

6. På sidan **skapa en skydds** väljer du det nyligen skapade `AzureBastionSubnet` i listan över tillgängliga undernät.

    >[!div class="mx-imgBorder"]
    >![skapa en Azure skydds-värd med ett speciellt undernät](./media/functions-create-private-site-access/create-bastion-basics-2.png)

7. Välj **granska & skapa**. När verifieringen är klar väljer du **skapa**. Det tar några minuter för Azure skydds-resursen att skapas.

## <a name="create-an-azure-functions-app"></a>Skapa en Azure Functions-app

Nästa steg är att skapa en Function-app i Azure med hjälp av [förbruknings planen](functions-scale.md#consumption-plan). Du distribuerar funktions koden till den här resursen senare i självstudien.

1. I portalen väljer du **Lägg till** överst i vyn resurs grupp.
2. Välj **compute > Funktionsapp**
3. I avsnittet **grundläggande** inställningar använder du funktionen appinställningar som anges i tabellen nedan.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Resursgrupp** | myResourceGroup | Välj den resurs grupp som innehåller alla resurser för den här självstudien.  Genom att använda samma resurs grupp för Function-appen och den virtuella datorn blir det enklare att rensa resurser när du är klar med den här självstudien. |
    | **Funktionsapp namn** | Globalt unikt namn | Namn som identifierar din nya funktionsapp. Giltiga tecken är a-z (Skift läges okänslig), 0-9 och-. |
    | **Publicera** | Kod | Alternativ för att publicera kod filer eller en Docker-behållare. |
    | **Körningsstack** | Önskat språk | Välj en körning som stöder det funktionsprogrammeringsspråk som du föredrar. |
    | **Region** | USA, norra centrala | Välj en [plats](https://azure.microsoft.com/regions/) nära dig eller nära andra tjänster som kommer att användas i dina funktioner. |

    Välj **Nästa: Hosting >** -knappen.
4. I avsnittet **värd** väljer du rätt **lagrings konto**, **operativ system**och **planerar** enligt beskrivningen i följande tabell.

    | Inställning      | Föreslaget värde  | Beskrivning      |
    | ------------ | ---------------- | ---------------- |
    | **Lagringskonto** | Globalt unikt namn | Skapa ett lagringskonto som används av din funktionsapp. Namnet på ett lagringskonto måste vara mellan 3 och 24 tecken långt och får endast innehålla siffror och gemener. Du kan också använda ett befintligt konto som måste uppfylla kraven för [lagrings kontot](./functions-scale.md#storage-account-requirements). |
    | **Operativsystem** | Önskat operativ system | Ett operativ system är i förväg valt för dig baserat på ditt val av körnings stack, men du kan ändra inställningen om det behövs. |
    | **Planera** | Förbrukning | [Värd planen](./functions-scale.md) styr hur Function-appen skalas och vilka resurser som är tillgängliga för varje instans. |
5. Välj **Granska + skapa** för att granska konfigurations valen för appen.
6. Välj **Skapa** för att etablera och distribuera funktionsappen.

## <a name="configure-access-restrictions"></a>Konfigurera åtkomst begränsningar

Nästa steg är att konfigurera [åtkomst begränsningar](../app-service/app-service-ip-restrictions.md) för att säkerställa att endast resurser i det virtuella nätverket kan anropa funktionen.

Åtkomst till [privat webbplats](functions-networking-options.md#private-site-access) är aktiverat genom att skapa en Azure Virtual Network [service-slutpunkt](../virtual-network/virtual-network-service-endpoints-overview.md) mellan Function-appen och det angivna virtuella nätverket. Åtkomst begränsningar implementeras via tjänstens slut punkter. Tjänst slut punkter säkerställer endast trafik från det angivna virtuella nätverket som har åtkomst till den angivna resursen. I det här fallet är den angivna resursen Azure-funktionen.

1. I Function-appen fortsätter du till fliken **plattforms funktioner** . Välj **nätverks** länken under rubriken nätverks *avsnitt för att öppna avsnittet status* för nätverks funktion.
2. Sidan **status för nätverks funktion** är start punkten för att konfigurera Azure-frontend, Azure CDN och även åtkomst begränsningar. Välj **Konfigurera åtkomst begränsningar** för att konfigurera åtkomst till privat plats.
3. På sidan **åtkomst begränsningar** ser du bara standard begränsningen på plats. Som standard placeras inga begränsningar för åtkomst till Function-appen.  Välj **Lägg till regel** för att skapa en åtkomst begränsnings konfiguration för privata platser.
4. I fönstret **Lägg till åtkomst begränsning** väljer du **Virtual Network** i list rutan **typ** och väljer sedan det tidigare skapade virtuella nätverket och under nätet.
5. Sidan **åtkomst begränsningar** visar nu att det finns en ny begränsning. Det kan ta några sekunder innan **slut punkts statusen** ändras från `Disabled` via `Provisioning` till `Enabled`.

    >[!IMPORTANT]
    > Varje Function-app har en [kudu-webbplats (Advanced Tool)](../app-service/app-service-ip-restrictions.md#scm-site) som används för att hantera Function app-distributioner. Den här webbplatsen nås från en URL som: `<FUNCTION_APP_NAME>.scm.azurewebsites.net`. Eftersom åtkomst begränsningar inte har Aktiver ATS på den här distributions platsen kan du fortfarande distribuera din projekt kod från en lokal utvecklare eller en build-tjänst utan att behöva etablera en agent i det virtuella nätverket.

## <a name="access-the-functions-app"></a>Öppna Functions-appen

1. Gå tillbaka till den tidigare skapade funktions appen.  I avsnittet **Översikt** kopierar du URL: en.

    >[!div class="mx-imgBorder"]
    >![Hämta URL: en för Function-appen](./media/functions-create-private-site-access/access-function-overview.png)

2. Om du försöker komma åt Function-appen nu från din dator utanför det virtuella nätverket får du en HTTP 403-sida som visar att appen har stoppats.  Appen har inte stoppats. Svaret är i själva verket en HTTP 403-status som är förbjuden.
3. Nu kommer du åt din funktion från den tidigare skapade virtuella datorn, som är ansluten till det virtuella nätverket. För att få åtkomst till platsen från den virtuella datorn måste du ansluta till den virtuella datorn via Azure skydds-tjänsten.  Välj först **Anslut** och välj sedan **skydds**.
4. Ange det användar namn och lösen ord som krävs för att logga in på den virtuella datorn.  Välj **Anslut**. Ett nytt webbläsarfönster öppnas så att du kan interagera med den virtuella datorn.
5. Eftersom den här virtuella datorn har åtkomst till funktionen via det virtuella nätverket är det möjligt att komma åt webbplatsen från webbläsaren på den virtuella datorn.  Det är viktigt att Observera att även om Function-appen endast är tillgänglig från det angivna virtuella nätverket, förblir en offentlig DNS-post. Som det visas ovan kommer försök att komma åt webbplatsen att resultera i ett HTTP 403-svar.

## <a name="create-a-function"></a>Skapa en funktion

Nästa steg i den här självstudien är att skapa en HTTP-utlöst Azure-funktion. Om du anropar funktionen via en HTTP GET-eller POST-POST ska du svara på "Hello, {name}".  

1. Följ någon av följande snabb starter för att skapa och distribuera din Azure Functions-app.

    * [Visual Studio Code](./functions-create-first-function-vs-code.md)
    * [Visual Studio](./functions-create-your-first-function-visual-studio.md)
    * [Kommandorad](./functions-create-first-azure-function-azure-cli.md)
    * [Maven (Java)](./functions-create-first-java-maven.md)

2. När du publicerar Azure Functions-projektet väljer du den Function app-resurs som du skapade tidigare i den här självstudien.
3. Kontrol lera att funktionen har distribuerats.

    >[!div class="mx-imgBorder"]
    >![distribuerad funktion i en lista över funktioner](./media/functions-create-private-site-access/verify-deployed-function.png)

## <a name="invoke-the-function-directly"></a>Anropa funktionen direkt

1. För att kunna testa åtkomsten till funktionen måste du kopiera funktions webb adressen. Välj den distribuerade funktionen och välj sedan **</> Hämta funktions webb adress**. Klicka sedan på **kopierings** knappen för att kopiera webb adressen till Urklipp.

    >[!div class="mx-imgBorder"]
    >![kopiera funktions webb adressen](./media/functions-create-private-site-access/get-function-url.png)

    > [!NOTE]
    > När funktionen körs visas ett körnings fel i portalen som talar om att funktionen runtime inte kan starta. Trots meddelande texten körs Function-appen faktiskt. Felet är ett resultat av de nya åtkomst begränsningarna, vilket hindrar portalen från att fråga för att kontrol lera körningen.

2. Klistra in URL-adressen i en webbläsare. När du försöker komma åt Function-appen från en dator utanför det virtuella nätverket får du ett HTTP 403-svar som anger att appen har stoppats.

## <a name="invoke-the-function-from-the-virtual-network"></a>Anropa funktionen från det virtuella nätverket

Att komma åt funktionen via en webbläsare (med hjälp av Azure skydds-tjänsten) på den konfigurerade virtuella datorn i det virtuella nätverket resulterar i lyckad!

>[!div class="mx-imgBorder"]
>![få åtkomst till Azure-funktionen via Azure skydds](./media/functions-create-private-site-access/access-function-via-bastion-final.png)

[!INCLUDE [clean-up-section-portal](../../includes/clean-up-section-portal.md)]

## <a name="next-steps"></a>Nästa steg


> [!div class="nextstepaction"]
> [Lär dig mer om nätverks alternativen i functions](./functions-networking-options.md)
