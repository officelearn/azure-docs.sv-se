---
title: Övervaka ditt webbprogram med flera steg och Azure Application Insights | Microsoft Docs
description: Konfigurera flerstegstest tester för att övervaka dina webbprogram med Azure Application Insights
services: application-insights
author: mrbullwinkle
manager: carmonm
ms.assetid: 46dc13b4-eb2e-4142-a21c-94a156f760ee
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 06/19/2019
ms.reviewer: sdash
ms.author: mbullwin
ms.openlocfilehash: d8bfe92af4e8afc4edae76efb2e1cb7b287c7aa9
ms.sourcegitcommit: 82efacfaffbb051ab6dc73d9fe78c74f96f549c2
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/20/2019
ms.locfileid: "67304998"
---
# <a name="multi-step-web-tests"></a>Webbtester med flera steg

Du kan övervaka en inspelade serie URL: er och interaktioner med en webbplats via flera steg. Den här artikeln vägleder dig genom processen med att skapa ett webbtest med flera steg med Visual Studio Enterprise.

> [!NOTE]
> Webbtester med flera steg ha ytterligare kostnader som är kopplade till standardrisknivåer. Läs mer finns det [officiella guiden för prissättning](https://azure.microsoft.com/pricing/details/application-insights/).

## <a name="pre-requisites"></a>Förutsättningar

* Visual Studio 2017 Enterprise eller större.
* Visual Studio-webbprestanda och belastning testverktyg.

Att hitta testning verktyg uppfyller. Starta den **installationsprogrammet för Visual Studio** > **enskilda komponenter** > **felsökning och testning**  >   **Webb-prestanda och läsa in testverktyg**.

![Skärmbild av installationsprogrammet för Visual Studio Användargränssnittet med enskilda komponenter som är markerad med en kryssruta bredvid objektet för webbprestanda och belastning testverktyg](./media/availability-multistep/web-performance-load-testing.png)

## <a name="record-a-multi-step-web-test"></a>Registrera ett webbtest med flera steg

Om du vill skapa ett test med flera steg spelar du in scenariot med hjälp av Visual Studio Enterprise och laddar sedan upp inspelningen till Application Insights. Application Insights spelar upp scenariot med jämna mellanrum och verifierar svaret.

> [!IMPORTANT]
> * Du kan inte använda kodade funktioner eller loopar i dina tester. Testet måste ingå i .webtest-skriptet. Du kan dock använda standardplugin-program.
> * Endast engelska tecken stöds i webbtester med flera steg. Om du använder Visual Studio på andra språk måste du uppdatera definitionsfilen för webbtesten för att översätta/exkludera icke-engelska tecken.

Spela in en webbsession med Visual Studio Enterprise.

1. Skapa en webbprestanda och belastning testprojekt. **Filen** > **nya** > **projekt** > **Visual C#**   >  **Test**

    ![Visual Studio nytt projekt UI](./media/availability-multistep/vs-web-performance-and-load-test.png)

2. Öppna den `.webtest` fil och börja inspelningen.

    ![Spela in Användargränssnittet för Visual Studio-test](./media/availability-multistep/open-web-test.png)

3. Klicka dig igenom de steg som du vill att testet att simulera som en del av inspelningen.

    ![Webbläsarinspelning UI](./media/availability-multistep/record.png)

4. Redigera testet om du vill:

    * Lägg till valideringar för att kontrollera texten som tas emot och svarskoderna.
    * Ta bort eventuella uneccesary interaktioner. Du kan också ta bort beroende förfrågningar för bilder eller lägga till spårnings-platser som inte är relevant för dig och överväger testet lyckas.
    
    Tänk på att du kan bara redigera testskriptet – du kan lägga till anpassad kod eller anropa andra webbtester. Infoga inte loopar i testet. Du kan använda standard-plugin-program för webbtester.

5. Kör testet i Visual Studio för att verifiera och kontrollera att det fungerar.

    En webbläsare öppnas och de åtgärder som du har spelat in upprepas. Kontrollera att allt fungerar som förväntat.

## <a name="upload-the-web-test"></a>Ladda upp webbtestet

1. Välj i Application Insights-portalen i fönstret tillgänglighet **skapa testa** > **Testtyp** > **flerstegstest för webbplatser**.

2. Ange platserna för tillgänglighetstester, frekvens och aviseringsparametrar.

### <a name="frequency--location"></a>Frekvens och plats

|Inställning| Förklaring
|----|----|----|
|**Testfrekvens**| Anger hur ofta testet körs från varje testplats. Med en standardfrekvens på fem minuter och fem testplatser testas din webbplats i genomsnitt varje minut.|
|**Testplatser**| Är platserna som våra servrar skickar webbförfrågningar till din URL. **Vår minsta antal rekommenderade testplatser är fem** för att se till att du kan skilja mellan problem på din webbplats nätverksproblem. Du kan välja upp till 16 platser.

### <a name="success-criteria"></a>Villkor för lyckat test

|Inställning| Förklaring
|----|----|----|
| **Timeout för test** |Minska det här värdet om du vill få aviseringar om långsamma svar. Testet räknas som misslyckat om svaren från din webbplats inte har tagits emot inom denna period. Om du valde **Parsa beroende begäranden** måste alla bilder, formatfiler, skript och andra beroende resurser ha tagits emot inom denna period.|
| **HTTP-svar** | Den returnerade statuskoden som räknas som ett lyckat test. 200 är koden som anger att en normal webbsida har returnerats.|
| **Innehållsmatchning** | En sträng, t.ex. ”Välkommen”! Vi testar i varje svar om någon skiftlägeskänslig matchning förekommer. Den måste vara en enkel sträng utan jokertecken. Glöm inte att du kan behöva uppdatera sidan om innehållet ändras. **Endast engelska tecken stöds med innehållsmatchning** |

### <a name="alerts"></a>Aviseringar

|Inställning| Förklaring
|----|----|----|
|**Nära realtid (förhandsversion)** | Vi rekommenderar att du använder aviseringar i nära realtid. Konfigurera den här typen av avisering görs efter din tillgänglighetstestet har skapats.  |
|**Klassisk** | Vi rekommenderar inte längre med klassiska aviseringar för nya tillgänglighetstester.|
|**Aviseringsplats**|Vi rekommenderar minst 3/5 platser. Optimal relationen mellan aviseringsplats och antalet testplatser är **aviseringsplats** = **antal testplatser - 2, med minst fem testplatser.**|

## <a name="advanced-configuration"></a>Avancerad konfiguration

### <a name="plugging-time-and-random-numbers-into-your-test"></a>Ansluta tid och slumptal till testet

Anta att du testar ett verktyg som hämtar tidsberoende data, till exempel aktier från ett externt flöde. När du spelar in webbtestet måste du ange specifika tider, men du anger dem som parametrar för testet, StartTime och EndTime.

![Min fantastiska lagerartiklar app-skärmbild](./media/availability-multistep/app-insights-72webtest-parameters.png)

När du kör testet vill du att EndTime alltid ska vara den aktuella tiden, och StartTime 15 minuter tidigare.

Plugin-programmet Web Test datum tid ger ett sätt att hantera Parameterisera tider.

1. Lägg till ett plugin-program för webbtester för varje variabelt parametervärde som du behöver. I verktygsfältet Webbtest väljer du **Lägg till plugin-program för webbtest**.
    
    ![Lägg till Webbtest-plugin-programmet](./media/availability-multistep/app-insights-72webtest-plugin-name.png)
    
    I det här exemplet ska vi använda två instanser av plugin-programmet för datum och tid. En instans är för ”15 minuter tidigare” och en annan för ”nu”.

2. Öppna egenskaperna för varje plugin-program. Lägg till ett namn och ange att den aktuella tiden ska användas. För den ena anger du Lägg till minuter = -15.

    ![Kontextparametrar](./media/availability-multistep/app-insights-72webtest-plugin-parameters.png)

3. Du refererar till plugin-namn genom att använd {{plugin-name}} i parametrarna för webbtestet.

    ![StartTime](./media/availability-multistep/app-insights-72webtest-plugins.png)

Ladda upp testet till portalen. De dynamiska värdena används i varje körning av testet.

### <a name="dealing-with-sign-in"></a>Hantera inloggning

Om användarna måste logga in i din app kan du välja mellan olika alternativ för att simulera inloggningen, så att du kan testa sidorna bakom inloggningen. Vilken metod du använder beror på vilken typ av säkerhet som tillhandahålls av appen.

I samtliga fall bör du skapa ett konto i ditt program som endast används för testning. Om möjligt begränsar du behörigheterna för det här testkontot så att webbtestningen inte påverkar riktiga användare.

**Enkelt användarnamn och lösenord** spela ett webbtest som vanligt. Ta bort cookies först.

**SAML-autentisering** använder SAML-plugin-programmet som är tillgängligt för webbtester. Åtkomst till plugin-programmet genom att...

**Klienthemlighet** om din app kräver med en inloggning som en klienthemlighet, använder du. Azure Active Directory (AAD) är ett exempel på en tjänst som erbjuder inloggning med klienthemligheter. I AAD är klienthemligheten appnyckeln.

Här är ett exempel på ett webbtest för en Azure-webbapp som använder en appnyckel:

![Skärmbild av exempel](./media/availability-multistep/client-secret.png)

Hämta en token från AAD med hjälp av en klienthemlighet (AppKey).
Extrahera ägartoken från svaret.
Anropa API:et med hjälp av ägartoken i auktoriseringshuvudet.
Kontrollera att webbtestet är en riktig klient – det vill säga den har en egen app i AAD, och Använd dess klient-ID och app nyckel. Tjänsten som testas har också sin egen app i AAD: appID-URI för den här appen visas i webbtestet i resursfältet.

### <a name="open-authentication"></a>Öppen autentisering
Ett exempel på öppen autentisering är inloggning med ett Microsoft- eller Google-konto. Många appar som använder OAuth erbjuder möjligheten att använda en klienthemlighet, så det första du bör göra är att ta reda på detta.

Om testet måste logga in med OAuth är den allmänna riktlinjen att:

Använda ett verktyg som Fiddler för att undersöka trafiken mellan webbläsaren, autentiseringswebbplatsen och din app.
Utföra två eller flera inloggningar med olika datorer eller webbläsare, eller med långa intervall (så att token upphör att gälla).
Genom att jämföra olika sessioner identifiera den token som skickas tillbaka från autentiseringswebbplatsen, som sedan skickas till din appserver efter inloggningen.
Spela in ett webbtest med hjälp av Visual Studio.
Parameterisera token genom att ange parametern när token returneras från autentiseraren och använda den i frågan till webbplatsen. (Visual Studio försöker parameterisera testet, men kan inte parameterisera token korrekt.)

## <a name="troubleshooting"></a>Felsökning

Dedikerad [felsökningsartikeln](troubleshoot-availability.md).

## <a name="next-steps"></a>Nästa steg

* [Tillgänglighet aviseringar](availability-alerts.md)
* [URL-Pingtest web](monitor-web-app-availability.md)
