---
title: Azure SQL-databasinformation Discovery & klassificering | Microsoft Docs
description: Azure SQL-databasinformation Discovery & klassificering
services: sql-database
documentationcenter: 
author: giladm
manager: shaik
ms.reviewer: carlrab
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: security
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: giladm
ms.openlocfilehash: e6b9b5e497258f37037e0dc3f8efe656d5206af3
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/09/2018
---
# <a name="azure-sql-database-data-discovery-and-classification"></a>Azure SQL-databasinformation identifiering och klassificering
Identifiering av data & klassificering (för närvarande i förhandsversion) ger avancerade funktioner finns inbyggda i Azure SQL Database för **identifiera**, **klassificera**, **etiketter**  &  **skyddar** känsliga data i databasen.
Identifiera och klassificera dina största känsliga data (företag för finansiella, hälsovård, personligt identifierbar information, etc.) kan en nyckelroll i din organisation Information protection status. Den kan fungera som infrastrukturen för:
* Olika säkerhetsscenarier, till exempel övervakning (granskning) och aviseringar om avvikande tillgång till känsliga data.
* Kontrollera åtkomst till och Härdning av säkerheten för databaser som innehåller känsliga data.
* Hjälper att uppfylla data sekretess standarder och regelefterlevnad krav, t.ex BNPR.

## <a id="subheading-1"></a>Översikt över
Identifiering av data & klassificering innehåller en uppsättning avancerade tjänster och nya SQL-funktioner, som utgör en ny SQL-informationsskydd paradigmet syftar till att skydda data, inte bara databasen:
* **Discovery & rekommendationer** – klassificering motorn genomsöker din databas och identifierar kolumner som innehåller potentiellt känsliga data. Det ger dig ett enkelt sätt att granska och Använd korrekt klassificering rekommendationer via Azure portal.
* **Etiketter** – känslighet klassificeringsetiketter taggas beständigt på kolumner med hjälp av nya klassificeringen metadataattribut som introducerades i SQL-motorn. Dessa metadata kan sedan användas för avancerade känslighet-baserade gransknings- och skydd scenarier.
* **Frågeresultatet känslighet** – känslighet frågeresultatet beräknas i realtid för granskningsändamål.
* **Synlighet** -klassificering databastillståndet kan visas i en detaljerad instrumentpanel i portalen. Dessutom kan du hämta en rapport (i Excel-format) som ska användas för efterlevnad och granskning, samt andra behov.

## <a id="subheading-2"></a>Identifiering, klassificering och etikettering känsliga kolumner
I följande avsnitt beskrivs stegen för att identifiera, klassificera och etiketter kolumner som innehåller känsliga data i din databas, samt visa aktuell status för klassificering av databasen och export av rapporter.

Klassificeringen innehåller två metadataattribut:
* Etiketter – huvudsakliga klassificering-attribut används för att definiera Känslighetsnivån för data som lagras i kolumnen.  
* Informationstyper – ange ytterligare granularitet till vilken typ av data som lagras i kolumnen.

<br>
**Att klassificera din SQL-databas:**

1. Gå till [Azure-portalen](https://portal.azure.com).

2. Navigera till den **Data discovery & klassificering (förhandsgranskning)** i SQL-databasen.

    ![Navigeringsfönstret][1]

3. Den **översikt** innehåller en sammanfattning av det aktuella tillståndet för klassificering av databasen, inklusive en detaljerad lista över alla klassificerad kolumner som du kan också filtrera för att visa endast specifika schemat delar, informationstyper och etiketter. Om du ännu inte har klassificerats några kolumner [vidare till steg 5](#step-5).

    ![Navigeringsfönstret][2]

4. Om du vill hämta en rapport i Excel-format, klicka på den **exportera** alternativet i den översta menyn i fönstret.

    ![Navigeringsfönstret][3]

5.  <a id="step-5"></a>Påbörja klassificera dina data genom att klicka på den **klassificering fliken** längst upp i fönstret.

    ![Navigeringsfönstret][4]

6. Klassificering motorn söker igenom din databas för kolumner som innehåller potentiellt känsliga data och visar en lista över **rekommenderade kolumnen klassificeringar**. Visa och tillämpa klassificering rekommendationer:

    * Klicka på panelen rekommendationer längst ned i fönstret om du vill visa listan över rekommenderade kolumnen klassificeringar:

        ![Navigeringsfönstret][5]

    * Granska listan med rekommendationer – för att godkänna en rekommendation för en viss kolumn, markera kryssrutan i den vänstra kolumnen i den aktuella raden. Du kan också markera *alla rekommendationer* som accepterad genom att markera kryssrutan i tabellrubriken rekommendationer.

        ![Navigeringsfönstret][6]

    * Om du vill använda de valda rekommendationerna, klicka på blå **acceptera valda rekommendationer** knappen.

        ![Navigeringsfönstret][7]

7. Du kan också **manuellt klassificera** kolumner som ett alternativ eller dessutom rekommendation-baserad klassificering:

    * Klicka på **lägger till klassificeringen** på huvudmenyn i fönstret.

        ![Navigeringsfönstret][8]

    * I kontexten-fönstret som öppnas väljer du schemat > Tabell > kolumn som du vill klassificera och information typ och känslighet etiketten. Klicka på blå **lägger till klassificeringen** längst ned i fönstret kontext.

        ![Navigeringsfönstret][9]

8. Klicka på Slutför din klassificering och beständigt etikett (tagg) databasen kolumner med nya metadata för klassificering, **spara** på huvudmenyn i fönstret.

    ![Navigeringsfönstret][10]

## <a id="subheading-3"></a>Granska åtkomst till känsliga data

En viktig del av information protection paradigmet är möjligheten att övervaka åtkomsten till känsliga data. [Azure SQL Database Auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) har förbättrats så att ett nytt fält i granskningsloggen kallas *data_sensitivity_information*, som loggar klassificeringen känslighet (etiketter) på data som returnerades av frågan.

![Navigeringsfönstret][11]

## <a id="subheading-4"></a>Nästa steg
Överväg att konfigurera [Azure SQL Database Auditing](https://docs.microsoft.com/en-us/azure/sql-database/sql-database-auditing) för övervakning och granskning av åtkomst till dina klassificerad känsliga data.

<!--Anchors-->
[SQL Data Discovery & Classification overview]: #subheading-1
[Discovering, classifying & labeling sensitive columns]: #subheading-2
[Auditing access to sensitive data]: #subheading-3
[Next Steps]: #subheading-4

<!--Image references-->
[1]: ./media/sql-data-discovery-and-classification/1_data_classification_settings_menu.png
[2]: ./media/sql-data-discovery-and-classification/2_data_classification_overview_dashboard.png
[3]: ./media/sql-data-discovery-and-classification/3_data_classification_export_report.png
[4]: ./media/sql-data-discovery-and-classification/4_data_classification_classification_tab_click.png
[5]: ./media/sql-data-discovery-and-classification/5_data_classification_recommendations_panel.png
[6]: ./media/sql-data-discovery-and-classification/6_data_classification_recommendations_list.png
[7]: ./media/sql-data-discovery-and-classification/7_data_classification_accept_selected_recommendations.png
[8]: ./media/sql-data-discovery-and-classification/8_data_classification_add_classification_button.png
[9]: ./media/sql-data-discovery-and-classification/9_data_classification_manual_classification.png
[10]: ./media/sql-data-discovery-and-classification/10_data_classification_save.png
[11]: ./media/sql-data-discovery-and-classification/11_data_classification_audit_log.png
