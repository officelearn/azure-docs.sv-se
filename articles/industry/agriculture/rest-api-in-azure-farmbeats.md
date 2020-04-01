---
title: Referenser
description: ''
author: sunasing
ms.topic: article
ms.date: 11/04/2019
ms.author: sunasing
ms.openlocfilehash: 717a09d8377a7b95fe24300cc65222f307e7419f
ms.sourcegitcommit: ced98c83ed25ad2062cc95bab3a666b99b92db58
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/31/2020
ms.locfileid: "80437528"
---
# <a name="references"></a>Referenser

I den här artikeln beskrivs Azure FarmBeats API:er.

## <a name="rest-api"></a>REST-API

Azure FarmBeats API:er ger jordbruksföretag ett standardiserat RESTful-gränssnitt med JSON-baserade svar som hjälper dig att dra nytta av Azure FarmBeats-funktioner, till exempel:

- API:er för att hämta sensor-, kamera-, drönar-, väder-, satellit- och kurerade markdata.
- Normalisering och kontextualisering av data över vanliga dataleverantörer.
- Schematerade åtkomst- och frågefunktioner för alla intvalda data.
- Automatisk generering av metadata som kan efterfrågas, baserat på agronomiska funktioner.
- Genereras automatiskt tidssekvensaggregat för snabb modellbyggnad.
- Integrerad Azure Data Factory-motor för att enkelt skapa anpassade databehandlingspipelor.

## <a name="application-development"></a>Programutveckling

FarmBeats API:er innehåller teknisk dokumentation för Swagger. Information om alla API:er och motsvarande begäranden eller svar finns i [Swagger](https://aka.ms/FarmBeatsDatahubSwagger).

I följande tabell sammanfattas alla objekt och resurser i FarmBeats Datahub:

| Objekt och resurser | Beskrivning
--- | ---|
Gård | Farm motsvarar en fysisk plats av intresse inom FarmBeats systemet. Varje gård har ett gårdsnamn och ett unikt server-ID. |
Enhet  | Enheten motsvarar en fysisk enhet som finns på servergruppen. Varje enhet har ett unikt enhets-ID. En enhet etableras vanligtvis i en servergrupp med ett servergrupps-ID.
DeviceModel  | DeviceModel motsvarar enhetens metadata, till exempel tillverkaren och typen av enhet, som antingen är gateway eller nod.
Sensor  | Sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligtvis ansluten till en enhet med ett enhets-ID.
SensorModel (SensorModel)  | SensorModel motsvarar sensorns metadata, till exempel tillverkaren, typen av sensor, som är antingen analog eller digital, och sensormätningen, såsom omgivningstemperatur och tryck.
Telemetri  | Telemetri ger möjlighet att läsa telemetrimeddelanden för en viss sensor och tidsintervall.
Jobb  | Jobbet motsvarar alla arbetsflöden för aktiviteter som körs i FarmBeats-systemet för att få en önskad utdata. Varje jobb är associerat med ett jobb-ID och jobbtyp.
Jobbtyp  | JobType motsvarar olika jobbtyper som stöds av systemet. Systemdefinierade och användardefinierade jobbtyper ingår.
ExtendedType (Uttr.  | ExtendedType motsvarar listan över system- och användardefinierade typer i systemet. ExtendedType hjälper till att konfigurera en ny sensor, scen eller scenfiltyp i FarmBeats-systemet.
Partner  | Partnern motsvarar sensor- och bildintegrationspartnern för FarmBeats.
Scen  | Scenen motsvarar alla genererade utdata i samband med en servergrupp. Varje scen har ett scen-ID, scenkälla, scentyp och servergrupps-ID som är associerat med det. Varje scen-ID kan ha flera scenfiler associerade med sig.
ScenFil |SceneFile motsvarar alla filer som genereras för en enskild scen. Ett enda scen-ID kan ha flera SceneFile-ID:er associerade med sig.
Regel  |Regeln motsvarar ett villkor för servergruppsrelaterade data för att utlösa en avisering. Varje regel är i samband med en servergrupps data.
Varning  | Avisering motsvarar ett meddelande som genereras när ett regelvillkor uppfylls. Varje avisering är i samband med en regel.
RoleDefinition  | RoleDefinition definierar tillåtna och otillåtna åtgärder för en roll.
RollTilldelning  |RollTilldelning motsvarar tilldelningen av en roll till en användare eller ett tjänsthuvudnamn.

### <a name="data-format"></a>Dataformat

JSON är ett vanligt språkoberoende dataformat som ger en enkel textrepresentation av godtyckliga datastrukturer. Mer information finns på [JSON:s webbplats](https://www.json.org/).

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

HTTP-begäranden till REST API skyddas med Azure Active Directory (Azure AD).
För att göra en autentiserat begäran till REST-API:erna kräver klientkoden autentisering med giltiga autentiseringsuppgifter innan du kan anropa API:et. Autentisering samordnas mellan de olika aktörerna av Azure AD. Det ger din klient en åtkomsttoken som bevis på autentiseringen. Token skickas sedan i HTTP-auktoriseringshuvudet för REST API-begäranden. Mer information om Azure AD-autentisering finns i [Azure Active Directory](https://portal.azure.com) för utvecklare.

Åtkomsttoken måste skickas i efterföljande API-begäranden, i rubrikavsnittet, som:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

### <a name="http-request-headers"></a>HTTP-begäranden

Här är de vanligaste begäranderubrikerna som du måste ange när du ringer ett API-anrop till Azure FarmBeats Datahub.


**Huvudet** | **Beskrivning och exempel**
--- | ---
Content-Type  | Formatet för begäran (Innehållstyp: program/<format>). För Azure FarmBeats Datahub API:er är formatet JSON. Innehållstyp: program/json
Auktorisering  | Anger den åtkomsttoken som krävs för att ringa ett API-anrop. Auktorisering: Bärare <Access-Token>
Acceptera | Svarsformatet. För Azure FarmBeats Datahub API:er är formatet JSON. Acceptera: ansökan/json

### <a name="api-requests"></a>API-begäranden

Om du vill göra en REST API-begäran kombinerar du metoden HTTP(GET, POST, PUT eller DELETE), URL:en till API-tjänsten, URI till en resurs som ska frågas, skicka data till, uppdatera eller ta bort och sedan lägga till ett eller flera HTTP-begäranden.

URL:en till API-tjänsten är din Datahub-URL,\<till exempel https:// dittdatahub-webbplatsnamn>.azurewebsites.net.

Du kan också inkludera frågeparametrar för GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Följande exempelbegäran används för att hämta listan över enheter:

```bash
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

De flesta GET-, POST- och PUT-samtal kräver en JSON-begäran.

Följande exempelbegäran skapar en enhet. Denna begäran har matat in JSON med begäran kroppen.

```bash
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

### <a name="query-parameters"></a>Frågeparametrar

För REST GET-anrop kan du filtrera, begränsa storleken på och sortera data i ett API-svar genom att inkludera en eller flera frågeparametrar på uri-begäran. För frågeparametrarna finns i API-dokumentationen och de enskilda GET-anropen.
När du till exempel frågar listan över enheter (GET-anrop på /Device) kan följande frågeparametrar anges:

![Lista över enheter](./media/references-for-azure-farmbeats/query-parameters-device-1.png)

### <a name="error-handling"></a>Felhantering

Azure FarmBeats Datahub API:er returnerar standard-HTTP-fel. De vanligaste felkoderna är följande:

 |Felkod             | Beskrivning |
 |---                    | --- |
 |200                    | Lyckades |
 |201                    | Skapa (post) framgång |
 |400                    | Felaktig begäran. Det finns ett fel i begäran. |
 |401                    | Obehörig. Anroparen av API:et har inte behörighet att komma åt resursen. |
 |404                    | Det gick inte att hitta resursen |
 |5xx                    | Internt serverfel. Felkoderna som börjar med 5XX innebär att det finns ett visst fel på servern. Mer information finns i serverloggar och följande avsnitt. |


Utöver standard-HTTP-fel returnerar Azure FarmBeats Datahub API:er även interna fel i följande format:

```json
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
```

I det här exemplet, när en servergrupp skapades, angavs inte det obligatoriska fältet "Namn" i indatanyttolasten. Det resulterande felmeddelandet skulle vara:

 ```json    
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
  ```

## <a name="add-users-or-app-registrations-to-azure-active-directory"></a>Lägga till användare eller appregistreringar i Azure Active Directory

Azure FarmBeats API:er kan nås av en användare eller en appregistrering i Azure Active Directory. Så här skapar du en appregistrering i Azure Active Directory:

1. Gå till [Azure-portalen](https://portal.azure.com)och välj **Azure Active Directory** > **App registreringar** > **Ny registrering**. Du kan också använda ett befintligt konto.
2. Gör följande för ett nytt konto:

    - Ange ett namn.
    - Välj **Konton endast i den här organisationskatalogen (En enda klient)**.
    - Använd standardvärdena i resten av fälten.
    - Välj **Registrera**.

3. Gör följande i fönstret **för** ny och befintlig appregistrering:

    - Samla **in klient-ID** och **klient-ID**.
    - Gå till **Certifikat och hemligheter** för att skapa en ny klienthemlighet och fånga **klienthemligheten**.
    - Gå tillbaka till **Översikt**och välj länken bredvid **Hantera program i lokal katalog**.
    - Gå till **Egenskaper** för att fånga **objekt-ID**.

4. Gå till din Datahub Swagger<yourdatahub>(https:// .azurewebsites.net/swagger/index.html) och gör följande:
    - Gå till **Api:et för rolltilldelning**.
    - Utför ett POST för att skapa ett **RollAssignment-objekt** för **det objekt-ID** som du just skapade.

  > [!NOTE]
  > Mer information om hur du lägger till användare och Active Directory-registrering finns i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal).

När du har avslutat föregående steg kan din appregistrering (klient) anropa Azure FarmBeats API:er med hjälp av en åtkomsttoken via bärareautentisering.

Använd åtkomsttoken för att skicka den i efterföljande API-begäranden i rubrikavsnittet som:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
