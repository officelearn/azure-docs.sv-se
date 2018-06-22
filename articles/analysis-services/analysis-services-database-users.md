---
title: Hantera databasroller och användare i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar databasroller och användare på en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8870c4199d5f24d1e8d07bc97d61a09c07052c1e
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/21/2018
ms.locfileid: "36307987"
---
# <a name="manage-database-roles-and-users"></a>Hantera databasroller och användare

Alla användare måste höra till en roll på databasnivå modellen. Rollerna för att definiera användare med särskilda behörigheter för modelldatabasen. Alla användare eller säkerhetsgrupp som lagts till i en roll måste ha ett konto i Azure AD-klient på samma prenumeration som servern. 

Hur du definierar roller skiljer sig åt beroende på hur du använder verktyget men effekten är samma.

Rollbehörigheter inkluderar:
*  **Administratören** -användare har fullständig behörighet till databasen. Databasroller med administratörsbehörighet skiljer sig från server-administratörer.
*  **Processen** -användare kan ansluta till och utföra åtgärder för processen på databasen och analysera modellen databasdata.
*  **Läs** -användare kan använda ett klientprogram att ansluta till och analysera modellen databasdata.

När du skapar en tabellmodell-projekt kan du skapa roller och Lägg till användare eller grupper rollerna genom att använda rollhanteraren i SSDT. När distribuerats till en server kan du använda SSMS, [Analysis Services PowerShell-cmdlets](https://msdn.microsoft.com/library/hh758425.aspx), eller [Tabular modellen Scripting språk](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) för att lägga till eller ta bort roller och användaren medlemmar.

> [!NOTE]
> Säkerhetsgrupper måste ha den `MailEnabled` egenskapen `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Att lägga till eller hantera roller och användare i SSDT  
  
1.  I SSDT > **Tabular modellen Explorer**, högerklicka på **roller**.  
  
2.  Klicka på **Ny** i **rollhanteraren**.  
  
3.  Ange ett namn för rollen.  
  
     Som standard numreras inkrementellt namnet på standardrollen som för varje ny roll. Det rekommenderas att du skriver ett namn som tydligt identifierar medlemstypen, till exempel finansiella chefer eller personal specialister.  
  
4.  Välj något av följande behörigheter:  
  
    |Behörighet|Beskrivning|  
    |----------------|-----------------|  
    |**Ingen**|Medlemmar kan inte ändra modellschemat och det går inte att fråga efter data.|  
    |**Läsa**|Medlemmar kan fråga efter data (baserat på radfilter) men det går inte att ändra modellschemat.|  
    |**Läs- och processen**|Medlemmar kan fråga data (baserat på radnivå filter) och kör processen och bearbeta alla åtgärder, men det går inte att ändra modellschemat.|  
    |**Processen**|Medlemmar kan köra processen och bearbeta alla åtgärder. Det går inte att ändra modellschemat och det går inte att fråga efter data.|  
    |**Administratören**|Medlemmar kan ändra modellschemat och läsa alla data.|   
  
5.  Om rollen som du skapar har läs- eller Läs- och processen behörighet, du kan lägga till radfilter med hjälp av en DAX-formel. Klicka på den **radfilter** , och sedan markera en tabell och klicka sedan på den **DAX-Filter** fältet och skriv sedan en DAX-formel.
  
6.  Klicka på **medlemmar** > **lägga till externa**.  
  
8.  I **lägga till extern medlem**, ange användare eller grupper i din Azure AD-klient med e-postadress. När du klickar på OK och stänga rollhanteraren visas roller och medlemmar i rollen i tabellformat modellen Explorer. 
 
     ![Roller och användare i tabellform modellen Explorer](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Distribuera till Azure Analysis Services-servern.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Att lägga till eller hantera roller och användare i SSMS
Lägg till roller och användare i en distribuerad modelldatabasen, måste du vara ansluten till servern som en serveradministratör eller redan i en databasroll med administratörsbehörighet.

1. Högerklicka i objektet Exporer **roller** > **ny roll**.

2. I **skapa roll**, ange role-namn och beskrivning.

3. Markera en behörighet.
   |Behörighet|Beskrivning|  
   |----------------|-----------------|  
   |**Fullständig behörighet (administratör)**|Medlemmar kan ändra modellschemat, bearbeta och kan fråga efter alla data.| 
   |**Process-databas**|Medlemmar kan köra processen och bearbeta alla åtgärder. Det går inte att ändra modellschemat och det går inte att fråga efter data.|  
   |**Läsa**|Medlemmar kan fråga efter data (baserat på radfilter) men det går inte att ändra modellschemat.|  
  
4. Klicka på **medlemskap**, ange en användare eller grupp i din Azure AD-klient av e-postadress.

     ![Lägga till användare](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Om du skapar rollen har behörighet att läsa, kan du lägga till radfilter med hjälp av en DAX-formel. Klicka på **radfilter**, markera en tabell och skriv sedan en DAX-formel i den **DAX-Filter** fältet. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Lägg till roller och användare med ett TMSL skript
Du kan köra ett skript för TMSL i fönstret XMLA i SSMS eller med hjälp av PowerShell. Använd den [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) kommando och [roller](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objekt.

**Exempelskript för TMSL**

I det här exemplet läggs en B2B externa användare och en grupp till rollen analytiker med läsbehörighet för databasen SalesBI. Både externa användare och grupper måste vara i samma Azure AD-klient.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Lägg till roller och användare med PowerShell
Den [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modulen innehåller cmdlet: ar uppgiftsspecifika databasen och den allmänna Invoke-ASCmd cmdlet som accepterar en tabellvy modellen Scripting språk (TMSL) fråga eller skript. Följande cmdlets används för att hantera databasroller och användare.
  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Lägg till RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Lägga till en medlem i en databasroll.| 
|[Ta bort RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Ta bort en medlem från en databasroll.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Köra ett skript för TMSL.|

## <a name="row-filters"></a>Radfilter  
Radfilter definiera vilka rader i en tabell kan efterfrågas av medlemmar i en viss roll. Radfilter definieras för varje tabell i en modell med hjälp av DAX-formler.  
  
Radfilter kan definieras endast för roller med läs- och läs och processen behörigheter. Som standard om ett radfilter inte har definierats för en viss tabell kan medlemmar fråga efter alla rader i tabellen om filtrering av mellan gäller från en annan tabell.
  
 Radfilter kräver en DAX-formel måste utvärderas till ett TRUE/FALSE-värde, att definiera de rader som kan efterfrågas av medlemmar i specifika rollen. Det går inte att uppdatera rader som inte ingår i DAX-formel. Till exempel tabellen kunder med följande rad filtrerar uttryck, *= kunder [Land] = ”USA”*, medlemmar i rollen kan bara se kunder i USA.  
  
Radfilter gäller för de angivna raderna och relaterade rader. När en tabell har flera relationer, tillämpa filter säkerhet för relationen är aktiv. Radfilter överlappar med andra raden filter som definierats för relaterade tabeller, till exempel:  
  
|Tabell|DAX-uttryck|  
|-----------|--------------------|  
|Region|= Region [Land] = ”USA”|  
|Produktkategori|= Produktkategori [Name] = ”cyklar”|  
|Transaktioner|= Transaktioner [år] = 2016|  
  
 Net effekten är att medlemmar kan fråga datarader där kunden finns i USA, produktkategorin cyklar och året 2016. Användare kan inte fråga transaktioner utanför USA, transaktioner som inte cyklar eller transaktioner inte 2016 såvida de inte är medlem i en annan roll som ger dessa behörigheter.
  
 Du kan använda filter, *=FALSE()*, för att neka åtkomst till alla rader för en hel tabell.

## <a name="next-steps"></a>Nästa steg
  [Hantera server-administratörer](analysis-services-server-admins.md)   
  [Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md)  
  [Tabellmodell Scripting Språkreferens (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

