---
title: "Testa prestanda för en molnbaserad tjänst | Microsoft Docs"
description: "Testa prestanda för en tjänst i molnet med hjälp av Visual Studio-profiler"
services: visual-studio-online
documentationcenter: n/a
author: kraigb
manager: ghogen
editor: 
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: kraigb
ms.openlocfilehash: eafcc2f9d53bcdae63036df070e5adec24cbc252
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testa prestanda för en tjänst i molnet
## <a name="overview"></a>Översikt
Du kan testa prestanda för en tjänst i molnet på följande sätt:

* Använd Azure-diagnostik samla in information om begäranden och anslutningar och granska platsstatistik som visar hur tjänsten utför ur customer. Kom igång med finns [konfigurera diagnostik för virtuella datorer och Azure Cloud Services](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Använd Visual Studio-profiler för att få en detaljerad analys av beräkningar aspekter av hur tjänsten körs. Du kan använda profileraren för att mäta prestanda som en tjänst som körs i Azure som det här avsnittet beskrivs. Information om hur du använder profileraren för att mäta prestanda som en tjänst körs lokalt i en beräkningsemulatorn finns [testa prestanda för ett Azure Cloud Service lokalt i en Compute Emulator med hjälp av Visual Studio profileraren](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Att välja en metod för prestandatestning
### <a name="use-azure-diagnostics-to-collect"></a>Använd Azure-diagnostik för att samla in:
* Statistik på webbsidor eller tjänster, till exempel begäranden och anslutningar.
* Statistik över roller, till exempel hur ofta en roll har startats om.
* Allmän information om minnesanvändning, till exempel procentandelen av tid skräpinsamlingen tar eller minnet uppsättning den pågående rollen.

### <a name="use-the-visual-studio-profiler-to"></a>Använd Visual Studio-profiler till:
* Avgör vilka funktioner ta ut mesta möjliga tid.
* Mäter hur lång tid tar för varje del av ett beräkningsmässigt intensiva program.
* Jämför detaljerad prestandarapporter för två versioner av en tjänst.
* Analysera minnesallokering i detalj än enskilda minnesallokering.
* Analysera samtidighet problem i flertrådade kod.

När du använder profileraren kan du samla in data när en molnbaserad tjänst körs lokalt eller i Azure.

### <a name="collect-profiling-data-locally-to"></a>Samla in profileringsdata lokalt till:
* Testa prestanda hos en del av en molntjänst, t.ex körningen av specifika arbetsroll som inte kräver en realistisk simulerade belastning.
* Testa prestanda för en tjänst i molnet i isolering under kontrollerade förhållanden.
* Testa prestanda för en tjänst i molnet innan du distribuerar det till Azure.
* Testa prestanda för en tjänst i molnet privat, utan att störa befintliga distributioner.
* Testa prestanda hos tjänsten utan att det medför kostnader för körs i Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Samla in profileringsdata i Azure för att:
* Testa prestanda för en tjänst i molnet under en simulerad eller verkliga belastning.
* Använd metoden instrumentation för att samla in profileringsdata, som det här avsnittet beskrivs senare.
* Testa prestanda för tjänsten i samma miljö som när tjänsten körs i produktion.

Du simulera vanligtvis en belastning test molntjänster under normal eller stress villkor.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilering av en tjänst i molnet i Azure
När du publicerar Molntjänsten från Visual Studio kan du ange vilka inställningar som ger dig den information som du vill profilen tjänsten. En profilering session startas för varje instans av en roll. Mer information om hur du publicerar din tjänst från Visual Studio finns [publicering till en Azure-molntjänst från Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Om du vill veta mer om profilering av prestanda i Visual Studio, se [nybörjare Guide för prestanda profilering](https://msdn.microsoft.com/library/azure/ms182372.aspx) och [Analysera prestanda med hjälp av verktyg för profilering](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Du kan aktivera IntelliTrace eller profilering när du publicerar Molntjänsten. Du kan aktivera båda.
> 
> 

### <a name="profiler-collection-methods"></a>Profileraren samling metoder
Du kan använda annan samling metoder för profilering, baserat på dina prestandaproblem:

* **CPU-provtagning** -den här metoden samlar in programstatistik som är användbara för inledande analys av problem med CPU-användning. CPU-provtagning är den föreslagna metoden för att starta de flesta prestanda undersökningar. Det finns en låg påverkan på det program som du profilering när du samlar in CPU provtagning data.
* **Instrumentation** -den här metoden samlar in detaljerad tidsinställning data som är användbar för fokuserad analys och för att analysera i/o-prestanda. Metoden instrumentation registrerar varje post och avsluta funktionsanropet av funktioner i en modul under en profilering kör. Den här metoden är användbar för att samla in detaljerade tidsinställning information om en del av din kod och för att förstå effekten av inkommande och utgående åtgärder på programmets prestanda. Den här metoden är inaktiverad för en dator som kör ett 32-bitars operativsystem. Det här alternativet är tillgängligt endast när du kör Molntjänsten i Azure, inte lokalt i beräkningsemulatorn.
* **Minnesallokering för .NET** -den här metoden samlar in .NET Framework minne allokering data med hjälp av beräkningarna profilering metod. Insamlade data omfattar antal och storlek på allokerade objekt.
* **Concurrency** -den här metoden samlar in konkurrens resursdata och process- och Körningsdata som är användbara vid analys av flertrådade och flerprocessig program. Metoden samtidighet samlar in data för varje händelse som blockerar körning av din kod, till exempel när en tråd väntar låst åtkomst till en resurs för program att frigöras. Den här metoden är användbar för att analysera flertrådiga program.
* Du kan också aktivera **nivå interaktion profilering**, som tillhandahåller ytterligare information om körningstider för synkron ADO.NET anropar i funktioner i flera nivåer program som kommunicerar med en eller flera databaser. Du kan samla in data för nivån interaktion med någon av metoderna profilering. Läs mer om nivån interaktion profilering [nivå interaktioner visa](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Konfigurera inställningar för profilering
Följande bild visar hur du konfigurerar inställningarna profilering från dialogrutan Publicera Azure-program.

![Konfigurera profilering inställningar](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Så här aktiverar du den **Aktivera profilering** kryssrutan, måste du ha profileraren installerad på den lokala datorn som du använder för att publicera din tjänst i molnet. Profileraren installeras som standard när du installerar Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Att konfigurera inställningar för profilering
1. Öppna snabbmenyn för din Azure-projekt i Solution Explorer och välj sedan **publicera**. Detaljerade anvisningar om hur du publicerar en tjänst i molnet finns [publicering av en tjänst i molnet med Azure-verktyg](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. I den **publicera Azure-programmet** dialogrutan väljer den **avancerade inställningar** fliken.
3. Välj för att aktivera profilering av **Aktivera profilering** kryssrutan.
4. Så här konfigurerar du inställningarna för profilering av **inställningar** hyperlänk. Dialogrutan profilering inställningar visas.
5. Från den **vilken metod för profilering av du vill använda** alternativknappar, Välj typ av profilering som du behöver.
6. Om du vill samla in nivå interaktionen profilering data, Välj den **aktivera nivå interaktion profilering** kryssrutan.
7. Spara inställningarna genom att välja den **OK** knappen.
   
    När du publicerar programmet används de här inställningarna för att skapa profilering session för varje roll.

## <a name="viewing-profiling-reports"></a>Visa profilering rapporter
En profilering session skapas för varje instans av en roll i Molntjänsten. Om du vill visa profilering av varje session från Visual Studio, kan du visa Server Explorer-fönstret och välj sedan Azure Compute-nod för att välja en instans av en roll. Du kan sedan visa rapporten profilering som visas i följande bild.

![Visa profilering rapport från Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Visa profilering rapporter
1. Om du vill visa fönstret Server Explorer i Visual Studio, på menyraden Välj Visa, Server Explorer.
2. Välj Azure Compute-nod och väljer sedan noden Azure-distribution för den molntjänst som du har valt att profilen när du publicerade från Visual Studio.
3. Om du vill visa vilka rapporter för en instans, väljer du vilken roll i tjänsten, öppna snabbmenyn för en specifik instans och välj sedan **visa profilering rapport**.
   
    Rapporten kan en .vsp fil nu laddas ned från Azure och status för hämtningen visas i Azure-aktivitetsloggen. När nedladdningen är klar profilering rapporten visas på en flik i Redigeraren för Visual Studio med namnet <Role name>  *<Instance Number>*  <identifier>.vsp. Sammanfattningsdata för rapporten visas.
4. För att visa olika vyer av rapporten i aktuell vy listan, Välj typ av vy som du vill. Mer information finns i [profilering verktyg rapportvyer](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Nästa steg
[Felsökning av molntjänster](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicering till en Azure-molntjänst från Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

