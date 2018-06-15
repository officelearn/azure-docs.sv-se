---
title: Profilering av en tjänst i molnet lokalt i Beräkningsemulatorn | Microsoft Docs
services: cloud-services
description: Undersöka prestandaproblem i molntjänster med Visual Studio-profiler
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
ms.openlocfilehash: 8ff7b88a3086488ab669288687c274237ca30b47
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/30/2018
ms.locfileid: "30284565"
---
# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testa prestanda för en tjänst i molnet lokalt i Azure-Beräkningsemulatorn med hjälp av Visual Studio-Profiler
En mängd olika verktyg och tekniker som är tillgängliga för att testa prestanda för molntjänster.
När du publicerar en tjänst i molnet till Azure kan du har Visual Studio samla in profileringsdata och analysera den lokalt, enligt beskrivningen i [profilering ett Azure-program][1].
Du kan också använda diagnostik för att spåra ett antal prestandaräknare, enligt beskrivningen i [med hjälp av prestandaräknare i Azure][2].
Du kanske också vill profilen programmet lokalt i beräkningsemulatorn innan du distribuerar den till molnet.

Artikeln handlar om CPU-samplingsmetoden för profilering som kan göras lokalt i emulatorn. CPU-provtagning är en metod för profilering som inte är mycket påträngande. Vid ett avsedda exempelintervall tar profileraren en ögonblicksbild av anropsstacken. Data som samlas in under en viss tidsperiod och visas i en rapport. Den här metoden för profilering tenderar att ange där i ett beräkningsmässigt intensiva program mesta av arbetet CPU görs.  Detta ger dig möjlighet att fokusera på ”varm sökvägen” där programmet tillbringar mest tid.

## <a name="1-configure-visual-studio-for-profiling"></a>1: Konfigurera Visual Studio för profilering
Det finns först ett fåtal konfigurationsalternativ för Visual Studio som kan vara användbara när profilering. Om du vill vara meningsfullt profilering rapporter behöver symboler (.pdb-filer) för programmet och även symboler för bibliotek. Du vill kontrollera att du refererar till de tillgängliga symbol-servrarna. Gör på den **verktyg** -menyn i Visual Studio väljer **alternativ**, Välj **Debugging**, sedan **symboler**. Kontrollera att Microsoft Symbol-servrar visas **Symbol filplatser (.pdb)**.  Du kan också referera http://referencesource.microsoft.com/symbols, som kan ha ytterligare symbol-filer.

![Symbolalternativ][4]

Om du vill kan förenkla du de rapporter som profileraren genererar genom att ange bara min kod. Med bara min kod aktiverat, förenklas funktionen anropsstackar så att anrop helt interna för bibliotek och .NET Framework är dolda från rapporterna. På den **verktyg** -menyn, Välj **alternativ**. Expandera den **prestandaverktyg** nod, och välj **allmänna**. Markera kryssrutan för **aktivera bara min kod för profiler rapporter**.

![Bara min kod alternativ][17]

Du kan använda dessa instruktioner med ett befintligt projekt eller med ett nytt projekt.  Om du skapar ett nytt projekt om du vill testa de tekniker som beskrivs nedan väljer du en C# **Azure Cloud Service** projektet och välj en **Webbroll** och en **Arbetsrollen**.

![Azure Cloud Service Projektroller][5]

Till exempel kan lägga till vissa kod projektet som tar mycket tid och visar vissa uppenbara prestandaproblem. Till exempel lägga till följande kod i en arbetsrollsprojektet:

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

Anropa den här koden från metoden RunAsync i worker-rollen RoleEntryPoint-härledd klass. (Ignorera varningen om metoden körs synkront.)

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

Skapa och köra Molntjänsten lokalt utan felsökning (Ctrl + F5) med konfigurationsuppsättning för lösningen att **versionen**. Detta säkerställer att alla filer och mappar skapas för att köra programmet lokalt, och säkerställer att alla emulatorerna har startats. Kontrollera att arbetsrollen körs börja Compute Emulator UI från Aktivitetsfältet.

## <a name="2-attach-to-a-process"></a>2: Anslut till en process
Du måste koppla profileraren till en process som körs i stället för profilering programmet genom att starta från Visual Studio 2010 IDE. 

Bifoga profileraren till en process på den **analysera** -menyn, Välj **Profiler** och **Lägg till/ta bort**.

![Koppla profil][6]

Hitta WaWorkerHost.exe processen för en arbetsroll.

![WaWorkerHost process][7]

Om din projektmapp finns på en nätverksenhet, profileraren blir du ombedd att ange en annan plats om du vill spara profilering rapporter.

 Du kan också koppla till en webbroll genom att koppla till WaIISHost.exe.
Om det finns flera arbetsprocesser roll i ditt program, måste du använder ett process-ID för att skilja dem. Du kan fråga processID via programmering genom att öppna processobjektet. Om du lägger till den här koden Run-metoden i klassen RoleEntryPoint-härledda i en roll kan tittar du till exempel på loggen i Compute Emulator UI veta vilken process för att ansluta till.

```csharp
var process = System.Diagnostics.Process.GetCurrentProcess();
var message = String.Format("Process ID: {0}", process.Id);
Trace.WriteLine(message, "Information");
```

Om du vill visa loggen starta Compute Emulator UI.

![Starta Compute Emulator UI][8]

Öppna konsolfönstret worker-rollen logg i Compute Emulator UI genom att klicka på konsolen namnlist. Du kan se den process-ID i loggen.

![Visa process-ID][9]

Ett du har kopplat utför steg i programmets användargränssnitt (vid behov) för att återskapa scenariot.

När du vill stoppa profilering, välja den **stoppa profilering** länk.

![Stoppa profilering alternativet][10]

## <a name="3-view-performance-reports"></a>3: Visa prestandarapporter
Systemprestanda-rapport för ditt program visas.

Nu profileraren stoppas utförandet, sparar data i en .vsp-fil och visar en rapport som visar en analys av data.

![Profileraren rapport][11]

Om du ser String.wstrcpy i varm sökvägen klickar du på bara min kod för att ändra vyn så att endast användarkod.  Om du ser String.Concat försök att trycka på knappen Visa All kod.

Du bör se sammanfoga metoden och String.Concat tar upp en stor del av körningstiden.

![Analys av rapporten][12]

Om du har lagt till sträng sammanfogning koden i den här artikeln bör du se en varning i uppgiftslistan för detta. Du kan också se en varning om att det finns en orimlig mängd skräpinsamling beror på antalet strängar som skapas och tagits bort.

![Prestanda varningar][14]

## <a name="4-make-changes-and-compare-performance"></a>4: gör ändringar och jämföra prestanda
Du kan också jämföra prestanda före och efter en kodändring.  Stoppa processen och redigera koden för att ersättningsåtgärden sträng sammanfogning med hjälp av StringBuilder:

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

Göra en annan prestanda kör och jämför prestanda. I Utforskaren prestanda om körs i samma session, du kan bara markera båda rapporterna, öppna snabbmenyn och välj **jämför Prestandarapporterna**. Om du vill jämföra med en körning i en annan prestanda session, öppna den **analysera** -menyn och välj **jämför Prestandarapporterna**. Ange båda filerna i dialogrutan som visas.

![Jämför prestanda rapporter alternativet][15]

Rapporterna Markera skillnaderna mellan de två körs.

![Jämförelserapport][16]

Grattis! Du har kommit igång med profileraren.

## <a name="troubleshooting"></a>Felsökning
* Kontrollera att du profilering en slutversion och starta utan felsökning.
* Om alternativet Lägg till/ta bort inte är aktiverat på menyn Profiler, kör du guiden prestanda.
* Använd Compute Emulator UI om du vill visa status för ditt program. 
* Om du har problem med att starta program i emulatorn eller bifoga profiler kan stängas ned beräkningsemulatorn och starta om den. Om det inte löser problemet, startar du om datorn. Det här problemet kan inträffa om du använder Compute Emulator att inaktivera och ta bort körs distributioner.
* Om du använder något av kommandona profilering från kommandoraden, särskilt de globala inställningarna, kontrollerar du att VSPerfClrEnv /globaloff har anropats och att VsPerfMon.exe har avslutats.
* Om när provtagning, visas meddelandet ”PRF0025: inga data samlades in”, kontrollera att du har kopplat till processen har CPU-aktivitet. Program som inte gör några beräkningsarbete kan inte skapa några provtagning data.  Det är också möjligt att processen avslutades innan alla provtagning utfördes. Kontrollera att metoden Kör för en roll som du profilering inte avslutas.

## <a name="next-steps"></a>Nästa steg
Instrumentering Azure binärfiler i emulatorn stöds inte i Visual Studio-profiler, men om du vill testa minnesallokering kan du välja alternativet när profilering. Du kan också välja samtidighet profilering, som hjälper dig att avgöra om trådar slösa tid konkurrerar om Lås eller nivå interaktion profilering, vilket gör det möjligt att spåra prestandaproblem vid interaktion mellan nivåer för ett program, oftast mellan datanivå och en arbetsroll.  Du kan visa databasfrågor som genererar din app och använder profileringsdata för att förbättra din användning av databasen. Information om nivån interaktion profilering bloggposten [genomgång: med nivå interaktion profileraren i Visual Studio Team System 2010][3].

[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
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
