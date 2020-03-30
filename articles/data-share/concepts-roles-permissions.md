---
title: Roller och krav för Azure Data Share
description: Lär dig mer om de behörigheter som krävs för att dela och ta emot data med Azure Data Share.
author: joannapea
ms.author: joanpo
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 36a492f6a3e86cfb2fc9505550cc2d9f4746e070
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79265511"
---
# <a name="roles-and-requirements-for-azure-data-share"></a>Roller och krav för Azure Data Share 

I den här artikeln beskrivs roller och behörigheter som krävs för att dela och ta emot data med azure data share-tjänsten. 

## <a name="roles-and-requirements"></a>Roller och krav

Med Azure Data Share-tjänsten kan du dela data utan att utbyta autentiseringsuppgifter mellan dataprovider och konsument. Azure Data Share-tjänsten använder hanterade identiteter (tidigare kallade MSI:er) för att autentisera till Azure-datalager. 

Azure Data Share-resursens hanterade identitet måste beviljas åtkomst till Azure-datalager. Azure Data Share-tjänsten använder sedan den här hanterade identiteten för att läsa och skriva data för ögonblicksbildbaserad delning och för att upprätta en symbolisk länk för delning på plats. 

För att dela eller ta emot data från ett Azure-datalager behöver användaren minst följande behörigheter. Ytterligare behörigheter krävs för SQL-baserad delning.
* Behörighet att skriva till Azure-datalagret. Vanligtvis finns den här behörigheten i rollen **Deltagare.**
* Behörighet att skapa rolltilldelning i Azure-datalagret. Vanligtvis finns behörighet att skapa rolltilldelningar i rollen **Ägare,** rollen Administratör för Användaråtkomst eller en anpassad roll med Microsoft.Authorization/rolltilldelningar/skrivbehörighet som tilldelats. Den här behörigheten krävs inte om dataresursresursens hanterade identitet redan har beviljats åtkomst till Azure-datalagret. Se tabellen nedan för önskad roll.

Nedan följer en sammanfattning av de roller som tilldelats datadelningsresursens hanterade identitet:

| |  |  |
|---|---|---|
|**Typ av datalager**|**Datalager för dataproviderkälla**|**Data Konsumentmåldata store**|
|Azure Blob Storage| Dataläsare för lagringsblobb | Storage Blob Data Contributor
|Azure Data Lake Gen1 | Ägare | Stöds inte
|Azure Data Lake Gen2 | Dataläsare för lagringsblobb | Storage Blob Data Contributor
|Azure SQL Server | SQL DB-deltagare | SQL DB-deltagare
|Azure Data Explorer-kluster | Deltagare | Deltagare
|

För SQL-baserad delning måste en SQL-användare skapas från en extern provider i SQL-databasen med samma namn som Azure Data Share-resursen. Nedan följer en sammanfattning av den behörighet som krävs av SQL-användaren.

| |  |  |
|---|---|---|
|**SQL-databastyp**|**Sql-användarbehörighet för dataprovider**|**Användarbehörighet för datakonsument SQL**|
|Azure SQL Database | db_datareader | db_datareader, db_datawriter, db_ddladmin
|Azure Synapse Analytics (tidigare SQL DW) | db_datareader | db_datareader, db_datawriter, db_ddladmin
|


### <a name="data-provider"></a>Uppgiftslämnare 
Om du vill lägga till en datauppsättning i Azure Data Share måste providerdataresursens hanterade identitet beviljas åtkomst till källdatalagringen i Azure. När det till exempel gäller lagringskonto beviljas dataresursens hanterade identitet rollen Storage Blob Data Reader. 

Detta görs automatiskt av Azure Data Share-tjänsten när användaren lägger till datauppsättning via Azure-portalen och användaren har rätt behörighet. Användaren är till exempel ägare till Azure-datalagret eller är medlem i en anpassad roll som har tilldelats Tilldelnings-/rolltilldelningar/skrivbehörighet för Microsoft.Authorization/role. 

Alternativt kan användaren ha ägaren av Azure-datalagret lägga till dataresursens hanterade identitet i Azure-datalagret manuellt. Den här åtgärden behöver bara utföras en gång per dataresurs.

Så här skapar du en rolltilldelning för dataresursresursens hanterade identitet:

1. Navigera till Azure-datalagret.
1. Välj **Åtkomstkontroll (IAM)**.
1. Välj **Lägg till en rolltilldelning**.
1. Under *Roll*väljer du rollen i rolltilldelningstabellen ovan (till exempel för lagringskonto väljer du *Storage Blob Data Reader*).
1. Skriv in namnet på din Azure Data Share-resurs under *Välj.*
1. Klicka på *Spara*.

För SQL-baserade källor måste en SQL-användare, utöver ovanstående steg, skapas från en extern provider i SQL-databasen med samma namn som Azure Data Share-resursen. Den här användaren måste beviljas *db_datareader* behörighet. Ett exempelskript tillsammans med andra förutsättningar för SQL-baserad delning finns i [resursstudien för data.](share-your-data.md) 

### <a name="data-consumer"></a>Data konsument
För att ta emot data måste konsumentdataresursens hanterade identitet beviljas åtkomst till mål azure-datalagret. När det till exempel gäller lagringskonto beviljas dataresursens hanterade identitet rollen Storage Blob Data Contributor. 

Detta görs automatiskt av Azure Data Share-tjänsten om användaren anger ett måldatalager via Azure-portalen och användaren har rätt behörighet. Användaren är till exempel ägare till Azure-datalagret eller är medlem i en anpassad roll som har tilldelats tilldelningar/skrivbehörighet för Microsoft.Authorization/role. 

Alternativt kan användaren ha ägaren av Azure-datalagret lägga till dataresursens hanterade identitet i Azure-datalagret manuellt. Den här åtgärden behöver bara utföras en gång per dataresurs.

Så här skapar du en rolltilldelning för dataresursresursens hanterade identitet manuellt:

1. Navigera till Azure-datalagret.
1. Välj **Åtkomstkontroll (IAM)**.
1. Välj **Lägg till en rolltilldelning**.
1. Under *Roll*väljer du rollen i rolltilldelningstabellen ovan (till exempel för lagringskonto väljer du *Storage Blob Data Reader*).
1. Skriv in namnet på din Azure Data Share-resurs under *Välj.*
1. Klicka på *Spara*.

För SQL-baserat mål måste en SQL-användare, utöver ovanstående steg, skapas från en extern provider i SQL-databasen med samma namn som Azure Data Share-resursen. Den här användaren måste beviljas *db_datareader, db_datawriter, db_ddladmin* behörighet. Ett exempelskript tillsammans med andra förutsättningar för SQL-baserad delning finns i självstudiekursen [för att acceptera och ta emot data.](subscribe-to-data-share.md) 

Om du delar data med REST API:er måste du skapa dessa rolltilldelningar manuellt. 

Mer information om hur du lägger till en rolltilldelning finns i [den här dokumentationen](https://docs.microsoft.com/azure/role-based-access-control/role-assignments-portal#add-a-role-assignment). 

## <a name="resource-provider-registration"></a>Registrering av resursprovider 

Om du vill visa inbjudan till Azure-datadelning för första gången i din Azure-klient kan du behöva registrera Microsoft.DataShare-resursleverantören manuellt i din Azure-prenumeration. Följ dessa steg för att registrera Microsoft.DataShare-resursleverantören i din Azure-prenumeration. Du behöver *deltagaråtkomst* till Azure-prenumerationen för att registrera resursprovidern.

1. Navigera till **Prenumerationer**i Azure-portalen .
1. Välj den prenumeration som du använder för Azure Data Share.
1. Klicka på **Resursleverantörer**.
1. Sök efter Microsoft.DataShare.
1. Klicka på **Registrera**.

## <a name="next-steps"></a>Nästa steg

- Läs mer om roller i Azure – [Förstå rolldefinitioner](../role-based-access-control/role-definitions.md)

