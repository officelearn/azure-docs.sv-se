---
title: En lokal SQLServer
titleSuffix: Azure Machine Learning Studio
description: Använda data från en lokal SQL Server-databas för att utföra avancerade analyser med Azure Machine Learning Studio.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: xiaoharper
ms.author: amlstudiodocs
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9590728cec663b36c889dc26a6216c3d474244e4
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60735463"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-using-an-on-premises-sql-server-database"></a>Utföra analyser med Azure Machine Learning Studio med hjälp av en lokal SQL Server-databas

Ofta företag som fungerar med lokala data vill dra nytta av skalan och flexibilitet med molnet för sina maskininlärningsarbetsbelastningar. Men de vill inte störa deras aktuella affärsprocesser och arbetsflöden genom att flytta sina lokala data till molnet. Azure Machine Learning Studio stöder nu dina data läses från en lokal SQL Server-databas och sedan träning och bedömning av en modell med dessa data. Du har inte längre att manuellt kopiera och synkronisera data mellan molnet och den lokala servern. I stället den **importdata** modul i Azure Machine Learning Studio kan nu läsa direkt från din lokala SQL Server-databas för utbildning och bedömning jobb.

Den här artikeln innehåller en översikt över hur du ingående lokala SQL server-data till Azure Machine Learning Studio. Det förutsätts att du är bekant med Studio begrepp som arbetsytor, moduler, datauppsättningar, experiment, *etc.*.

> [!NOTE]
> Den här funktionen är inte tillgänglig för kostnadsfria arbetsytor. Läs mer om Machine Learning-priser och nivåer [Azure Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Installera Data Factory lokal Integration Runtime
För att komma åt en lokal SQL Server-databas i Azure Machine Learning Studio, måste du ladda ned och installera den Data Factory lokal Integration Runtime, tidigare känd som Data Management Gateway. När du konfigurerar anslutningen i Machine Learning Studio, har du möjlighet att ladda ned och installera Integration Runtime (IR) med den **nedladdning och registrera datagateway** dialogrutan som beskrivs nedan.


Du kan också installera IR förväg genom att hämta och köra MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera en befintlig IR till den senaste versionen med alla inställningar som bevaras.

Data Factory lokal Integration Runtime har följande krav:

* Data Factory lokala Integration kräver en 64-bitars operativsystem med .NET Framework 4.6.1 eller senare.
* Versioner för Windows-operativsystemet är Windows 10, Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Den rekommenderade konfigurationen för den IR-datorn är minst 2 GHz, processor med 4 kärnor, 8GB RAM-minne och 80GB disk.
* Om värddatorn i viloläge, svara IR inte på begäranden. Därför kan konfigurera en lämplig energischema på datorn innan du installerar i IR. Om datorn är konfigurerad för att viloläge, visas ett meddelande i IR-installationen.
* Eftersom Kopieringsaktivitet inträffar med en frekvens för specifika följer samma mönster med högsta och inaktivitetstid också i Resursanvändning (processor, minne) på datorn. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopia jobb pågår, ska du se resursanvändningen gå upp under Högbelastningstider. Den lägsta konfigurationen som anges ovan är tekniskt sett tillräcklig, du har en konfiguration med fler resurser än den lägsta konfigurationen beroende på dina specifika belastningen för dataförflyttning.

Tänk på följande när du ställer in och använder en Data Factory lokal Integration Runtime:

* Du kan installera endast en instans av IR på en dator.
* Du kan använda en enda IR för flera lokala datakällor.
* Du kan ansluta flera IRs på olika datorer till samma lokala datakällan.
* Du kan konfigurera en IRs för endast en arbetsyta i taget. IRs kan för närvarande inte delas mellan arbetsytor.
* Du kan konfigurera flera IRs för en enda arbetsyta. Du kan till exempel vill använda en IR som är ansluten till dina datakällor för test under utveckling och produktion IR när du är redo att utföra åtgärder.
* IR behöver inte finnas på samma dator som datakällan. Men att närmare datakällan minskar tid som gatewayen ska ansluta till datakällan. Vi rekommenderar att du installerar IR på en dator som skiljer sig från det som är värd för den lokala datakällan, så att gatewayen och datakällan inte konkurrerar om resurser.
* Om du redan har en IR som installerats på datorn som betjänar Power BI eller Azure Data Factory scenarier kan du installera ett separat IR för Azure Machine Learning Studio på en annan dator.

  > [!NOTE]
  > Du kan inte köra Data Factory lokal Integration Runtime och Power BI Gateway på samma dator.
  >
  >
* Du måste använda Data Factory lokal Integration Runtime för Azure Machine Learning Studio, även om du använder Azure ExpressRoute för andra data. Du bör hantera din datakälla som en lokal datakälla (som finns bakom en brandvägg) även när du använder ExpressRoute. Använd Data Factory lokal Integration Runtime för att upprätta en anslutning mellan Machine Learning och datakällan.

Du hittar detaljerad information om installationskraven installationsstegen och felsökningstips i artikeln [Integration Runtime i Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Inkommande data från din lokala SQL Server-databas till Azure Machine Learning
I den här genomgången ska du ställa in en Integreringskörning för Azure Data Factory i en Azure Machine Learning-arbetsyta, konfigurera den och läsa data från en lokal SQL Server-databas.

> [!TIP]
> Innan du börjar måste du inaktivera webbläsarens popup-blockerare för `studio.azureml.net`. Om du använder webbläsaren Google Chrome, ladda ned och installera ett av flera plugin-programmen på Google Chrome WebStore [klickar du på en gång App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory lokal Integration Runtime kallades tidigare Data Management Gateway. Stegvisa självstudier fortsätter att referera till den som en gateway.  

### <a name="step-1-create-a-gateway"></a>Steg 1: Skapa en gateway
Det första steget är att skapa och konfigurera gatewayen för att få åtkomst till din lokala SQL-databas.

1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/Home/) och välj den arbetsyta som du vill arbeta.
2. Klicka på den **inställningar** bladet till vänster och klicka sedan på den **DATA GATEWAYS** fliken högst upp.
3. Klicka på **ny personlig GATEWAY** längst ned på skärmen.

    ![Ny personlig Gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. I den **ny personlig gateway** dialogrutan, ange den **Gatewaynamn** och eventuellt lägga till en **beskrivning**. Klicka på pilen i det nedre högra hörnet att gå till nästa steg i konfigurationen.

    ![Ange gatewaynamn och beskrivning](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. I dialogrutan ladda ned och registrera data gateway du kopiera REGISTRERINGSNYCKEL GATEWAY till Urklipp.

    ![Ladda ned och registrera datagateway](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Om du har ännu inte har hämtat och installerat Microsoft Data Management Gateway, sedan klickar du på **Download gateway för datahantering**. Detta tar dig till Microsoft Download Center där du kan välja den gateway-version som du behöver ladda ned den och installera den. Du hittar detaljerad information om installationskraven installationsstegen och felsökningstips i början avsnitten i artikeln [flytta data mellan lokala källor och molnet med Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md) .
7. När gatewayen har installerats kan Data Management Gateway Configuration Manager öppnas och **registrera gatewayen** dialogrutan visas. Klistra in den **Gateway registreringsnyckel** som du kopierade till Urklipp och klicka på **registrera**.
8. Om du redan har en installerad gateway kan köra Data Management Gateway Configuration Manager. Klicka på **ändringsnyckel**, klistra in den **Gateway registreringsnyckel** som du kopierade till Urklipp i föregående steg och klicka på **OK**.
9. När installationen är klar, den **registrera gatewayen** visas dialogrutan för Microsoft Data Management Gateway Configuration Manager. Klistra in GATEWAY-REGISTRERINGSNYCKEL som du kopierade till Urklipp i föregående steg och klicka på **registrera**.

    ![Registrera gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Gateway-konfigurationen är klar när följande värden är inställda på den **Start** fliken i Microsoft Data Management Gateway Configuration Manager:

    * **Gatewaynamn** och **instansnamn** är inställda på att namnet på gatewayen.
    * **Registrering** är inställd på **registrerad**.
    * **Status för** är inställd på **startad**.
    * Statusfältet längst ned visar **är ansluten till Molntjänsten för Data Management Gateway** tillsammans med en grön bockmarkering.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio uppdateras också när registreringen lyckas.

    ![Lyckade gateway-registrering](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. I den **ladda ned och registrera datagateway** dialogrutan klickar du på kryssmarkeringen för att slutföra installationen. Den **inställningar** sidan visar statusen för gatewayen som ”Online”. Du hittar i den högra rutan, status och annan användbar information.

    ![Gateway-inställningar](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. I Microsoft Data Management Gateway Configuration Manager växla till den **certifikat** fliken. Certifikatet som anges på den här fliken används för att kryptera/dekryptera autentiseringsuppgifterna för det lokala datalagret som du anger i portalen. Det här certifikatet är standardcertifikatet. Microsoft rekommenderar att du ändrar detta till ditt eget certifikat som du säkerhetskopierar det certifikat hanteringssystemet. Klicka på **ändra** att använda ditt eget certifikat i stället.

    ![Ändra gateway-certifikatet](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (valfritt) Om du vill aktivera utförlig loggning för att felsöka problem med gateway i Microsoft Data Management Gateway Configuration Manager växla till den **diagnostik** fliken och markera den **aktivera utförlig loggning i felsökningssyfte** alternativet. Loggningsinformation finns i Windows Loggboken under den **applikationer och tjänsteloggar**  - &gt; **Data Management Gateway** noden. Du kan också använda den **diagnostik** flik för att testa anslutningen till en lokal datakälla med hjälp av gatewayen.

    ![Aktivera utförlig loggning](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Nu är du klar med installationen gateway i Azure Machine Learning Studio.
Du är nu redo att använda dina lokala data.

Du kan skapa och konfigurera flera gateways i Studio för varje arbetsyta. Du kan till exempel ha en gateway som du vill ansluta till dina datakällor för test under utvecklingen och en annan gateway för dina datakällor för produktion. Azure Machine Learning Studio ger dig flexibiliteten att ställa in flera gateways beroende på din företagsmiljö. För närvarande du kan inte dela en gateway mellan arbetsytor och endast en gateway kan installeras på en dator. Mer information finns i [flytta data mellan lokala källor och molnet med Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Steg 2: Använda gatewayen för att läsa data från en lokal datakälla
När du har konfigurerat gatewayen kan du lägga till en **importdata** modul till ett experiment som indata data från en lokal SQL Server-databasen.

1. I Machine Learning Studio, väljer du den **EXPERIMENT** fliken **+ ny** i nedre vänstra hörnet och välj **tomt Experiment** (eller välja en av flera exempel experiment tillgänglig).
2. Leta upp och dra den **importdata** modulen till experimentarbetsytan.
3. Klicka på **Spara som** under arbetsytan. Ange ”Azure Studio lokala SQL Server självstudie om Machine Learning” för experiment namn, Välj arbetsytan och klicka på den **OK** kryssmarkeringen.

   ![Spara experiment med ett nytt namn](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klicka på den **importera Data** modul för att markera den sedan i den **egenskaper** rutan till höger om arbetsytan och välj ”lokala SQL-databas” i den **datakälla** listrutan.
5. Välj den **datagateway** du installerats och registrerats. Du kan ställa in en annan gateway genom att välja ”(Lägg till ny personlig Gateway...)”.

   ![Välj datagateway för modulen importera Data](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Ange SQL **Databasservernamnet** och **databasnamn**, tillsammans med SQL **databasfråga** du vill köra.
7. Klicka på **ange värden** under **användarnamn och lösenord** och ange dina Databasautentiseringsuppgifter för. Du kan använda Windows-integrerad autentisering eller SQL Server-autentisering beroende på hur din lokala SQL Server är konfigurerat.

   ![Ange autentiseringsuppgifterna för databasens](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Meddelandet ”values krävs” ändringarna (värden set) med en grön bockmarkering. Du behöver bara ange autentiseringsuppgifterna som en gång, såvida inte databasinformation eller lösenord ändras. Azure Machine Learning Studio använder det certifikat som du angav när du har installerat gatewayen att kryptera autentiseringsuppgifterna i molnet. Azure lagrar aldrig lokala autentiseringsuppgifter utan kryptering.

   ![Importera modulen dataegenskaper](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klicka på **kör** att köra experimentet.

När experimentet är klar kan du visualisera de data du har importerat från databasen genom att klicka på utdataporten för den **importdata** modulen och välja **visualisera**.

När du är klar med att utveckla ditt experiment kan du distribuera och driftsätta modellen. Med hjälp av Batch Execution Service data från en lokal SQL Server-databasen som konfigurerats i den **importdata** modulen läses och används för bedömning. Du kan använda tjänsten Begär svar för bedömning lokala data, Microsoft rekommenderar att du använder den [Excel-tillägget](excel-add-in-for-web-services.md) i stället. För närvarande kan skriva till en lokal SQL Server-databas via **exportera Data** är inte antingen stöds i dina experiment eller publicerade webbtjänster.
