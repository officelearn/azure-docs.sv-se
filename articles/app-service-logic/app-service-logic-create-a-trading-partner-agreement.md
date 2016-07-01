<properties 
   pageTitle="Skapa ett handelspartneravtal i Azure Apptjänst | Microsoft Azure" 
   description="Skapa handelspartneravtal" 
   services="app-service\logic" 
   documentationCenter=".net,nodejs,java" 
   authors="rajram" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
    ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="04/20/2016"
   ms.author="rajram"/>

# Skapa ett handelspartneravtal   

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Handelspartner är de enheter som ingår i B2B-kommunikation (Business to Business). När två parter upprättar en relation kallas det för ett *avtal*. Det definierade avtalet baseras på den kommunikation som de två parterna vill uppnå och är protokoll- eller transportspecifikt. De olika B2B-protokollen och B2B-transporterna som stöds av Azure Apptjänst är:

- AS2 (Applicability Statement 2)
- EDIFACT (FN:s regler om elektroniskt datautbyte inom administration, handel och transport (FN/EDIFACT))
- X12 (ASC X12)

### BizTalk-API Apps som stöder B2B-scenarier
Följande API Apps aktiverar de här funktionerna med en omfattande och intuitiv upplevelse i Azure Portal:


## BizTalk-handelspartnerhantering (TPM)
- Skapa och hantera partner, profiler och identiteter
- Lagra och hantera EDI-scheman
- Lagra och hantera certifikat (som används i AS2-protokoll)
- Skapa och hantera AS2-avtal
- Skapa och hantera EDIFACT-avtal (inklusive batchbearbetning på sändningssidan)
- Skapa och hantera X12-avtal (inklusive batchbearbetning på sändningssidan)

![][1]


## AS2-koppling
- Kör AS2-avtal enligt definitionen i den relaterade TPM-API-app-instansen
- Hämtar AS2-bearbetnings/-spårningsinformation för felsökning


## BizTalk EDIFACT
- Kör EDIFACT-avtal enligt definitionen i den relaterade TPM-API-app-instansen
- Hämtar EDIFACT-bearbetnings/-spårningsinformation för felsökning
- Tillhandahåller tillståndshantering av batchar (start och stopp) enligt definitionen i EDIFACT-avtalen i den relaterade TPM-API-app-instansen


## BizTalk X12
- Kör X12-avtal enligt definitionen i den relaterade TPM-API-app-instansen 
- Hämtar X12-bearbetnings/-spårningsinformation för felsökning
- Tillhandahåller tillståndshantering av batchar (start och stopp) enligt definitionen i X12-avtalen i den relaterade TPM-API-app-instansen

Som tidigare beskrivits kräver AS2-, X12- och EDIFACT-API Appsna en TPM-API-app för att fungera som förväntat.


## Komma igång
Skapa handelspartneravtal:

1. Skapa en instans av kopplingen **BizTalk-handelspartnerhantering**. För att det ska fungera behövs en tom SQL-databas. Innan du börjar bör du ha en tom databas tillgänglig och redo för användning.
2. Överför scheman och certifikat som krävs av avtalen. Gör det genom att bläddra i den TPM-instans som skapats och gå till ”Scheman” eller ”Certifikat”
3. Bläddra till den TPM-instans som har skapats och gå till delen **Partners**
4. Skapa partners enligt önskemål. Redigera även profil(er) efter behov och lägg till de nödvändiga identiteterna
5. Nu kan du använda delen **Avtal** för att skapa avtal. Du måste välja det protokoll som ska användas när du skapar ett avtal. Återstående konfigurationsalternativ baseras på det protokoll som du har valt.

![][2]

![][3]

<!--Image references-->
[1]: ./media/app-service-logic-create-a-trading-partner-agreement/TPMResourceView.png
[2]: ./media/app-service-logic-create-a-trading-partner-agreement/ProtocolSelection.png
[3]: ./media/app-service-logic-create-a-trading-partner-agreement/X12AgreementCreation.png
 



<!--HONumber=Jun16_HO2-->


