---
title: Profilering av en molntjänst lokalt i Beräkningsemulatorn | Microsoft Docs
services: cloud-services
description: Undersöka prestandaproblem i cloud services med Visual Studio profiler
documentationcenter: ''
author: mikejo
manager: douge
editor: ''
tags: ''
ms.assetid: 25e40bf3-eea0-4b0b-9f4a-91ffe797f6c3
ms.service: cloud-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/18/2016
ms.author: mikejo
ms.openlocfilehash: ea46039583681bd89e254d153997e3a300041d4e
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37341362"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testa prestanda hos en molntjänst lokalt i Azure-Beräkningsemulatorn med hjälp av Visual Studio Profiler
En mängd olika verktyg och tekniker som är tillgängliga för att testa prestanda för molntjänster.
När du publicerar en molnbaserad tjänst på Azure kan du har Visual Studio samlar in profildata och analysera den lokalt, enligt beskrivningen i [profilering ett Azure-program][1].
Du kan också använda diagnostik för att spåra en mängd olika prestandaräknare, enligt beskrivningen i [med prestandaräknare i Azure][2].
Du kanske också vill Profilera ditt program lokalt i beräkningsemulatorn innan du distribuerar den till molnet.

Artikeln handlar om CPU-samplingsmetoden för profilering som kan göras lokalt i emulatorn. CPU-sampling är en metod för profilering som inte är mycket påträngande. Vid ett avsedda exempelintervall tar profiler en ögonblicksbild av anropsstacken. Data som samlas in under en viss tidsperiod och visas i en rapport. Profileringsmetoden tenderar att ange var i ett beräkningsintensivt program de flesta av CPU-arbetet sker.  Detta ger dig möjlighet att fokusera på den ”heta sökvägen” där ditt program ödslar mest tid.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurera Visual Studio för att profilera
Det finns först ett fåtal konfigurationsalternativ för Visual Studio som kan vara användbar när Profileringen. Om du vill göra uppfattning om Profileringen rapporterna måste symboler (.pdb-filer) för ditt program och även symboler för-bibliotek. Du vill kontrollera att du hänvisar till de tillgängliga symbol-servrarna. Att göra detta på den **verktyg** i Visual Studio, Välj **alternativ**, välj sedan **Debugging**, sedan **symboler**. Se till att Microsoft Symbol-servrar visas **symbolen filplatser (.pdb)**.  Du kan också referera http://referencesource.microsoft.com/symbols, som kan ha andra filer.

![Symbolalternativ][4]

Om du vill kan förenkla du de rapporter som profiler genererar genom att ange bara min kod. Med bara min kod aktiverat förenklas funktionen anropsstackar så att anrop helt interna för bibliotek och .NET Framework är dolda från rapporterna. På den **verktyg** menyn, Välj **alternativ**. Expandera sedan den **prestandaverktyg** nod, och välj **Allmänt**. Markera kryssrutan för **aktivera bara min kod för profiler rapporter**.

![Alternativ för bara min kod][17]

Du kan använda dessa instruktioner med ett befintligt projekt eller med ett nytt projekt.  Om du skapar ett nytt projekt om du vill prova de metoder som beskrivs nedan, väljer du ett C# **Azure Cloud Service** projektet och välj en **Webbroll** och en **Arbetsroll**.

![Azure Cloud Service-projekt-roller][5]

Betecknas Lägg exempelvis till lite kod i projektet som tar lång tid och visar några uppenbara prestandaproblem. Till exempel lägga till följande kod till en arbetsrollsprojektet:

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

Anropa den här koden från RunAsync-metod i worker-roll RoleEntryPoint-härledd klass. (Ignorera varningen om metoden körs synkront.)

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

Skapa och köra din molntjänst lokalt utan felsökning (Ctrl + F5) med lösningskonfigurationen inställd **versionen**. Detta säkerställer att alla filer och mappar har skapats för att köra programmet lokalt och säkerställer att alla emulatorerna har startats. Kontrollera att din arbetsroll körs genom att börja Compute Emulator UI från Aktivitetsfältet.

## <a name="2-attach-to-a-process"></a>2: Anslut till en process
Du måste koppla profiler till en process som körs i stället för profilering programmet genom att starta den från Visual Studio 2010 IDE. 

Att koppla profiler till en process på den **analysera** menyn, Välj **Profiler** och **bifoga till/ta bort**.

![Bifoga profil][6]

För en arbetsroll, hittar du WaWorkerHost.exe-processen.

![WaWorkerHost process][7]

Om din projektmapp, till exempel finns på en nätverksenhet, profiler blir du ombedd att ange en annan plats om du vill spara profilering rapporterna.

 Du kan även bifoga i en webbroll genom att koppla till WaIISHost.exe.
Om det finns flera arbetsprocesser roll i ditt program kan behöva du använda ett process-ID för att skilja dem. Du kan fråga processID programmässigt genom att öppna processobjektet. Om du lägger till den här koden Run-metoden i klassen RoleEntryPoint-härledd i en roll kan titta du exempelvis på loggen i Compute Emulator UI veta vilken process för att ansluta till.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Om du vill visa loggen starta Compute Emulator UI.

![Starta Compute Emulator UI][8]

Öppna konsolfönstret för worker-rollen log i Compute Emulator UI genom att klicka på konsolen namnlist. Du kan se process-ID i loggen.

![Visa process-ID][9]

Något du har anslutit utför stegen i programmets användargränssnitt (vid behov) för att återskapa scenariot.

När du vill stoppa Profileringen, välja den **stoppa Profileringen** länk.

![Stoppa Profileringen alternativet][10]

## <a name="3-view-performance-reports"></a>3: Visa sestavy výkonu
Systemprestanda-rapport för ditt program visas.

Nu profiler stoppar kör, sparar data i en .vsp-fil och visar en rapport som visar en analys av dessa data.

![Profiler-rapport][11]

Om du ser String.wstrcpy i den heta sökvägen, klickar du på bara min kod för att ändra vyn så att endast användarkod.  Om du ser String.Concat kan du prova att trycka på knappen Visa All kod.

Du bör se sammanfoga metoden och String.Concat tar upp en stor del av körningstiden.

![Analysen av rapporter][12]

Om du har lagt till sträng sammanfogning koden i den här artikeln bör du se en varning i uppgiftslistan för detta. Du kan också se en varning om att det finns en orimlig mängd skräpinsamling, som beror på antalet strängar som skapas och tas bort.

![Prestanda-varningar][14]

## <a name="4-make-changes-and-compare-performance"></a>4: ändra och jämför resultat
Du kan också jämföra prestanda före och efter en kodändring.  Stoppa processen och redigera koden för att ersätta strängen sammanfogning igen med hjälp av StringBuilder:

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

Göra en annan prestanda kör, och sedan jämföra prestanda. I Explorer prestanda om körningar som finns i samma session, du kan bara markera båda rapporterna, öppna snabbmenyn och välj **jämför prestandarapporter**. Om du vill jämföra med en körs i en annan session för prestanda, öppna den **analysera** -menyn och välj **jämför prestandarapporter**. Ange båda filerna i dialogrutan som visas.

![Jämför Prestandaalternativ för rapporter][15]

Rapporterna markerar skillnaderna mellan de två körningarna.

![Jämförelse av rapport][16]

Grattis! Du har kommit igång med profiler.

## <a name="troubleshooting"></a>Felsökning
* Kontrollera att du profilering en slutversion och starta utan felsökning.
* Om alternativet Anslut till/ta bort inte är aktiverad på menyn Profiler, kör du guiden prestanda.
* Använd Compute Emulator UI om du vill visa status för ditt program. 
* Om du har problem med start av program i emulatorn eller koppla profiler, stänga ned beräkningsemulatorn och starta om den. Om det inte löser problemet, startar du om datorn. Det här problemet kan uppstå om du använder Compute-emulatorn att inaktivera och ta bort körs distributioner.
* Om du har använt någon av Profileringen kommandon från kommandoraden, särskilt de globala inställningarna, se till att VSPerfClrEnv /globaloff har anropats och att VsPerfMon.exe har stängts av.
* Om när sampling, visas meddelandet ”PRF0025: inga data har samlats in”, kontrollera att den process som du har bifogat har CPU-aktivitet. Program som inte genomför någon dataarbetet kan inte ge någon sampling av data.  Det är också möjligt att processen har avslutats innan alla sampling utfördes. Kontrollera att metoden Run för en roll som du Profileringen inte avslutar.

## <a name="next-steps"></a>Nästa steg
Instrumentering av Azure-binärfiler i emulatorn stöds inte i Visual Studio-profiler, men om du vill testa minnesallokering du väljer det alternativet när Profileringen. Du kan också välja samtidighet profilering, som hjälper dig att avgöra om trådar slösa tid konkurrerande för lås, eller nivå interaktion profilering, som hjälper dig att spåra problem med prestanda när du interagerar mellan nivåer för ett program, mest ofta mellan datanivå och en arbetsroll.  Du kan visa databasfrågor som din app genererar och använder profileringsdata för att förbättra din användning av databasen. Information om nivån interaktion profilering, finns i bloggposten [genomgång: med nivån interaktion Profiler i Visual Studio Team System 2010][3].

[1]: https://docs.microsoft.com/azure/application-insights/app-insights-profiler
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
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
