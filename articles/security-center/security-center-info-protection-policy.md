---
title: Anpassa SQL information protection-principen i Azure Security Center | Microsoft Docs
description: Lär dig hur du anpassar principer för informationsskydd i Azure Security Center.
services: security-center
documentationcenter: na
author: rkarlin
manager: barbkess
editor: ''
ms.assetid: 2ebf2bc7-232a-45c4-a06a-b3d32aaf2500
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/20/2018
ms.author: rkarlin
ms.openlocfilehash: 9b63fb963408b8f22453c7ea78e36a49402273a7
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/12/2019
ms.locfileid: "56105730"
---
# <a name="customize-the-sql-information-protection-policy-in-azure-security-center-preview"></a>Anpassa SQL information protection-principen i Azure Security Center (förhandsversion)
 
En SQL information protection-princip kan definieras och som är anpassad efter din hela Azure-klient i Azure Security Center.

Information protection är en avancerad säkerhetsfunktion för identifiering, klassificering, märkning och skydd av känsliga data i resurserna i Azure data. Identifiera och klassificera dina mest känsliga data (business, ekonomi, hälsovård, personligt identifierbar information, etc.) kan spela upp en central roll i din organisations information protection datasekretesstandarder. Det kan fungera som en infrastruktur för:
- Hjälpa uppfylla data sekretesstandarder och efterlevnadskrav
- Olika säkerhetsscenarier, till exempel övervakning (granskning) och Varna vid avvikande åtkomsten till känsliga data
- Kontrollera åtkomst till och Härdning av säkerheten för data lagras som innehåller mycket känsliga data
 
[SQL-informationsskydd](../sql-database/sql-database-data-discovery-and-classification.md) implementerar den här paradigm för dina SQL-datalager som stöds för närvarande för Azure SQL Database. SQL-informationsskydd automatiskt identifierar och klassificerar potentiellt känsliga data är en etikettering mekanism för beständigt tagga känsliga data med klassificering attribut och ger en detaljerad instrumentpanel som visar den klassificering tillståndet för databasen. Dessutom beräknas resultatet känslighet för SQL-frågor, så att frågor som hämtar känsliga data kan granskas uttryckligen och data kan skyddas. Mer information om SQL Information Protection finns i [Azure SQL Database-Dataidentifiering och klassificering](../sql-database/sql-database-data-discovery-and-classification.md).
 
Mekanismen för klassificering är baserad på två primära konstruktionerna som utgör klassificering taxonomi - **etiketter** och **informationstyper**.
- **Etiketter** – attributen huvudsakliga klassificering används för att definiera Känslighetsnivån för de data som lagras i kolumnen. 
- **Informationstyper** – tillhandahåller ytterligare granularitet i vilken typ av data som lagras i kolumnen.
 
Information Protection levereras med en inbyggd uppsättning etiketter och typer av information som används som standard. För att anpassa dessa, kan du anpassa information protection-principen i Azure Security Center.
 
## <a name="customize-the-information-protection-policy"></a>Anpassa informationsskyddsprincipen
Om du vill anpassa information protection-principen för din Azure-klient, måste du ha [administratörsbehörighet på klientens rot-hanteringsgruppen](security-center-management-groups.md). 
 
1. I huvudmenyn i Security Center väljer **säkerhetsprincip**.
2. Välj **hierarkisk vy (förhandsgranskning)**, och sedan under **klient rot grupp**, klickar du på **redigera inställningar för**.
 
   ![Konfigurera Information protection-principen](./media/security-center-info-protection-policy/security-policy.png) 
 
3. Under **principkomponenter**, klickar du på **informationsskydd**. I den **Information protection-inställningar** kan du visa din aktuella uppsättningen etiketter. Det här är de viktigaste klassificering-attribut som används för att kategorisera Känslighetsnivån av dina data. Härifrån kan du konfigurera den **Information protection-etiketter** och **informationstyper** för klienten. 
 
### <a name="customizing-labels"></a>Anpassa etiketter
 
1. Du kan redigera eller ta bort alla befintliga etiketten eller lägga till en ny etikett. Om du vill redigera en befintlig etikett, väljer du den etiketten och klicka sedan på **konfigurera**, antingen överst eller från snabbmenyn som visas till höger. Lägg till en ny etikett, klicka på **skapa etikett** i den övre menyraden eller längst ned i tabellen etiketter.
2. I den **konfigurera känslighetsetikett** skärmen, du kan skapa eller ändra Etikettnamnet och beskrivningen. Du kan också ange om etiketten är aktiv eller inaktiverad genom att ändra den **aktiverad** växla eller inaktivera. Slutligen kan du lägga till eller ta bort typer av information som är associerade med etiketten. Alla data som identifierats som matchar att informationstypen läggs det automatiskt till den associerade känslighetsetikett i klassificeringsrekommendationer.
3. Klicka på **OK**.
 
   ![Konfigurera känslighetsetikett](./media/security-center-info-protection-policy/config-sensitivity-label.png)
 
4. Etiketter är listade i stigande känslighet. Om du vill ändra rangordning mellan etiketter, drar du etiketter för att ordna om dem i tabellen, eller använda den **Flytta upp** och **Flytta ned** knappar för att ändra ordning. 
 
    ![Konfigurera Information protection-principen](./media/security-center-info-protection-policy/move-up.png)
 
5. Se till att klicka på **spara** överst på skärmen när du är klar.
 
 
## <a name="adding-and-customizing-information-types"></a>Lägga till och anpassa informationstyper
 
1. Du kan hantera och anpassa informationstyper genom att klicka på **hantera informationstyper**.
2. Att lägga till en ny **informationstypen**väljer **skapa informationstypen** på den översta menyn. Du kan konfigurera ett namn, beskrivning och söksträngar mönstret för den **informationstypen**. Söka efter mönster strängar kan du kan också använda nyckelord med jokertecken (med tecknet '%'), som automatiserade identifieringsmotor använder för att identifiera känsliga data i dina databaser, baserat på de kolumner metadata.
 
    ![Konfigurera Information protection-principen](./media/security-center-info-protection-policy/info-types.png)
 
3. Du kan också konfigurera inbyggt **informationstyper** genom att lägga till ytterligare mönstret söksträngar, inaktivera några av de befintliga strängarna, eller genom att ändra beskrivningen. Du kan inte ta bort inbyggda **informationstyper** eller redigera deras namn. 
4. **Informationstyper** är listade i stigande identifiering rangordning, vilket innebär att typerna som är högre upp i listan ska försöka matcha först. Dra typerna till rätt plats i tabellen om du vill ändra rangordning mellan typer av information, eller Använd den **Flytta upp** och **Flytta ned** knappar för att ändra ordning. 
5. Klicka på **OK** när du är klar.
6. När du slutfört hantera typer av information, måste du associera de relevanta typerna med relevanta etiketterna genom att klicka på **konfigurera** för en viss etikett och lägga till eller ta bort informationstyper efter behov.
7. Se till att klicka på **spara** i huvudsakliga **etiketter** bladet för att tillämpa ändringarna.
 
När din Information protection-princip är fullständigt definierat och sparat, gäller den klassificering av data på alla Azure SQL-databaser i din klient.
 
 
## <a name="next-steps"></a>Nästa steg
 
I den här artikeln har du lärt dig om hur du definierar en SQL Information Protection-principen i Azure Security Center. Mer information om hur du använder SQL-informationsskydd för att klassificera och skydda känsliga data i SQL-databaserna finns [Azure SQL Database-Dataidentifiering och klassificering](../sql-database/sql-database-data-discovery-and-classification.md). 

Mer information om säkerhetsprinciper och datasäkerhet i Azure Security Center finns i följande artiklar:
 
- [Översikt över säkerhetsprinciper](security-center-policies-overview.md): Få en översikt över säkerhetsprinciper i Security Center
- [Ange säkerhetsprinciper i Azure Security Center](tutorial-security-policy.md): Lär dig hur du ställer in säkerhetsprinciper för dina Azure-prenumerationer och resursgrupper
- [Datasäkerhet i Azure Security Center](security-center-data-security.md): Lär dig hur Security Center hanterar och skyddar data


