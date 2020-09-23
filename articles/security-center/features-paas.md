---
title: Azure Security Center funktioner för Azure PaaS-resurser som stöds.
description: Den här sidan visar tillgängligheten för Azure Security Center funktioner för Azure PaaS-resurser som stöds.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.assetid: be2ab6d5-72a8-411f-878e-98dac21bc5cb
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/01/2020
ms.author: memildin
ms.openlocfilehash: 77bf0f4cd60ba6e85763334a4d864975945ec2c3
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/22/2020
ms.locfileid: "90894782"
---
# <a name="feature-coverage-for-azure-paas-services"></a>Funktions täckning för Azure PaaS Services <a name="paas-services"></a>

Tabellen nedan visar tillgängligheten för Azure Security Center funktioner för Azure PaaS-resurser som stöds.

|Tjänst|Rekommendationer (kostnads fri)|Säkerhets aviseringar (Azure Defender)|Sårbarhets bedömning (Azure Defender)|
|:----|:----:|:----:|:----:|
|Azure App Service|✔|✔|-|
|Azure Automation konto|✔|-|-|
|Azure Batch-konto|✔|-|-|
|Azure Blob Storage|✔|✔|-|
|Azure Cache for Redis|✔|-|-|
|Azure Cloud Services|✔|-|-|
|Azure Cognitive Search|✔|-|-|
|Azure Container Registry|-|-|✔|
|Azure Cosmos DB *|-|✔|-|
|Azure Data Lake Analytics|✔|-|-|
|Azure Data Lake Storage|✔|-|-|
|Azure Database for MySQL *|✔|✔|-|
|Azure Database for PostgreSQL *|✔|✔|-|
|Namn område för Azure Event Hubs|✔|-|-|
|Azure Functions App|✔|-|-|
|Azure Key Vault|✔|✔|-|
|Azure Kubernetes Service|✔|✔|-|
|Azure Load Balancer|✔|-|-|
|Azure Logic Apps|✔|-|-|
|Azure SQL Database|✔|✔|✔|
|Azure SQL-hanterad instans|✔|✔|✔|
|Azure Service Bus namnrymd|✔|-|-|
|Azure Service Fabric-konto|✔|-|-|
|Azure Storage-konton|✔|-|-|
|Azure Stream Analytics|✔|-|-|
|Azure Subscription (Azure-prenumeration)|✔ **|✔|-|
|Azure Virtual Network</br> (inklusive undernät, nätverkskort och nätverks säkerhets grupper)|✔|-|-|

\* Dessa funktioner stöds för närvarande som för hands version.

\*\* Azure Active Directory (Azure AD) rekommendationer är bara tillgängliga för prenumerationer med Azure Defender aktiverat.