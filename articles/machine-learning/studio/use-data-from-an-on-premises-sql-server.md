---
title: Använda en lokal SQLServer i Azure Machine Learning | Microsoft Docs
description: Använd data från en lokal SQL Server-databas för att utföra avancerade analyser med Azure Machine Learning.
services: machine-learning
documentationcenter: ''
author: heatherbshapiro
ms.author: hshapiro
manager: hjerez
editor: cgronlun
ms.assetid: 08e4610d-02b6-4071-aad7-a2340ad8e2ea
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/13/2017
ms.openlocfilehash: 73b68ec612f10fabe0891bfddfa7783b981642bc
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/23/2018
---
# <a name="perform-advanced-analytics-with-azure-machine-learning-using-data-from-an-on-premises-sql-server-database"></a>Utföra avancerade analyser med Azure Machine Learning med hjälp av data från en lokal SQL Server-databas

[!INCLUDE [import-data-into-aml-studio-selector](../../../includes/machine-learning-import-data-into-aml-studio.md)]

Ofta företag som arbetar med lokala data vill dra nytta av skalan och flexibilitet i molnet för sina maskininlärning arbetsbelastningar. Men de vill inte störa deras aktuella affärsprocesser och arbetsflöden genom att flytta sina lokala data till molnet. Azure Machine Learning har nu stöd för att läsa data från en lokal SQL Server-databasen och sedan utbildning och poängsättning av en modell med dessa data. Du har inte längre att manuellt kopiera och synkronisera data mellan molnet och lokala servern. I stället den **importera Data** modul i Azure Machine Learning Studio kan nu läsa direkt från din lokala SQL Server-databasen för din utbildning och bedömningen jobb.

Den här artikeln innehåller en översikt över hur du ingång lokala SQL server-data i Azure Machine Learning. Det förutsätts att du är bekant med Azure Machine Learning-begrepp som arbetsytor, moduler, datauppsättningar, experiment, *etc*.

> [!NOTE]
> Den här funktionen är inte tillgänglig för kostnadsfria arbetsytor. Mer information om Machine Learning-priser och nivåer finns [Azure Machine Learning-priser](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->

[!INCLUDE [machine-learning-free-trial](../../../includes/machine-learning-free-trial.md)]

## <a name="install-the-microsoft-data-management-gateway"></a>Installera Microsoft Data Management Gateway
För att komma åt en lokal SQL Server-databas i Azure Machine Learning, måste du hämta och installera Microsoft Data Management Gateway. När du konfigurerar gateway-anslutningen i Machine Learning Studio har möjlighet att hämta och installera en gateway med hjälp av den **hämtningen och registrera datagatewayen** dialogrutan som beskrivs nedan.

Du kan också installera Data Management Gateway i förväg genom att hämta och köra MSI-installationspaketet från den [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717).
Välj den senaste versionen, välja 32-bitars eller 64-bitars som passar din dator. MSI-filerna kan också användas för att uppgradera en befintlig Data Management Gateway till den senaste versionen med alla inställningar som bevaras.

Gatewayen har följande krav:

* Stöds versioner av Windows-operativsystemet är Windows 7, Windows 8/8.1, Windows 10, Windows Server 2008 R2, Windows Server 2012 och Windows Server 2012 R2.
* Den rekommenderade konfigurationen för gateway-datorn är minst 2 GHz 4 kärnor, 8GB RAM-minne och 80GB disk.
* Om värddatorn i viloläge, svara gatewayen inte på begäranden. Därför konfigurera en lämplig energischema på datorn innan du installerar gateway. Om datorn är konfigurerad för viloläge, visas ett meddelande i gateway-installationen.
* Eftersom kopieringsaktiviteten sker på en specifik frekvens, följer Resursanvändning (CPU, minne) på datorn också samma mönster med belastning och ledig tid. Resursutnyttjande beror också kraftigt på mängden data som flyttas. När flera kopiera jobb pågår, ska du se Resursanvändning gå upp under tider med låg belastning. Den lägsta konfigurationen som anges ovan är tekniskt tillräckligt, önskade har en konfiguration med mer resurser än den lägsta konfigurationen beroende på dina specifika belastningen för dataförflyttning.

Tänk på följande när du konfigurerar och använder en Data Management Gateway:

* Du kan installera bara en instans av Data Management Gateway på en dator.
* Du kan använda en enda gateway för flera lokala datakällor.
* Du kan ansluta flera gateways på olika datorer till samma lokala datakällan.
* Du kan konfigurera en gateway för endast en arbetsyta i taget. Gateways kan för närvarande inte delas mellan arbetsytorna.
* Du kan konfigurera flera gateways för en enda arbetsyta. Du kanske vill använda en gateway som är ansluten till din test datakällor under utvecklingen och en gateway för produktionen när du är redo att operationalisera.
* Gatewayen behöver inte finnas på samma dator som datakälla. Men används närmare datakällan minskar tiden för gateway för att ansluta till datakällan. Vi rekommenderar att du installerar en gateway på en dator som skiljer sig från det som är värd för den lokala datakällan så att gatewayen och datakälla inte konkurrerar om resurser.
* Om du redan har en gateway som har installerats på datorn som betjänar Power BI eller Azure Data Factory scenarier, installera en separat gateway för Azure Machine Learning på en annan dator.

  > [!NOTE]
  > Du kan inte köra Data Management Gateway och Power BI Gateway på samma dator.
  >
  >
* Du måste använda Data Management Gateway för Azure Machine Learning, även om du använder Azure ExpressRoute för andra data. Datakällan ska behandlas som en lokal datakälla (som finns bakom en brandvägg) även om du använder ExpressRoute. Använda Data Management Gateway för att upprätta en anslutning mellan Machine Learning och datakällan.

Du hittar detaljerad information om installationskraven och installationssteg felsökningstips i artikeln [Data Management Gateway](../../data-factory/v1/data-factory-data-management-gateway.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-idtoc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingång data från din lokala SQL Server-databas till Azure Machine Learning
I den här genomgången ska du ställa in en Data Management Gateway i en Azure Machine Learning-arbetsytan, konfigurera den och läsa data från en lokal SQL Server-databas.

> [!TIP]
> Innan du börjar inaktivera webbläsarens popup-blockerare för `studio.azureml.net`. Om du använder webbläsaren Google Chrome, hämta och installera ett av flera plugin-programmen finns på Google Chrome vi besvarar [klickar du på en gång App Extension](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
>

### <a name="step-1-create-a-gateway"></a>Steg 1: Skapa en gateway
Det första steget är att skapa och konfigurera gatewayen för att få åtkomst till din lokala SQL-databas.

1. Logga in på [Azure Machine Learning Studio](https://studio.azureml.net/Home/) och välj arbetsytan som du vill arbeta.
2. Klicka på den **inställningar** bladet till vänster och klicka sedan på den **DATAGATEWAYAR** högst upp.
3. Klicka på **nya DATA GATEWAY** längst ned på skärmen.

    ![New Data Gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. I den **nya datagateway** dialogrutan, ange den **Gatewaynamnet** och eventuellt lägga till en **beskrivning**. Klicka på pilen i det nedre högra hörnet att gå till nästa steg i konfigurationen.

    ![Ange gatewaynamn och beskrivning](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. Kopiera GATEWAYNYCKELN för registrering i dialogrutan hämtningen och registrera data gateway till Urklipp.

    ![Hämta och registrera gateway](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Om du har ännu inte har hämtat och installerat Microsoft Data Management Gateway, klicka på **Download data management gateway**. Då kommer du till Microsoft Download Center där du kan välja gateway-version som du måste ladda ned det och installera den. Du hittar detaljerad information om installationskraven och installationssteg felsökningstips i början avsnitten i artikeln [flytta data mellan lokala källor och moln med Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md) .
7. Data Management Gateway Configuration Manager öppnas efter att gatewayen har installerats och **registrera gateway** dialogrutan visas. Klistra in den **Gateway registreringsnyckel** du kopierade till Urklipp och klicka på **registrera**.
8. Om du redan har en installerad gateway kör Data Management Gateway Configuration Manager. Klicka på **Byt nyckel**, klistra in den **Gateway registreringsnyckel** som du kopierade till Urklipp i föregående steg och klicka på **OK**.
9. När installationen är klar, den **registrera gateway** visas dialogrutan för Microsoft Data Management Gateway Configuration Manager. Klistra in GATEWAY-REGISTRERINGSNYCKEL som du kopierade till Urklipp i föregående steg och klickar på **registrera**.

    ![Registrera gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Gateway-konfigurationen är klar när följande värden anges på den **Start** fliken i Microsoft Data Management Gateway Configuration Manager:

    * **Gatewaynamnet** och **instansnamn** är inställda på att namnet på gatewayen.
    * **Registrering** är inställd på **registrerade**.
    * **Status för** är inställd på **igång**.
    * Statusfältet längst ned visar **ansluten till Data Management Gateway Cloud Service** tillsammans med en grön bock.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio också uppdateras när registreringen lyckas.

    ![Gateway-registrering lyckades](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. I den **ladda ned och registrera gateway** dialogrutan, klicka på kryssmarkeringen för att slutföra installationen. Den **inställningar** visar sidan gatewayens status som ”Online”. Du hittar i den högra rutan, status och annan användbar information.

    ![Inställningar för gateway](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. I det Microsoft Data Management Gateway Configuration Manager växla till den **certifikat** fliken. Certifikatet som anges på den här fliken används för att kryptera/dekryptera autentiseringsuppgifterna för lokal datalagret som du anger i portalen. Det här certifikatet är standardcertifikatet. Microsoft rekommenderar att du ändrar detta till ditt eget certifikat som du säkerhetskopierar det certifikat hanteringssystemet. Klicka på **ändra** använda ditt eget certifikat i stället.

    ![Ändra gateway-certifikat](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. (valfritt) Om du vill aktivera utförlig loggning för att felsöka problem med en gateway i det Microsoft Data Management Gateway Configuration Manager växla till den **diagnostik** och kontrollerar de **aktivera utförlig loggning i felsökningssyfte** alternativet. Loggningsinformationen om finns i Loggboken under den **program- och tjänstloggar**  - &gt; **Data Management Gateway** nod. Du kan också använda den **diagnostik** att testa anslutningen till en lokal datakälla med gatewayen.

    ![Aktivera utförlig loggning](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Detta Slutför installationsprocessen gateway i Azure Machine Learning.
Du är nu redo att använda dina lokala data.

Du kan skapa och konfigurera flera gateways i Studio för varje arbetsyta. Du kan till exempel ha en gateway som du vill ansluta till datakällor test under utveckling och en annan gateway för dina datakällor för produktion. Azure Machine Learning ger dig möjlighet att ställa in flera gateways beroende på din företagsmiljö. För närvarande kan du dela inte en gateway mellan arbetsytorna och bara en gateway kan installeras på en dator. Mer information finns i [flytta data mellan lokala källor och moln med Data Management Gateway](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Steg 2: Använda gateway för att läsa data från en lokal datakälla
När du har skapat gatewayen som du kan lägga till en **importera Data** modulen till ett experiment som anger om data från den lokala SQL Server-databasen.

1. I Machine Learning Studio, markera den **EXPERIMENT** klickar du på **+ ny** i nedre vänstra hörnet och välj **tomt Experiment** (eller välj en av flera exempel experiment tillgänglig).
2. Leta upp och dra den **importera Data** modulen till experimentarbetsytan.
3. Klicka på **Spara som** under arbetsytan. Ange ”Azure lokala SQL Server självstudie om Maskininlärning” för experimentet namnet, Välj arbetsytan och klicka på den **OK** är markerat.

   ![Spara experiment med ett nytt namn](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klickar du på den **importera Data** modulen och välj sedan, i den **egenskaper** rutan till höger om arbetsytan och välj ”lokal SQL-databas” i den **datakällan** listrutan.
5. Välj den **datagatewayen** du installerad och registrerad. Du kan ställa in en annan gateway genom att välja ”(lägga till nya Datagateway...)”.

   ![Välj datagateway för modulen importera Data](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Ange SQL **Databasservernamnet** och **databasnamnet**, tillsammans med SQL **databasfrågan** du vill köra.
7. Klicka på **ange värden** under **användarnamn och lösenord** och ange dina Databasautentiseringsuppgifter. Du kan använda Windows-integrerad autentisering eller beroende på hur dina lokala SQL Server är konfigurerat för SQL Server-autentisering.

   ![Ange autentiseringsuppgifter på databasen](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Meddelandet ”värden som krävs” ändringar i ”Ange värdena” med en grön bock. Du behöver bara ange autentiseringsuppgifter en gång om databasinformation eller lösenord ändras. Azure Machine Learning använder det certifikat som du angav när du har installerat din gateway att kryptera autentiseringsuppgifterna i molnet. Azure lagrar aldrig lokala autentiseringsuppgifter utan kryptering.

   ![Importera Data modulen egenskaper](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klicka på **kör** att köra experimentet.

När experimentet är klar kan du visualisera data du har importerat från databasen genom att klicka på utdataporten för den **importera Data** modulen och välja **visualisera**.

När du är klar med att utveckla ditt experiment kan du distribuera och driftsätta modellen. Med hjälp av Batch Execution Service data från den lokala SQL Server-databas som konfigurerats i den **importera Data** modulen läses och används för resultatfunktioner. Du kan använda tjänsten begära svar för poängsättning av lokala data, Microsoft rekommenderar att du använder den [Excel-tillägget](excel-add-in-for-web-services.md) i stället. För närvarande kan skriva till en lokal SQL Server-databasen via **exportera Data** är inte stöds antingen i experimenten eller publicerade webbtjänster.
