---
title: Hitta tillgängliga rum med Azure Digital Twins (C#) | Microsoft Docs
description: I den här snabbstarten kör du två .NET Core-exempelprogram som skickar simulerad telemetri för rörelse och koldioxid till ett utrymme i Azure Digital Twins. Målet är att hitta tillgängliga rum med frisk luft via hanterings-API:er efter beräknad bearbetning i molnet.
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc
ms.date: 11/7/2018
ms.author: alinast
ms.openlocfilehash: 6e83ca543937948ad8028969cceca0f8787972c9
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/08/2018
ms.locfileid: "51281226"
---
# <a name="quickstart-find-available-rooms-using-azure-digital-twins"></a>Snabbstart: Hitta tillgängliga rum med hjälp av Azure Digital Twins

Med tjänsten Azure Digital Twins kan du återskapa en digital bild av den fysiska miljön. Du kan sedan få ett meddelande via händelser i din miljö och anpassa dina svar efter dem. 

I den här snabbstarten används [ett par .NET-exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp) för att digitalisera en tänkt kontorsbyggnad, och du får se hur du hittar tillgängliga rum i byggnaden. Du kan associera flera sensorer med Digital Twins med din miljö. Utöver rummets tillgänglighet kan du också ta reda på om luftkvaliteten i dina tillgängliga utrymmen är optimal med hjälp av en simulerad sensor för koldioxid. Ett av exempelprogrammen genererar slumpmässiga sensordata som hjälper dig att visualisera det här scenariot.

Följande videoklipp innehåller en sammanfattning av snabbstartsinstallationen:

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Nödvändiga komponenter

1. Om du inte har något Azure-konto [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. De två konsolprogram som du kör i den här snabbstarten skrivs med C#. Du måste installera [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn. Om du har installerat .NET Core SDK kan du kan kontrollera den aktuella versionen av C# på din utvecklingsdator som kör `dotnet --version` i en kommandotolk.

1. Ladda ned [C#-exempelprojektet](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip) och extrahera arkivet digital-twins-samples-csharp-master.zip. 


## <a name="create-a-digital-twins-instance"></a>Skapa en Digital Twins-instans

Använd stegen i det här avsnittet för att skapa en ny instans av Digital Twin i [portalen](https://portal.azure.com).

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Ange behörigheter för din app

Det här avsnittet registrerar ditt exempelprogram till Azure Active Directory (AAD), så att det kan komma åt din Digital Twins-instans. Om du redan har en AAD-appregistrering kan du återanvända den för ditt exempel. Se till att den är konfigurerad enligt beskrivningen i det här avsnittet. 

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="build-application"></a>Skapa program

Skapa programmet för rumsanvändning hjälp av följande steg:

1. Öppna en kommandotolk och gå till mappen som dina digital-twins-samples-csharp-master.zip-filer extraherades till.
1. Kör `cd occupancy-quickstart/src`.
1. Kör `dotnet restore`.
1. Redigera *appSettings.json* för att uppdatera följande variabler:
    - *ClientId*: Ange *program-ID* för din AAD-appregistrering som beskrevs i föregående avsnitt.
    - *Klient*: Ange *katalog-ID* för din AAD-klient, som också beskrevs i föregående avsnitt.
    - *BaseUrl*: Webbadressen till *hanterings-API:et* för din Digital Twins-instans som är i formatet `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Ersätt platshållarna i webbadressen med värden för din instans från föregående avsnitt.

## <a name="provision-graph"></a>Etablera graf

I det här steget etableras den rumsliga grafen för Digital Twins med flera utrymmen, en enhet, två sensorer, en anpassad funktion och en rolltilldelning. Den rumsliga grafen etableras med filen [*provisionSample.yaml*](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Kör `dotnet run ProvisionSample`.
    >[!NOTE]
    >Vi använder Azure CLI-verktyget Inloggning på enhet för att autentisera användaren för Azure AD. Användaren måste ange en viss kod för att autentisera med hjälp av [Microsofts inloggningssida](https://microsoft.com/devicelogin). När koden anges följer du stegen för att autentisera. Användaren ombeds att autentisera varje gång verktyget körs.
    
    >[!TIP]
    > Om du får följande fel när du kör det här steget kan du kontrollera att dina variabler har kopierats korrekt. 
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. Etableringssteget kan ta några minuter. Det tillhandahåller också en IoT-hubb i din Digital Twins-instans och det loopas tills IoTHub har Status=`Running`.

    ![Etablera exempel][4]

1. I slutet av körningen kopierar du enhetens `ConnectionString` för användning i exemplet med enhetssimulatorn. Kopiera den sträng som beskrivs i bilden nedan:

    ![Etablera exempel][1]

## <a name="send-sensor-data"></a>Skicka sensordata

Du kan skapa och köra sensorsimulatorprogrammet med hjälp av stegen nedan:

1. Öppna en ny kommandotolk och gå till projektet som du har laddat ned i mappen digital-twins-samples-csharp-master.
1. Kör `cd device-connectivity`.
1. Kör `dotnet restore`.
1. Redigera *appsettings.json* för att uppdatera *DeviceConnectionString* med `ConnectionString` ovan.
1. Kör `dotnet run` för att börja skicka sensordata. Du bör se att de skickas till Digital Twins-tjänsten som i bilden nedan:

     ![Enhetsanslutning][2]

1. Låt den här simulatorn köras så att du kan visa resultat sida vid sida med nästa steg. Det här fönstret visar de simulerade sensordata som skickas till Digital Twins, och i nästa steg ställs en fråga i realtid för att hitta tillgängliga rum med frisk luft.

    >[!TIP]
    > Om du får följande fel när du kör det här steget kan du kontrollera att din `DeviceConnectionString` har kopierats korrekt.  
    > `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Hitta tillgängliga utrymmen med frisk luft

Sensorexemplet simulerar slumpmässiga datavärden för två sensorer, rörelse och koldioxid. Tillgängliga områden med frisk luft definieras i vårt exempel utan någon närvaro i rummet och en koldioxidnivå under 1 000 ppm. Om villkoret inte uppfylls är utrymmet inte tillgängligt, eller så är luftkvaliteten dålig.

1. Öppna kommandotolken du använde för att köra etableringssteget ovan.
1. Kör `dotnet run GetAvailableAndFreshSpaces`.
1. Titta på den här kommandotolken och sensordatas kommandotolk sida vid sida som du ser nedan. 
1. En kommandotolk skickar simulerade data för rörelse och koldioxid till Digital Twins var femte sekund. Det andra kommandot läser diagrammet i realtid för att ta reda på tillgängliga rum med frisk luft baserat på slumpmässiga simulerade data. Det visar något av dessa villkor i nära realtid baserat på vad sensordata senast skickade:
    - Tillgängliga rum med frisk luft.
    - Upptaget eller dålig luftkvalitet i rummet.

     ![Hämta tillgängliga utrymmen med frisk luft][3]

För att förstå vad som hände i den här snabbstarten och vad API: er har anropats, öppna [Visual Studio Code](https://code.visualstudio.com/Download) med kodprojektet för arbetsytan finns i digital-twins-samples-csharp (se kommandot nedan). Självstudierna gör en djupdykning i koden och lär dig att ändra konfigurationsdata och vilka API:er som anropas. Om du vill veta mer om hanterings-API:er går du sidan Digital Twins Swagger `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net//management/swagger` eller bläddrar till [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger). 

```
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna visar information om hur du skapar ett program som anläggningschefer kan använda för att öka produktiviteten och driva byggnaden mer effektivt.

Om du planerar att fortsätta med självstudierna, ska du inte rensa resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta kan du använda stegen nedan för att ta bort alla resurser som har skapats i den här snabbstarten:

1. Ta bort mappen som skapades när du laddade ned exempellagringsplatsen.
1. Välj **Alla resurser** på menyn till vänster i [Azure-portalen](http://portal.azure.com) och välj sedan din Digital Twins-resurs. Klicka på **Ta bort** överst i fönstret **Alla resurser**.

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten gav dig en översikt över ett enkelt scenario för att hitta rum med bra driftsförhållanden. För en mer detaljerad analys av det här scenariot fortsätter du till den här självstudien:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera Azure Digital Twins och konfigurera ett spatialt diagram](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
