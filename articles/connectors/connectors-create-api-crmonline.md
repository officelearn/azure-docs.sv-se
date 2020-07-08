---
title: Ansluta till Dynamics 365
description: Skapa och hantera Dynamics 365-poster med hjälp av Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jdaly, logicappspm
ms.topic: conceptual
ms.date: 05/09/2020
tags: connectors
ms.openlocfilehash: 00bf8ea2b783e09711a95f203bdfcce0e6b90b2c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2020
ms.locfileid: "82994312"
---
# <a name="create-and-manage-records-in-dynamics-365-by-using-azure-logic-apps"></a>Skapa och hantera poster i Dynamics 365 med hjälp av Azure Logic Apps

Dynamics 365 använder [common data service](https://docs.microsoft.com/powerapps/maker/common-data-service/data-platform-intro). För anslutningar till Dynamics 365 använder du [common data service-anslutningen](https://docs.microsoft.com/connectors/commondataservice/).

> [!IMPORTANT]
> [Dynamics 365-anslutningen](https://docs.microsoft.com/connectors/dynamicscrmonline/) är föråldrad men fortsätter att fungera tills den tagits bort. Använd inte Dynamics 365-anslutningen för nya Logic Apps. Ingen tids linje har presenter ATS för att ta bort Dynamics 365-anslutningen. Du behöver inte konvertera befintliga logikappar till Common Data Service-anslutningsprogrammet eller något annat nytt anslutningsprogram, men du måste konvertera dina logikappar när anslutningsprogrammet tas bort. Mer information finns i [Dynamics 365-kopplingen är föråldrad](https://docs.microsoft.com/power-platform/important-changes-coming).
>
> [Common Data Service-anslutningsprogrammet](https://docs.microsoft.com/connectors/commondataservice/) ger samma funktioner som inaktuella Dynamics 365 Connector, men innehåller förbättringar som ökar tillförlitligheten. Information om hur du använder Common Data Service-anslutningsprogrammet i Logic Apps finns i [Skapa och hantera poster i Common Data Service med Azure Logic Apps](../connectors/connect-common-data-service.md).

Mer information om Common Data Service finns i följande avsnitt:

* [Lär dig: kom igång med Common Data Service](https://docs.microsoft.com/learn/modules/get-started-with-powerapps-common-data-service/)
* [Lär dig: ansluta och analysera dina Dynamics 365-data med hjälp av Power Platform och Common Data Service](https://docs.microsoft.com/learn/wwl/connect-analyze-dynamics-365-data/)