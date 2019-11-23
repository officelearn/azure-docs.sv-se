---
title: Azure Blockchain Workbench Preview overview
description: Overview of Azure Blockchain Workbench Preview and its capabilities.
ms.date: 09/05/2019
ms.topic: overview
ms.reviewer: brendal
ms.openlocfilehash: 9c40f95aa1490362b9232ec8d87a199d7a41c731
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/22/2019
ms.locfileid: "74324554"
---
# <a name="what-is-azure-blockchain-workbench"></a>Vad är Azure Blockchain Workbench?

Azure Blockchain Workbench Preview is a collection of Azure services and capabilities designed to help you create and deploy blockchain applications to share business processes and data with other organizations. Azure Blockchain Workbench tillhandahåller infrastrukturen för att skapa blockkedjeprogram och gör det möjligt för utvecklarna att fokusera på att skapa affärslogik och smarta kontrakt. Dessutom blir det enklare att skapa blockkedjeprogram genom att integrera flera Azure-tjänster och funktioner så att vanliga utvecklingsuppgifter kan automatiseras.

[!INCLUDE [Preview note](./includes/preview.md)]

## <a name="create-blockchain-applications"></a>Skapa blockkedjeprogram

Med Blockchain Workbench kan du definiera blockkedjeprogram genom konfiguration och att skriva smart kontraktkod. Du kan rivstarta utvecklingen av blockkedjeprogram och fokusera på att definiera ditt kontrakt och skriva affärslogik istället för att skapa scaffold-teknik och konfigurera stödtjänster.

## <a name="manage-applications-and-users"></a>Hantera program och användare

Azure Blockchain Workbench innehåller ett webbprogram och REST API:er för att hantera blockkedjeprogram och användare. Blockchain Workbench-administratörer kan hantera programåtkomst och tilldela dina användare programroller. Azure AD-användare mappas automatiskt till medlemmar i programmet.

## <a name="integrate-blockchain-with-applications"></a>Integrera blockkedja med program

Du kan integrera med befintliga system genom att använda Blockchain Workbench REST API:er och meddelandebaserade API:er. API:erna tillhandahåller ett gränssnitt som gör det möjligt att ersätta eller använda flera tekniker för distribuerad redovisning, lagring och databaserbjudanden.

Blockchain Workbench kan omvandla meddelanden som skickats till dess meddelandebaserade API och skapa transaktioner i ett format som förväntas av blockkedjans egna API.  Workbench kan logga in och vidarebefordra transaktioner till lämplig blockkedja. 

Workbench levererar automatiskt händelser till Service Bus och Event Grid så att meddelanden kan skickas till underordnade konsumenter. Utvecklarna kan integrera med endera av dessa meddelandesystem för att driva transaktioner och studera resultaten.

## <a name="deploy-a-blockchain-network"></a>Distribuera ett blockkedjenätverk

Azure Blockchain Workbench förenklar konfigurationen av blockkedjenätverket som en förkonfigurerad lösning med en Azure Resource Manager-lösningsmall. Mallen tillhandahåller en förenklad distribution som distribuerar alla komponenter som behövs för att köra ett konsortium. Blockchain Workbench stöder för närvarande Ethereum.

## <a name="use-active-directory"></a>Använd Active Directory

Med befintliga blockkedjeprotokoll representeras blockkedjeidentiteterna som en adress i nätverket. Azure Blockchain Workbench avlägsnar direkt blockkedjeidentiteten genom att associera den med en Active Directory-identitet, vilket gör det enklare att skapa företagsprogram med Active Directory-identiteter.

## <a name="synchronize-on-chain-data-with-off-chain-storage"></a>Synkronisera kedjedata med lagring utanför kedjan

Azure Blockchain Workbench gör det lättare att analysera blockkedjehändelser och data genom att automatiskt synkronisera data i blockkedjan med lagring utanför kedjan. I stället för att extrahera data direkt från blockkedjan kan du fråga databassystem utanför kedjan som t.ex. SQL Server. Blockchain expertise is not required for end users who are doing data analysis tasks.

## <a name="next-steps"></a>Nästa steg

> [!div class="nextstepaction"]
> [Azure Blockchain Workbench-arkitektur](architecture.md)
