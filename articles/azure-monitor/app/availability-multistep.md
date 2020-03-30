---
title: Övervaka med webbtester i flera steg – Azure Application Insights
description: Konfigurera webbtester i flera steg för att övervaka dina webbprogram med Azure Application Insights
ms.topic: conceptual
ms.date: 10/23/2019
ms.reviewer: sdash
ms.openlocfilehash: 3b8baad127b16a1bd9d071d0c3d4df68da8c3304
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "77655948"
---
# <a name="multi-step-web-tests"></a>Webbtester med flera steg

Du kan övervaka en inspelad sekvens av webbadresser och interaktioner med en webbplats via webbtester i flera steg. Den här artikeln kommer att gå igenom processen att skapa ett flerstegs webbtest med Visual Studio Enterprise.

> [!NOTE]
> Webbtester i flera steg beror på Visual Studio-webbtestfiler. Det [meddelades](https://devblogs.microsoft.com/devops/cloud-based-load-testing-service-eol/) att Visual Studio 2019 blir den sista versionen med webtest funktionalitet. Det är viktigt att förstå att även om inga nya funktioner kommer att läggas till, stöds för närvarande webbtestfunktioner i Visual Studio 2019 och kommer att fortsätta att stödjas under produktens supportlivscykel. Azure Monitor-produktteamet har tagit upp frågor om framtiden för tillgänglighetstester i flera steg [här](https://github.com/MicrosoftDocs/azure-docs/issues/26050#issuecomment-468814101).  

## <a name="pre-requisites"></a>Förutsättningar

* Visual Studio 2017 Enterprise eller senare.
* Visual Studio webbprestanda och ladda testverktyg.

För att hitta de testverktyg som krävs. Starta **Visual Studio Installer** > **Enskilda komponenter** > **Felsökning och testning** > **av webbprestanda och belastningstestverktyg**.

![Skärmbild av visual studio-installationsgränssnittet med enskilda komponenter markerade med en kryssruta bredvid objektet för webbprestanda och läsinläsningsverktyg](./media/availability-multistep/web-performance-load-testing.png)

> [!NOTE]
> Webbtester i flera steg har ytterligare kostnader i samband med dem. Om du vill veta mer finns i den [officiella prisguiden](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="record-a-multi-step-web-test"></a>Spela in ett webbtest i flera steg 

> [!WARNING]
> Vi rekommenderar inte längre att du använder flerstegsinspelaren. Brännaren har utvecklats för statiska HTML-sidor med grundläggande interaktioner, och ger inte en funktionell upplevelse för moderna webbsidor.

Om du vill ha vägledning om hur du skapar Visual Studio-webbtester finns i den [officiella Visual Studio 2019-dokumentationen](https://docs.microsoft.com/visualstudio/test/how-to-create-a-web-service-test?view=vs-2019).

## <a name="upload-the-web-test"></a>Ladda upp webbtestet

1. I programinsiktsportalen i fönstret Tillgänglighet väljer du **Skapa** > **testtyp** > **Webbtest i flera steg**.

2. Ange testplatser, frekvens och varningsparametrar.

### <a name="frequency--location"></a>Frekvens & plats

|Inställning| Förklaring
|----|----|----|
|**Testfrekvens**| Anger hur ofta testet körs från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Testplatser**| Är de platser där våra servrar skickar webbförfrågningar till din webbadress. **Vårt minsta antal rekommenderade testplatser är fem** för att försäkra att du kan skilja problem på din webbplats från nätverksproblem. Du kan välja upp till 16 platser.

### <a name="success-criteria"></a>Kriterier för framgång

|Inställning| Förklaring
|----|----|----|
| **Tidsgränsen för testet** |Minska det här värdet för att få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade statuskoden som räknas som en framgång. 200 är koden som anger att en normal webbsida har returnerats.|
| **Matchning av innehåll** | En sträng, som "Välkommen!" Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehållsmatchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära realtid (förhandsgranskning)** | Vi rekommenderar att du använder varningar om nära realtid. Konfigurera den här typen av avisering görs när ditt tillgänglighetstest har skapats.  |
|**Klassisk** | Vi rekommenderar inte längre att du använder klassiska aviseringar för nya tillgänglighetstester.|
|**Tröskelvärde för variseringsplats**|Vi rekommenderar minst 3/5 platser. Det optimala förhållandet mellan tröskelvärdet för varningsplats och antalet testplatser är =  **tröskelvärdet för varningsplats****för testplatser - 2, med minst fem testplatser.**|

## <a name="configuration"></a>Konfiguration

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Koppla tid och slumptal till testet

Anta att du testar ett verktyg som hämtar tidsberoende data, till exempel aktier från ett externt flöde. När du spelar in webbtestet måste du ange specifika tider, men du anger dem som parametrar för testet, StartTime och EndTime.

![Min awesome lager app skärmdump](./media/availability-multistep/app-insights-72webtest-parameters.png)

När du kör testet vill du att EndTime alltid ska vara den aktuella tiden, och StartTime 15 minuter tidigare.

Plugin för webbtestdatumtid är ett sätt att hantera parametertider.

1. Lägg till ett plugin-program för webbtester för varje variabelt parametervärde som du behöver. I verktygsfältet Webbtest väljer du **Lägg till plugin-program för webbtest**.
    
    ![Lägg till plugin-program för webbtest](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    I det här exemplet ska vi använda två instanser av plugin-programmet för datum och tid. En instans är för ”15 minuter tidigare” och en annan för ”nu”.

2. Öppna egenskaperna för varje plugin-program. Lägg till ett namn och ange att den aktuella tiden ska användas. För den ena anger du Lägg till minuter = -15.

    ![Kontextparametrar](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Du refererar till plugin-namn genom att använd {{plugin-name}} i parametrarna för webbtestet.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ladda upp testet till portalen. De dynamiska värdena används i varje körning av testet.

### <a name="dealing-with-sign-in"></a>Hantera inloggning

Om användarna måste logga in i din app kan du välja mellan olika alternativ för att simulera inloggningen, så att du kan testa sidorna bakom inloggningen. Vilken metod du använder beror på vilken typ av säkerhet som tillhandahålls av appen.

I samtliga fall bör du skapa ett konto i ditt program som endast används för testning. Om möjligt begränsar du behörigheterna för det här testkontot så att webbtestningen inte påverkar riktiga användare.

**Enkelt användarnamn och lösenord** Spela in ett webbtest på vanligt sätt. Ta bort cookies först.

**SAML-autentisering**

|Egenskapsnamn| Beskrivning|
|----|-----|
| Publik Uri | Målgrupps-URI för SAML-token.  Det här är URI för ACS (Access Control Service) – inklusive ACS-namnområde och värdnamn. |
| Lösenord för certifikat | Lösenordet för klientcertifikatet som ger åtkomst till den inbäddade privata nyckeln. |
| Klientcertifikat  | Klientcertifikatvärdet med privat nyckel i Base64-kodat format. |
| Namn-ID | Namnidentifieraren för token |
| Inte efter | Tidsintervallet som token kommer att vara giltig för.  Standardvärdet är 5 minuter. |
| Inte före | Tidsspannet för vilken en token som skapats tidigare är giltig (för att åtgärda tidssnedskjutningar).  Standardvärdet är (negativt) 5 minuter. |
| Namn på målkontextparameter | Kontextparametern som ska ta emot det genererade påståendet. |


**Klienthemlighet** Om din app har en inloggningsväg som involverar en klienthemlighet använder du den vägen. Azure Active Directory (AAD) är ett exempel på en tjänst som erbjuder inloggning med klienthemligheter. I AAD är klienthemligheten appnyckeln.

Här är ett exempel på ett webbtest för en Azure-webbapp som använder en appnyckel:

![Exempel på skärmdump](./media/availability-multistep/client-secret.png)

Hämta en token från AAD med hjälp av en klienthemlighet (AppKey).
Extrahera ägartoken från svaret.
Anropa API:et med hjälp av ägartoken i auktoriseringshuvudet.
Se till att webbtestet är en faktisk klient - det vill ha en egen app i AAD - och använd dess clientId + appnyckel. Din tjänst under test har också en egen app i AAD: appID URI för denna app återspeglas i webbtestet i resursfältet.

### <a name="open-authentication"></a>Öppen autentisering
Ett exempel på öppen autentisering är inloggning med ett Microsoft- eller Google-konto. Många appar som använder OAuth erbjuder möjligheten att använda en klienthemlighet, så det första du bör göra är att ta reda på detta.

Om testet måste logga in med OAuth är den allmänna riktlinjen att:

Använda ett verktyg som Fiddler för att undersöka trafiken mellan webbläsaren, autentiseringswebbplatsen och din app.
Utföra två eller flera inloggningar med olika datorer eller webbläsare, eller med långa intervall (så att token upphör att gälla).
Genom att jämföra olika sessioner identifiera den token som skickas tillbaka från autentiseringswebbplatsen, som sedan skickas till din appserver efter inloggningen.
Spela in ett webbtest med hjälp av Visual Studio.
Parameterisera token genom att ange parametern när token returneras från autentiseraren och använda den i frågan till webbplatsen. (Visual Studio försöker parameterisera testet, men kan inte parameterisera token korrekt.)

## <a name="troubleshooting"></a>Felsökning

Särskild [felsökningsartikel](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighetsaviseringar](availability-alerts.md)
* [Url ping webbtester](monitor-web-app-availability.md)
