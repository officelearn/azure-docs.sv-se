---
title: Anpassa SQL-informationsskydd – Azure Security Center
description: Lär dig hur du anpassar principer för informationsskydd i Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/29/2019
ms.author: memildin
ms.openlocfilehash: 9c776a32b4a35c72fc40a16afb87db9896a763cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75611074"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Anpassa SQL-informationsskyddsprincipen i Azure Security Center (förhandsversion)
 
Du kan definiera och anpassa en SQL-informationsskyddsprincip för hela Azure-klienten i Azure Security Center.

Informationsskydd är en avancerad säkerhetsfunktion för att upptäcka, klassificera, etikettera och rapportera känsliga data i dina Azure-dataresurser. Att upptäcka och klassificera dina mest känsliga data (företag, ekonomi, hälso- och sjukvård, personuppgifter osv.) kan spela en central roll i din organisationsinformationsskyddsstatus. Tjänsten kan fungera som infrastruktur inom följande områden:
- Hjälper till att uppfylla standarder för datasekretess och efterlevnadskrav för föreskrifter
- Säkerhetsscenarier som övervakning (granskning) och aviseringar om avvikande åtkomst till känsliga data
- Styra åtkomsten till och härdning av datalagers säkerhet som innehåller mycket känsliga data
 
[SQL Information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementerar det här paradigmet för dina SQL-datalager, som för närvarande stöds för Azure SQL Database. SQL Information Protection identifierar och klassificerar automatiskt potentiellt känsliga data, tillhandahåller en märkningsmekanism för att ständigt tagga känsliga data med klassificeringsattribut och ger en detaljerad instrumentpanel som visar klassificeringstillståndet för databasen. Dessutom beräknas resultatuppsättningens känslighet för SQL-frågor, så att frågor som extraherar känsliga data uttryckligen kan granskas och data kan skyddas. Mer information om SQL-informationsskydd finns i [Azure SQL Database Data Discovery and Classification](../sql-database/sql-database-data-discovery-and-classification.md).
 
Klassificeringsmekanismen baseras på två primära konstruktioner som utgör klassificeringstaxonomin - **etiketter** och **informationstyper**.
- **Etiketter** – De viktigaste klassificeringsattributen, som används för att definiera känslighetsnivån för de data som lagras i kolumnen. 
- **Informationstyper** – Ger ytterligare granularitet till den typ av data som lagras i kolumnen.
 
Informationsskydd levereras med en inbyggd uppsättning etiketter och informationstyper som används som standard. Om du vill anpassa dessa etiketter och typer kan du anpassa informationsskyddsprincipen i Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Anpassa informationsskyddsprincipen
Om du vill anpassa informationsskyddsprincipen för din Azure-klient måste du ha [administratörsbehörighet i klientens rothanteringsgrupp](security-center-management-groups.md). 
 
1. På huvudmenyn för Säkerhetscenter går du till **Data & lagring** under **RESURSSÄKERHETSHYGIEN** och klickar på **SQL-informationsskyddsknappen.**

   ![Konfigurera princip för informationsskydd](./media/security-center-info-protection-policy/security-policy.png) 
 
2. På sidan **SQL Information Protection** kan du visa den aktuella uppsättningen etiketter. Det här är de viktigaste klassificeringsattributen som används för att kategorisera känslighetsnivån för dina data. Härifrån kan du konfigurera **informationsskyddsetiketterna** och **informationstyperna** för klienten. 
 
### <a name="customizing-labels"></a>Anpassa etiketter
 
1. Du kan redigera eller ta bort en befintlig etikett eller lägga till en ny etikett. Om du vill redigera en befintlig etikett markerar du den etiketten och klickar sedan på **Konfigurera**, antingen högst upp eller på snabbmenyn till höger. Om du vill lägga till en ny etikett klickar du på **Skapa etikett** i den övre menyraden eller längst ned i etiketttabellen.
2. På skärmen **Konfigurera känslighetsetikett** kan du skapa eller ändra etikettnamnet och beskrivningen. Du kan också ange om etiketten är aktiv eller inaktiverad genom att växla **aktiverad** eller inaktiverad. Slutligen kan du lägga till eller ta bort informationstyper som är associerade med etiketten. Alla data som upptäcks som matchar den informationstypen kommer automatiskt att inkludera den associerade känslighetsetiketten i klassificeringsrekommendationerna.
3. Klicka på **OK**.
 
   ![Konfigurera känslighetsetikett](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etiketterna visas i ordning efter stigande känslighet. Om du vill ändra rangordningen mellan etiketterna drar du etiketterna för att ändra ordning på dem i tabellen eller använder knapparna **Flytta upp** och **Flytta nedåt** för att ändra ordningen. 
 
    ![Konfigurera princip för informationsskydd](./media/security-center-info-protection-policy/move-up.png)
 
5. Var noga med att klicka på **Spara** högst upp på skärmen när du är klar.
 
 
## <a name="adding-and-customizing-information-types"></a>Lägga till och anpassa informationstyper
 
1. Du kan hantera och anpassa informationstyper genom att klicka på **Hantera informationstyper**.
2. Om du vill lägga till en ny **informationstyp**väljer du **Skapa informationstyp** på den övre menyn. Du kan konfigurera ett namn, en beskrivning och en sökmönstersträng för **typen Information**. Sökmönstersträngar kan eventuellt använda nyckelord med jokertecken (med tecknet %'), som den automatiserade identifieringsmotorn använder för att identifiera känsliga data i dina databaser, baserat på kolumnernas metadata.
 
    ![Konfigurera princip för informationsskydd](./media/security-center-info-protection-policy/info-types.png)
 
3. Du kan också konfigurera de inbyggda **informationstyperna** genom att lägga till ytterligare sökmönstersträngar, inaktivera några av de befintliga strängarna eller genom att ändra beskrivningen. Du kan inte ta bort inbyggda **informationstyper** eller redigera deras namn. 
4. **Informationstyper** visas i ordning efter stigande identifieringsrankning, vilket innebär att de typer som är högre i listan försöker matcha först. Om du vill ändra rangordningen mellan informationstyper drar du typerna till rätt plats i tabellen eller använder knapparna **Flytta upp** och **Flytta nedåt** för att ändra ordningen. 
5. Klicka på **OK** när du är klar.
6. När du har hanterat dina informationstyper måste du associera de relevanta typerna med de relevanta etiketterna genom att klicka på **Konfigurera** för en viss etikett och lägga till eller ta bort informationstyper efter behov.
7. Var noga med att klicka på **Spara** i **huvudbladet Etiketter** för att tillämpa alla dina ändringar.
 
När din informationsskyddsprincip har definierats och sparats gäller den för klassificering av data på alla Azure SQL-databaser i din klientorganisation.
 
 
## <a name="next-steps"></a>Nästa steg
 
I den här artikeln lärde du dig mer om att definiera en SQL-informationsskyddsprincip i Azure Security Center. Mer information om hur du använder SQL Information Protection för att klassificera och skydda känsliga data i dina SQL-databaser finns i [Azure SQL Database Data Discovery and Classification](../sql-database/sql-database-data-discovery-and-classification.md). 

Mer information om säkerhetsprinciper och datasäkerhet i Azure Security Center finns i följande artiklar:
 
- [Ange säkerhetsprinciper i Azure Security Center:](tutorial-security-policy.md)Lär dig hur du konfigurerar säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper
- [Datasäkerhet i Azure Security Center](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data
