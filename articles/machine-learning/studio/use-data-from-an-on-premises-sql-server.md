---
title: En lokal SQLServer
titleSuffix: ML Studio (classic) - Azure
description: Använd data från en lokal SQL Server databas för att utföra avancerad analys med Azure Machine Learning Studio (klassisk).
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: conceptual
author: likebupt
ms.author: keli19
ms.custom: seodec18
ms.date: 03/13/2017
ms.openlocfilehash: 9afac1adef801956f176dd339c795e2df533a2c7
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169120"
---
# <a name="perform-analytics-with-azure-machine-learning-studio-classic-using-an-on-premises-sql-server-database"></a>Utföra analyser med Azure Machine Learning Studio (klassisk) med hjälp av en lokal SQL Server-databas

Ofta företag som fungerar med lokala data vill dra nytta av skalan och flexibilitet med molnet för sina maskininlärningsarbetsbelastningar. Men de vill inte störa deras aktuella affärsprocesser och arbetsflöden genom att flytta sina lokala data till molnet. Azure Machine Learning Studio (klassisk) har nu stöd för att läsa data från en lokal SQL Server-databas och sedan träna och värdera en modell med dessa data. Du har inte längre att manuellt kopiera och synkronisera data mellan molnet och den lokala servern. I stället kan modulen **Importera data** i Azure Machine Learning Studio (klassisk) nu läsa direkt från den lokala SQL Server databasen för dina utbildnings-och Poäng uppgifter.

Den här artikeln innehåller en översikt över hur du intränger mot lokala SQL Server-data i Azure Machine Learning Studio (klassisk). Det förutsätter att du är bekant med Studio (klassiska) begrepp som arbets ytor, moduler, data uppsättningar, experiment *osv.*

> [!NOTE]
> Den här funktionen är inte tillgänglig för kostnadsfria arbetsytor. Mer information om priser och nivåer för Machine Learning finns [Azure Machine Learning prissättning](https://azure.microsoft.com/pricing/details/machine-learning/).
>
>

<!-- -->



## <a name="install-the-data-factory-self-hosted-integration-runtime"></a>Installera Data Factory lokal Integration Runtime
Om du vill komma åt en lokal SQL Server databas i Azure Machine Learning Studio (klassisk) måste du ladda ned och installera Data Factory egen värd Integration Runtime, tidigare kallat Data Management Gateway. När du konfigurerar anslutningen i Machine Learning Studio (klassisk) har du möjlighet att ladda ned och installera Integration Runtime (IR) med hjälp av dialog rutan **Hämta och registrera data Gateway** som beskrivs nedan.


Du kan också installera IR i förväg genom att ladda ned och köra MSI-installationspaketet från [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=39717). MSI kan också användas för att uppgradera en befintlig IR till den senaste versionen, med alla inställningar bevarade.

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
* Du konfigurerar en IRs för endast en arbets yta i taget. IRs kan för närvarande inte delas mellan arbetsytor.
* Du kan konfigurera flera IRs för en enda arbetsyta. Du kanske till exempel vill använda en IR som är ansluten till dina test data källor under utveckling och en produktions-IR när du är redo att operationalisera.
* IR behöver inte finnas på samma dator som datakällan. Men att närmare datakällan minskar tid som gatewayen ska ansluta till datakällan. Vi rekommenderar att du installerar IR på en dator som skiljer sig från det som är värd för den lokala datakällan, så att gatewayen och datakällan inte konkurrerar om resurser.
* Om du redan har en IR-installation på datorn som hanterar Power BI eller Azure Data Factory scenarier installerar du en separat IR för Azure Machine Learning Studio (klassisk) på en annan dator.

  > [!NOTE]
  > Du kan inte köra Data Factory lokal Integration Runtime och Power BI Gateway på samma dator.
  >
  >
* Du måste använda Data Factory egen värd Integration Runtime för Azure Machine Learning Studio (klassisk) även om du använder Azure-ExpressRoute för andra data. Du bör hantera din datakälla som en lokal datakälla (som finns bakom en brandvägg) även när du använder ExpressRoute. Använd Data Factory lokal Integration Runtime för att upprätta en anslutning mellan Machine Learning och datakällan.

Du hittar detaljerad information om installations krav, installations steg och fel söknings tips i artikeln [integration runtime i Data Factory](../../data-factory/concepts-integration-runtime.md).

## <a name="span-idusing-the-data-gateway-step-by-step-walk-classanchorspan-id_toc450838866-classanchorspanspaningress-data-from-your-on-premises-sql-server-database-into-azure-machine-learning"></a><span id="using-the-data-gateway-step-by-step-walk" class="anchor"><span id="_Toc450838866" class="anchor"></span></span>Ingress data från din lokala SQL Server-databas till Azure Machine Learning
I den här genom gången ska du konfigurera ett Azure Data Factory Integration Runtime på en Azure Machine Learning arbets yta, konfigurera den och sedan läsa data från en lokal SQL Server databas.

> [!TIP]
> Innan du börjar inaktiverar du webbläsarens blockering av popup-fönster för `studio.azureml.net`. Om du använder Google Chrome-webbläsaren kan du ladda ned och installera en av de många plugin-program som är tillgängliga i Google Chrome WebStore [Klicka en gång till app-tillägget](https://chrome.google.com/webstore/search/clickonce?_category=extensions).
>
> [!NOTE]
> Azure Data Factory lokal Integration Runtime kallades tidigare Data Management Gateway. Stegvisa självstudier fortsätter att referera till den som en gateway.  

### <a name="step-1-create-a-gateway"></a>Steg 1: Skapa en gateway
Det första steget är att skapa och konfigurera gatewayen för att få åtkomst till din lokala SQL-databas.

1. Logga in på [Azure Machine Learning Studio (klassisk)](https://studio.azureml.net/Home/) och välj den arbets yta som du vill arbeta i.
2. Klicka på bladet **Inställningar** till vänster och klicka sedan på fliken **datagatewayer** överst.
3. Klicka på **ny datagateway** längst ned på skärmen.

    ![Ny personlig Gateway](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-button.png)
4. I dialog rutan **ny datagateway** anger du **Gateway-namnet** och lägger eventuellt till en **Beskrivning**. Klicka på pilen i det nedre högra hörnet att gå till nästa steg i konfigurationen.

    ![Ange gatewaynamn och beskrivning](./media/use-data-from-an-on-premises-sql-server/new-data-gateway-dialog-enter-name.png)
5. I dialogrutan ladda ned och registrera data gateway du kopiera REGISTRERINGSNYCKEL GATEWAY till Urklipp.

    ![Ladda ned och registrera datagateway](./media/use-data-from-an-on-premises-sql-server/download-and-register-data-gateway.png)
6. <span id="note-1" class="anchor"></span>Om du ännu inte har laddat ned och installerat Microsoft Data Management Gateway klickar du på **Hämta data Management Gateway**. Detta tar dig till Microsoft Download Center där du kan välja den gateway-version som du behöver ladda ned den och installera den. Du hittar detaljerad information om installations krav, installations steg och fel söknings tips i de inledande avsnitten i artikeln [Flytta data mellan lokala källor och molnet med data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).
7. När gatewayen har installerats öppnas Data Management Gateway Configuration Manager och dialog rutan **Registrera Gateway** visas. Klistra in **nyckeln för gateway-registreringen** som du kopierade till Urklipp och klicka på **Registrera**.
8. Om du redan har en installerad gateway kan köra Data Management Gateway Configuration Manager. Klicka på **ändra nyckel**, klistra in den **Gateway-registreringsnyckeln** som du kopierade till Urklipp i föregående steg och klicka på **OK**.
9. När installationen är klar visas dialog rutan **Registrera Gateway** för Microsoft Data Management Gateway Configuration Manager. Klistra in nyckeln för GATEWAY-registreringen som du kopierade till Urklipp i föregående steg och klicka på **Registrera**.

    ![Registrera gateway](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-register-gateway.png)
10. Gateway-konfigurationen är slutförd när följande värden anges på fliken **Start** i Microsoft Data Management Gateway Configuration Manager:

    * **Gateway-namn** och **instans namn** anges till namnet på gatewayen.
    * **Registreringen** har angetts till **registrerad**.
    * **Status** är inställt på **startad**.
    * Statusfältet längst ned visar **anslutna till data Management Gateway moln tjänst** tillsammans med en grön bock markering.

      ![Data Management Gateway Manager](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-registered.png)

      Azure Machine Learning Studio (klassisk) uppdateras också när registreringen är klar.

    ![Lyckade gateway-registrering](./media/use-data-from-an-on-premises-sql-server/gateway-registered.png)
11. I dialog rutan **Ladda ned och registrera datagateway** klickar du på kryss markeringen för att slutföra installationen. På sidan **Inställningar** visas Gateway-status som "online". Du hittar i den högra rutan, status och annan användbar information.

    ![Gateway-inställningar](./media/use-data-from-an-on-premises-sql-server/gateway-status.png)
12. I Microsoft Data Management Gateway-Configuration Manager växlar du till fliken **certifikat** . Det certifikat som anges på den här fliken används för att kryptera/dekryptera autentiseringsuppgifter för det lokala data lager som du anger i portalen. Det här certifikatet är standardcertifikatet. Microsoft rekommenderar att du ändrar detta till ditt eget certifikat som du säkerhetskopierar det certifikat hanteringssystemet. Klicka på **ändra** om du vill använda ditt eget certifikat i stället.

    ![Ändra gateway-certifikatet](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-certificate.png)
13. valfritt Om du vill aktivera utförlig loggning för att felsöka problem med gatewayen, i Microsoft Data Management Gateway Configuration Manager växlar du till fliken **diagnostik** och markerar alternativet **Aktivera utförlig loggning för fel sökning** . Du hittar loggnings informationen i Windows-Loggboken under noden **program-och tjänst loggar** -&gt; **Data Management Gateway** noden. Du kan också använda fliken **diagnostik** för att testa anslutningen till en lokal data källa med hjälp av gatewayen.

    ![Aktivera utförlig loggning](./media/use-data-from-an-on-premises-sql-server/data-gateway-configuration-manager-verbose-logging.png)

Detta slutför installations processen för gateway i Azure Machine Learning Studio (klassisk).
Du är nu redo att använda dina lokala data.

Du kan skapa och konfigurera flera gateways i Studio (klassisk) för varje arbets yta. Du kan till exempel ha en gateway som du vill ansluta till dina datakällor för test under utvecklingen och en annan gateway för dina datakällor för produktion. Azure Machine Learning Studio (klassisk) ger dig flexibiliteten att konfigurera flera gateways beroende på din företags miljö. För närvarande du kan inte dela en gateway mellan arbetsytor och endast en gateway kan installeras på en dator. Mer information finns i [Flytta data mellan lokala källor och molnet med data Management Gateway](../../data-factory/tutorial-hybrid-copy-portal.md).

### <a name="step-2-use-the-gateway-to-read-data-from-an-on-premises-data-source"></a>Steg 2: Använda gatewayen för att läsa data från en lokal datakälla
När du har konfigurerat gatewayen kan du lägga till en modul för att **Importera data** till ett experiment som indata från den lokala SQL Server databasen.

1. Välj fliken **experiment** i Machine Learning Studio (klassisk), klicka på **+ ny** i det nedre vänstra hörnet och välj **Tom experiment** (eller Välj ett av flera exempel experiment som är tillgängliga).
2. Leta upp och dra modulen **Importera data** till experimentets arbets yta.
3. Klicka på **Spara som** under arbets ytan. Ange "Azure Machine Learning Studio (klassisk) lokal SQL Server självstudie" för experimentets namn, Välj arbets ytan och klicka på kryss markeringen **OK** .

   ![Spara experiment med ett nytt namn](./media/use-data-from-an-on-premises-sql-server/experiment-save-as.png)
4. Klicka på modulen **Importera data** för att markera den. i rutan **Egenskaper** till höger om arbets ytan väljer du lokal SQL Database i list rutan **data källa** .
5. Välj den **datagateway** som du har installerat och registrerat. Du kan ställa in en annan gateway genom att välja ”(Lägg till ny personlig Gateway...)”.

   ![Välj datagateway för modulen importera Data](./media/use-data-from-an-on-premises-sql-server/import-data-select-on-premises-data-source.png)
6. Ange SQL **Database-servernamnet** och **databas namnet**, tillsammans med den SQL **Database-fråga** som du vill köra.
7. Klicka på **Ange värden** under **användar namn och lösen ord** och ange autentiseringsuppgifterna för databasen. Du kan använda Windows-integrerad autentisering eller SQL Server-autentisering beroende på hur din lokala SQL Server är konfigurerat.

   ![Ange autentiseringsuppgifterna för databasens](./media/use-data-from-an-on-premises-sql-server/database-credentials.png)

   Meddelandet ”values krävs” ändringarna (värden set) med en grön bockmarkering. Du behöver bara ange autentiseringsuppgifterna som en gång, såvida inte databasinformation eller lösenord ändras. Azure Machine Learning Studio (klassisk) använder det certifikat du angav när du installerade gatewayen för att kryptera autentiseringsuppgifterna i molnet. Azure lagrar aldrig lokala autentiseringsuppgifter utan kryptering.

   ![Importera modulen dataegenskaper](./media/use-data-from-an-on-premises-sql-server/import-data-properties-entered.png)
8. Klicka på **Kör** för att köra experimentet.

När experimentet har körts kan du visualisera data som du har importerat från databasen genom att klicka på utdataporten för modulen **Importera data** och välja **visualisera**.

När du är klar med att utveckla ditt experiment kan du distribuera och driftsätta modellen. Med batch-körnings tjänsten kommer data från den lokala SQL Server-databasen som kon figurer ATS i modulen **Importera data** att läsas och användas för poängsättning. Även om du kan använda tjänsten Request Response för att värdera lokala data, rekommenderar Microsoft att du använder [Excel-tillägget](excel-add-in-for-web-services.md) i stället. För närvarande stöds inte att skriva till en lokal SQL Server-databas via **export data** , varken i experimentet eller de publicerade webb tjänsterna.
