---
title: Hantera databasroller och användare i Azure Analysis Services | Microsoft Docs
description: Lär dig hur du hanterar databasroller och användare på en Analysis Services-server i Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: ab238e31ba022427dc55fec842f3e89e72fa530a
ms.sourcegitcommit: dbfd977100b22699823ad8bf03e0b75e9796615f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/30/2018
ms.locfileid: "50238887"
---
# <a name="manage-database-roles-and-users"></a>Hantera databasroller och användare

På databasnivå modellen måste alla användare hör till en roll. Rollerna definierar användare med specifika behörigheter för modelldatabasen. Alla användare eller säkerhetsgrupp som tilldelas en roll måste ha ett konto i en Azure AD-klient i samma prenumeration som servern. 

Hur du definierar roller skiljer sig beroende på vilket verktyg som du använder, men effekten är samma.

Behörigheter är:
*  **Administratören** -användare har fullständiga behörigheter för databasen. Databasroller med administratörsbehörighet skiljer sig från server-administratörer.
*  **Processen** -användare kan ansluta till och utföra processen åtgärder på databasen och analysera modellen databasdata.
*  **Läs** -användare kan använda ett klientprogram för att ansluta till och analysera modellen databasdata.

När du skapar ett projekt för tabellmodeller kan du skapa roller och lägga till användare eller grupper i rollerna genom att använda rollhanteraren i SSDT. När du distribuerat till en server kan du använda SSMS, [Analysis Services PowerShell-cmdletar](https://msdn.microsoft.com/library/hh758425.aspx), eller [Tabular Model Scripting Language](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) för att lägga till eller ta bort roller och användarmedlemmar.

> [!NOTE]
> Säkerhetsgrupper måste ha den `MailEnabled` egenskapen `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Att lägga till eller hantera roller och användare i SSDT  
  
1.  I SSDT > **Tabellmodellutforskaren**, högerklicka på **roller**.  
  
2.  Klicka på **Ny** i **rollhanteraren**.  
  
3.  Skriv ett namn för rollen.  
  
     Som standard är namnet på standardrollen stegvis numrerade för varje ny roll. Vi rekommenderar att du skriver ett namn som tydligt identifierar medlemstypen, till exempel Ekonomi chefer eller personal specialister.  
  
4.  Välj något av följande behörigheter:  
  
    |Behörighet|Beskrivning|  
    |----------------|-----------------|  
    |**Ingen**|Medlemmar kan inte ändra modellschemat och det går inte att fråga efter data.|  
    |**Läsa**|Medlemmar kan fråga efter data (baserat på radfilter) men det går inte att ändra modellschemat.|  
    |**Läsa och bearbeta**|Medlemmar kan fråga data (baserat på radnivå filter) och kör processen och bearbeta alla åtgärder, men det går inte att ändra modellschemat.|  
    |**Processen**|Medlemmar kan köra processen och bearbeta alla åtgärder. Det går inte att ändra modellschemat och det går inte att fråga efter data.|  
    |**administratör**|Medlemmar kan ändra modellschemat och fråga efter alla data.|   
  
5.  Om rollen som du skapar har läs- eller läsa och bearbeta behörighet, du kan lägga till radfilter med hjälp av en DAX-formel. Klicka på den **radfilter** , och sedan markera en tabell och klicka sedan på den **DAX-Filter** fältet och skriv sedan en DAX-formel.
  
6.  Klicka på **medlemmar** > **Lägg till extern**.  
  
8.  I **Lägg till extern medlem**, ange användare eller grupper i Azure AD-klienten genom e-postadress. När du klickar på OK och stänga rollhanteraren visas roller och rollmedlemmar i Tabellmodellutforskaren. 
 
     ![Roller och användare i Tabellmodellutforskaren](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Distribuera till Azure Analysis Services-servern.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Att lägga till eller hantera roller och användare i SSMS
Om du vill lägga till roller och användare i en distribuerad modell-databas, måste du vara ansluten till servern som en serveradministratör eller redan i en databasroll med administratörsbehörighet.

1. Högerklicka i objektet Exporer **roller** > **ny roll**.

2. I **skapa roll**, ange ett rollnamn och beskrivning.

3. Markera en behörighet.
   |Behörighet|Beskrivning|  
   |----------------|-----------------|  
   |**Fullständig kontroll (administratör)**|Medlemmar kan ändra modellschemat, bearbeta och kan fråga efter alla data.| 
   |**Process-databas**|Medlemmar kan köra processen och bearbeta alla åtgärder. Det går inte att ändra modellschemat och det går inte att fråga efter data.|  
   |**Läsa**|Medlemmar kan fråga efter data (baserat på radfilter) men det går inte att ändra modellschemat.|  
  
4. Klicka på **medlemskap**, ska du ange en användare eller grupp i Azure AD-klienten genom e-postadress.

     ![Lägga till användare](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Om du skapar rollen har läsbehörighet, kan du lägga till radfilter med hjälp av en DAX-formel. Klicka på **radfilter**, markera en tabell och skriv sedan en DAX-formel i den **DAX-Filter** fält. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Att lägga till roller och användare genom att använda en TMSL-skript
Du kan köra ett TMSL-skript i XMLA-fönstret i SSMS eller med hjälp av PowerShell. Använd den [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) kommandot och [roller](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objekt.

**Exempelskript för TMSL**

I det här exemplet läggs en externa B2B-användare och en grupp till rollen analytiker med läsbehörighet för databasen SalesBI. Både externa användare och grupper måste vara i samma Azure AD-klient.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Att lägga till roller och användare med hjälp av PowerShell
Den [SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modulen innehåller uppgiftsspecifika database management-cmdletar och den allmänna Invoke-ASCmd-cmdlet som accepterar en fråga Tabular Model Tabellmodellskriptspråket (TMSL) eller ett skript. Följande cmdletar som används för att hantera databasroller och användare.
  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Lägg till RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Lägga till en medlem i en databasroll.| 
|[Ta bort RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Ta bort medlem från en databasroll.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Köra ett TMSL-skript.|

## <a name="row-filters"></a>Radfilter  
Radfilter definierar vilka rader i en tabell kan efterfrågas av medlemmar i en viss roll. Radfilter definieras för varje tabell i en modell med hjälp av DAX-formler.  
  
Radfilter kan definieras endast för roller med läs- och läsa och bearbeta behörigheter. Som standard om ett radfilter inte har definierats för en viss tabell, medlemmar kan fråga efter alla rader i tabellen, såvida inte korsfiltrering gäller från en annan tabell.
  
 Radfilter kräver en DAX-formel, som måste utvärderas till ett TRUE/FALSE-värde, att definiera de rader som kan efterfrågas av medlemmar i specifika rollen. Rader som inte ingår i DAX-formeln kan inte frågas. Till exempel tabellen kunder med följande rad filtrerar uttryck, *= kunder [Land] = ”USA”*, medlemmar i rollen kan bara se kunder i USA.  
  
Radfilter gäller för de angivna raderna och relaterade rader. När en tabell har flera relationer, tillämpa filter säkerhet för relationen är aktiv. Radfilter överlappar med andra raden filter som definierats för relaterade tabeller, till exempel:  
  
|Tabell|DAX-uttryck|  
|-----------|--------------------|  
|Region|= Region [Land] = ”USA”|  
|ProductCategory|= ProductCategory [Name] = ”cyklar”|  
|Transaktioner|= Transaktioner [år] = 2016|  
  
 Nettoeffekten är medlemmar kan fråga efter rader med data där kunden finns i USA, produktkategorin cyklar och året 2016. Användare kan inte fråga transaktioner utanför USA, transaktioner som inte cyklar eller transaktioner inte i 2016 om de inte är medlem i en annan roll som ger dessa behörigheter.
  
 Du kan använda filtret, *=FALSE()*, för att neka åtkomst till alla rader för en hel tabell.

## <a name="next-steps"></a>Nästa steg
  [Hantera serveradministratörer](analysis-services-server-admins.md)   
  [Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md)  
  [Tabular Model Scripting Språkreferens (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

