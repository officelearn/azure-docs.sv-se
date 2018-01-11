---
title: "Cortana Intelligence tävlingar vanliga frågor och svar | Microsoft Docs"
description: "Vanliga frågor om Microsoft Cortana Intelligence tävlingar."
services: machine-learning
documentationcenter: 
author: hning86
manager: jhubbard
editor: cgronlun
ms.assetid: 9bac5154-a56c-4e78-9d67-34368b9d1624
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/18/2017
ms.author: haining;garye
ms.openlocfilehash: f7c839a8471dc54daebc47d0bb5a450358f5250d
ms.sourcegitcommit: 9a8b9a24d67ba7b779fa34e67d7f2b45c941785e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/08/2018
---
# <a name="microsoft-cortana-intelligence-competitions-faq"></a>Microsoft Cortana Intelligence, tävlingar, vanliga frågor och svar
**Vad är Cortana Intelligence tävlingar?**

Microsoft Cortana Intelligence tävlingar unites en global grupp data entusiaster av gemensamt lösa några av världens mest komplexa datavetenskap problem. Cortana Intelligence tävlingar kan data entusiaster från över hela världen till konkurrera och skapa hög korrekt och intelligent vetenskap datamodeller. Dessa värdbaserade tävlingar baserat på unika datamängder som har gjorts tillgängliga offentligt för första gången. Deltagare kan win utdelningen eller hämta recognition via de översta 10 offentliga resultattavlan. Du kan komma åt webbplatsen tävlingar på [aka.ms/CIComp](http://aka.ms/CIComp).

**Hur ofta Microsoft släpper nya tävlingar?**

Vi kommer att startas part 1, Microsoft ägda tävlingar regelbundet, ungefär var 8-12 veckor. 

**Där kan be allmänna frågor om datavetenskap?**

Du kan använda för allmänna frågor i [forum för Microsoft Azure Machine Learning](https://social.msdn.microsoft.com/forums/azure/home?forum=MachineLearning).

**Hur anger en konkurrensen?**

Åtkomst till den [tävlingar](https://gallery.cortanaintelligence.com/competitions) startsidan i den [Azure AI-galleriet](https://gallery.cortanaintelligence.com/), eller gå till [http://aka.ms/CIComp](http://aka.ms/CIComp). Sidan visas alla tävlingar som körs för närvarande. Varje konkurrens har detaljerade instruktioner och deltagande priser och varaktighet för dess registreringssidan.

1. Hitta konkurrens som du vill delta i, läsa alla instruktioner och titta på videon självstudiekursen. Klicka på den **ange konkurrensen** knappen och kopiera Starter experimentet till din befintliga Azure Machine Learning-arbetsytan. Om du inte redan har åtkomst till en arbetsyta, måste du skapa ett i förväg. Kör experimentet Starter, se prestanda måttet och sedan använda din kreativitet för att förbättra prestanda i modellen. Troligen tillbringar du merparten av din tid i det här steget.   

2. Skapa en Prediktivt Experiment med den tränade modellen utanför experimentet Starter. Sedan noggrant justera indata och utdata schemat för webbtjänsten för att se till att de uppfyller de krav som anges i dokumentationen för konkurrens. Självstudiekurs dokumentet har vanligtvis detaljerade instruktioner för detta. Du kan också titta på videon självstudiekurs om de är tillgängliga.   

3. Distribuera en webbtjänst utanför din Prediktivt Experiment. Testa din web service med hjälp av den **Test** knapp eller Excel-mallen som skapas automatiskt för dig att kontrollera att den fungerar korrekt.   

4. Skicka webbtjänsten som posten konkurrens och se offentliga poängen på sidan Azure AI-galleriet konkurrens. Och fira om du gör den till resultattavlan!  

När du har skickat en post kan du gå tillbaka till experimentet kopierade Starter. Sedan iterera och uppdatera din Prediktivt Experiment, uppdatera webbtjänsten och skicka en ny post.   

**Kan jag använda verktyg med öppen källkod för deltagande i dessa tävlingar?**

Konkurrensen deltagarna använder Azure Machine Learning Studio, en molnbaserad tjänst i Cortana Intelligence Suite för utveckling av vetenskap datamodeller och för att skapa konkurrensen poster för överföring. Machine Learning Studio ger inte bara ett GUI-gränssnitt för att konstruera maskininlärningsexperiment, det ger dig möjlighet att göra egna R eller Python-skript för intern körning. R och Python körningar i Studio har ett stort utbud av öppen källkod R/Python-paket. Du kan importera egna paket som en del av experimentet samt. Studio har också en inbyggd tjänst Jupyter-anteckningsboken att kostnadsfri style datagranskning. Du kan självklart alltid hämta datauppsättningar som används i konkurrensen och utforska i din favorit verktyget utanför Machine Learning Studio. 

**Måste jag vara en data-forskare att ange?**

Nej. I själva verket gärna data entusiaster någon nyfiken datavetenskap och andra blivande datavetare och ange vår tävling. Vi har gjort stöddokument för Tillåt alla att konkurrera. Målgruppen är:

* **Data utvecklare**, **Datavetare**, **BI** och **Analytics proffs**: personer som ansvarar för att framställa data och analyser innehåll för andra ska kunna använda
* **Data förvaltare**: de som har kunskapen om data vad det innebär och hur den har avsedd att användas och i vilket syfte
* **Studenter** & **forskare:** personer inlärning och få data som är relaterade kunskaper via academic program på universitet eller deltagare i massiv öppna onlinekurser (MOOCs)

**Kan jag in med min kollegor som ett team?**

Plattformen konkurrens stöder för närvarande inte team deltagande. Varje post i konkurrensen är kopplad till en enda användaridentitet. 

**Behöver jag betala för att delta i en tävling?**

Tävlingar kan delta i. Men behöver åtkomst till en Azure Machine Learning-arbetsytan delta. Du kan skapa en kostnadsfri arbetsyta utan kreditkort genom att logga in med ett giltigt microsoftkonto eller ett Office 365-konto. Om du redan är en kund med Azure eller Cortana Intelligence Suite kan du skapa och använda en Standard arbetsyta under samma Azure-prenumerationen. Om du vill köpa en Azure-prenumeration går du till den [priser för Azure](https://azure.microsoft.com/pricing) sidan. Observera att standardpriser gäller när du använder en Standard arbetsyta för att konstruera experiment. Mer information finns i [Azure Machine Learning prisinformation](https://azure.microsoft.com/pricing/details/machine-learning/). 

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

**Vad är offentliga och privata poäng?**

I de flesta tävlingar märke till att du får en offentlig poäng för varje överföring som du gör vanligen inom 10 20 minuter. Men efter konkurrensen ut, får du en privat poäng som används för slutlig rangordning. 

Här är vad som händer:

* Hela datamängden som används i konkurrensen delas slumpmässigt med stratifiering i träning och testning (återstående) data. Slumpmässiga delningen stratified för att säkerställa att distributioner av etiketterna i både träning och testning data är konsekventa.
* Utbildning data överförs och fått som en del av experimentet Starter i modulkonfigurationen importera Data.
* Tester data delas upp ytterligare i offentliga och privata testnings data med samma stratifieringen.
* Offentliga tester data används för inledande runda av bedömningen. Resultatet kallas den offentliga poängen. Det är vad som visas i historiken skickas när du skickar din inmatning. Det här resultatet beräknas för varje post som du skickar. Den här offentliga poäng används för att du är på den offentliga resultattavlan.
* Privata testnings data används för den slutliga hundratal bedömningen när konkurrensen upphör. Detta kallas för privata poäng. 
* För varje deltagare markeras automatiskt ett fast antal poster med högsta offentliga resultat att ange den privata bedömningsprofil round (det här antalet kan variera beroende på konkurrensen). Posten med den högsta privata poängen väljs sedan att ange den slutliga rangordning som slutligen avgör vinnarna pris.  

**Kunder kan vara värd en konkurrensen på vår plattform?**

Vi välkomnar 3 parts organisationer för att samarbeta med oss och vara värd för både offentliga och privata tävlingar på vår plattform. Vi har en konkurrens onboarding-teamet som glada över att diskutera onboarding-processen för sådana tävlingar.  Ta dig kontaktar du oss på [ compsupport@microsoft.com ](mailto:compsupport@microsoft.com) för mer information. 

**Vad är begränsningarna för bidrag?**

En typisk konkurrensen kan välja att begränsa antalet poster som du kan skicka i ett 24-timmars intervall (UTC-tid 00:00:00 till 23:59:59) och det totala antalet poster som du kan skicka under loppet av konkurrens. Du får felmeddelanden när en begränsning har överskridits. 

**Vad händer om jag Vinn en konkurrensen?**

Microsoft verifierar resultaten av privata resultattavlan och sedan vi ska kontakta dig. Kontrollera att din e-postadress i användarprofilen är uppdaterad.

**Hur kommer jag pris pengar om jag Vinn en konkurrensen?**

Om du är en konkurrensen vinnaren behöver du registrera en deklaration av behörighet, licenser och versionen från. Det här formuläret reiterates konkurrensregler. Vinnarna måste du fylla i ett formulär för oss skatt W-9 eller formuläret W-8BEN om de inte USA skattebetalarna. Vi kommer att kontakta alla vinnarna som en del av utdelningen utbetalningar processen genom att använda sin e-post för registrering. Se vår [villkor](http://aka.ms/comptermsandconditions) för ytterligare information.

**Vad händer om mer än en post får samma poäng?**

Skicka-tid är prioritet. Transaktionen har skickats tidigare outranks posten senare.

**Kan jag delta med gäst arbetsytan?**

Nej. Du måste använda en gratis eller en Standard arbetsyta delta. Du kan öppna konkurrensen starter experiment i ett gäst-arbetsyta, men du kommer inte att kunna skapa ett giltigt värde för överföring från arbetsytan. 

**Kan jag delta med en arbetsyta i Azure-region?**

Stöder för närvarande, plattformen konkurrensen bara poster som skickats från en arbetsyta i den **södra centrala USA** Azure-region. Alla kostnadsfria arbetsytor finns i södra centrala USA, så kan du skicka en post från en fri arbetsyta. Om du väljer att använda en Standard arbetsyta, kontrollerar du att den finns i södra centrala oss Azure-region, annars din ansökan inte lyckas. 

**Håller vi användarnas tävlingar lösningar och poster?**

Användarposter bevaras enbart i utvärderingssyfte att identifiera de prisbelönta lösningarna. Se vår [villkor](http://aka.ms/comptermsandconditions) för specifik information.

