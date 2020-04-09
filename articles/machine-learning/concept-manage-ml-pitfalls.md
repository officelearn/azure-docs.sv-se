---
title: Undvik övermontering & obalanserade data med AutoML
titleSuffix: Azure Machine Learning
description: Identifiera och hantera vanliga fallgropar med ML-modeller med Azure Machine Learnings automatiserade maskininlärningslösningar.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.reviewer: nibaccam
author: nibaccam
ms.author: nibaccam
ms.date: 04/09/2020
ms.openlocfilehash: 76f920ad6aae68defb567a7a6623d1ffd488af5f
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/08/2020
ms.locfileid: "80874865"
---
# <a name="prevent-overfitting-and-imbalanced-data-with-automated-machine-learning"></a>Förhindra övermontering och obalanserade data med automatiserad maskininlärning

Översittande och obalanserade data är vanliga fallgropar när du bygger maskininlärningsmodeller. Som standard tillhandahåller Azure Machine Learnings automatiserade maskininlärning diagram och mått som hjälper dig att identifiera dessa risker och implementerar metodtips för att minska dem. 

## <a name="identify-over-fitting"></a>Identifiera övermontering

Övermontering i maskininlärning sker när en modell passar träningsdata för bra, och som ett resultat inte kan exakt förutsäga på osynliga testdata. Med andra ord har modellen helt enkelt memorerat specifika mönster och brus i träningsdata, men är inte tillräckligt flexibel för att göra förutsägelser om verkliga data.

Tänk på följande utbildade modeller och deras motsvarande tåg- och testnoggrannhet.

| Modell | Tågets noggrannhet | Testets noggrannhet |
|-------|----------------|---------------|
| A | 99,9 % | 95% |
| B | 87% | 87% |
| C | 99,9 % | 45 % |

Med tanke på modell **A**finns det en vanlig missuppfattning att om testnoggrannheten på osynliga data är lägre än träningsnoggrannheten, är modellen övermonterad. Testnoggrannheten bör dock alltid vara mindre än träningsnoggrannheten, och skillnaden för över-fit kontra lämpligt passform beror på *hur mycket* mindre exakt. 

När man jämför modellerna **A** och **B**är modell **A** en bättre modell eftersom den har högre testnoggrannhet, och även om testnoggrannheten är något lägre vid 95 %, är det inte en signifikant skillnad som tyder på att det finns övermontering. Du skulle inte välja modell **B** bara för att tåget och testnoggrannheterna är närmare varandra.

Modell **C** är ett tydligt fall av övermontering. träningsnoggrannheten är mycket hög men testnoggrannheten är inte i närheten så hög. Denna distinktion är subjektiv, men kommer från kunskap om ditt problem och data, och vilka felstudering är acceptabla.

## <a name="prevent-over-fitting"></a>Förhindra övermontering

I de mest flagranta fall, en övermonterad modell kommer att anta att funktionen värdekombinationer ses under utbildningen kommer alltid att resultera i exakt samma produktion för målet.

Det bästa sättet att förhindra övermontering är att följa ML bästa praxis inklusive:

* Använda mer träningsdata och eliminera statistisk partiskhet
* Förhindra målläckage
* Använda färre funktioner
* **Legalisering och hyperparameteroptimering**
* **Begränsningar för modellkomplexitet**
* **Korsvalidering**

I samband med automatiserad ML är de tre första punkterna ovan **metodtips som du implementerar.** De senaste tre fetstilade objekt är **bästa praxis automatiserade ML genomför** som standard för att skydda mot övermontering. I andra inställningar än automatiserade ML är alla sex bästa metoder värda att följa för att undvika övermontering modeller.

### <a name="best-practices-you-implement"></a>Metodtips du implementerar

Att använda **mer data** är det enklaste och bästa möjliga sättet att förhindra övermontering, och som en extra bonus ökar vanligtvis noggrannheten. När du använder mer data blir det svårare för modellen att memorera exakta mönster, och den tvingas nå lösningar som är mer flexibla för att hantera fler förhållanden. Det är också viktigt att känna igen **statistisk partiskhet**för att säkerställa att dina träningsdata inte innehåller isolerade mönster som inte finns i live-förutsägelsedata. Det här scenariot kan vara svårt att lösa, eftersom det kanske inte finns övermontering mellan tåget och testuppsättningarna, men det kan finnas övermontering i present jämfört med aktiva testdata.

Målläckage är ett liknande problem, där du kanske inte ser övermontering mellan tåg / testuppsättningar, utan snarare visas vid förutsägelse-tid. Målläckage uppstår när din modell "fuskar" under träning genom att ha tillgång till data som den normalt inte borde ha vid förutsägelse-tid. Om ditt problem till exempel är att förutsäga på måndag vilket råvarupris som kommer att vara på fredag, men en av dina funktioner av misstag inkluderade data från torsdagar, skulle det vara data som modellen inte har vid förutsägelsetillfället eftersom den inte kan se in i framtiden. Målläckage är ett enkelt misstag att missa, men kännetecknas ofta av onormalt hög noggrannhet för ditt problem. Om du försöker förutsäga aktiekursen och tränade en modell på 95% noggrannhet, det är sannolikt målläckage någonstans i dina funktioner.

Ta bort funktioner kan också hjälpa till med över-montering genom att förhindra att modellen från att ha för många fält att använda för att memorera specifika mönster, vilket gör att den blir mer flexibel. Det kan vara svårt att mäta kvantitativt, men om du kan ta bort funktioner och behålla samma noggrannhet, har du sannolikt gjort modellen mer flexibel och har minskat risken för övermontering.

### <a name="best-practices-automated-ml-implements"></a>Bästa praxis automatiserade ML genomför

Regularization är processen att minimera en kostnadsfunktion för att straffa komplexa och övermonterade modeller. Det finns olika typer av legaliseringsfunktioner, men i allmänhet bestraffar de alla modellkoefficientstorlek, varians och komplexitet. Automatiserad ML använder L1 (Lasso), L2 (Ridge) och ElasticNet (L1 och L2 samtidigt) i olika kombinationer med olika modellhyperparameterinställningar som styr övermontering. Enkelt uttryckt, automatiserad ML kommer att variera hur mycket en modell regleras och välja det bästa resultatet.

Automatiserad ML implementerar också explicita begränsningar modellkomplexitet för att förhindra övermontering. I de flesta fall är denna implementering specifikt för beslutsträd eller skogsalgoritmer, där enskilda träd maxdjup är begränsat, och det totala antalet träd som används i skog eller ensemble tekniker är begränsade.

Korsvalidering (CV) är processen att ta många delmängder av dina fullständiga träningsdata och träna en modell på varje delmängd. Tanken är att en modell kan få "tur" och har stor noggrannhet med en delmängd, men genom att använda många delmängder modellen inte kommer att uppnå denna höga noggrannhet varje gång. När du gör CV tillhandahåller du en valideringsavstämningsdatauppsättning, anger dina CV-veck (antal delmängder) och automatiserade ML ska träna din modell och justera hyperparametrar för att minimera felet i valideringsuppsättningen. En CV-vik kan vara över-fit, men genom att använda många av dem det minskar sannolikheten för att din slutliga modellen är över-fit. Avvägningen är att CV resulterar i längre utbildningstider och därmed högre kostnad, för istället för att träna en modell en gång, tränar du det en gång för varje *n* CV-delmängder. 

> [!NOTE]
> Korsvalidering är inte aktiverat som standard. Den måste konfigureras i automatiska ML-inställningar. Men när korsvalidering har konfigurerats och en valideringsdatauppsättning har tillhandahållits, är processen automatiserad för dig. Se 

<a name="imbalance"></a>

## <a name="identify-models-with-imbalanced-data"></a>Identifiera modeller med obalanserade data

Obalanserade data finns ofta i data för maskininlärningsklassificeringsscenarier och refererar till data som innehåller ett oproportionerligt förhållande mellan observationer i varje klass. Denna obalans kan leda till en felaktigt upplevd positiv effekt av en modells noggrannhet, eftersom indata har fördomar mot en klass, vilket resulterar i den tränade modellen för att efterlikna denna partiskhet. 

Eftersom klassificeringsalgoritmer ofta utvärderas av noggrannhet är det ett bra sätt att identifiera en modells noggrannhetspoäng om den påverkades av obalanserade data. Hade den verkligen hög noggrannhet eller riktigt låg noggrannhet för vissa klasser?

Dessutom genererar automatiserade ML-körningar följande diagram automatiskt, vilket kan hjälpa dig att förstå riktigheten av klassificeringarna av din modell och identifiera modeller som kan påverkas av obalanserade data.

Diagram| Beskrivning
---|---
[Förvirring Matrix](how-to-understand-automated-ml.md#confusion-matrix)| Utvärderar de korrekt klassificerade etiketterna mot de faktiska etiketterna för uppgifterna. 
[Precision-återkallande](how-to-understand-automated-ml.md#precision-recall-chart)| Utvärderar förhållandet mellan korrekta etiketter och förhållandet mellan hittade etikettinstanser av data 
[ROC-kurvor](how-to-understand-automated-ml.md#roc)| Utvärderar förhållandet mellan korrekta etiketter mot förhållandet mellan falskt positiva etiketter.

## <a name="handle-imbalanced-data"></a>Hantera obalanserade data 

Som en del av sitt mål att förenkla arbetsflödet för maskininlärning har automatiserat ML inbyggda funktioner för att hantera obalanserade data som 

- En **viktkolonn:** automatiserad ML stöder en viktad kolumn som indata, vilket gör att rader i data viktas uppåt eller nedåt, vilket kan göra en klass mer eller mindre "viktig".

- Algoritmerna som används av automatiserade ML kan hantera obalans på upp till 20:1 på rätt sätt, vilket innebär att den vanligaste klassen kan ha 20 gånger fler rader i data än den minst vanliga klassen.

Följande tekniker är ytterligare alternativ för att hantera obalanserade data utanför automatiserad ML. 

- Omsampling till jämn klassobalans, antingen genom upp-provtagning av de mindre klasserna eller ned-provtagning de större klasserna. Dessa metoder kräver expertis för att bearbeta och analysera.

- Använd ett prestandamått som bättre hanterar obalanserade data. F1-poängen är till exempel ett viktat medelvärde av precision och återkallande. Precision mäter en klassificerares exakthet - låg precision indikerar ett stort antal falska positiva identifieringar--, medan återkallande mäter en klassificerares fullständighet - låg återkallelse indikerar ett stort antal falska negativ. 

## <a name="next-steps"></a>Nästa steg

Se exempel och lär dig hur du bygger modeller med automatiserad maskininlärning:

+ Följ [självstudien: Träna automatiskt en regressionsmodell med Azure Machine Learning](tutorial-auto-train-models.md)

+ Konfigurera inställningarna för automatiska träningsexperiment:
  + I Azure Machine Learning studio [använder du dessa steg](how-to-use-automated-ml-for-ml-models.md).
  + Använd dessa [steg](how-to-configure-auto-train.md)med Python SDK .


