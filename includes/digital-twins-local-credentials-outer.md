---
author: baanders
description: inkludera fil för att konfigurera lokal autentisering för DefaultAzureCredential i Azure Digitals-exempel med Intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35643370d6fd313d42f954a578b73befc025c040
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011269"
---
### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Mer information om olika sätt som en klient-app kan autentisera med Azure Digitals dubbla finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]