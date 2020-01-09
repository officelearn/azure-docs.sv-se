---
title: Anpassa SQL information Protection – Azure Security Center
description: Lär dig hur du anpassar information Protection-principer i Azure Security Center.
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
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/02/2020
ms.locfileid: "75611074"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Anpassa SQL information Protection-principen i Azure Security Center (för hands version)
 
Du kan definiera och anpassa en princip för SQL information Protection för hela Azure-klienten i Azure Security Center.

Information Protection är en avancerad säkerhets funktion för att upptäcka, klassificera, märka och rapportera känsliga data i dina Azure-dataresurser. Att upptäcka och klassificera dina mest känsliga data (affärs-, finans-, sjukvårds-och person uppgifter osv.) kan spela en pivot-roll i din organisations informations skydds datasekretesstandarder. Tjänsten kan fungera som infrastruktur inom följande områden:
- Hjälpa till att uppfylla data integritets standarder och krav för regelefterlevnad
- Säkerhets scenarier som övervakning (granskning) och aviseringar vid avvikande åtkomst till känsliga data
- Kontrol lera åtkomst till och härdning av säkerheten för data lager som innehåller mycket känsliga data
 
[Sql information Protection](../sql-database/sql-database-data-discovery-and-classification.md) implementerar den här paradigmen för dina SQL-datalager, som för närvarande stöds för Azure SQL Database. SQL Information Protection identifierar och klassificerar automatiskt potentiellt känsliga data, innehåller en etikett för att permanent tagga känsliga data med klassificerings attribut, och innehåller en detaljerad instrument panel som visar databasens klassificerings tillstånd. Dessutom beräknar den resultat uppsättnings känslighet för SQL-frågor, så att frågor som extraherar känsliga data kan granskas explicit och att data kan skyddas. Mer information om SQL-Information Protection finns i [Azure SQL Database identifiering och klassificering av data](../sql-database/sql-database-data-discovery-and-classification.md).
 
Klassificerings mekanismen baseras på två primära konstruktioner som utgör klassificeringens taxonomier, **Etiketter** och **informations typer**.
- **Etiketter** – huvudattributen för klassificering som används för att definiera känslighets nivån för de data som lagras i kolumnen. 
- **Informations typer** – ger ytterligare detaljerad information om vilken typ av data som lagras i kolumnen.
 
Information Protection levereras med en inbyggd uppsättning etiketter och informations typer som används som standard. Om du vill anpassa dessa etiketter och typer kan du anpassa information Protection-principen i Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Anpassa informationsskyddsprincipen
Om du vill anpassa information Protection-principen för din Azure-klient måste du ha [administratörs behörighet för klient organisationens rot hanterings grupp](security-center-management-groups.md). 
 
1. **Gå till** **data & lagring** på Security Center huvud menyn och klicka på knappen **SQL-information Protection** .

   ![Konfigurera information Protection-princip](./media/security-center-info-protection-policy/security-policy.png) 
 
2. På sidan **SQL-information Protection** kan du Visa din aktuella uppsättning etiketter. Detta är de viktigaste klassificerings attributen som används för att kategorisera data känslighets nivån. Härifrån kan du konfigurera **Information Protection-etiketter** och **informations typer** för klient organisationen. 
 
### <a name="customizing-labels"></a>Anpassa etiketter
 
1. Du kan redigera eller ta bort en befintlig etikett eller lägga till en ny etikett. Om du vill redigera en befintlig etikett väljer du etiketten och klickar sedan på **Konfigurera**, antingen överst eller på snabb menyn till höger. Om du vill lägga till en ny etikett klickar du på **skapa etikett** i den översta meny raden eller längst ned i tabellen etiketter.
2. På skärmen **Konfigurera känslighets etikett** kan du skapa eller ändra etikett namnet och beskrivningen. Du kan också ange om etiketten är aktiv eller inaktive rad genom att aktivera eller inaktivera den **aktiverade** växeln. Slutligen kan du lägga till eller ta bort informations typer som är kopplade till etiketten. Alla data som har identifierats som matchar den informations typen kommer automatiskt att inkludera den tillhör ande känslighets etiketten i klassificerings rekommendationerna.
3. Klicka på **OK**.
 
   ![Konfigurera känslighets etikett](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etiketter visas i stigande känslighets ordning. Om du vill ändra rangordningen mellan etiketter drar du etiketterna för att ändra ordning på dem i tabellen, eller Använd knapparna **Flytta upp** och **Flytta ned** för att ändra ordningen. 
 
    ![Konfigurera information Protection-princip](./media/security-center-info-protection-policy/move-up.png)
 
5. Se till att klicka på **Spara** längst upp på skärmen när du är färdig.
 
 
## <a name="adding-and-customizing-information-types"></a>Lägga till och anpassa informations typer
 
1. Du kan hantera och anpassa informations typer genom att klicka på **Hantera informations typer**.
2. Om du vill lägga till en ny **informations typ**väljer du **skapa informations typ** på den översta menyn. Du kan konfigurera ett namn, en beskrivning och Sök efter mönster strängar för **informations typen**. Sök mönster strängar kan också använda nyckelord med jokertecken (med tecknet%) som den automatiserade identifierings motorn använder för att identifiera känsliga data i dina databaser, baserat på kolumnernas metadata.
 
    ![Konfigurera information Protection-princip](./media/security-center-info-protection-policy/info-types.png)
 
3. Du kan också konfigurera inbyggda **informations typer** genom att lägga till fler Sök mönster strängar, inaktivera några av de befintliga strängarna eller genom att ändra beskrivningen. Du kan inte ta bort inbyggda **informations typer** eller redigera deras namn. 
4. **Informations typer** visas i ordning efter stigande identifierings rangordning, vilket innebär att typerna som är högre i listan försöker matcha först. Om du vill ändra rangordningen mellan informations typer drar du typerna till rätt plats i tabellen eller använder knapparna **Flytta upp** och **Flytta ned** för att ändra ordningen. 
5. Klicka på **OK** när du är färdig.
6. När du har slutfört hanteringen av dina informations typer måste du associera de relevanta typerna med relevanta etiketter, genom att klicka på **Konfigurera** för en viss etikett och lägga till eller ta bort informations typer efter behov.
7. Se till att klicka på **Spara** i bladet med huvud **Etiketter** för att tillämpa alla ändringar.
 
När din information Protection-princip är fullständigt definierad och Sparad, kommer den att gälla för klassificering av data på alla Azure SQL-databaser i din klient organisation.
 
 
## <a name="next-steps"></a>Nästa steg
 
I den här artikeln har du lärt dig hur du definierar en princip för SQL-Information Protection i Azure Security Center. Mer information om hur du använder SQL-Information Protection för att klassificera och skydda känsliga data i dina SQL-databaser finns i [Azure SQL Database identifiering och klassificering av data](../sql-database/sql-database-data-discovery-and-classification.md). 

Mer information om säkerhets principer och data säkerhet i Azure Security Center finns i följande artiklar:
 
- [Ställa in säkerhets principer i Azure Security Center](tutorial-security-policy.md): Lär dig hur du konfigurerar säkerhets principer för dina Azure-prenumerationer och resurs grupper
- [Azure Security Center data säkerhet](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data
