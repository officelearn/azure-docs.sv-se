---
title: Lokalt SQL Server
titleSuffix: ML Studio (classic) - Azure
description: Använd data från en lokal SQL Server-databas för att utföra avancerad analys med Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 648dbdb7e9e9d1b20c55d3fa5b314b7e4657d5e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79204190"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Utföra analyser med Azure Machine Learning Studio (klassisk) med hjälp av en lokal SQL Server-databas

[!INCLUDE [Notebook deprecation notice](../../../includes/aml-studio-notebook-notice.md)]

Ofta vill företag som arbetar med lokala data dra nytta av molnets omfattning och smidighet för sina maskininlärningsarbetsbelastningar. Men de vill inte störa sina nuvarande affärsprocesser och arbetsflöden genom att flytta sina lokala data till molnet. Azure Machine Learning Studio (klassisk) stöder nu att läsa dina data från en lokal SQL Server-databas och sedan träna och göra en modell med dessa data. Du behöver inte längre kopiera och synkronisera data manuellt mellan molnet och den lokala servern. I stället kan **modulen Importera data** i Azure Machine Learning Studio (klassisk) nu läsa direkt från din lokala SQL Server-databas för dina utbildnings- och bedömningsjobb.

Den här artikeln innehåller en översikt över hur du inträngningar lokala SQL-serverdata i Azure Machine Learning Studio (klassisk). Det förutsätter att du är bekant med Studio (klassiska) begrepp som arbetsytor, moduler, datauppsättningar, experiment, *etc.*.

> [!NOTE]
> Den här funktionen är inte tillgänglig för kostnadsfria arbetsytor. Mer information om machine learning-priser och nivåer finns i [Azure Machine Learning Pricing](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Installera datafabrikens självvärderade integrationskörning
Om du vill komma åt en lokal SQL Server-databas i Azure Machine Learning Studio (klassisk) måste du hämta och installera Data Factory Self-hosted Integration Runtime, tidigare känd som Data Management Gateway. När du konfigurerar anslutningen i Machine Learning Studio (klassisk) har du möjlighet att hämta och installera Integration Runtime (IR) med hjälp av dialogrutan **Hämta och registrera datagateway** som beskrivs nedan.


Du kan också installera IR i förväg genom att ladda ner och köra MSI-installationspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera en befintlig IR till den senaste versionen, med alla inställningar bevarade.

Data Factory Self-Hosted Integration Runtime har följande förutsättningar:

* Data Factory Self-Hosted Integration kräver ett 64-bitars operativsystem med .NET Framework 4.6.1 eller högre.
* De Windows-operativsystemversioner som stöds är Windows 10 , Windows Server 2012, Windows Server 2012 R2, Windows Server 2016. 
* Den rekommenderade konfigurationen för IR-datorn är minst 2 GHz, 4 Kärnprocessor, 8 GB RAM och 80 GB disk.
* Om värddatorn försätts i viloläge svarar IR inte på databegäranden. Konfigurera därför ett lämpligt energischema på datorn innan du installerar IR.Therefore, configure an appropriate power plan on the computer before installing the IR. Om datorn är konfigurerad för viloläge visas ett meddelande i IR-installationen.
* Eftersom kopieringsaktiviteten sker med en viss frekvens följer resursanvändningen (CPU, minne) på datorn också samma mönster med topp- och inaktiva tider. Resursutnyttjandet beror också mycket på mängden data som flyttas. När flera kopieringsjobb pågår ser du resursanvändningen gå upp under rusningstid. Även om den minsta konfigurationen som anges ovan är tekniskt tillräcklig, kanske du vill ha en konfiguration med fler resurser än den minsta konfigurationen beroende på din specifika belastning för dataförflyttning.

Tänk på följande när du konfigurerar och använder en självvärd för datafabrikens integrationskörning:

* Du kan bara installera en instans av IR på en enda dator.
* Du kan använda en enda IR för flera lokala datakällor.
* Du kan ansluta flera IR:er på olika datorer till samma lokala datakälla.
* Du konfigurerar en IR för endast en arbetsyta i taget. För närvarande kan IRs inte delas mellan arbetsytor.
* Du kan konfigurera flera IR:er för en enda arbetsyta. Du kanske till exempel vill använda en IR som är ansluten till dina testdatakällor under utveckling och en produktions-IR när du är redo att operationalisera.
* IR behöver inte vara på samma dator som datakällan. Men att hålla sig närmare datakällan minskar tiden för gatewayen att ansluta till datakällan. Vi rekommenderar att du installerar IR på en dator som skiljer sig från den som är värd för den lokala datakällan så att gatewayen och datakällan inte konkurrerar om resurser.
* Om du redan har en IR installerad på datorn som betjänar Power BI- eller Azure Data Factory-scenarier installerar du en separat IR för Azure Machine Learning Studio (klassisk) på en annan dator.

  > [!NOTE]
  > Du kan inte köra Data Factory Self-hosted Integration Runtime och Power BI Gateway på samma dator.
  >
  >
* Du måste använda Data Factory Self-hosted Integration Runtime for Azure Machine Learning Studio (klassisk) även om du använder Azure ExpressRoute för andra data. Du bör behandla datakällan som en lokal datakälla (det är bakom en brandvägg) även när du använder ExpressRoute. Använd Data Factory Self-hosted Integration Runtime för att upprätta anslutning mellan Machine Learning och datakällan.

Du hittar detaljerad information om installationsförutsättning, installationssteg och felsökningstips i artikeln [Integration Runtime i Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Inträngningsdata från din lokala SQL Server-databas till Azure Machine Learning
I den här genomgången konfigurerar du en Azure Data Factory Integration Runtime i en Azure Machine Learning-arbetsyta, konfigurerar den och läser sedan data från en lokal SQL Server-databas.

> [!TIP]
> Innan du börjar inaktiverar du webbläsarens popup-blockerare för `studio.azureml.net`. Om du använder webbläsaren Google Chrome laddar du ned och installerar något av de flera plugin-program som finns på Google Chrome WebStore [Click Once App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory Self-hosted Integration Runtime var tidigare känd som Data Management Gateway. Den steg för steg handledning kommer att fortsätta att hänvisa till det som en gateway.  

### <a name="step-1-create-a-gateway"></a>Steg 1: Skapa en gateway
Det första steget är att skapa och konfigurera gatewayen för att komma åt din lokala SQL-databas.

1. Logga in på [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net/Home/) och välj den arbetsyta som du vill arbeta i.
2. Klicka på **bladet INSTÄLLNINGAR** till vänster och klicka sedan på fliken **DATA GATEWAYS** högst upp.
3. Klicka på **NY DATAGATEWAY** längst ned på skärmen.

    ![Ny datagateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. I dialogrutan **Ny datagateway** anger du **gatewaynamnet** och lägger eventuellt till en **beskrivning**. Klicka på pilen längst ned till höger för att gå till nästa steg i konfigurationen.

    ![Ange gatewaynamn och beskrivning](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Kopiera gateway-registreringsnyckeln till Urklipp i dialogrutan Hämta och registrera datagateway.

    ![Ladda ned och registrera datagateway](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Om du ännu inte har hämtat och installerat Microsoft Data Management Gateway klickar du på **Hämta datahanteringsgateway**. Detta tar dig till Microsoft Download Center där du kan välja den gateway-version du behöver, ladda ner den och installera den. Du hittar detaljerad information om installationsförutsättning, installationssteg och felsökningstips i början av artikeln [Flytta data mellan lokala källor och moln med Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. När gatewayen har installerats öppnas Configuration Manager för datahanteringsgateway och dialogrutan **Registrera gateway** visas. Klistra in **gatewayregistreringsnyckeln** som du kopierade till Urklipp och klicka på **Registrera**.
8. Om du redan har en gateway installerad kör du Configuration Manager för Data Management Gateway. Klicka på **Ändra nyckel,** klistra in **gatewayregistreringsnyckeln** som du kopierade till Urklipp i föregående steg och klicka på **OK**.
9. När installationen är klar visas dialogrutan **Registrera gateway** för Microsoft Data Management Gateway Configuration Manager. Klistra in gateway-registreringsnyckeln som du kopierade till Urklipp i ett föregående steg och klicka på **Registrera**.

    ![Registrera gatewayen](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Gateway-konfigurationen slutförs när följande värden anges på fliken **Start** i Konfigurationshanteraren för Microsoft Data Management Gateway:

    * **Gateway-namn** och **instansnamn** anges till namnet på gatewayen.
    * **Registreringen** är registrerad **.**
    * **Statusen** är inställd **på Startad**.
    * Statusfältet längst ned visar **Molntjänsten Ansluten till datahantering gateway** tillsammans med en grön bock.

      ![Gateway-hanteraren för datahantering](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klassisk) uppdateras också när registreringen lyckas.

    ![Gateway-registreringen lyckades](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. I dialogrutan **Hämta och registrera datagateway** klickar du på bocken för att slutföra installationen. Sidan **Inställningar** visar gatewaystatusen som "Online". I den högra rutan hittar du status och annan användbar information.

    ![Gateway-inställningar](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. Växla till fliken Certifikat i Konfigurationshanteraren **för** Microsoft Data Management Gateway. Certifikatet som anges på den här fliken används för att kryptera/dekryptera autentiseringsuppgifter för det lokala datalagret som du anger i portalen. Det här certifikatet är standardcertifikatet. Microsoft rekommenderar att du ändrar detta till ett eget certifikat som du säkerhetskopierar i certifikathanteringssystemet. Klicka på **Ändra** om du vill använda ditt eget certifikat i stället.

    ![Ändra gatewaycertifikat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (valfritt) Om du vill aktivera utförlig loggning för att felsöka problem med gatewayen växlar du i **Konfigurationshanteraren** för Microsoft Data Management Gateway till fliken Diagnostik och kontrollerar alternativet **Aktivera utförlig loggning för felsökning.** Loggningsinformationen finns i Loggboken i Windows under noden - &gt; **Datahanteringsgated** **för program och tjänster.** Du kan också använda fliken **Diagnostik** för att testa anslutningen till en lokal datakälla med hjälp av gatewayen.

    ![Aktivera utförlig loggning](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Detta slutför gateway-installationsprocessen i Azure Machine Learning Studio (klassisk).
Du är nu redo att använda dina lokala data.

Du kan skapa och konfigurera flera gateways i Studio (klassisk) för varje arbetsyta. Du kan till exempel ha en gateway som du vill ansluta till dina testdatakällor under utvecklingen och en annan gateway för dina produktionsdatakällor. Azure Machine Learning Studio (klassisk) ger dig flexibiliteten att konfigurera flera gateways beroende på din företagsmiljö. För närvarande kan du inte dela en gateway mellan arbetsytor och endast en gateway kan installeras på en enda dator. Mer information finns i [Flytta data mellan lokala källor och molnet med Data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Steg 2: Använd gatewayen för att läsa data från en lokal datakälla
När du har konfigurerat gatewayen kan du lägga till en **importdatamodul** i ett experiment som matar in data från den lokala SQL Server-databasen.

1. I Machine Learning Studio (klassisk) väljer du fliken **EXPERIMENT,** klickar på **+NYTT** i det nedre vänstra hörnet och väljer **Tomt experiment** (eller välj ett av flera tillgängliga exempelexperiment).
2. Hitta och dra modulen **Importera data** till experimentarbetsytan.
3. Klicka på **Spara som** under arbetsytan. Ange "Azure Machine Learning Studio (klassisk) On-Premises SQL Server Tutorial" för experimentnamnet, välj arbetsytan och klicka på OK-bocken. **OK**

   ![Spara experiment med ett nytt namn](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klicka på modulen **Importera data** för att markera **den** och välj sedan "Lokal SQL-databas" i listrutan Datakälla i fönstret **Egenskaper.**
5. Välj den **datagateway** som du installerade och registrerade. Du kan ställa in en annan gateway genom att välja "(lägg till ny datagateway...)".

   ![Välj datagateway för modulen Importera data](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Ange SQL **Database-servernamnet** och **databasnamnet**tillsammans med den SQL **Database-fråga** som du vill köra.
7. Klicka på **Ange värden** under Användarnamn **och lösenord** och ange dina databasuppgifter. Du kan använda Windows Integrated Authentication eller SQL Server Authentication beroende på hur din lokala SQL Server är konfigurerad.

   ![Ange databasautentiseringsuppgifter](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Meddelandet "värden krävs" ändras till "värden inställda" med en grön bock. Du behöver bara ange autentiseringsuppgifterna en gång om inte databasinformationen eller lösenordet ändras. Azure Machine Learning Studio (klassisk) använder certifikatet som du angav när du installerade gatewayen för att kryptera autentiseringsuppgifterna i molnet. Azure lagrar aldrig lokala autentiseringsuppgifter utan kryptering.

   ![Egenskaper för importera datamodul](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klicka på **KÖR** för att köra experimentet.

När experimentet har körts kan du visualisera de data som du har importerat från databasen genom att klicka på utdataporten i modulen **Importera data** och välja **Visualisera**.

När du är klar med att utveckla experimentet kan du distribuera och operationalisera din modell. Med hjälp av batchkörningstjänsten läss data från den lokala SQL Server-databasen som konfigurerats i modulen **Importera data** och används för bedömning. Du kan använda tjänsten Begär svar för att göra lokala poäng, men Microsoft rekommenderar att du använder [Excel-tillägget i](excel-add-in-for-web-services.md) stället. För närvarande stöds inte att skriva till en lokal SQL Server-databas via **exportdata** varken i experimenten eller publicerade webbtjänster.
