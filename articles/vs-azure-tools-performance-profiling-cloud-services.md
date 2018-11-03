---
title: Testa prestanda för en tjänst i molnet | Microsoft Docs
description: Testa prestanda hos en molntjänst med Visual Studio profiler
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: aa8416bb6883a1aa76ddd370bf7061d7013904eb
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969484"
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testa prestanda för en molntjänst
## <a name="overview"></a>Översikt
Du kan testa prestanda hos en molntjänst på följande sätt:

* Använd Azure Diagnostics att samla in information om begäranden och anslutningar och granska platsstatistik som visar hur tjänsten utför ur en kund. Kom igång med, se [konfigurera diagnostik för Azure Cloud Services och virtuella datorer](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Använd Visual Studio profiler för att få en detaljerad analys av de databaserad aspekterna av hur tjänsten körs. Du kan använda profiler för att mäta prestanda som en tjänst som körs i Azure som det här avsnittet beskrivs. Information om hur du använder profiler för att mäta prestanda som en tjänst körs lokalt i en beräkningsemulatorn finns i [testa prestanda för en Azure Cloud Service lokalt i emulatorn Compute med Visual Studio-Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Välja en metod för prestandatest
### <a name="use-azure-diagnostics-to-collect"></a>Använd Azure Diagnostics för att samla in:
* Statistik på webbsidor eller tjänster, till exempel begäranden och anslutningar.
* Statistik på roller, t.ex hur ofta en roll startas.
* Information om användning av minne, till exempel procentandelen tid skräpinsamlingen tar eller minnet uppsättning övergripande den pågående rollen.

### <a name="use-the-visual-studio-profiler-to"></a>Använd Visual Studio-profiler om du vill:
* Avgöra vilka funktioner som tar längst tid.
* Mäta hur lång tid tar för varje del av ett beräkningsintensiva program.
* Jämför detaljerad prestandarapporter för två versioner av en tjänst.
* Analysera minnesallokering i detalj än nivån för enskilda minnesallokeringar.
* Analysera problem med samtidighet med kod.

När du använder profiler, kan du samla in data när en molnbaserad tjänst som körs lokalt eller i Azure.

### <a name="collect-profiling-data-locally-to"></a>Samla in profileringsdata lokalt till:
* Testa prestanda hos en del av en molntjänst, t.ex körningen av specifika arbetsroll, som inte kräver en realistisk simulerad belastning.
* Testa prestanda hos en molntjänst avskilt under kontrollerade förhållanden.
* Testa prestanda hos en molnbaserad tjänst innan du distribuerar den till Azure.
* Testa prestanda hos en molntjänst privat, utan att störa befintliga distributioner.
* Testa prestanda hos tjänsten utan att det medför kostnader för att köra i Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Samla in profildata i Azure för att:
* Testa prestanda hos en molntjänst simulerad eller verklig belastning.
* Använd metoden instrumentation insamling av profileringsdata, eftersom det här avsnittet beskrivs senare.
* Testa prestanda hos tjänsten i samma miljö som när tjänsten körs i produktion.

Du simulera vanligtvis en belastning för att testa molntjänster under normal eller stress villkor.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilering av en molntjänst i Azure
När du publicerar din molntjänst från Visual Studio kan du ange profilering inställningar som ger dig den information som du vill profilera tjänsten. En profilering session startas för varje instans av en roll. Mer information om hur du publicerar din tjänst från Visual Studio finns i [publicera på Azure molntjänst från Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Om du vill veta mer om prestanda Profileringen i Visual Studio, se [nybörjare Guide för prestanda profilering](https://msdn.microsoft.com/library/azure/ms182372.aspx) och [analys av programmets prestanda med hjälp av verktyg för profilering](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Du kan aktivera IntelliTrace eller profilering när du publicerar din molntjänst. Du kan inte aktivera båda.
> 
> 

### <a name="profiler-collection-methods"></a>Profiler samlingsmetoder
Du kan använda olika samlingsmetoder Profileringen, baserat på dina prestandaproblem:

* **CPU-sampling** -den här metoden samlar in programstatistik som är användbara för inledande analys av problem med CPU-användning. CPU-sampling är den föreslagna metoden för att starta de flesta prestanda undersökningar. Det finns ett låga påverkan på det program som du Profileringen när du samlar in CPU sampling av data.
* **Instrumentation** -den här metoden samlar in detaljerad tidsinställningsdata som är användbar för fokuserad analys och för att analysera problem med i/o-prestanda. Metoden instrumentation registrerar varje post och avsluta funktionsanrop funktioner i en modul under en Profileringen kör. Den här metoden är användbar för att samla in detaljerade tidsinformation om en del av din kod och för att förstå effekten av inkommande och utgående åtgärder på programmets prestanda. Den här metoden har inaktiverats för en dator som kör ett 32-bitars operativsystem. Det här alternativet är tillgängligt endast när du kör Molntjänsten i Azure, inte lokalt i compute-emulatorn.
* **Minnesallokering för .NET** -den här metoden samlar in .NET Framework minne allokering data med hjälp av sampling profilering av metoden. Insamlade data omfattar antal och storlek för allokerade objekt.
* **Samtidighet** -den här metoden samlar in resursdata konkurrens och process- och identitetväxlingar Körningsdata som är användbar för att analysera flera trådar och flera processer. Metoden samtidighet samlar in data för varje händelse som blockerar körning av din kod, till exempel när en tråd väntar låst åtkomst till en resurs för en som ska frigöras. Den här metoden är användbar för att analysera flertrådiga program.
* Du kan också aktivera **nivån interaktion profilering**, som innehåller ytterligare information om körningstider för synkron ADO.NET-anrop i funktioner på flera nivåer program som kommunicerar med en eller flera databaser. Du kan samla in data för nivån interaktion med någon av metoderna profilering. Läs mer om nivån interaktion profilering [nivån interaktioner visa](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Konfigurera inställningar för profilering
Följande bild visar hur du konfigurerar inställningarna profilering från dialogrutan Publicera Azure-program.

![Konfigurera profilering inställningar](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Att aktivera den **Aktivera profilering** kryssrutan, måste du ha profiler som är installerad på den lokala datorn som du använder för att publicera din molntjänst. Som standard installeras profiler när du installerar Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Konfigurera inställningar för profilering
1. Öppna snabbmenyn för din Azure-projekt i Solution Explorer och väljer sedan **publicera**. Detaljerade anvisningar om hur du publicerar en molntjänst i [publicerar en molntjänst med Azure-verktygen](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. I den **publicera Azure-program** dialogrutan har valt den **avancerade inställningar** fliken.
3. Om du vill aktivera profilering, Välj den **Aktivera profilering** markerar du kryssrutan.
4. Så här konfigurerar du inställningarna för profilering den **inställningar** hyperlänk. Dialogrutan profilering inställningar visas.
5. Från den **vilken metod för profilering skulle du vilja använda** alternativknappar, Välj typ av Profileringen att du behöver.
6. Om du vill samla in nivån interaktionen profilering data, Välj den **aktivera nivån interaktion profilering** markerar du kryssrutan.
7. Spara inställningarna genom att välja den **OK** knappen.
   
    När du publicerar det här programmet kan används de här inställningarna för att skapa profilering sessionen för varje roll.

## <a name="viewing-profiling-reports"></a>Visar Profileringsrapporter
En profilering session skapas för varje instans av en roll i din molntjänst. Om du vill visa profilering av varje session från Visual Studio, kan du visa fönstret Server Explorer och välj sedan Azure Compute-nod för att välja en instans av en roll. Du kan sedan visa sestavu profilace enligt följande bild.

![Zobrazit Sestavu Profilace från Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Visa profilering rapporter
1. Om du vill visa fönstret Server Explorer i Visual Studio på menyraden Välj Visa, Server Explorer.
2. Välj Azure Compute-nod och välj sedan noden Azure-distribution för den molntjänst som du har valt att profilen när du har publicerat från Visual Studio.
3. Om du vill visa profilering rapporter för en instans, väljer du vilken roll i tjänsten, öppna snabbmenyn för en specifik instans och välj sedan **visa profilering rapport**.
   
    Rapport, en .vsp-fil hämtas nu från Azure och status för nedladdningen visas i Azure-aktivitetsloggen. När nedladdningen är klar sestavu profilace visas på en flik i Redigeraren för Visual Studio med namnet <Role name> *<Instance Number>* <identifier>.vsp. Sammanfattning av data för rapporten visas.
4. Välj typ av vy som du vill använda för att visa olika vyer i rapporten, i listan över aktuell vy. Mer information finns i [profilering verktyg rapportvyer](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Nästa steg
[Distribuera Cloud Services](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publicera till en Azure-molntjänst från Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

