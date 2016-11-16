---
title: Azure Service Bus | Microsoft Docs
description: "En introduktion till att använda Service Bus för att ansluta Azure-appar till andra program."
services: service-bus
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: 
ms.assetid: 12654cdd-82ab-4b95-b56f-08a5a8bbc6f9
ms.service: service-bus
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/31/2016
ms.author: sethm
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: c8d8549db680b0189fa94064b930d4f91ff2472b


---
# <a name="azure-service-bus"></a>Azure Service Bus
Oavsett om en app eller tjänst körs i molnet eller lokalt, måste den ofta samverka med andra program eller tjänster. Microsoft Azure tillhandahåller Service Bus för att du ska kunna göra detta på ett fritt och praktiskt sätt. I den här artikeln tar vi en titt på denna teknik och beskriver vad den är och varför du kanske vill använda den.

## <a name="service-bus-fundamentals"></a>Grunderna i Service Bus
Olika situationer kräver olika kommunikationsstilar. Ibland är den bästa lösningen att låta appar skicka och ta emot meddelanden via en enkel kö. I vissa situationer räcker det inte med en vanlig kö, utan det är bättre att använda en kö med en mekanism av typen publicera och prenumerera. I andra fall är allt som behövs en anslutning mellan apparna. Köer är överflödiga. Service Bus gör att du kan använda alla tre alternativ och ser till att dina appar kan interagera på flera olika sätt.

Service Bus är en molntjänst med flera klienter, vilket innebär att tjänsten delas av flera användare. Varje användare, till exempel en programutvecklare, skapar ett *namnområde*, och sedan definierar utvecklaren de kommunikationsmekanismer som han eller hon vill använda inom det här namnområdet. Bild 1 visar hur detta ser ut.

![][1]

**Bild 1: Service Bus erbjuder en tjänst för flera innehavare för att ansluta program via molnet.**

Du kan använda en eller flera instanser av fyra olika kommunikationsmekanismer inom namnområdet. Dessa ansluter till appar på olika sätt. Alternativen är:

* *Köer*, som tillåter en enkelriktad kommunikation. Varje kö fungerar som en mellanhand (kallas ibland för en *koordinator*) som sparar skickade meddelanden tills de tas emot. Varje meddelande tas emot av en enda mottagare.
* *Ämnen*, som ger enkelriktad kommunikation med hjälp av *prenumerationer*. Ett ämne kan ha flera prenumerationer. Precis som med köerna fungerar ämnena som en koordinator (broker), men varje prenumeration kan sedan använda ett filter för att endast ta emot meddelanden som uppfyller specifika villkor.
* *Reläer* (relays), som ger dubbelriktad kommunikation. Till skillnad från köer och ämnen lagrar inte ett relä meddelanden som redan startats. Det är alltså inte en koordinator. I stället skickar reläet bara meddelandena vidare till målprogrammet.

När du skapar en kö, ett ämne eller ett relä ger du dem ett namn. Det här namnet, tillsammans med det namn som du gav namnområdet, skapar en unik identifierare för objektet. Appen kan överföra detta namn till Service Bus och sedan använda den kön, det ämnet eller det reläet i sin kommunikation med varandra. 

Windows-program kan använda Windows Communication Foundation (WCF) för att använda något av dessa objekt vid vidarebefordran. För köer och ämnen kan Windows-program använda API:er för Service Bus-definierade meddelandefunktioner. Om du vill att det ska vara enklare att använda objekten från andra program som inte är från Windows, tillhandahåller Microsoft SDK:er för Java, Node.js och andra programmeringsspråk. Du kan även få åtkomst till köer och ämnen med hjälp av REST-API:er via HTTP. 

Det är viktigt att förstå att även om själva Service Bus körs i molnet (det vill säga i datacenter för Microsoft Azure) så kan apparna som använder den köras var som helst. Du kan använda Service Bus för att ansluta appar som körs på Azure, till exempel, eller program som körs i ditt eget datacenter. Du kan också använda Service Bus för att ansluta en app som körs på Azure eller på en annan molnplattform med ett lokalt program, eller med surfplattor och smartmobiler. Det går till och med att ansluta hushållsapparater, sensorer och andra enheter till ett centralt program eller till varandra. Service Bus är en kommunikationsmekanism i molnet och den är tillgänglig från nästan var som helst. Hur du använder den beror på vad dina appar behöver göra.

## <a name="queues"></a>Köer
Anta att du bestämmer dig för att ansluta två appar med hjälp av en Service Bus-kö. Bild 2 visar den här situationen.

![][2]

**Bild 2: Service Bus-köer erbjuder enkelriktade asynkrona köer.**

Processen är enkel: En avsändare skickar ett meddelande till en Service Bus-kö och en mottagare hämtar meddelandet vid ett senare tillfälle. En kö kan ha bara en enkel mottagare, som bild 2 visar. Eller så kan flera program läsa från samma kö. I det senare fallet läses varje meddelande av enbart en mottagare. För en tjänst med multi-cast bör du istället använda ett ämne.

Varje meddelande består av två delar: en uppsättning av egenskaper, som alla är ett nyckel-/värdepar, och en binär meddelandetext. Hur de används beror på vad en app försöker utföra. Ett exempel: Ett program som skickar ett meddelande om en nyligen genomförd försäljning kan innehålla egenskaperna *Säljare="Ava"* och *Belopp= 10000*. Meddelandetexten (brödtexten) kan innehålla en skannad bild av försäljningsavtalet, eller om det inte finns något sådant, bara vara tomt.

En mottagare kan läsa ett meddelande från en Service Bus-kö på två olika sätt. Det första alternativet, som kallas *ReceiveAndDelete*, tar bort meddelandet från kön och raderar det direkt. Detta är ett enkelt alternativ men om mottagaren kraschar innan den har slutfört behandlingen av meddelandet så kommer det att gå förlorat. Eftersom det har tagits bort från kön kan inga andra mottagare komma åt det. 

Det andra alternativet, *PeekLock*, är utformat för att lösa detta problem. Precis som **ReceiveAndDelete**, tar **PeekLock** bort meddelandet från kön. Men det raderar inte meddelandet. I stället låser det meddelandet, vilket gör det osynligt för andra mottagare, och sedan inväntar alternativet en av följande tre händelser:

* Om mottagaren bearbetar meddelandet på ett framgångsrikt sätt, anropar det **Slutför** och kön raderar meddelandet. 
* Om mottagaren beslutar att den inte kan bearbeta meddelandet på rätt sätt, anropar det **Avbryt**. Kön tar bort spärren från meddelandet och gör det tillgängligt för andra mottagare.
* Om mottagaren inte anropar något av dessa alternativ inom en konfigurerbar tidsperiod (standardvärdet är 60 sekunder), förutsätter kön att mottagaren har misslyckats. I det här fallet reagerar den som om mottagaren hade anropat **Avbryt** och gör meddelandet tillgängligt för andra mottagare.

Observera vad som kan inträffa här: samma meddelande kan levereras två gånger, kanske till två olika mottagare. Appar som använder Service Bus-köer måste förberedas för detta. För att förenkla identifiering av dubbletter har varje meddelande en unik **MessageID**-egenskap. Denna är som standard alltid densamma, oavsett hur många gånger meddelandet har lästs från en kö. 

Köer är användbara i ett ganska stort antal situationer. De gör att appar kan kommunicera även när båda inte körs på samma gång, något som är särskilt praktiskt med batch- och mobilappar. En kö med flera mottagare ger också automatisk belastningsbalansering eftersom skickade meddelanden sprids ut bland dessa mottagare.

## <a name="topics"></a>Ämnen
Även om köer ofta är väldigt användbara, är de inte alltid den rätta lösningen. Ibland är det bättre att använda Service Bus-ämnen. Bild 3 ger dig en uppfattning om varför det är så.

![][3]

**Bild 3: Baserat på de filter ett prenumererande program specificerar så kan det ta emot en del eller alla meddelanden som skickats till ett Service Bus-ämne.**

Ett *ämne* liknar på många sätt en kö. Avsändare skickar meddelanden till ett ämne på samma sätt som de skickar meddelanden till en kö. Och dessa meddelanden ser likadana ut som när de används i köer. Den stora skillnaden är att ämnen gör att varje mottagande app kan skapa sin egen *prenumeration* genom att definiera ett *filter*. Prenumeranten kommer därefter endast att se de meddelanden som matchar filtret. Bild 3 visar till exempel en avsändare och ett ämne med tre prenumeranter, var och en med sitt eget filter:

* Prenumerant 1 får endast meddelanden som innehåller egenskapen *Säljare="Ava"*.
* Prenumerant 2 tar emot meddelanden som innehåller egenskapen *Säljare="Ruth"* och/eller innehåller en egenskap för *belopp* vars värde är större än 100 000. Ruth kanske är försäljningschef så hon vill se både sina egna försäljningar och alla stora försäljningar, oavsett vem som gör dem.
* Prenumerant 3 har ställt in sitt filter på *Sant*, vilket innebär att han eller hon får alla meddelanden. Den här appen kan till exempel ansvara för att bibehålla ett revisionsspår och därför behöver den visa alla meddelanden.

På samma sätt som med köer kan de som prenumererar på ett ämne läsa meddelanden med antingen **ReceiveAndDelete** eller **PeekLock**. Men till skillnad från köer kan ett enskilt meddelande som skickas till ett ämne tas emot av flera prenumerationer. Den här metoden, som ofta kallas *publicera och prenumerera*, (eller *pub/sub*) är användbar när flera appar är intresserade av samma meddelanden. Genom att definiera rätt typ av filter kan varje prenumerant ta del av enbart den del av meddelandeströmmen som de behöver se.

## <a name="relays"></a>Reläer
Både köer och ämnen ger dig enkelriktad, asynkron kommunikation via en koordinator. Trafiken flödar endast i en riktning och det finns ingen direkt anslutning mellan avsändarna och mottagarna. Men vad händer om du inte vill använda denna lösning? Anta att dina appar både måste kunna skicka och ta emot meddelanden. Eller så kanske du vill ha en direktlänk mellan dem och du har inget behov av en koordinator för att lagra meddelanden. För att kunna hantera scenarier som detta tillhandahåller Service Bus *reläer* (relays), som visas på bild 4.

![][4]

**Bild 4: Service Bus Relay erbjuder synkron, dubbelriktad kommunikation mellan program.**

Den naturliga frågan när det gäller reläer är: varför bör jag använda ett relä? Även om jag inte behöver köer, varför göra så att appar kommunicerar via en molnbaserad tjänst i stället för att helt enkelt interagera direkt med varandra? Svaret är att det här med att prata direkt med varandra kan vara svårare än du tror.

Anta att du vill ansluta två lokala appar. Båda körs på företagets eget datacenter. Var och en av dessa appar ligger bakom en brandvägg och varje datacenter använder antagligen nätadressöversättning (NAT). Brandväggen blockerar inkommande data på i princip alla portar och NAT-användningen antyder att datorerna där apparna körs inte har en fast IP-adress som gör att du kan nå dem direkt från en plats utanför datacentret. Utan extra hjälp är det problematiskt att ansluta dessa appar till varandra med hjälp av en offentlig internetanslutning.

Det kan hjälpa att använda ett Service Bus-relä. För att kommunicera i båda riktningarna via ett relä, upprättar varje app en utgående TCP-anslutning med Service Bus och håller den sedan öppen. All kommunikation mellan de två apparna överförs via dessa anslutningar. Eftersom varje anslutning har upprättats inifrån datacentret tillåter brandväggen inkommande trafik till varje app utan att öppna nya portar. Med hjälp av den här metoden kringgår du också problemet med NAT eftersom varje app har en konsistent slutpunkt i molnet under hela kommunikationen. Genom att utföra utbyte av data via reläet kan apparna undvika problem som annars skulle ha försvårat kommunikationen. 

För att kunna använda Service Bus Relays stöder sig apparna på Windows Communication Foundation (WCF). Service Bus ger dig WCF-bindningar som gör det enkelt för Windows-program att interagera via reläer. Program som redan använder WCF behöver normalt bara ange en av dessa bindningar och sedan kan de kommunicera med varandra via ett relä. Men till skillnad från köer och ämnen kräver användningen av reläer från andra program än Windows-program en hel del programmeringsarbete, även om det går att genomföra. Det medföljer inga standardbibliotek.

Till skillnad från köer och ämnen skapar apparna inga reläer rent uttryckligen. När en app som du vill ta emot meddelanden med upprättar en TCP-anslutning med Service Bus, skapas istället ett relä automatiskt. När anslutningen avbryts tas reläet bort. Om du vill aktivera en app för att hitta det relä som skapats av en viss lyssnare, innehåller Service Bus ett register som gör det möjligt för appar att söka efter ett visst relä. Sökningen görs efter namn.

Reläer är den rätta lösningen när du behöver upprätta direkt kommunikation mellan program. Föreställ dig till exempel ett bokningssystem från ett flygbolag som körs i ett lokalt datacenter. Detta måste kunna nås från incheckningskiosker, mobila enheter och andra datorer. Program som körs på alla dessa system kan förlita sig på Service Bus Relays i molnet för att kommunicera med varandra, oavsett var de körs.

## <a name="summary"></a>Sammanfattning
Att ansluta program har alltid varit en del av arbetet när man skapar kompletta lösningar. Mängden scenarier som dyker upp och kräver att program och tjänster ska kunna kommunicera med varandra kommer med all säkerhet att öka eftersom fler och fler program och enheter ansluts till Internet. Syftet med Service Bus är att göra denna absolut nödvändiga funktion enklare att implementera och se till att tekniken blir mer tillgänglig, genom att tillhandahålla molnbaserad teknik och använda köer, ämnen och reläer.

## <a name="next-steps"></a>Nästa steg
Nu när du har lärt dig grunderna om Azure Service Bus, kan du följa dessa länkar om du vill veta mer.

* Använda [Service Bus-köer](service-bus-dotnet-get-started-with-queues.md)
* Använda [Service Bus-ämnen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
* Använda [Service Bus-reläer](../service-bus-relay/service-bus-dotnet-how-to-use-relay.md)
* [Service Bus-exempel](service-bus-samples.md)

[1]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_01_architecture.png
[2]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_02_queues.png
[3]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_03_topicsandsubscriptions.png
[4]: ./media/service-bus-fundamentals-hybrid-solutions/SvcBus_04_relay.png



<!--HONumber=Nov16_HO2-->


