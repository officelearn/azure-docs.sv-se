---
title: Databasvyer för Azure Blockchain Workbench
description: Översikt över tillgängliga Azure Blockchain Workbench Preview SQL DB databasvyer.
ms.date: 09/05/2019
ms.topic: article
ms.reviewer: mmercuri
ms.openlocfilehash: 585084b4c85c48533bdad96d4f99813ef2e418b4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325978"
---
# <a name="azure-blockchain-workbench-database-views"></a>Databasvyer för Azure Blockchain Workbench

Azure Blockchain Workbench Preview levererar data från distribuerade liggare till en *sql DB-databas utanför kedjan.* Databasen utanför kedjan gör det möjligt att använda SQL och befintliga verktyg, till exempel [SQL Server Management Studio,](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms?view=sql-server-2017)för att interagera med blockchain-data.

Azure Blockchain Workbench innehåller en uppsättning databasvyer som ger åtkomst till data som kan vara till hjälp när du utför dina frågor. Dessa vyer är kraftigt denormaliserade för att göra det enkelt att snabbt komma igång med att skapa rapporter, analyser och på annat sätt använda blockchain-data med befintliga verktyg och utan att behöva omskola databaspersonal.

Det här avsnittet innehåller en översikt över databasvyerna och de data de innehåller.

> [!NOTE]
> All direkt användning av databastabeller som finns i databasen utanför dessa vyer stöds, även om det är möjligt, inte.
>

## <a name="vwapplication"></a>vwApplication (vwApplication)

Den här vyn innehåller information om **program** som har överförts till Azure Blockchain Workbench.

| Namn                             | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|----------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                    | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                  | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDescription           | nvarchar(255) | Ja         | En beskrivning av ansökan |
| ApplicationDisplayName           | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled               | bit           | Inga          | Identifierar om programmet är aktiverat<br /> **Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen. |
| UppladdadDTM                     | datumtid2(7)  | Inga          | Datum och tid då ett kontrakt laddades upp |
| UploadedByUserId                 | int           | Inga          | ID för den användare som laddade upp programmet |
| UploadedByUserExternalId         | nvarchar(255) | Inga          | Den externa identifieraren för användaren som laddade upp programmet. Som standard är detta ID användaren från Azure Active Directory för konsortiet.                                                                                                |
| UploadedByUserProvisioningStatus | int           | Inga          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – Användaren har skapats av API:et<br />1 – En nyckel har associerats med användaren i databasen<br />2 – Användaren är helt etablerad                         |
| UploadedByUserFirstName          | nvarchar(50)  | Ja         | Förnamnet på den användare som laddade upp kontraktet |
| UploadedByUserLastName           | nvarchar(50)  | Ja         | Efternamnet på den användare som laddade upp kontraktet |
| UploadedByUserEmailAddress       | nvarchar(255) | Ja         | E-postadressen till den användare som laddade upp kontraktet |

## <a name="vwapplicationrole"></a>vwApplicationRole

Den här vyn innehåller information om de roller som har definierats i Azure Blockchain Workbench-program.

I ett *tillgångsöverföringsprogram,* till exempel, kan roller som *köpar-* och *säljarroller* definieras.

| Namn                   | Typ             | Kan vara Null | Beskrivning                                       |
|------------------------|------------------|-------------|---------------------------------------------------|
| ApplicationId          | int              | Inga          | En unik identifierare för programmet           |
| ApplicationName        | nvarchar(50)     | Inga          | Namnet på programmet                       |
| ApplicationDescription | nvarchar(255)    | Ja         | En beskrivning av ansökan                  |
| ApplicationDisplayName | nvarchar(255)    | Inga          | Namnet som ska visas i ett användargränssnitt      |
| RoleId (RollId)                 | int              | Inga          | En unik identifierare för en roll i programmet |
| RoleName               | nvarchar50)      | Inga          | Namnet på rollen                              |
| RollBeskrivning        | beskrivning(255) | Ja         | En beskrivning av rollen                         |

## <a name="vwapplicationroleuser"></a>vwApplicationRoleUser

Den här vyn innehåller information om de roller som har definierats i Azure Blockchain Workbench-program och de användare som är associerade med dem.

I ett *tillgångsöverföringsprogram* kan till exempel *John Smith* associeras med rollen *Köpare.*

| Namn                       | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                           |
|----------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId              | int           | Inga          | En unik identifierare för programmet                                                                                                                                                                                               |
| ApplicationName            | nvarchar(50)  | Inga          | Namnet på programmet                                                                                                                                                                                                           |
| ApplicationDescription     | nvarchar(255) | Ja         | En beskrivning av ansökan                                                                                                                                                                                                      |
| ApplicationDisplayName     | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt                                                                                                                                                                                          |
| ApplicationRoleId          | int           | Inga          | En unik identifierare för en roll i programmet                                                                                                                                                                                     |
| ApplicationRoleName        | nvarchar50)   | Inga          | Namnet på rollen                                                                                                                                                                                                                  |
| ApplicationRoleDescription | nvarchar(255) | Ja         | En beskrivning av rollen                                                                                                                                                                                                             |
| UserId                     | int           | Inga          | ID:et för den användare som är associerad med rollen |
| UserExternalId             | nvarchar(255) | Inga          | Den externa identifieraren för den användare som är associerad med rollen. Som standard är detta ID användaren från Azure Active Directory för konsortiet.                                                                     |
| Status för användaretablering     | int           | Inga          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – Användaren har skapats av API:et<br />1 – En nyckel har associerats med användaren i databasen<br />2 – Användaren är helt etablerad |
| UserFirstName              | nvarchar(50)  | Ja         | Förnamnet på den användare som är associerad med rollen |
| Användarnamn               | nvarchar(255) | Ja         | Efternamnet på den användare som är associerad med rollen |
| UserEmailAdress           | nvarchar(255) | Ja         | E-postadressen till den användare som är associerad med rollen |

## <a name="vwconnectionuser"></a>vwConnectionUser (vwConnectionUser)

Den här vyn innehåller information om de anslutningar som definierats i Azure Blockchain Workbench och de användare som är associerade med dem. För varje anslutning innehåller den här vyn följande data:

-   Information om tillhörande redovisning
-   Associerad användarinformation

| Namn                     | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                           |
|--------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId (på)-anslutning)             | int           | Inga          | Den unika identifieraren för en anslutning i Azure Blockchain Workbench |
| ConnectionEndpointUrl    | nvarchar(50)  | Inga          | Slutpunktsadressen för en anslutning |
| AnslutningFinansieringKonto | nvarchar(255) | Ja         | Finansieringskontot som är kopplat till en anslutning, om tillämpligt |
| LedgerId (Räkt)                 | int           | Inga          | Den unika identifieraren för en redovisning |
| RedovisningNamn               | nvarchar(50)  | Inga          | Namnet på redovisningen |
| LedgerDisplayName        | nvarchar(255) | Inga          | Namnet på den liggare som ska visas i användargränssnittet |
| UserId                   | int           | Inga          | ID:et för den användare som är associerad med anslutningen |
| UserExternalId           | nvarchar(255) | Inga          | Den externa identifieraren för den användare som är associerad med anslutningen. Som standard är detta ID användaren från Azure Active Directory för konsortiet. |
| Status för användaretablering   | int           | Inga          |Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – Användaren har skapats av API:et<br />1 – En nyckel har associerats med användaren i databasen<br />2 – Användaren är helt etablerad |
| UserFirstName            | nvarchar(50)  | Ja         | Förnamnet på den användare som är associerad med anslutningen |
| Användarnamn             | nvarchar(255) | Ja         | Efternamnet på den användare som är associerad med anslutningen |
| UserEmailAdress         | nvarchar(255) | Ja         | E-postadressen till den användare som är associerad med anslutningen |

## <a name="vwcontract"></a>vwKontrakt

Den här vyn innehåller information om distribuerade kontrakt. För varje kontrakt innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Associerad redovisningsimplementering för funktionen
-   Information om den användare som initierade åtgärden
-   Detaljer relaterade till block och transaktion

| Namn                                     | Typ           | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------------------|----------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ConnectionId (på)-anslutning)                             | int            | Inga          | Den unika identifieraren för en anslutning i Azure Blockchain Workbench.                                                                                                                                                                                         |
| ConnectionEndpointUrl                    | nvarchar(50)   | Inga          | Slutpunktsadressen för en anslutning |
| AnslutningFinansieringKonto                 | nvarchar(255)  | Ja         | Finansieringskontot som är kopplat till en anslutning, om tillämpligt |
| LedgerId (Räkt)                                 | int            | Inga          | Den unika identifieraren för en redovisning |
| RedovisningNamn                               | nvarchar(50)   | Inga          | Namnet på redovisningen |
| LedgerDisplayName                        | nvarchar(255)  | Inga          | Namnet på den liggare som ska visas i användargränssnittet |
| ApplicationId                            | int            | Inga          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar (50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar (255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                       | bit            | Inga          | Identifierar om programmet är aktiverat för tillfället.<br /> **Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen.  |
| WorkflowId (ArbetsflödeId)                               | int            | Inga          | En unik identifierare för arbetsflödet som är associerat med ett kontrakt |
| Arbetsflödesnamn                             | nvarchar(50)   | Inga          | Namnet på arbetsflödet som är associerat med ett kontrakt |
| ArbetsflödeDisplayName                      | nvarchar(255)  | Inga          | Namnet på arbetsflödet som är associerat med kontraktet som visas i användargränssnittet |
| ArbetsflödeSbeskrivning                      | nvarchar(255)  | Ja         | Beskrivningen av arbetsflödet som är associerat med ett kontrakt |
| KontraktCodeId                           | int            | Inga          | En unik identifierare för kontraktskoden som är associerad med kontraktet |
| KontraktFileName                         | int            | Inga          | Namnet på filen som innehåller den smarta kontraktskoden för det här arbetsflödet. |
| KontraktLaddadDtTm                     | int            | Inga          | Datum och tid då kontraktskoden laddades upp |
| KontraktId                               | int            | Inga          | Den unika identifieraren för kontraktet |
| Status för kontraktsbeetablering               | int            | Inga          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – Kontraktet har skapats av API:et i databasen<br />1 – Kontraktet har skickats till redovisningen<br />2 – Kontraktet har distribuerats till redovisningen<br />3 eller 4 - Kontraktet kunde inte distribueras till redovisningen<br />5 - Kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1.5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen, visa **vwContractV0** är tillgänglig som endast stöder värden 0 till 2. |
| KontraktLedgerIdentifier                 | nvarchar (255) |             | E-postadressen till den användare som distribuerade kontraktet |
| KontraktDeployedAvUserId                 | int            | Inga          | En extern identifierare för användaren som distribuerade kontraktet. Som standard är detta ID det guid som representerar Azure Active Directory ID för användaren.                                                                                                          |
| KontraktDeployedAvUserExternalId         | nvarchar(255)  | Inga          | En extern identifierare för användaren som distribuerade kontraktet. Som standard är detta ID det guid som representerar Azure Active Directory ID för användaren.                                                                                                         |
| KontraktDeployedAvUserProvisioningStatus | int            | Inga          | Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – användaren har skapats av API:et<br />1 – En nyckel har associerats med användaren i databasen <br />2 – Användaren är helt etablerad                     |
| KontraktDeployedAvUserFirstName          | nvarchar(50)   | Ja         | Förnamnet på den användare som distribuerade kontraktet |
| KontraktDeployedAvUserLastName           | nvarchar(255)  | Ja         | Efternamnet på den användare som distribuerade kontraktet |
| KontraktDeployedAvUserEmailAddress       | nvarchar(255)  | Ja         | E-postadressen till den användare som distribuerade kontraktet |

## <a name="vwcontractaction"></a>vwContractAction

Den här vyn representerar majoriteten av informationen om åtgärder som vidtas för kontrakt och är utformad för att enkelt underlätta gemensamma rapporteringsscenarier. För varje åtgärd som vidtas innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Associerad smart kontraktsfunktion och parameterdefinition
-   Associerad redovisningsimplementering för funktionen
-   Specifika förekomstvärden som tillhandahålls för parametrar
-   Information om den användare som initierade åtgärden
-   Detaljer relaterade till block och transaktion

| Namn                                     | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                                                    |
|------------------------------------------|---------------|-------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                            | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                          | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName                   | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                       | bit           | Inga          | Det här fältet identifierar om programmet är aktiverat för tillfället. Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen.                                                  |
| WorkflowId (ArbetsflödeId)                               | int           | Inga          | En unik identifierare för ett arbetsflöde |
| Arbetsflödesnamn                             | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName                      | nvarchar(255) | Inga          | Namnet på arbetsflödet som ska visas i ett användargränssnitt |
| ArbetsflödeSbeskrivning                      | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet |
| KontraktId                               | int           | Inga          | En unik identifierare för kontraktet |
| Status för kontraktsbeetablering               | int           | Inga          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – Kontraktet har skapats av API:et i databasen<br />1 – Kontraktet har skickats till redovisningen<br />2 – Kontraktet har distribuerats till redovisningen<br />3 eller 4 - Kontraktet kunde inte distribueras till redovisningen<br />5 - Kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1.5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen visas **vwContractActionV0** som endast stöder värden 0 till 2. |
| KontraktCodeId                           | int           | Inga          | En unik identifierare för kodimplementering av kontraktet |
| KontraktLedgerIdentifier                 | nvarchar(255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en viss distribuerad redovisning. Till exempel Ethereum. |
| KontraktDeployedAvUserId                 | int           | Inga          | Den unika identifieraren för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserFirstName          | nvarchar(50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserLastName           | nvarchar(255) | Ja         | Efternamn på den användare som distribuerade kontraktet |
| KontraktDeployedAvUserExternalId         | nvarchar(255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID:et det guid som representerar deras identitet i konsortiet Azure Active Directory.                                                                                                                                                |
| KontraktDeployedAvUserEmailAddress       | nvarchar(255) | Ja         | E-postadressen till den användare som distribuerade kontraktet |
| ArbetsflödesfunktionId                       | int           | Inga          | En unik identifierare för en arbetsflödesfunktion |
| ArbetsflödesfunktionNamn                     | nvarchar(50)  | Inga          | Namnet på funktionen |
| ArbetsflödeFunktionDisplayName              | nvarchar(255) | Inga          | Namnet på en funktion som ska visas i användargränssnittet |
| ArbetsflödesfunktionDescription              | nvarchar(255) | Inga          | Beskrivningen av funktionen |
| KontraktActionId                         | int           | Inga          | Den unika identifieraren för en kontraktsåtgärd |
| Status för status för kontraktsåtgärdsprovisioner         | int           | Inga          | Identifierar den aktuella statusen för etableringsprocessen för kontraktsåtgärden. Möjliga värden: <br />0 – Kontraktsåtgärden har skapats av API:et i databasen<br />1 – Kontraktsåtgärden har skickats till redovisningen<br />2 – Kontraktsåtgärden har distribuerats till redovisningen<br />3 eller 4 - Kontraktet kunde inte distribueras till redovisningen<br />5 - Kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1.5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen visas **vwContractActionV0** som endast stöder värden 0 till 2. |
| KontraktActionTimestamp                  | datumtid(2,7) | Inga          | Tidsstämpeln för kontraktsåtgärden |
| ContractActionExecutedAvUserId           | int           | Inga          | Unik identifierare för den användare som utförde kontraktsåtgärden |
| ContractActionExecutedAvUserFirstName    | int           | Ja         | Förnamn för den användare som utförde kontraktsåtgärden |
| ContractActionExecutedAvUserLastName     | nvarchar(50)  | Ja         | Efternamn på den användare som utförde kontraktsåtgärden |
| ContractActionExecutedAvUserExternalId   | nvarchar(255) | Ja         | Extern identifierare för den användare som utförde kontraktsåtgärden. Som standard är det här ID:et det guid som representerar deras identitet i konsortiet Azure Active Directory. |
| ContractActionExecutedAvUserEmailAddress | nvarchar(255) | Ja         | E-postadressen till den användare som utförde kontraktsåtgärden |
| ArbetsflödesfunktionParameterId              | int           | Inga          | En unik identifierare för en parameter för funktionen |
| ArbetsflödeFunktionParameterNamn            | nvarchar(50)  | Inga          | Namnet på en parameter för funktionen |
| ArbetsflödeFunktionParameterDisplayName     | nvarchar(255) | Inga          | Namnet på en funktionsparameter som ska visas i användargränssnittet |
| ArbetsflödeFunktionParameterDataTypeId      | int           | Inga          | Den unika identifieraren för den datatyp som är associerad med en arbetsflödesfunktionsparameter |
| ArbetsflödeParameterDataTypeName            | nvarchar(50)  | Inga          | Namnet på en datatyp som är associerad med en arbetsflödesfunktionsparameter |
| KontraktActionParameterValue             | nvarchar(255) | Inga          | Värdet för parametern som lagras i det smarta kontraktet |
| BlockHash (blockhash)                                | nvarchar(255) | Ja         | Hash av blocket |
| BlockNummer                              | int           | Ja         | Numret på blocket i redovisningen |
| BlockTimestamp                           | datumtid(2,7) | Ja         | Tidsstämpeln för blocket |
| TransaktionId (transaktionId)                            | int           | Inga          | En unik identifierare för transaktionen |
| Transaktionfrån                          | nvarchar(255) | Ja         | Den part som påbörjade transaktionen |
| TransaktionTill                            | nvarchar(255) | Ja         | Det parti som genomfördes med |
| TransaktionHash                          | nvarchar(255) | Ja         | Hash för en transaktion |
| TransactionIsWorkbenchTransaction        | bit           | Ja         | En bit som identifierar om transaktionen är en Azure Blockchain Workbench-transaktion |
| Status för transaktionsetablering            | int           | Ja         | Identifierar den aktuella statusen för etableringsprocessen för transaktionen. Möjliga värden: <br />0 – Transaktionen har skapats av API:et i databasen<br />1 – Transaktionen har skickats till redovisningen<br />2 – Transaktionen har distribuerats till redovisningen                 |
| Transaktionsvärde                         | decimal(32,2) | Ja         | Värdet på transaktionen |

## <a name="vwcontractproperty"></a>vwContractProperty

Den här vyn representerar majoriteten av informationen som är relaterad till egenskaper som är associerade med ett kontrakt och är utformad för att enkelt underlätta vanliga rapporteringsscenarier. För varje egenskap som tas, innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om den användare som distribuerade arbetsflödet
-   Associerad definition av smart kontraktsegenskap
-   Specifika förekomstvärden för egenskaper
-   Uppgifter om avtalets statliga egendom

| Namn                               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                 | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen.                      |
| WorkflowId (ArbetsflödeId)                         | int           | Inga          | Den unika identifieraren för arbetsflödet |
| Arbetsflödesnamn                       | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName                | nvarchar(255) | Inga          | Namnet på arbetsflödet som visas i användargränssnittet |
| ArbetsflödeSbeskrivning                | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet |
| KontraktId                         | int           | Inga          | Den unika identifieraren för kontraktet |
| Status för kontraktsbeetablering         | int           | Inga          | Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – Kontraktet har skapats av API:et i databasen<br />1 – Kontraktet har skickats till redovisningen<br />2 – Kontraktet har distribuerats till redovisningen<br />3 eller 4 - Kontraktet kunde inte distribueras till redovisningen<br />5 - Kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1.5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen, visa **vwContractPropertyV0** är tillgänglig som endast stöder värden 0 till 2. |
| KontraktCodeId                     | int           | Inga          | En unik identifierare för kodimplementering av kontraktet |
| KontraktLedgerIdentifier           | nvarchar(255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en viss distribuerad redovisning. Till exempel Ethereum. |
| KontraktDeployedAvUserId           | int           | Inga          | Den unika identifieraren för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserFirstName    | nvarchar(50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserLastName     | nvarchar(255) | Ja         | Efternamn på den användare som distribuerade kontraktet |
| KontraktDeployedAvUserExternalId   | nvarchar(255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID:et det guid som representerar deras identitet i konsortiet Azure Active Directory |
| KontraktDeployedAvUserEmailAddress | nvarchar(255) | Ja         | E-postadressen till den användare som distribuerade kontraktet |
| WorkflowPropertyId                 | int           |             | En unik identifierare för en egenskap i ett arbetsflöde |
| WorkflowPropertyDataTypeId         | int           | Inga          | ID för egenskapens datatyp |
| ArbetsflödesförslagDataTypeName       | nvarchar(50)  | Inga          | Namnet på egenskapens datatyp |
| Arbetsflödesnamn               | nvarchar(50)  | Inga          | Namnet på arbetsflödesegenskapen |
| ArbetsflödesfelDisplayName        | nvarchar(255) | Inga          | Visningsnamnet för arbetsflödesegenskapen |
| ArbetsflödesåtgärdBeskrivelse        | nvarchar(255) | Ja         | En beskrivning av fastigheten |
| KontraktFöravärdera              | nvarchar(255) | Inga          | Värdet för en fastighet på kontraktet |
| Tillståndsnamn                          | nvarchar(50)  | Ja         | Om den här egenskapen innehåller kontraktets tillstånd är det visningsnamnet för tillståndet. Om det inte är associerat med tillståndet är värdet null. |
| StateDisplayName (StateDisplayName)                   | nvarchar(255) | Inga          | Om den här egenskapen innehåller tillståndet är det visningsnamnet för tillståndet. Om det inte är associerat med tillståndet är värdet null. |
| StateValue (statligtvärde)                         | nvarchar(255) | Ja         | Om den här egenskapen innehåller tillståndet är det tillståndsvärdet. Om det inte är associerat med tillståndet är värdet null. |

## <a name="vwcontractstate"></a>vwContractState (vwContractState)

Den här vyn representerar majoriteten av informationen om tillståndet i ett visst kontrakt och är utformad för att enkelt underlätta vanliga rapporteringsscenarier. Varje post i den här vyn innehåller följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om den användare som distribuerade arbetsflödet
-   Associerad definition av smart kontraktsegenskap
-   Uppgifter om avtalets statliga egendom

| Namn                               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                                        |
|------------------------------------|---------------|-------------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                      | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                    | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName             | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                 | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen. |
| WorkflowId (ArbetsflödeId)                         | int           | Inga          | En unik identifierare för arbetsflödet |
| Arbetsflödesnamn                       | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName                | nvarchar(255) | Inga          | Namnet som visas i användargränssnittet |
| ArbetsflödeSbeskrivning                | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet |
| KontraktLedgerImplementId     | nvarchar(255) | Ja         | En unik identifierare som är associerad med den distribuerade versionen av ett smart kontrakt för en viss distribuerad redovisning. Till exempel Ethereum. |
| KontraktId                         | int           | Inga          | En unik identifierare för kontraktet |
| Status för kontraktsbeetablering         | int           | Inga          |Identifierar den aktuella statusen för etableringsprocessen för kontraktet. Möjliga värden: <br />0 – Kontraktet har skapats av API:et i databasen<br />1 – Kontraktet har skickats till redovisningen<br />2 – Kontraktet har distribuerats till redovisningen<br />3 eller 4 - Kontraktet kunde inte distribueras till redovisningen<br />5 - Kontraktet har distribuerats till redovisningen <br /><br />Från och med version 1.5 stöds värdena 0 till 5. För bakåtkompatibilitet i den aktuella versionen visas det i **vwContractStateV0** som endast stöder värden 0 till 2. |
| ConnectionId (på)-anslutning)                       | int           | Inga          | En unik identifierare för blockchain-instansen som arbetsflödet distribueras till |
| KontraktCodeId                     | int           | Inga          | En unik identifierare för kodimplementering av kontraktet |
| KontraktDeployedAvUserId           | int           | Inga          | Unik identifierare för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserExternalId   | nvarchar(255) | Inga          | Extern identifierare för den användare som distribuerade kontraktet. Som standard är det här ID:et det guid som representerar deras identitet i konsortiet Azure Active Directory. |
| KontraktDeployedAvUserFirstName    | nvarchar(50)  | Ja         | Förnamn för den användare som distribuerade kontraktet |
| KontraktDeployedAvUserLastName     | nvarchar(255) | Ja         | Efternamn på den användare som distribuerade kontraktet |
| KontraktDeployedAvUserEmailAddress | nvarchar(255) | Ja         | E-postadressen till den användare som distribuerade kontraktet |
| WorkflowPropertyId                 | int           | Inga          | En unik identifierare för en arbetsflödesegenskap |
| WorkflowPropertyDataTypeId         | int           | Inga          | ID för datatypen för arbetsflödesegenskapen |
| ArbetsflödesförslagDataTypeName       | nvarchar(50)  | Inga          | Namnet på datatypen för egenskapen arbetsflöde |
| Arbetsflödesnamn               | nvarchar(50)  | Inga          | Namnet på arbetsflödesegenskapen |
| ArbetsflödesfelDisplayName        | nvarchar(255) | Inga          | Visningsnamnet på egenskapen som ska visas i ett användargränssnitt |
| ArbetsflödesåtgärdBeskrivelse        | nvarchar(255) | Ja         | Beskrivningen av fastigheten |
| KontraktFöravärdera              | nvarchar(255) | Inga          | Värdet för en fastighet som lagras i kontraktet |
| Tillståndsnamn                          | nvarchar(50)  | Ja         | Om den här egenskapen innehåller tillståndet är det visningsnamnet för tillståndet. Om det inte är associerat med tillståndet är värdet null. |
| StateDisplayName (StateDisplayName)                   | nvarchar(255) | Inga          | Om den här egenskapen innehåller tillståndet är det visningsnamnet för tillståndet. Om det inte är associerat med tillståndet är värdet null. |
| StateValue (statligtvärde)                         | nvarchar(255) | Ja         | Om den här egenskapen innehåller tillståndet är det tillståndsvärdet. Om det inte är associerat med tillståndet är värdet null. |

## <a name="vwuser"></a>vwUser (vwUser)

Den här vyn innehåller information om konsortiemedlemmar som har etablerats för att använda Azure Blockchain Workbench. Som standard fylls data i genom den första etableringen av användaren.

| Namn               | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                               |
|--------------------|---------------|-------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ID                 | int           | Inga          | En unik identifierare för en användare |
| ExterntID         | nvarchar(255) | Inga          | En extern identifierare för en användare. Som standard är detta ID det guid som representerar Azure Active Directory ID för användaren. |
| EtableringStatus | int           | Inga          |Identifierar den aktuella statusen för etableringsprocessen för användaren. Möjliga värden: <br />0 – Användaren har skapats av API:et<br />1 – En nyckel har associerats med användaren i databasen<br />2 – Användaren är helt etablerad |
| FirstName          | nvarchar(50)  | Ja         | Användarens förnamn |
| LastName           | nvarchar(50)  | Ja         | Användarens efternamn |
| Emailaddress       | nvarchar(255) | Ja         | Användarens e-postadress |

## <a name="vwworkflow"></a>vwArbetsflöde

Den här vyn representerar information om grundläggande arbetsflödemetadata samt arbetsflödets funktioner och parametrar. Den är utformad för rapportering och innehåller också metadata om programmet som är associerat med arbetsflödet. Den här vyn innehåller data från flera underliggande tabeller för att underlätta rapporteringen om arbetsflöden. För varje arbetsflöde innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om associerad arbetsflödesstarttillstånd

| Namn                              | Typ          | Kan vara Null | Beskrivning                                                                                                                                |
|-----------------------------------|---------------|-------------|--------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                     | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                   | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName            | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                | bit           | Inga          | Identifierar om programmet är aktiverat |
| WorkflowId (ArbetsflödeId)                        | int           | Ja         | En unik identifierare för ett arbetsflöde |
| Arbetsflödesnamn                      | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName               | nvarchar(255) | Inga          | Namnet som visas i användargränssnittet |
| ArbetsflödeSbeskrivning               | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet. |
| WorkflowConstructorFunctionId     | int           | Inga          | Identifieraren för arbetsflödesfunktionen som fungerar som konstruktör för arbetsflödet |
| ArbetsflödeStartStateId              | int           | Inga          | En unik identifierare för tillståndet |
| ArbetsflödeStartStateName            | nvarchar(50)  | Inga          | Namnet på staten |
| ArbetsflödeStartStateDisplayName     | nvarchar(255) | Inga          | Namnet som ska visas i användargränssnittet för tillståndet |
| ArbetsflödeStartStateDescription     | nvarchar(255) | Ja         | En beskrivning av arbetsflödets tillstånd |
| ArbetsflödeStartStateStyle           | nvarchar(50)  | Ja         | Det här värdet identifierar den procentuella slutförande som arbetsflödet är när i det här tillståndet |
| ArbetsflödeStartStateValue           | int           | Inga          | Värdet av tillståndet |
| ArbetsflödeStartStatePercentComplete | int           | Inga          | En textbeskrivning som ger en ledtråd till klienter om hur du återger det här tillståndet i användargränssnittet. Stater som stöds inkluderar *framgång* och *misslyckande* |

## <a name="vwworkflowfunction"></a>vwArbetsflödeFunktion

Den här vyn representerar information om grundläggande arbetsflödemetadata samt arbetsflödets funktioner och parametrar. Den är utformad för rapportering och innehåller också metadata om programmet som är associerat med arbetsflödet. Den här vyn innehåller data från flera underliggande tabeller för att underlätta rapporteringen om arbetsflöden. För varje arbetsflödesfunktion innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om arbetsflödesfunktion

| Namn                                 | Typ          | Kan vara Null | Beskrivning                                                                          |
|--------------------------------------|---------------|-------------|--------------------------------------------------------------------------------------|
| ApplicationId                        | int           | Inga          | En unik identifierare för programmet |
| ApplicationName                      | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName               | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled                   | bit           | Inga          | Identifierar om programmet är aktiverat |
| WorkflowId (ArbetsflödeId)                           | int           | Inga          | En unik identifierare för ett arbetsflöde |
| Arbetsflödesnamn                         | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName                  | nvarchar(255) | Inga          | Namnet på arbetsflödet som visas i användargränssnittet |
| ArbetsflödeSbeskrivning                  | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet |
| ArbetsflödesfunktionId                   | int           | Inga          | En unik identifierare för en funktion |
| ArbetsflödesfunktionNamn                 | nvarchar(50)  | Ja         | Namnet på funktionen |
| ArbetsflödeFunktionDisplayName          | nvarchar(255) | Inga          | Namnet på en funktion som ska visas i användargränssnittet |
| ArbetsflödesfunktionDescription          | nvarchar(255) | Ja         | Beskrivningen av arbetsflödesfunktionen |
| ArbetsflödesfunktionIsKonstruktör        | bit           | Inga          | Identifierar om arbetsflödesfunktionen är konstruktorn för arbetsflödet |
| ArbetsflödesfunktionParameterId          | int           | Inga          | En unik identifierare för en parameter för en funktion |
| ArbetsflödeFunktionParameterNamn        | nvarchar(50)  | Inga          | Namnet på en parameter för funktionen |
| ArbetsflödeFunktionParameterDisplayName | nvarchar(255) | Inga          | Namnet på en funktionsparameter som ska visas i användargränssnittet |
| ArbetsflödeFunktionParameterDataTypeId  | int           | Inga          | En unik identifierare för den datatyp som är associerad med en arbetsflödesfunktionsparameter |
| ArbetsflödeParameterDataTypeName        | nvarchar(50)  | Inga          | Namnet på en datatyp som är associerad med en arbetsflödesfunktionsparameter |

## <a name="vwworkflowproperty"></a>vwArbetsflödeFörslag

Den här vyn representerar de egenskaper som definierats för ett arbetsflöde. För varje egenskap innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om arbetsflödesegenskap

| Namn                         | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Inga          | En unik identifierare för programmet |
| ApplicationName              | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| ApplicationEnabled           | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen. |
| WorkflowId (ArbetsflödeId)                   | int           | Inga          | En unik identifierare för arbetsflödet |
| Arbetsflödesnamn                 | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName          | nvarchar(255) | Inga          | Namnet som ska visas för arbetsflödet i ett användargränssnitt |
| ArbetsflödeSbeskrivning          | nvarchar(255) | Ja         | En beskrivning av arbetsflödet |
| WorkflowPropertyID           | int           | Inga          | En unik identifierare för en egenskap i ett arbetsflöde |
| Arbetsflödesnamn         | nvarchar(50)  | Inga          | Namnet på egenskapen |
| ArbetsflödesåtgärdBeskrivelse  | nvarchar(255) | Ja         | En beskrivning av fastigheten |
| ArbetsflödesfelDisplayName  | nvarchar(255) | Inga          | Namnet som ska visas i ett användargränssnitt |
| WorkflowPropertyWorkflowId   | int           | Inga          | ID:et för det arbetsflöde som den här egenskapen är associerad med |
| WorkflowPropertyDataTypeId   | int           | Inga          | ID:et för den datatyp som definierats för egenskapen |
| ArbetsflödesförslagDataTypeName | nvarchar(50)  | Inga          | Namnet på den datatyp som definierats för egenskapen |
| WorkflowPropertyIsState      | bit           | Inga          | Det här fältet identifierar om den här arbetsflödesegenskapen innehåller arbetsflödets tillstånd |

## <a name="vwworkflowstate"></a>vwWorkflowState (S)

Den här vyn representerar de egenskaper som är associerade med ett arbetsflöde. För varje kontrakt innehåller den här vyn följande data:

-   Associerad programdefinition
-   Associerad arbetsflödesdefinition
-   Information om arbetsflödestillstånd

| Namn                         | Typ          | Kan vara Null | Beskrivning                                                                                                                                                                                                                                                   |
|------------------------------|---------------|-------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| ApplicationId                | int           | Inga          | En unik identifierare för programmet |
| ApplicationName              | nvarchar(50)  | Inga          | Namnet på programmet |
| ApplicationDisplayName       | nvarchar(255) | Inga          | En beskrivning av ansökan |
| ApplicationEnabled           | bit           | Inga          | Identifierar om programmet är aktiverat för tillfället.<br />**Anm.:** Även om ett program kan återspeglas som inaktiverat i databasen finns associerade kontrakt kvar på blockkedjan och data om dessa kontrakt finns kvar i databasen. |
| WorkflowId (ArbetsflödeId)                   | int           | Inga          | Den unika identifieraren för arbetsflödet |
| Arbetsflödesnamn                 | nvarchar(50)  | Inga          | Namnet på arbetsflödet |
| ArbetsflödeDisplayName          | nvarchar(255) | Inga          | Namnet som visas i användargränssnittet för arbetsflödet |
| ArbetsflödeSbeskrivning          | nvarchar(255) | Ja         | Beskrivningen av arbetsflödet |
| WorkflowStateID              | int           | Inga          | Den unika identifieraren för tillståndet |
| Arbetsflödesnamn            | nvarchar(50)  | Inga          | Namnet på staten |
| ArbetsflödeStateDisplayName     | nvarchar(255) | Inga          | Namnet som ska visas i användargränssnittet för tillståndet |
| ArbetsflödeStateDescription     | nvarchar(255) | Ja         | En beskrivning av arbetsflödets tillstånd |
| WorkflowStatePercentComplete | int           | Inga          | Det här värdet identifierar den procentuella slutförande som arbetsflödet är när i det här tillståndet |
| ArbetsflödeStateValue           | nvarchar(50)  | Inga          | Värdet av staten |
| WorkflowStateStyle           | nvarchar(50)  | Inga          | En textbeskrivning som ger en ledtråd till klienter om hur du återger det här tillståndet i användargränssnittet. Stater som stöds inkluderar *framgång* och *misslyckande* |
