---
title: Profilering av en molntjänst lokalt i beräkningsemulatorn | Microsoft-dokument
services: cloud-services
description: Undersöka prestandaproblem i molntjänster med Visual Studio-profiler
documentationcenter: ''
author: mikejo
manager: jillfra
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: 21270d3c7143ce063ffe30d939368b9813e9072e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "70094106"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testa prestanda för en molntjänst lokalt i Azure Compute Emulator med visual studioprofiler
En mängd olika verktyg och tekniker finns tillgängliga för att testa prestanda för molntjänster.
När du publicerar en molntjänst till Azure kan du låta Visual Studio samla in profileringsdata och sedan analysera dem lokalt, enligt beskrivningen i [Profilering av ett Azure-program][1].
Du kan också använda diagnostik för att spåra en mängd olika prestandaräknare, enligt beskrivningen i [Använda prestandaräknare i Azure][2].
Du kanske också vill profilera ditt program lokalt i beräkningemulatorn innan du distribuerar det till molnet.

Den här artikeln täcker CPU-samplingsmetoden för profilering, vilket kan göras lokalt i emulatorn. CPU-sampling är en metod för profilering som inte är särskilt påträngande. Vid ett angivet samplingsintervall tar profileren en ögonblicksbild av anropsstacken. Uppgifterna samlas in under en viss tidsperiod och visas i en rapport. Denna metod för profilering tenderar att ange var i en beräkningsintensiv tillämpning de flesta av CPU-arbete görs.  Detta ger dig möjlighet att fokusera på den "heta vägen" där din ansökan spenderar mest tid.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurera Visual Studio för profilering
Först finns det några Visual Studio-konfigurationsalternativ som kan vara användbara vid profilering. För att förstå profileringsrapporterna behöver du symboler (.pdb-filer) för ditt program och även symboler för systembibliotek. Du bör se till att du refererar till de tillgängliga symbolservrarna. Det gör du på **Verktyg-menyn** i Visual Studio **och**väljer sedan **Felsökning**och sedan **symboler**. Kontrollera att Microsoft Symbol-servrar visas under **Symbolfilsplatser (.pdb).**  Du kan https://referencesource.microsoft.com/symbolsockså referera till , som kan ha ytterligare symbolfiler.

![Alternativ för symbol][4]

Om så önskas kan du förenkla de rapporter som profilerar genererar genom att ange Just My Code. Med Just My Code aktiverat förenklas funktionsanropsstaplarna så att anrop helt interna till bibliotek och .NET Framework döljs från rapporterna. Välj **Alternativ**på **Verktyg-menyn** . Expandera sedan noden **Prestandaverktyg** och välj **Allmänt**. Markera kryssrutan för **Aktivera just min kod för profilerrapporter**.

![Bara mina kodalternativ][17]

Du kan använda dessa instruktioner med ett befintligt projekt eller med ett nytt projekt.  Om du skapar ett nytt projekt för att prova de tekniker som beskrivs nedan väljer du ett C# **Azure Cloud Service-projekt** och väljer en **webbroll** och en **arbetarroll**.

![Azure Cloud Service-projektroller][5]

Lägg till exempel till lite kod i projektet som tar mycket tid och visar några uppenbara prestandaproblem. Lägg till exempel till följande kod i ett arbetsrollprojekt:

```csharp
public class Concatenator
{
    public static string Concatenate(int number)
    {
        int count;
        string s = "";
        for (count = 0; count < number; count++)
        {
            s += "\n" + count.ToString();
        }
        return s;
    }
}
```

Anropa den här koden från metoden RunAsync i arbetarrollens RollEntryPoint-härledda klass. (Ignorera varningen om metoden som körs synkront.)

```csharp
private async Task RunAsync(CancellationToken cancellationToken)
{
    // TODO: Replace the following with your own logic.
    while (!cancellationToken.IsCancellationRequested)
    {
        Trace.TraceInformation("Working");
        Concatenator.Concatenate(10000);
    }
}
```

Skapa och kör molntjänsten lokalt utan felsökning (Ctrl+F5), med lösningskonfigurationen inställd på **Release**. Detta säkerställer att alla filer och mappar skapas för att köra programmet lokalt och säkerställer att alla emulatorer startas. Starta beräkningsemulatorgränssnittet från Aktivitetsfältet för att kontrollera att arbetarrollen körs.

## <a name="2-attach-to-a-process"></a>2: Fäst vid en process
I stället för att profilera programmet genom att starta det från Visual Studio 2010 IDE måste du bifoga profileraren till en pågående process. 

Om du vill koppla profileren till en process väljer du **Profiler** **och Bifoga/Koppla från**profiler på menyn **Analysera.**

![Alternativet Bifoga profil][6]

Leta reda på WaWorkerHost.exe-processen för en arbetsroll.

![WaWorkerHost-processen][7]

Om projektmappen finns på en nätverksenhet ber profilerningen dig att ange en annan plats för att spara profilrapporterna.

 Du kan också koppla till en webbroll genom att ansluta till WaIISHost.exe.
Om det finns flera arbetsrollprocesser i ditt program måste du använda processID för att skilja dem. Du kan fråga processID programmässigt genom att komma åt processobjektet. Om du till exempel lägger till den här koden i metoden Kör för klassen RoleEntryPoint-härledd i en roll kan du titta på loggen i beräkningsemulatorgränssnittet för att veta vilken process som ska anslutas till.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Om du vill visa loggen startar du användargränssnittet för beräkningsemulator.

![Starta användargränssnittet för beräkningsemulator][8]

Öppna fönstret för arbetarrollloggkonsolen i användargränssnittet för beräkningsemulator genom att klicka på konsolfönstrets namnlist. Du kan se process-ID i loggen.

![Visa process-ID][9]

En som du har bifogat, utför stegen i programmets användargränssnitt (om det behövs) för att återskapa scenariot.

När du vill sluta profilera väljer du länken **Stoppa profilering.**

![Alternativet Stoppa profilering][10]

## <a name="3-view-performance-reports"></a>3: Visa resultatrapporter
Prestandarapporten för ditt program visas.

Nu slutar profileren att köra, sparar data i en VSP-fil och visar en rapport som visar en analys av dessa data.

![Profilerrapport][11]

Om du ser String.wstrcpy i den heta sökvägen klickar du på Just My Code för att ändra vyn så att endast användarkod visas.  Om du ser String.Concat kan du prova att trycka på knappen Visa alla kod.

Du bör se Sammanfoga metoden och String.Concat tar upp en stor del av körningstiden.

![Analys av rapporten][12]

Om du har lagt till strängsammanfogningskoden i den här artikeln bör du se en varning i uppgiftslistan för detta. Du kan också se en varning om att det finns en överdriven mängd skräpinsamling, vilket beror på antalet strängar som skapas och tas bort.

![Varningar för prestanda][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Göra ändringar och jämföra prestanda
Du kan också jämföra prestanda före och efter en kodändring.  Stoppa löpprocessen och redigera koden för att ersätta strängsammanfogningsåtgärden med hjälp av StringBuilder:

```csharp
public static string Concatenate(int number)
{
    int count;
    System.Text.StringBuilder builder = new System.Text.StringBuilder("");
    for (count = 0; count < number; count++)
    {
        builder.Append("\n" + count.ToString());
    }
    return builder.ToString();
}
```

Gör en annan prestandakörning och jämför sedan prestanda. Om körningarna finns i samma session i Prestandautforskaren kan du bara markera båda rapporterna, öppna snabbmenyn och välja **Jämför prestandarapporter**. Om du vill jämföra med en körning i en annan prestandasession öppnar du menyn **Analysera** och väljer **Jämför prestandarapporter**. Ange båda filerna i dialogrutan som visas.

![Alternativet Jämför resultatrapporter][15]

Rapporterna belyser skillnader mellan de två körningarna.

![Jämförelserapport][16]

Grattis! Du har börjat med profileraren.

## <a name="troubleshooting"></a>Felsökning
* Se till att du profilerar en Release-version och börja utan felsökning.
* Om alternativet Bifoga/Koppla från inte är aktiverat på Profil-menyn kör du prestandaguiden.
* Använd användargränssnittet för beräkningsemulator för att visa status för ditt program. 
* Om du har problem med att starta program i emulatorn eller bifoga profilerar du, stänger du av beräkningsemulatorn och startar om den. Om det inte löser problemet kan du prova att starta om. Det här problemet kan uppstå om du använder beräkningsemulatorn för att pausa och ta bort distributioner som körs.
* Om du har använt något av profileringskommandona från kommandoraden, särskilt de globala inställningarna, kontrollerar du att VSPerfClrEnv /globaloff har anropats och att VsPerfMon.exe har stängts av.
* Om meddelandet "PRF0025: Inga data samlades in" visas när du komst visas, kontrollera att processen som du kopplade till har CPU-aktivitet. Program som inte utför något beräkningsarbete kanske inte producerar några samplingsdata.  Det är också möjligt att processen avslutades innan någon provtagning gjordes. Kontrollera att metoden Kör för en roll som du profilerar inte upphör.

## <a name="next-steps"></a>Efterföljande moment
Instrumentering av Azure-binärfiler i emulatorn stöds inte i Visual Studio-profiler, men om du vill testa minnesallokering kan du välja det alternativet vid profilering. Du kan också välja samtidighetsprofilering, vilket hjälper dig att avgöra om trådar slösar tid på att konkurrera om lås eller nivåinteraktionsprofilering, vilket hjälper dig att spåra prestandaproblem när du interagerar mellan nivåer i ett program, de flesta ofta mellan datanivån och en arbetarroll.  Du kan visa de databasfrågor som appen genererar och använda profileringsdata för att förbättra din användning av databasen. Information om interaktionsprofilering på nivånivå finns i [genomgången blogginlägg: Använda nivåinteraktionsprofilering i Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: https://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: https://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
