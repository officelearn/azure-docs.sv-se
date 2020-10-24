---
author: baanders
description: inkludera fil för DefaultAzureCredential i Azure Digitals-exempel – Obs!
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 35996595158994308a78f4d1197dcdc8f00af618
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494474"
---
>[!NOTE]
> I det här exemplet används [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential?preserve-view=true&view=azure-dotnet) (del av `Azure.Identity` biblioteket) för att autentisera användare med Azure Digitals-instansen när du kör den på din lokala dator. Med den här typen av autentisering söker exemplet efter autentiseringsuppgifter för Azure i din lokala miljö, till exempel en inloggning från en lokal [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) eller Visual Studio/Visual Studio Code.
>
> Mer `DefaultAzureCredential` information om hur du använder och andra autentiseringsalternativ finns i [*How-to: Write app authentication code*](../articles/digital-twins/how-to-authenticate-client.md).