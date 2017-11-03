---
title: Struktur projekt med mallen Team datavetenskap Process | Microsoft Docs
description: "Så här instansiera Team Data vetenskap processen (TDSP) mallar i Azure Machine Learning struktur projekt för samarbete"
services: machine-learning
documentationcenter: 
author: bradsev
manager: cgronlun
editor: cgronlun
ms.assetid: 
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/16/2017
ms.author: bradsev
ms.openlocfilehash: efb681b85d3d7434e3114b8576abc64d00891f03
ms.sourcegitcommit: 9ae92168678610f97ed466206063ec658261b195
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/17/2017
---
# <a name="structure-projects-with-the-team-data-science-process-template"></a>Strukturen projekt med mallen Team datavetenskap Process

Det här dokumentet innehåller instruktioner om hur du skapar datavetenskap projekt i Azure Machine Learning med Team Data vetenskap processen (TDSP) mallar. Dessa mallar att strukturen projekt för samarbete och reproducerbara. 


## <a name="what-is-the-team-data-science-process"></a>Vad är Team av vetenskapliga data?
TDSP är en flexibel och iterativ, datavetenskap process för att köra och leverera lösningar för avancerad analys. Den har utformats för att förbättra samarbete och effektiviteten för datavetenskap team i företag. Den stöder dessa mål med fyra viktiga komponenter:

   * En standard [datavetenskap livscykel](../team-data-science-process/lifecycle.md) definition.
   * En standardiserad projektstruktur [dokumentation och rapportering mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate).
   * En infrastruktur och resurser för projektet körning, till exempel, en beräkning och lagring infrastruktur och kod databaser.
   * [Verktyg och hjälpmedel](https://github.com/Azure/Azure-TDSP-Utilities) för datavetenskap projektet uppgifter, exempelvis:
      - Samarbetsfunktioner versionskontroll
      - Granska koden
      - Datagranskning och modellering
      - Planera arbete

En fullständig beskrivning av TDSP finns i [Team datavetenskap processöversikt](../team-data-science-process/overview.md).

## <a name="why-should-you-use-the-tdsp-structure-and-templates"></a>Varför ska du använda TDSP struktur och mallar?
Standardisering av strukturen, livscykel och dokumentation av datavetenskap projekt är nyckeln till att underlätta effektivt samarbete på datavetenskap team. Skapa machine learning-projekt med mallen TDSP för att ange sådana ett ramverk för samordnade samarbete.

Tidigare släppte vi en [GitHub-lagringsplatsen för TDSP projektstruktur och mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för att uppnå dessa mål. Men det var inte möjligt, fram till nu att instansiera TDSP struktur och mallar i ett datavetenskap verktyg. Det går nu att skapa machine learning-projekt som instantierar TDSP struktur och dokumentation mallar. 

## <a name="things-to-note-before-creating-a-new-project"></a>Saker att Observera innan du skapar ett nytt projekt
Granska följande *innan* du skapa ett nytt projekt:
* Granska Maskininlärning TDSP [mallen](https://aka.ms/tdspamlgithubrepo).
* Innehållet (annat än det som redan finns i dokumentmappen ””) måste vara mindre än 25 MB i storlek. Se anmärkningen efter den här listan.
* Exemplet\_datamappen är endast avsedd för små filer (mindre än 5 MB) som du kan använda för att testa din kod eller starta tidig utveckling.
* Lagring av filer, till exempel Word och PowerPoint-filer, kan storleken på dokumentmappen ”” öka avsevärt. Vi starkt att du kan hitta en gemensam Wiki [SharePoint](https://products.office.com/en-us/sharepoint/collaboration), eller annan samarbetsfunktioner resurs för att lagra dessa filer.
* Om du vill lära dig mer om att hantera stora filer och utdata i Machine Learning, läsa [beständighet ändringar samt hantera stora filer](http://aka.ms/aml-largefiles).

> [!NOTE]
> Alla relaterade dokumentationen innehåll (text, markdowns, bilder och andra dokument-filer) som är *inte* används under körning av projektet, andra än readme.md-filen måste finnas i mappen med namnet ”dokument” (gemener). Dokumentmappen ”” är en särskild mapp ignoreras av Machine Learning körningen så att inte hämta kopieras innehållet i den här mappen för att beräkna mål i onödan. Objekt i den här mappen räknas inte mot 25 MB fästpunkten för projektet. Dokumentmappen ”” är till exempel för att lagra stora bildfiler som behövs i dokumentationen. Dessa filer är fortfarande spåras av Git via körningshistoriken. 

## <a name="instantiate-the-tdsp-structure-and-templates-from-the-machine-learning-template-gallery"></a>Skapa en instans av TDSP struktur och mallar i Machine Learning mallgalleriet
Slutför följande procedurer för att skapa ett nytt projekt med TDSP struktur och dokumentation mallar.

### <a name="create-a-new-project"></a>Skapa ett nytt projekt
Om du vill skapa ett nytt projekt, öppna Azure Machine Learning. Under **projekt** i det övre vänstra fönstret, klicka på plustecknet (**+**), och välj sedan **nytt projekt**.

![Nytt projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-1.png)


### <a name="create-a-new-tdsp-structured-project"></a>Skapa ett nytt projekt TDSP strukturerad
   1. Ange parametrar och information i motsvarande ruta eller en lista:

      - Projektets namn
      - Projektkatalogen
      - Projektbeskrivning av
      - En tom sökväg för Git-lagringsplats
      - Arbetsytans namn

   2. I den **Sök** ange **TDSP**. 
   3. När den **struktur för ett projekt med TDSP** alternativ visas, väljer du mallen. 
   4. Välj den **skapa** för att skapa det nya projektet med en TDSP struktur. Om du anger en tom Git-lagringsplats när du skapar projekt (i motsvarande textruta) ska databasen fylla med projektstruktur och innehåll efter att projektet har skapats.

![Skapa ett TDSP-projekt](./media/how-to-use-tdsp-in-azure-ml/instantiation-2.png)


## <a name="examine-the-tdsp-project-structure"></a>Granska TDSP projektstruktur
När det nya projektet har skapats kan du undersöka dess struktur (se den vänstra panelen i följande bild). Den innehåller alla aspekter av standardiserade dokumentationen för företag att förstå. Dessa objekt omfattar steg i livscykeln TDSP, plats, definitioner och arkitekturen för den här dokumentationen mallen. 

Strukturen är härledd från TDSP-struktur som publiceras i [TDSP struktur, dokument och artefakt projektmallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate), med vissa ändringar. Till exempel flera dokumentmallar slås samman i en markdown, nämligen [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport). 

### <a name="project-folder-structure"></a>Mappstruktur för projektet
Projektmallen TDSP innehåller följande översta mappar:
   - **koden**: innehåller koden.
   - **dokumenten**: innehåller dokumentation om projektet (till exempel markdown-filer och relaterade media).
   - **sample_data**: innehåller **exempel (liten)** data som du kan använda för tidig utveckling och testning. Normalt dessa uppsättningar inte är större än flera (5) MB. Denna mapp är inte för fullständig eller stora datamängder.

![Exempeldata](./media/how-to-use-tdsp-in-azure-ml/instantiation-3.png)


## <a name="use-the-tdsp-structure-and-templates"></a>Använd TDSP struktur och mallar
Du måste lägga till information om projektet struktur och mallar. Du är förväntade att fylla i dem med koden och informationen som behövs för att köra och tillhandahålla ditt projekt. Den [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) filen är en mall som du behöver ändra med information som är relevanta för ditt projekt. Den innehåller en uppsättning frågor som hjälper dig ange information för var och en av de fyra stegen i den [TDSP livscykel](../team-data-science-process/lifecycle.md).

Ett exempel på ett projektstrukturen ser ut under körningen eller efter slutförande visas i den vänstra panelen i följande bild. Det här projektet är från den [Team datavetenskap Process exempelprojektet: klassificera inkomster från USA inventering data i Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) exempelprojektet.

![Exempel projektstruktur](./media/how-to-use-tdsp-in-azure-ml/instantiation-4.png)

## <a name="document-your-project"></a>Dokumentera ditt projekt
Referera till den [TDSP dokumentationen mallar](https://github.com/Azure/Azure-TDSP-ProjectTemplate) för information om hur du dokumentera dina projekt. I den aktuella Machine Learning TDSP dokumentationen mallen, rekommenderar vi att du inkluderar all information i den [ProjectReport](https://aka.ms/tdspamlgithubrepoprojectreport) fil. Den här mallen ska fyllas i med information som är specifik för ditt projekt. 

Vi tillhandahåller även en [ProjectLearnings](https://aka.ms/tdspamlgithubrepoprojectlearnings) mall. Du kan använda den här mallen till innehåller all information som inte ingår i det primära projektdokumentet, men som är användbar till dokumentet. 

### <a name="example-project-report"></a>Projektet exempelrapport
Du kan hämta ett [projekt exempelrapport](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome/blob/master/docs/deliverable_docs/ProjectReport.md). Projektet rapporten för den [USA intäkter klassificering exempelprojektet](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome) visar hur du skapa en instans av och använda mallen TDSP för ett projekt för vetenskapliga data.

## <a name="next-steps"></a>Nästa steg
För att underlätta din förståelse för hur du använder TDSP struktur och mallar i Machine Learning-projekt, tillhandahåller vi flera slutförda projekt exemplen i dokumentationen för Machine Learning:

- Ett exempel som visar hur du skapar ett TDSP projekt i Machine Learning finns [Team datavetenskap Process exempelprojektet: klassificera inkomster från oss inventering data i Azure Machine Learning](https://github.com/Azure/MachineLearningSamples-TDSPUCIAdultIncome).
- Ett exempel som använder djup learning i naturligt språk bearbetas (NLP) i ett TDSP instansierad projekt i Machine Learning finns [biomedicin entitet bokföring med hjälp av naturligt språk bearbetning med djup learning](https://github.com/Azure/MachineLearningSamples-BiomedicalEntityExtraction).

