---
author: baanders
description: inkludera fil för att konfigurera lokal autentisering för DefaultAzureCredential i Azure Digitals-exempel med Intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 56c63ee13c5e42350a2b544074a0b0527377ca4c
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494661"
---
### <a name="set-up-local-azure-credentials"></a>Konfigurera lokala autentiseringsuppgifter för Azure

I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Mer information om olika sätt som en klient-app kan autentisera med Azure Digitals dubbla finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).

[!INCLUDE [Azure Digital Twins: local credentials prereq (inner)](digital-twins-local-credentials-inner.md)]