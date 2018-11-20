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
ms.openlocfilehash: 590a7aa875f5f8c40576d69d7e73bdfc31fffbf8
ms.sourcegitcommit: 542964c196a08b83dd18efe2e0cbfb21a34558aa
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/14/2018
ms.locfileid: "51636262"
---
# <a name="quickstart-find-available-rooms-by-using-azure-digital-twins"></a>Snabbstart: Hitta tillgängliga rum med hjälp av Azure Digital Twins

Med tjänsten Azure Digital Twins kan du återskapa en digital bild av den fysiska miljön. Du kan sedan få ett meddelande via händelser i din miljö och anpassa dina svar efter dem. 

I den här snabbstarten används [ett par .NET-exempel](https://github.com/Azure-Samples/digital-twins-samples-csharp) för att digitalisera en tänkt kontorsbyggnad. Här visas hur du hittar tillgängliga rum i byggnaden. Du kan associera många sensorer med Digital Twins med din miljö. Du kan även få reda på om luftkvaliteten i det tillgängliga rummet är optimalt med hjälp av en simulerad koldioxidsensor. Ett av exempelprogrammen genererar slumpmässiga sensordata som hjälper dig att visualisera det här scenariot.

Följande videoklipp innehåller en sammanfattning av snabbstartsinstallationen:

> [!VIDEO https://www.youtube.com/embed/1izK266tbMI]

## <a name="prerequisites"></a>Nödvändiga komponenter

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

1. Öppna en kommandotolk. Gå till mappen där digital-twins-samples-csharp-master.zip-filerna extraherades.
1. Kör `cd occupancy-quickstart/src`.
1. Kör `dotnet restore`.
1. Redigera **appSettings.json** för att uppdatera följande variabler:
    - **ClientId**: Ange program-ID för din Azure AD-appregistrering som beskrevs i föregående avsnitt.
    - **Klient**: Ange katalog-ID för din Azure AD-klient, som också beskrevs i föregående avsnitt.
    - **BaseUrl**: URL:en för API för hantering för Digital Twins-instansen har formatet `https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/`. Ersätt platshållarna i webbadressen med värden för din instans från föregående avsnitt.

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
    >Azure CLI-verktyget Inloggning på enhet används för att autentisera användaren för Azure AD. Användaren måste ange en viss kod för att autentisera med hjälp av [Microsofts inloggningssida](https://microsoft.com/devicelogin). När koden anges följer du stegen för att autentisera. Användaren måste autentiseras när verktyget körs.
    
    >[!TIP]
    > När du kör det här steget kontrollerar du att variablerna har kopierats om följande felmeddelande visas: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`


1. Etableringssteget kan ta några minuter. En IoT-hubb etableras också i Digital Twins-instansen. Den loopas tills IoT-hubben visar Status=`Running`.

    ![Etablera exempel][4]

1. I slutet av körningen kopierar du enhetens `ConnectionString` för användning i exemplet med enhetssimulatorn. Kopiera den sträng som beskrivs i den här bilden.

    ![Etablera exempel][1]

## <a name="send-sensor-data"></a>Skicka sensordata

Skapa och kör sensorsimulatorprogrammet med hjälp av följande steg.

1. Öppna en ny kommandotolk. Gå till det projekt du har laddat ned i mappen digital-twins-samples-csharp-master.
1. Kör `cd device-connectivity`.
1. Kör `dotnet restore`.
1. Redigera **appsettings.json** för att uppdatera **DeviceConnectionString** med föregående `ConnectionString`.
1. Kör `dotnet run` för att börja skicka sensordata. Du ser dem skickas till Digital Twins enligt följande bild.

     ![Enhetsanslutning][2]

1. Låt den här simulatorn köras så att du kan visa resultat sida vid sida med nästa steg. I det här fönstret visas de simulerade sensordata som skickats till Digital Twins. I nästa steg ställs frågor i realtid för att hitta tillgängliga rum med frisk luft.

    >[!TIP]
    > När du kör det här steget kontrollerar du att `DeviceConnectionString` har kopierats om följande felmeddelande visas: `EXIT: Unexpected error: The input is not a valid Base-64 string ...`

## <a name="find-available-spaces-with-fresh-air"></a>Hitta tillgängliga utrymmen med frisk luft

Sensorexemplet simulerar slumpmässiga datavärden för två sensorer. Det är rörelse och koldioxid. Tillgängliga utrymmen med frisk luft definieras i exemplet av ingen närvaro i rummet. De definieras även av en koldioxidnivå på under 1 000. Om villkoret inte uppfylls är utrymmet inte tillgängligt eller så är luftkvaliteten dålig.

1. Öppna kommandotolken du använde för att köra det föregående etableringssteget.
1. Kör `dotnet run GetAvailableAndFreshSpaces`.
1. Titta på den här kommandotolken och sensordatas kommandotolk sida vid sida. 

    En kommandotolk skickar simulerade data för rörelse och koldioxid till Digital Twins var femte sekund. Det andra kommandot läser grafen i realtid för att ta reda på tillgängliga rum med frisk luft baserat på slumpmässiga simulerade data. Den visar ett av dessa villkor i nära realtid baserat på sensordata som skickades senast:
    - Tillgängliga rum med frisk luft.
    - Upptaget eller dålig luftkvalitet i rummet.

     ![Hämta tillgängliga utrymmen med frisk luft][3]

För att förstå vad som hände i den här snabbstarten och vilka API:er som har anropats öppnar du [Visual Studio Code](https://code.visualstudio.com/Download) med kodprojektet för arbetsytan som finns i digital-twins-samples-csharp (se kommandot nedan). Ange följande kommando:

```plaintext
<path>\occupancy-quickstart\src>code ..\..\digital-twins-samples.code-workspace
```

Självstudierna går på djupet i koden. Du lär dig hur du ändrar konfigurationsdata och vilka API:er som anropas. Mer information om API:er för hantering finns på Digital Twins Swagger-sidan:

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Namn | Ersätt med |
| --- | --- |
| YOUR_INSTANCE_NAME | Namnet på Digital Twins-instansen |
| YOUR_LOCATION | Den serverregion som instansen finns i |

Eller för att göra det enkelt kan du bläddra till [Digital Twins Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

## <a name="clean-up-resources"></a>Rensa resurser

Självstudierna visar i detalj hur du: 

- Skapar ett program för förvaltningsansvariga för att öka beläggningsproduktiviteten. 
- Driver byggnaden effektivare.

Om du vill fortsätta med självstudierna ska du inte rensa resurserna som du skapade i den här snabbstarten. Om du inte planerar att fortsätta tar du bort alla resurser som har skapats i den här snabbstarten.

1. Ta bort mappen som skapades när du laddade ned exempellagringsplatsen.
1. Välj **Alla resurser** på menyn på vänster sida av [Azure-portalen](http://portal.azure.com). Välj sedan din Digital Twins-resurs. Välj **Ta bort** överst i fönstret **Alla resurser**.

## <a name="next-steps"></a>Nästa steg

Den här snabbstarten har använt ett enkelt scenario som visar hur du hittar rum med bra arbetsförhållanden. Djupanalys av det här scenariot finns i den här självstudien:

> [!div class="nextstepaction"]
> [Självstudie: Distribuera Azure Digital Twins och konfigurera ett spatialt diagram](tutorial-facilities-setup.md)

<!-- Images -->
[1]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample.png
[2]: media/quickstart-view-occupancy-dotnet/digital-twins-device-connectivity.png
[3]: media/quickstart-view-occupancy-dotnet/digital-twins-get-available.png
[4]: media/quickstart-view-occupancy-dotnet/digital-twins-provision-sample1.png
