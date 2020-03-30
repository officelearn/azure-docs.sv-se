---
title: 'Snabbstart: Hitta tillgängliga rum - Azure Digital Twins | Microsoft-dokument'
description: I den här snabbstarten kör du två .NET Core-exempelprogram som skickar simulerad telemetri för rörelse och koldioxid till ett utrymme i Azure Digital Twins. Målet är att hitta tillgängliga rum med frisk luft via hanterings-API:er efter beräknad bearbetning i molnet.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.devlang: csharp
ms.topic: quickstart
ms.custom: mvc seodec18
ms.date: 01/10/2020
ms.openlocfilehash: de8611e53339d38a03836bc9272ad42ff88a59b8
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/26/2020
ms.locfileid: "79371434"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Snabbstart: Hitta tillgängliga rum med hjälp av Azure Digital Twins

[!INCLUDE [digital-twins-preview-limit-alert](../../includes/digital-twins-preview-limit-alert.md)]

Med tjänsten Azure Digital Twins kan du återskapa en digital bild av den fysiska miljön. Du kan sedan få ett meddelande via händelser i din miljö och anpassa dina svar efter dem.

I den här snabbstarten används [ett par .NET-exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp) för att digitalisera en tänkt kontorsbyggnad. Här visas hur du hittar tillgängliga rum i byggnaden. Du kan associera många sensorer med Digital Twins med din miljö. Du kan även få reda på om luftkvaliteten i det tillgängliga rummet är optimalt med hjälp av en simulerad koldioxidsensor. Ett av exempelprogrammen genererar slumpmässiga sensordata som hjälper dig att visualisera det här scenariot.

I följande video sammanfattas snabbstartsinställningarna:

>[!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Krav

1. Om du inte har något Azure-konto [skapar du ett kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) innan du börjar.

1. De två konsolprogram som du kör i den här snabbstarten skrivs med C#. Installera [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn. Om du har .NET Core SDK installerat verifierar du den aktuella versionen av C# på utvecklingsdatorn. Kör `dotnet --version` i en kommandotolk.

1. Ladda ned [C#-exempelprojektet](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). Extrahera arkivet digital-twins-samples-csharp-master.zip.

## <a name="create-a-digital-twins-instance"></a>Skapa en Digital Twins-instans

Använd stegen i det här avsnittet för att skapa en ny instans av Digital Twin i [portalen](https://portal.azure.com).

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]

## <a name="set-permissions-for-your-app"></a>Ange behörigheter för din app

Det här avsnittet registrerar ditt exempelprogram till Azure Active Directory (Azure AD), så att det kan komma åt din Digital Twins-instans. Om du redan har en Azure AD-appregistrering kan du återanvända den för ditt exempel. Kontrollera att den är konfigurerad enligt beskrivningen i det här avsnittet.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]

## <a name="build-application"></a>Skapa program

Skapa beläggningsprogrammet med hjälp av följande steg.

1. Öppna en kommandotolk. Gå till mappen där dina `digital-twins-samples-csharp-master.zip`-filer har extraherats.
1. Kör `cd occupancy-quickstart/src`.
1. Kör `dotnet restore`.
1. Redigera [appSettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/appSettings.json) för att uppdatera följande variabler:
    - **ClientId**: Ange program-ID för din Azure AD-appregistrering som beskrevs i föregående avsnitt.
    - **Klient**: Ange katalog-ID för din Azure AD-klient, som också beskrevs i föregående avsnitt.
    - **BaseUrl**: URL:en för API för hantering för Digital Twins-instansen har formatet `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Ersätt platshållarna i webbadressen med värden för din instans från föregående avsnitt.

    Spara den uppdaterade filen.

## <a name="provision-graph"></a>Etablera graf

I det här steget etableras den rumsliga Digital Twins-grafen med:

- Flera utrymmen.
- En enhet.
- Två sensorer.
- En anpassad funktion.
- En rolltilldelning.

En rumslig graf etableras med hjälp av filen [provisionSample.yaml](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/occupancy-quickstart/src/actions/provisionSample.yaml).

1. Kör `dotnet run ProvisionSample`.

    >[!NOTE]
    >Azure CLI-verktyget Inloggning på enhet används för att autentisera användaren för Azure AD. Användaren måste ange en viss kod för att autentisera med hjälp av [Microsofts inloggningssida](https://microsoft.com/devicelogin). När koden har angetts följer du stegen för att autentisera. Användaren måste autentiseras när verktyget körs.

    >[!TIP]
    > När du kör det här steget kontrollerar du att variablerna har kopierats om följande felmeddelande visas: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

1. Etableringssteget kan ta några minuter. En IoT-hubb etableras också i Digital Twins-instansen. Den loopas tills IoT-hubben visar Status=`Running`.

    [![Etablera exemplet - Status=Körs](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-provision-sample.png#lightbox)

1. I slutet av körningen kopierar du enhetens `ConnectionString` för användning i exemplet med enhetssimulatorn. Kopiera den sträng som beskrivs i den här bilden.

    [![Kopiera anslutningssträngen](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-connection-string.png#lightbox)

    >[!TIP]
    > Du kan visa och ändra ett spatialt diagram med hjälp av [Visningsprogrammet för Azure Digital Twins Graph](https://github.com/Azure/azure-digital-twins-graph-viewer).

Håll konsolfönstret öppet för användning igen senare.

## <a name="send-sensor-data"></a>Skicka sensordata

Skapa och kör sensorsimulatorenhetens program genom att följa dessa steg.

1. Öppna en ny kommandotolk. Gå till projektet som du `digital-twins-samples-csharp-master` hämtade i mappen.
1. Kör `cd device-connectivity`.
1. Kör `dotnet restore`.
1. Redigera [appsettings.json](https://github.com/Azure-Samples/digital-twins-samples-csharp/blob/master/device-connectivity/appsettings.json) för att uppdatera **DeviceConnectionString** med föregående `ConnectionString`. Spara den uppdaterade filen.
1. Kör `dotnet run` för att börja skicka sensordata. Det skickas till Azure Digital Twins som visas i följande bild.

     [![Enhetsanslutning](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-device-connectivity.png#lightbox)

1. Låt den här simulatorn köras så att du kan visa resultat sida vid sida med nästa steg. I det här fönstret visas de simulerade sensordata som skickats till Digital Twins. I nästa steg ställs frågor i realtid för att hitta tillgängliga rum med frisk luft.

    >[!TIP]
    > När du kör det här steget kontrollerar du att `DeviceConnectionString` har kopierats om följande felmeddelande visas: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Hitta tillgängliga utrymmen med frisk luft

Sensorexemplet simulerar slumpmässiga datavärden för två sensorer. Det är rörelse och koldioxid. Tillgängliga utrymmen med frisk luft definieras i exemplet av ingen närvaro i rummet. De definieras även av en koldioxidnivå på under 1 000. Om villkoret inte uppfylls är utrymmet inte tillgängligt eller så är luftkvaliteten dålig.

1. Öppna den kommandotolk som du använde för att köra etableringssteget tidigare.
1. Kör `dotnet run GetAvailableAndFreshSpaces`.
1. Titta på den här kommandotolken och sensordatas kommandotolk sida vid sida.

    Sensordatakommandot skickar simulerade rörelse- och koldioxiddata till Digital Twins var femte sekund. Den andra kommandotolken läser diagrammet i realtid för att ta reda på tillgängliga rum med frisk luft baserat på slumpmässiga simulerade data. Den visar ett av dessa villkor i nära realtid baserat på sensordata som skickades senast:
   - `Room is available and air is fresh`
   - `Room is not available or air quality is poor`

     [![Hämta tillgängliga utrymmen med frisk luft](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png)](media/quickstart-view-occupancy-dotnet/azure-digital-twins-quickstart-get-available.png#lightbox)

Om du vill förstå vad som hände i den här snabbstarten och vad `digital-twins-samples-csharp`API:er kallades öppnar du [Visual Studio-kod](https://code.visualstudio.com/Download) med kodarbetsyteprojektet som finns i . Ange följande kommando:

```cmd
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Självstudierna går på djupet i koden. Du lär dig hur du ändrar konfigurationsdata och vilka API:er som anropas. Mer information om API:er för hantering finns på Digital Twins Swagger-sidan:

```URL
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Namn | Ersätt med |
| --- | --- |
| YOUR_INSTANCE_NAME | Namnet på Digital Twins-instansen |
| YOUR_LOCATION | Den serverregion där din instans är värd |

Eller för att göra det enkelt kan du bläddra till [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna visar i detalj hur du:

- Skapar ett program för förvaltningsansvariga för att öka beläggningsproduktiviteten.
- Driver byggnaden effektivare.

Om du vill fortsätta med självstudierna ska du inte rensa resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort alla resurser som har skapats i den här snabbstarten.

1. Ta bort mappen som skapades när du laddade ned exempellagringsplatsen.
1. Välj **Alla resurser** på menyn på vänster sida av [Azure-portalen](https://portal.azure.com). Välj sedan din Digital Twins-resurs. Välj **Ta bort** överst i fönstret **Alla resurser**.

    > [!TIP]
    > Om du tidigare har haft problem med att ta bort din Digital Twins-instans har en tjänstuppdatering distribuerats med korrigeringen. Försök att ta bort instansen igen.

## <a name="next-steps"></a>Nästa steg

Denna snabbstart använde ett enkelt scenario och exempelprogram för att visa hur Digital Twins kan användas för att hitta rum med bra arbetsförhållanden. För djupgående analys av det här scenariot, läs den här självstudien:

>[!div class="nextstepaction"]
>[Självstudie: Distribuera Azure Digital Twins och konfigurera ett spatialt diagram](tutorial-facilities-setup.md)
