---
title: Jakt funktioner med hjälp av bärbara datorer i Azure-Sentinel-förhandsversionen | Microsoft Docs
description: Den här artikeln beskriver hur du använder anteckningsböcker med funktioner för Azure Sentinel jakt.
services: sentinel
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 1721d0da-c91e-4c96-82de-5c7458df566b
ms.service: sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2019
ms.author: rkarlin
ms.openlocfilehash: ae9d52e4a26825e4318a6afb8aadc86ac29fa2b3
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60403112"
---
# <a name="use-jupyter-notebooks-to-hunt-for-security-threats"></a>Använd Jupyter-anteckningsböcker för jakt efter säkerhetshot

> [!IMPORTANT]
> Azure Sentinel är för närvarande i offentlig förhandsversion.
> Den här förhandsversionen tillhandahålls utan serviceavtal och rekommenderas inte för produktionsarbetsbelastningar. Vissa funktioner kanske inte stöds eller kan vara begränsade.
> Mer information finns i [Kompletterande villkor för användning av Microsoft Azure-förhandsversioner](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Grunden för Azure Sentinel är datalager; den kombinerar frågor till dynamiskt schema och skalas till stora datavolymer med höga prestanda. Sentinel-Azure-portalen och alla Sentinel-Azure-verktyg kan du använda ett gemensamt API för att komma åt det här datalagret. Samma API: et är också tillgängligt för externa verktyg som [Jupyter](https://jupyter.org/) anteckningsböcker och Python. Även om många vanliga uppgifter kan utföras i portalen, utvidgar Jupyter omfånget för vad du kan göra med dessa data. Den kombinerar fullständig programmering med en stor samling bibliotek för machine learning, visualisering och analys. Dessa attribut Se Jupyter ett sådant fascinerande verktyg för säkerhetsundersökning och jakt.

![Exempel-anteckningsbok](./media/notebooks/sentinel-nb-mapandtimeline.png)

Vi har integrerat Jupyter-upplevelsen till Sentinel-Azure-portalen, vilket gör det enkelt för dig att skapa och köra anteckningsböcker för att analysera dina data. Den *Kqlmagic* biblioteket innehåller den sammanlänkande som låter dig ta frågor från Azure Sentinel och kör dem direkt i en anteckningsbok. Frågar används den [Kusto-frågespråket](https://kusto.azurewebsites.net/docs/query/index.html). Flera anteckningsböcker, har utvecklats av några av Microsofts säkerhetsanalytiker, paketeras med Sentinel-Azure. Vissa av dessa anteckningsböcker skapas för ett specifikt scenario och kan användas som – är. Andra är avsedda som exempel för att illustrera tekniker och funktioner som du kan kopiera eller anpassa för användning i dina egna anteckningsböcker. Andra anteckningsböcker får också importeras från Sentinel-Azure-communityn GitHub.

Den integrerade upplevelsen som Jupyter använder [Azure anteckningsböcker](https://notebooks.azure.com/) för att lagra, dela och köra anteckningsböcker. Du kan också köra dessa anteckningsböcker lokalt (om du har en Python-miljön och Jupyter på datorn) eller i andra JupterHub miljöer, till exempel Azure Databricks.

Bärbara datorer har två komponenter:

- Det webbläsarbaserade gränssnittet där du anger och kör frågor och kod, och där visas resultatet av körningen.
- en *kernel* som är ansvarig för parsning och körning av själva koden berörs. 

I Azure-anteckningsböcker, den här kernel som körs på Azure *kostnadsfria Cloud Compute och Storage* som standard. Om dina anteckningsböcker inkluderar komplexa machine learning-modeller eller visualiseringar bör du använda mer kraftfulla, dedikerad beräkningsresurser som [virtuella datorer för datavetenskap](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/) (DSVM). Bärbara datorer i ditt konto förblir privata och om du inte väljer att dela dem.

Sentinel-Azure-datorer använder många populära Python-bibliotek som pandas, matplotlib, bokeh och andra. Det finns ett stort antal andra Python-paket som du kan välja mellan, som täcker områden som:

- visualiseringar och grafik
- bearbetning och analys
- statistik och numeriska databehandling
- Machine learning och djupinlärning

Vi har även lanserat vissa öppen källkod Jupyter säkerhetsverktyg i ett paket med namnet [msticpy](https://github.com/Microsoft/msticpy/). Det här paketet används i många av de inkluderade anteckningsböckerna. Msticpy verktyg har utformats speciellt för att hjälpa med att skapa anteckningsböcker för jakt och undersökning och vi arbetar aktivt med nya funktioner och förbättringar.

Inledande anteckningsböcker är:

- **Interaktiv undersökning - processen aviseringar**: Gör att du kan snabbt testa aviseringar genom att analysera aktiviteten på den berörda värdarna.
- **Guidad jakt - Windows-värd explorer**: Låter dig utforska kontoaktivitet, process körningar, nätverksaktivitet och andra händelser på en värd.  
- **Guidad jakt - Office365-utforska**: Hunt för misstänkt aktivitet i Office 365 i flera datauppsättningar för O365.

Den [Azure Sentinel-Community GitHub-lagringsplatsen](https://github.com/Azure/Azure-Sentinel) är platsen för alla framtida Sentinel-Azure-anteckningsböcker som skapats av Microsoft eller som tillförts från diskussionsgruppen.

## <a name="run-a-notebook"></a>Kör en anteckningsbok

I följande exempel skapa vi ett Azure-anteckningsböcker projekt från Sentinel-Azure-portalen, fylla i projektet med anteckningsböcker. Innan du använder dessa anteckningsböcker, är det en bra idé att göra en kopia av anteckningsboken och arbeta med kopian. Arbeta med kopior kan du uppdatera på ett säkert sätt för framtida versioner av bärbara datorer utan att skriva över någon av dina data.

1. Sentinel-Azure-portalen klickar du på **anteckningsböcker** på navigeringsmenyn. Om du vill skapa ett nytt projekt i Azure-datorer, klickar du på **klona Azure Sentinel-anteckningsböcker** eller att öppna din befintliga anteckningsböcker projekt klickar du på **går du till dina anteckningsböcker**.
  
   ![Välj anteckningsböcker](./media/notebooks/sentinel-az-notebooks-home.png)

2. Om du har valt **klona Azure Sentinel-anteckningsböcker** följande dialogruta visas i föregående steg. Klicka på **Import** att klona GitHub-lagringsplatsen till ditt projekt i Azure-anteckningsböcker. Om du inte har ett befintligt konto för Azure-anteckningsböcker, uppmanas du att skapa en och logga in.

   ![Import-anteckningsbok](./media/notebooks/sentinel-nb-signin-and-clone.png)

3. När du skapar ett nytt projekt kan behöva du ge projektet namnet – Använd standardnamnet eller typ i en ny. Kontrollera inte den **klona rekursivt** alternativet – det här alternativet avser länkade GitHub-lagringsplatser. När du klickar på **Import** börjar klona GitHub-innehåll, vilket kan ta några minuter att slutföra.

   ![Import-anteckningsbok](./media/notebooks/sentinel-create-nb-project.png)

4. Öppna den **anteckningsböcker** mappen för att se de bärbara datorerna. Varje notebook vägleder dig genom stegen för att utföra en hunt eller undersökning. Bibliotek och andra beroenden som krävs av anteckningsboken kan installeras från anteckningsboken själva eller via en enkel konfiguration procedur. Konfiguration som kopplar samman projektet notebook tillbaka till din Sentinel-Azure-prenumeration etableras automatiskt i föregående steg. Dina anteckningsböcker är redo att köra mot Azure Sentinel-Log Analytics-arbetsytan.

   ![Importera lagringsplats](./media/notebooks/sentinel-open-notebook1.png)

5. Öppna en anteckningsbok. Kostnadsfri beräkning väljs som standard för att köra anteckningsböcker (markerat). Om du har konfigurerat en DSVM om du vill använda (se ovan), Välj DSVM och autentisera innan du öppnar första anteckningsboken. Klicka på en bärbar dator att öppna den.

   ![Välj anteckningsbok](./media/notebooks/sentinel-open-notebook2.png)

6. Om du väljer Python-version. När du först öppnar en bärbar dator, uppmanas så du att välja en kernel-version. Om du inte väljer kerneln för att använda på följande sätt. Python 3.6 eller senare ska vara markerade kernel (uppe till höger i fönstret notebook).

   ![Välj anteckningsbok](./media/notebooks/sentinel-select-kernel.png)

För en snabb introduktion till frågor till data i Azure Sentinel tittar du på den [GetStarted](https://github.com/Azure/Azure-Sentinel/blob/master/Notebooks/Get%20Started.ipynb) anteckningsboken i mappen huvudsakliga anteckningsböcker. Ytterligare exempelanteckningsböcker finns i den **Exempelanteckningsböcker** undermappen. Exempelanteckningsböcker som har sparats med data, så att det blir lättare att se avsedda utdata (Vi rekommenderar att visa dem i [nbviewer](https://nbviewer.jupyter.org/)). Den **HowTos** mappen innehåller anteckningsböcker som beskriver, till exempel: inställningen standard Python-version, konfigurera en DSVM kan skapa Azure Sentinel bokmärken från en bärbar dator och andra ämnen.

Dessa datorer är avsedda som båda användbara verktyg och som bilder och kodexempel som du kan använda i utvecklingen av egna anteckningsböcker.

Vi tar gärna emot feedback, om förslag, begäranden om funktioner, som tillförts anteckningsböcker, buggrapporter eller förbättringar och tillägg till befintliga anteckningsböcker. Gå till den [Azure Sentinel-Community GitHub](https://github.com/Azure/Azure-Sentinel) att skapa ett problem eller en förgrening och ladda upp ett bidrag.

## <a name="next-steps"></a>Nästa steg

I den här artikeln lärde du dig att komma igång med Jupyter-anteckningsböcker i Azure Sentinel. Mer information om Azure Sentinel finns i följande artiklar:

- [Hitta proaktivt hot](hunting.md)
- [Använda bokmärken för att spara intressant information vid jakt](bookmarks.md)