---
title: Referenser för FarmBeats
description: ''
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 057037807a75e50eb2305bfab19d1fcff7fe77ce
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/09/2019
ms.locfileid: "73889594"
---
# <a name="references"></a>Referenser

Nedan visas en samling av anteckningar och instruktioner som beskriver Azure FarmBeats-API: erna.

## <a name="rest-api"></a>REST-API

Azure FarmBeats-API: er ger jord handel med ett standardiserat RESTful-gränssnitt med JSON-baserade svar och det hjälper dig att utnyttja Azure FarmBeats-funktioner:

- API: er för att få sensor-, kamera-, drönare-, väder-, satellit-och undervisad mark information.
- Normalisering/contextualization av data över gemensamma data leverantörer.
- Schematiserade åtkomst och fråge funktioner på alla inmatade data.
- Automatisk generering av metadata som kan frågas, baserat på agronom-funktioner.  
- Automatiskt genererade tidssekvensiska agg regeringar för skapande av snabb modell.
- Integrerad Azure Data Factory-motor (ADF) för att enkelt bygga anpassade data behandlings pipeliner.

## <a name="application-development"></a>Programutveckling

API: erna innehåller Swagger teknisk dokumentation. Se [Swagger](https://aka.ms/FarmBeatsDatahubSwagger) för information om alla API: er och deras motsvarande förfrågningar/svar.

Detta är en sammanfattning av alla objekt/resurser i FarmBeats data Hub:

Anläggningen | Server gruppen motsvarar en fysisk placering av intresse i FarmBeats-systemet. Varje server grupp har ett Server grupps namn och ett unikt server grupps-ID.
--- | ---|
Enhet  | Enheten motsvarar en fysisk enhet som finns i Server gruppen. Varje enhet har ett unikt enhets-ID. Enheten tillhandahålls vanligt vis till en Server grupp med ett Server grupp-ID.
deviceModel  | DeviceModel motsvarar meta-data för enheten, till exempel tillverkaren, typen av enhet, antingen gateway eller nod.
Mäta  | Sensorn motsvarar en fysisk sensor som registrerar värden. En sensor är vanligt vis ansluten till en enhet med ett enhets-ID.
SensorModel  | SensorModel motsvarar meta-data för sensorn, till exempel tillverkare, typ av sensor, antingen analogt eller digitalt, sensor mått som omgivnings temperatur, tryck osv.
Telemetri  | Telemetri ger möjlighet att läsa telemetri meddelanden för ett visst sensor-och tidsintervall.
Jobb  | Jobbet motsvarar eventuella arbets flödes aktiviteter, som körs i FarmBeats-systemet för att få önskade utdata. Varje jobb är associerat med ett jobb-ID och jobb typ.
JobType  | JobType motsvarar olika jobb typer som stöds av systemet. Detta inkluderar systemdefinierade & användardefinierade jobb typer.
ExtendedType  | ExtendedType motsvarar listan över system & användardefinierade typer i systemet. På så sätt kan du konfigurera en ny sensor eller scen eller Scenefile-typ i FarmBeats-systemet.
Partner  | Partner motsvarar sensor-bilder integrerings partner för FarmBeats
Tegel  | Scenen motsvarar alla genererade utdata i kontexten för en Server grupp. Varje scen har ett scen-ID, en scen källa, en scen typ och ett Server grupp-ID som är associerat med den. Varje scen-ID kan ha flera associerade scen-filer.
SceneFile |SceneFile motsvarar alla filer som genereras för en enda scen. Ett enda scen-ID kan ha flera associerade SceneFile-ID: n.
Regel  |Regel motsvarar ett villkor för grupprelaterade data som utlöser en avisering. Varje regel kommer att finnas i kontexten för en server grupps data.
Varning  | Aviseringen motsvarar ett meddelande som skapas när ett regel villkor uppfylls. Varje avisering kommer att ingå i kontexten för en regel.
Roll definitions  | Roll definitions definierar tillåtna och otillåtna åtgärder för en roll.
RoleAssignment  |RoleAssignment motsvarar tilldelningen av en roll till en användare eller ett huvud namn för tjänsten.

**Dataformat**

JSON (JavaScript Object Notation) är ett gemensamt, språk oberoende data format som ger en enkel text representation av godtyckliga data strukturer. Mer information finns i json.org.

## <a name="authentication-and-authorization"></a>Autentisering och auktorisering

HTTP-begäranden till REST API skyddas med Azure Active Directory (Azure AD).
Om du vill göra en autentiserad begäran till REST-API: erna kräver klient koden autentisering med giltiga autentiseringsuppgifter innan du kan anropa API: et. Autentiseringen koordineras mellan de olika aktörerna av Azure AD och ger klienten en åtkomsttoken som bevis på autentiseringen. Token skickas sedan i HTTP-Authorization-huvudet för REST API begär Anden. Mer information om Azure AD-autentisering finns i [Azure Active Directory](https://portal.azure.com) för utvecklare.

Åtkomsttoken måste skickas i efterföljande API-begäranden i rubrik avsnittet som:

```
headers = {"Authorization": "Bearer " + **access_token**}
```

**Rubriker för HTTP-begäran**

Här är de vanligaste begärandehuvuden som måste anges när du gör ett API-anrop till Azure FarmBeats data Hub:


**Huvud** | **Beskrivning och exempel**
--- | ---
Content-Type  | Formatet för begäran (Content-Type: program/<format>) för Azure FarmBeats API-formatet för data hubb är JSON. Innehålls typ: Application/JSON
Auktorisering  | Anger den åtkomsttoken som krävs för att göra ett API-anrop. **Auktorisering: innehavare < åtkomst-token >**
godkänt | Svars formatet. För Azure FarmBeats data Hub-API: er är formatet JSON **Accept: Application/JSON**

**API-begäranden**

Om du vill göra en REST API-begäran kombinerar du metoden HTTP (GET, POST, tag eller DELETE), URL: en till API-tjänsten, URI: n till en resurs att fråga, skicka data till, uppdatera eller ta bort och en eller flera HTTP-begärandehuvuden.

URL-adressen till API-tjänsten är din datahubbs-URL https://\<yourdatahub-Site-Name >. azurewebsites. net, du kan även inkludera frågeparametrar på GET-anrop för att filtrera, begränsa storleken på och sortera data i svaren.

Nedanstående exempel förfrågan är att hämta listan över enheter:

```azurepowershell-interactive
curl -X GET "https://microsoft-farmbeats.azurewebsites.net/Device" -H "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>”
```

De flesta GET-, POST-och parkera-anrop kräver en text för JSON-begäran.

Nedanstående exempel förfrågan är att skapa en enhet (detta har en indataport med en indataport med begär ande texten).

```json
curl -X POST "https://microsoft-farmbeats.azurewebsites.net/Device" -H  "accept: application/json" -H  "Content-Type: application/json" -H "Authorization: Bearer <Access-Token>" -d "{  \"deviceModelId\": \"ID123\",  \"hardwareId\": \"MHDN123\",  \"reportingInterval\": 900,  \"name\": \"Device123\",  \"description\": \"Test Device 123\",}"
```

**Frågeparametrar**

För REST **Get** -anrop kan du filtrera, begränsa storleken på och sortera data i ett API-svar genom att inkludera en eller flera frågeparametrar i begärande-URI: n. Information om frågeparametrar finns i API-dokumentationen och i de enskilda GET-anropen.
När du till exempel frågar listan över enheter (GET Call på/Device) kan du ange följande frågeparametrar:  

![Taktslag i projekt grupp](./media/for-references/query-parameters-device-1.png)

**Felhantering**

Azure FarmBeats data Hub API returnerar HTTP-standardfel. De vanligaste fel koderna är följande:

 |Felkod             | Beskrivning |
 |---                    | --- |
 |200                    | Lyckades |
 |201                    | Create (post) lyckades |
 |400                    | Felaktig begäran. Det finns ett fel i begäran |
 |401                    | Tillstånd. Anroparen för API: et har inte behörighet att komma åt resursen |
 |404                    | Resursen hittades inte |
 |5XX                    | Internt Server fel. Fel koderna som börjar med 5XX innebär att det finns ett fel på servern. Se Server loggar och följande avsnitt för mer information. |


Förutom vanliga HTTP-fel returnerar Azure FarmBeats data Hub-API: er även interna fel i formatet nedan:

    ```
    {
      "message": "<More information on the error>",
      "status": "<error code>”,
      "code": "<InternalErrorCode>",
      "moreInfo": "<Details of the error>"
    }
    ```

Exempel: när du skapar en Server grupp angavs inte det obligatoriska fältet "namn" i nytto lasten. Det resulterande fel meddelandet skulle vara:

    ```json
    {
      "message": "Model validation failed",
      "status": 400,
      "code": "ModelValidationFailed",
      "moreInfo": "[\"The Name field is required.\"]"
    }
    ```

## <a name="adding-users-or-app-registrations-to-azure-active-directory"></a>Lägga till användare eller program registreringar i Azure Active Directory

Azure FarmBeats-API: er kan nås av en användare eller en app-registrering i Azure Active Directory. Gör så här om du vill skapa en app-registrering på Azure Active Directory:  

1. Gå till [Azure Portal](https://portal.azure.com) **Azure Active Directory, Appregistreringar**, > **ny registrering**. Du kan också använda ett befintligt konto.
2. För ett nytt konto, se till att slutföra följande:

    - Ange ett namn
    - Välj **konton endast i den här organisations katalogen (enskild klient)**
    - Standardvärdena i resten av fälten
    - Välj **register**

3. Från den nya/befintliga appens registrering, **Översikt**, Slutför följande

    - Avbilda **klient-ID och klient** **-ID**.
    - Gå till **certifikat och hemligheter** för att generera en ny klient hemlighet och avbilda **klient hemligheten**.
    - Gå tillbaka till översikten och klicka på länken bredvid **hantera program i lokal katalog**
    - Gå till **Egenskaper** för att avbilda **objekt-ID**

4. Gå till [Swagger för data hubb](https://<yourdatahub>.azurewebsites.net/swagger/index.html) och utför följande steg:
    - Navigera till **RoleAssignment-API**
    - Utför ett **inlägg** för att skapa en RoleAssignment för det **objekt-ID** som du nyss skapade. – Indataports-JSON är:

  > [!NOTE]
  > Mer information om hur du lägger till användare och AD-registrering finns i [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) .

När du har slutfört ovanstående steg kan registreringen av appen (klienten) anropa Azure FarmBeats-API: erna med hjälp av en åtkomsttoken via Bearer-autentisering.  

Använd åtkomsttoken för att skicka den i efterföljande API-begäranden i rubrik avsnittet som:

```
headers = {"Authorization": "Bearer " + **access_token**, "Content-Type" : "application/json" }
```
