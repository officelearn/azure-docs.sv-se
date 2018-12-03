---
title: Distribuera Azure Digital Twins | Microsoft Docs
description: Lär dig hur du distribuerar din instans av Azure Digital Twins och konfigurerar rumsliga resurser med hjälp av den här självstudien.
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: tutorial
ms.date: 10/15/2018
ms.author: dkshir
ms.openlocfilehash: 4491ec4661c93570893e5fafd5524715e0773d8c
ms.sourcegitcommit: 56d20d444e814800407a955d318a58917e87fe94
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/29/2018
ms.locfileid: "52582340"
---
# <a name="tutorial-deploy-azure-digital-twins-and-configure-a-spatial-graph"></a>Självstudie: Distribuera Azure Digital Twins och konfigurera en rumsligt graf

Du kan använda Azure Digital Twins-tjänsten till att samla personer, platser och enheter i ett sammanhängande rumsligt system. Den här serien med självstudier visar hur du använder Azure Digital Twins för att identifiera rumsbeläggning med optimala förhållanden för temperatur och luftkvalitet. 

Dessa självstudier går igenom ett .NET-konsolprogram för att skapa ett scenario för en kontorsbyggnad. Den här byggnaden har flera våningar och rum på varje våning. Rummen innehåller enheter med kopplade sensorer som identifierar rörelse, omgivningstemperatur och luftkvalitet. 

Du får lära dig hur du replikerar fysiska områden och enheter i byggnaden som digitala objekt med hjälp av Azure Digital Twins-tjänsten. Du simulerar enhetshändelserna med hjälp av ett annat konsolprogram. Därefter får du lära dig hur du övervakar händelserna som kommer från dessa fysiska områden och enheter i nära realtid. 

En kontorsadministratör kan använda den här informationen för att hjälpa en medarbetare som jobbar i den här byggnaden att boka mötesrum med optimala förhållanden. En förvaltningsansvarig kan använda din konfiguration till att få användningstrender för rummen och till att övervaka arbetsförhållanden i underhållssyfte.

I seriens första kurs lär du dig följande:

> [!div class="checklist"]
> * Distribuera Digital Twins.
> * Tilldela behörigheter till appen.
> * Ändra en Digital Twins-exempelapp.
> * Etablera byggnaden.


I de här kurserna används och ändras samma exempel som i [snabbstarten för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md), för en mer detaljerad och djupgående täckning av de här begreppen.


## <a name="prerequisites"></a>Nödvändiga komponenter

- En Azure-prenumeration. Om du inte har ett Azure-konto skapar du ett [kostnadsfritt konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

- .NET Core SDK. Azure Digital Twins-exemplen som används i dessa självstudier är skrivna i C#. Se till att installera [.NET Core SDK version 2.1.403 eller senare](https://www.microsoft.com/net/download) på utvecklingsdatorn för att skapa och köra exemplet. Kontrollera att rätt version är installerad på datorn genom att köra `dotnet --version` i ett kommandofönster.

- [Visual Studio Code](https://code.visualstudio.com/) för att utforska exempelkoden. 

<a id="deploy" />

## <a name="deploy-digital-twins"></a>Distribuera Digital Twins

Använd stegen i det här avsnittet för att skapa en ny instans av Azure Digital Twins-tjänsten. Bara en instans kan skapas per prenumeration. Hoppa till nästa avsnitt om du redan har en som körs. 

[!INCLUDE [create-digital-twins-portal](../../includes/digital-twins-create-portal.md)]


<a id="permissions" />

## <a name="grant-permissions-to-your-app"></a>Bevilja appen behörigheter

Digital Twins använder [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) (Azure AD) för att kontrollera [läs-/skrivåtkomst](../active-directory/develop/v1-permissions-and-consent.md) till tjänsten. Alla program som måste ansluta till din Digital Twins-instans måste registreras med Azure AD. Stegen i det här avsnittet visar hur du registrerar din exempelapp.

Om du redan har en appregistrering kan du återanvända den för ditt exempel. Men bläddra igen det här avsnittet för att se till att din appregistrering har konfigurerats på rätt sätt.

[!INCLUDE [digital-twins-permissions](../../includes/digital-twins-permissions.md)]


## <a name="configure-the-digital-twins-sample"></a>Konfigurera Digital Twins-exemplet

Det här avsnittet vägleder dig genom en Azure Digital Twins-app som kommunicerar med [Digital Twins REST-API:erna](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index). 

### <a name="download-the-sample"></a>Hämta exemplet
Om du redan har exemplen för [snabbstarten för att hitta tillgängliga rum](quickstart-view-occupancy-dotnet.md) kan du hoppa över dessa steg.

1. Ladda ned [Digital Twins .Net-exemplen](https://github.com/Azure-Samples/digital-twins-samples-csharp/archive/master.zip). 
2. Extrahera innehållet i zip-mappen på din dator. 

### <a name="explore-the-sample"></a>Utforska exemplet
I den extraherade exempelmappen öppnar du filen **digital-twins-samples-csharp\digital-twins-samples.code-workspace** i Visual Studio Code. Den innehåller två projekt: 

* Du kan använda etableringsexemplet **occupancy-quickstart** för att konfigurera och etablera en [graf med rumslig information](concepts-objectmodel-spatialgraph.md#graph). Den här grafen är en digital bild av fysiska utrymmen och resurserna i dem. Den använder en [objektmodell](concepts-objectmodel-spatialgraph.md#model) som definierar objekt för en smart byggnad. En komplett lista över Digital Twins-objekt och REST-API:er finns i [den här REST API-dokumentationen](https://docs.westcentralus.azuresmartspaces.net/management/swagger) eller den URL för API för hantering som har skapats för [din instans](#deploy).

   Om du vill utforska exemplet och se hur det kommunicerar med din Digital Twins-instans kan du börja med mappen **src\actions**. Filerna i den här mallen implementerar kommandon som du vill använda i dessa självstudier:
    - Filen **provisionSample.cs** visar hur du etablerar din rumsliga graf.
    - Filen **getSpaces.cs** hämtar information om etablerade utrymmen.
    - Filen **getAvailableAndFreshSpaces.cs** hämtar resultatet av en anpassad funktion som kallas för användardefinierad funktion.
    - Filen **createEndpoints.cs** skapar slutpunkter för att interagera med andra tjänster.

* Simuleringsexemplet **device-connectivity** simulerar sensordata och skickar det till den IoT-hubb som har etablerats för din Digital Twin-instans. Du använder det här exemplet i [nästa självstudie när du har etablerat den rumsliga grafen](tutorial-facilities-udf.md#simulate). Sensorn och enhetsidentifierarna som du använder för att konfigurera det här exemplet ska vara samma som det du använder för att etablera grafen.

### <a name="configure-the-provisioning-sample"></a>Konfigurera etableringsexemplet
1. Öppna ett kommandofönster och gå till det nedladdade exemplet. Kör följande kommando:

    ```cmd/sh
    cd occupancy-quickstart/src
    ```

1. Återställ beroenden till exempelprojektet genom att köra följande kommando:

    ```cmd/sh
    dotnet restore
    ```

1. I Visual Studio Code öppnar du filen **appSettings.json** i projektet **occupancy-quickstart**. Uppdatera följande värden:
   * **ClientId**: Ange program-ID:t för din Azure AD-appregistrering. Du antecknade ID:t i avsnittet där du [anger appbehörigheter](#permissions).
   * **Klientorganisation**: Ange katalog-ID:t för din [Azure AD-klient](https://docs.microsoft.com/azure/active-directory/develop/quickstart-create-new-tenant). Du antecknade även ID:t i avsnittet där du [anger appbehörigheter](#permissions).
   * **BaseUrl**: Ange URL:en för din Digital Twins-instans. Du kan hämta den här URL:en genom att ersätta platshållarna i den här URL:en med värdena för din instans: _https://yourDigitalTwinsName.yourLocation.azuresmartspaces.net/management/api/v1.0/_. Du kan också hämta URL:en genom att ändra URL:en för API för hantering i [distributionsavsnittet](#deploy). Ersätt **swagger/** med **api/v1.0/**.

1. Se en lista över Digital Twins-funktioner som du kan utforska med hjälp av exemplet. Kör följande kommando:

    ```cmd/sh
    dotnet run
    ```

<a id="provision-spaces" />

## <a name="understand-the-provisioning-process"></a>Förstå etableringsprocessen
I det här avsnittet visas hur exemplet etablerar en rumslig graf över en byggnad. 

I Visual Studio Code bläddrar du till mappen **occupancy-quickstart\src\actions** och öppnar filen **provisionSample.cs**. Observera följande funktion:

```csharp
public static async Task<IEnumerable<ProvisionResults.Space>> ProvisionSample(HttpClient httpClient, ILogger logger)
{
    IEnumerable<SpaceDescription> spaceCreateDescriptions;
    using (var r = new StreamReader("actions/provisionSample.yaml"))
    {
        spaceCreateDescriptions = await GetProvisionSampleTopology(r);
    }

    var results = await CreateSpaces(httpClient, logger, spaceCreateDescriptions, Guid.Empty);

    Console.WriteLine($"Completed Provisioning: {JsonConvert.SerializeObject(results, Formatting.Indented)}");

    return results;
}

```

Den här funktionen använder **provisionSample.yaml** i samma mapp. Öppna den här filen och notera hierarkin för en kontorsbyggnad: *Venue* (Plats), *Floor* (Våning), *Area* (Område) och *Rooms* (Rum). Vilken som helst av dessa fysiska utrymmen kan innehålla *devices* (enheter) och *sensors* (sensorer). Varje post har en fördefinierad `type`&mdash;, till exempel Floor (Våning), Room (Rum). 

I **yaml**-exempelfilen visas en rumslig graf som använder `Default` Digital Twins-objektmodellen. Den här modellen innehåller allmänna namn för de flesta typer. Allmänna namn är tillräckliga för en byggnad. Exempel är Temperature (Temperatur) för SensorDataType (Sensordatatyp) och Map (Karta) för SpaceBlobType (Utrymmesblobtyp). Ett exempel på utrymmestypen är Room (Rum) med undertyperna FocusRoom (Fokusrum), ConferenceRoom (Konferensrum) och så vidare. 

Om du var tvungen att skapa en rumslig graf för en annan typ av plats, till exempel en fabrik, kanske du behöver en annan objektmodell. Du kan få reda på vilka modeller som är tillgängliga att använda genom att köra kommandot `dotnet run GetOntologies` på kommandoraden för etableringsexemplet. 

Mer information om rumsliga grafer och objektmodellerna finns i [Förstå Digital Twins-objektmodellen och rumslig graf](concepts-objectmodel-spatialgraph.md). 

### <a name="modify-the-sample-spatial-graph"></a>Ändra exemplet på rumslig graf
Filen **provisionSample.yaml** innehåller följande noder:

- **resources** (resurser): Noden `resources` skapar en Azure IoT Hub-resurs för att kommunicera med enheterna i din konfiguration. En IoT-hugg på rotnoden i grafen kan kommunicera med alla enheterna och sensorerna i din graf.  

- **spaces** (utrymmen): I Digital Twins-objektmodellen representerar `spaces` de fysiska platserna. Varje utrymme har en `Type`&mdash;, till exempel Region, Venue (Plats) eller en Customer (Kund)&mdash;och ett eget `Name`. Utrymmen kan tillhöra andra utrymmen som skapar en hierarkisk struktur. Filen provisionSample.yaml har en rumslig graf av en föreställd byggnad. Observera den logiska kapslingen av utrymmen av typen `Floor` i `Venue`, `Area` på en våning och `Room`-noderna i ett område. 

- **devices** (enheter): Utrymmen kan innehålla `devices`, som är fysiska eller virtuella enheter som hanterar ett antal sensorer. Till exempel kan en enhet vara en användares telefon, en Raspberry Pi-sensor eller en gateway. I den föreställda byggnaden i exemplet kan du observera hur rummet med namnet **Focus Room** innehåller en **Raspberry Pi 3 A1**-enhet. Varje enhetsnod identifieras med ett unikt `hardwareId`, som är hårdkodat i exemplet. Om du vill konfigurera det här exemplet för faktisk produktion ersätter du dessa med värden från konfigurationen.  

- **sensors** (sensorer): en enhet kan innehålla flera `sensors`. De kan identifiera och registrera fysiska förändringar som temperatur, rörelse och batterinivå. Varje sensornod har unikt identifierats av ett `hardwareId`, hårdkodat här. För en faktisk app ersätter du dessa med hjälp av de unika identifierarna för sensorerna i konfigurationen. Filen provisionSample.yaml har två sensorer för att registrera *Motion* (Rörelse) och *CarbonDioxide* (Koldioxid). Lägg till en annan sensor för att registrera *Temperature* (Temperatur) genom att lägga till följande rader, nedanför raderna för CarbonDioxide-sensorn (Koldioxidsensorn). Observera att dessa tillhandahålls i provisionSample.yaml som kommenterade rader. Du kan ta bort kommentaren från dem genom att ta bort tecknet `#` före varje rad. 

    ```yaml
            - dataType: Temperature
              hardwareId: SAMPLE_SENSOR_TEMPERATURE
    ```
    > [!NOTE]
    > Kontrollera att justeringen av nycklarna `dataType` och `hardwareId` är i linje med uttrycken ovanför det här kodavsnittet. Se även till att redigeraren inte ersätter blanksteg med tabbar. 

Spara och stäng filen provisionSample.yaml. I nästa självstudie lägger du till mer information i den här filen och etablerar sedan Azure Digital Twins-exempelbyggnaden.


## <a name="clean-up-resources"></a>Rensa resurser

Om du inte vill utforska Azure Digital Twins nu kan du ta bort resurser som du har skapat i den här självstudien:

1. På den vänstra menyn i [Azure-portalen](http://portal.azure.com) väljer du **Alla resurser**, väljer din Digital Twins-resursgrupp och **Ta bort**.
   
    > [!TIP]
    > Om det inträffade problem när du skulle ta bort Digital Twins-instansen finns det nu en tjänstuppdatering som åtgärdar det. Försök att ta bort instansen igen.

1. Ta bort exempelprogrammet på datorn om det behövs. 


## <a name="next-steps"></a>Nästa steg

Fortsätt till nästa självstudie i serien för att lära dig hur du implementerar en anpassad logik för att övervaka villkor i exempelbyggnaden: 
> [!div class="nextstepaction"]
> [Självstudie: Etablera byggnaden och övervaka arbetsförhållanden](tutorial-facilities-udf.md)

