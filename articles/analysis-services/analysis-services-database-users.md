---
title: Hantera databasroller och användare i Azure Analysis Services | Microsoft-dokument
description: Lär dig hur du hanterar databasroller och användare på en Analysis Services-server i Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454211"
---
# <a name="manage-database-roles-and-users"></a>Hantera databasroller och användare

På modelldatabasnivå måste alla användare tillhöra en roll. Roller definierar användare med särskilda behörigheter för modelldatabasen. Alla användare eller säkerhetsgrupper som läggs till i en roll måste ha ett konto i en Azure AD-klientorganisation i samma prenumeration som servern. 

Hur du definierar roller är olika beroende på vilket verktyg du använder, men effekten är densamma.

Rollbehörigheter inkluderar:
*  **Administratör** - Användare har fullständiga behörigheter för databasen. Databasroller med administratörsbehörighet skiljer sig från serveradministratörer.
*  **Process** - Användare kan ansluta till och utföra processåtgärder i databasen och analysera modelldatabasdata.
*  **Läs** - Användare kan använda ett klientprogram för att ansluta till och analysera modelldatabasdata.

När du skapar ett tabellmodellprojekt skapar du roller och lägger till användare eller grupper i dessa roller med hjälp av Rollhanteraren i Visual Studio med Analysis Services-projekt. När du distribueras till en server använder du SQL Server Management Studio (SSMS), [Analysis Services PowerShell-cmdlets](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)eller [TMSL (Tabular Model Scripting Language)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) för att lägga till eller ta bort roller och användare.

När du lägger `obj:groupid@tenantid`till en **säkerhetsgrupp**använder du .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Så här lägger du till eller hanterar roller och användare i Visual Studio  
  
1.  Högerklicka på **Roller**i Utforskaren för **tabellmodell.**  
  
2.  Klicka på **Ny** **i Rollhanteraren.**  
  
3.  Skriv ett namn på rollen.  
  
     Som standard numreras namnet på standardrollen stegvis för varje ny roll. Vi rekommenderar att du skriver ett namn som tydligt identifierar medlemstypen, till exempel ekonomichefer eller personalspecialister.  
  
4.  Välj en av följande behörigheter:  
  
    |Behörighet|Beskrivning|  
    |----------------|-----------------|  
    |**Inget**|Medlemmar kan inte läsa eller ändra modellschemat och kan inte fråga data.|  
    |**Läsa**|Medlemmar kan fråga data (baserat på radfilter) men kan inte ändra modellschemat.|  
    |**Läs och bearbeta**|Medlemmar kan fråga data (baserat på radnivåfilter) och köra Process- och Process All-åtgärder, men kan inte ändra modellschemat.|  
    |**Process**|Medlemmar kan köra Process- och Process Alla-åtgärder. Det går inte att läsa eller ändra modellschemat och kan inte fråga data.|  
    |**Administratör**|Medlemmar kan ändra modellschemat och fråga alla data.|   
  
5.  Om rollen du skapar har behörigheten Läs eller Läs och Bearbeta kan du lägga till radfilter med hjälp av en DAX-formel. Klicka på fliken **Radfilter,** välj sedan en tabell, klicka sedan på fältet **DAX-filter** och skriv sedan en DAX-formel.
  
6.  Klicka på **Medlemmar** > **Lägg till externt**.  
  
8.  Ange användare eller grupper i din klient Azure AD via e-postadress i **Lägg till extern medlem.** När du har klickat på OK och stängt rollhanteraren visas roller och rollmedlemmar i Tabellmodellutforskaren. 
 
     ![Roller och användare i Tabellmodellutforskaren](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Distribuera till din Azure Analysis Services-server.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Så här lägger du till eller hanterar roller och användare i SSMS

Om du vill lägga till roller och användare i en distribuerad modelldatabas måste du vara ansluten till servern som serveradministratör eller redan i en databasroll med administratörsbehörighet.

1. Högerklicka på **Roller** > **ny roll**i Objekt exporer .

2. Ange ett rollnamn och en beskrivning i **Skapa roll.**

3. Välj en behörighet.

   |Behörighet|Beskrivning|  
   |----------------|-----------------|  
   |**Fullständig kontroll (administratör)**|Medlemmar kan ändra modellschemat, processen och kan fråga alla data.| 
   |**Behandla databas**|Medlemmar kan köra Process- och Process Alla-åtgärder. Det går inte att ändra modellschemat och kan inte fråga data.|  
   |**Läsa**|Medlemmar kan fråga data (baserat på radfilter) men kan inte ändra modellschemat.|  
  
4. Klicka på **Medlemskap**och ange sedan en användare eller grupp i din klient Azure AD via e-postadress.

     ![Lägga till användare](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Om rollen du skapar har läsbehörighet kan du lägga till radfilter med hjälp av en DAX-formel. Klicka på **Radfilter,** markera en tabell och skriv sedan en DAX-formel i fältet **DAX-filter.** 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Så här lägger du till roller och användare med hjälp av ett TMSL-skript

Du kan köra ett TMSL-skript i XMLA-fönstret i SSMS eller med PowerShell. Använd kommandot [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) och [roles-objektet.](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl)

**Exempel på TMSL-skript**

I det här exemplet läggs en extern B2B-användare och en grupp till i rollen Analytiker med läsbehörigheter för SalesBI-databasen. Både den externa användaren och gruppen måste finnas i samma klient Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Så här lägger du till roller och användare med PowerShell

[SqlServer-modulen](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) tillhandahåller uppgiftsspecifika databashanterings-cmdlets och den allmänna invoke-ASCmd-cmdleten som accepterar en TMSL-fråga (Tabular Model Scripting Language) eller skript. Följande cmdlets används för att hantera databasroller och användare.
  
|Cmdlet|Beskrivning|
|------------|-----------------| 
|[Tilläggsrollmedlem](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Lägg till en medlem i en databasroll.| 
|[Ta bort rollmedlem](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Ta bort en medlem från en databasroll.|   
|[Anropa-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Kör ett TMSL-skript.|

## <a name="row-filters"></a>Radfilter  

Radfilter definierar vilka rader i en tabell som kan efterfrågas av medlemmar i en viss roll. Radfilter definieras för varje tabell i en modell med hjälp av DAX-formler.  
  
Radfilter kan bara definieras för roller med läs- och läs- och processbehörigheter. Om ett radfilter inte har definierats för en viss tabell kan medlemmar som standard fråga alla rader i tabellen om inte korsfiltrering gäller från en annan tabell.
  
 Radfilter kräver en DAX-formel, som måste utvärderas till ett SANT/FALSKT värde, för att definiera de rader som kan efterfrågas av medlemmar i den aktuella rollen. Rader som inte ingår i DAX-formeln kan inte efterfrågas. Tabellen Kunder med följande radfilter uttryck, *=Kunder [Land] = "USA"*, medlemmar i rollen Försäljning kan bara se kunder i USA.  
  
Radfilter gäller för angivna rader och relaterade rader. När en tabell har flera relationer använder filtret säkerhet för den aktiva relationen. Radfilter korsas med andra radfilers som definierats för relaterade tabeller, till exempel:  
  
|Tabell|DAX-uttryck|  
|-----------|--------------------|  
|Region|=Region[Land]="USA"|  
|ProductCategory|=ProductCategory[Namn]="Cyklar"|  
|Transaktioner|=Transaktioner[År]=2016|  
  
 Nettoeffekten är att medlemmar kan fråga rader med data där kunden befinner sig i USA, produktkategorin är cyklar och året är 2016. Användare kan inte fråga transaktioner utanför USA, transaktioner som inte är cyklar eller transaktioner som inte är 2016 om de inte är medlemmar i en annan roll som ger dessa behörigheter.
  
 Du kan använda filtret *= FALSE()* för att neka åtkomst till alla rader för en hel tabell.

## <a name="next-steps"></a>Nästa steg

  [Hantera serveradministratörer](analysis-services-server-admins.md)   
  [Hantera Azure Analysis Services med PowerShell](analysis-services-powershell.md)  
  [TMSL-referens (Tabular Model Scripting Language)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

