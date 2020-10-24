---
author: baanders
description: inkludera fil för att konfigurera lokal autentisering för DefaultAzureCredential i Azure Digitals dubbla exempel – utan Intro
ms.service: digital-twins
ms.topic: include
ms.date: 10/22/2020
ms.author: baanders
ms.openlocfilehash: 8bf0590b867d37a22706f679bfbeee2140935637
ms.sourcegitcommit: d6a739ff99b2ba9f7705993cf23d4c668235719f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/24/2020
ms.locfileid: "92494654"
---
Med `DefaultAzureCredential` söker exemplet efter autentiseringsuppgifter i din lokala miljö, t. ex. en Azure-inloggning i en lokal [Azure CLI](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) eller Visual Studio/Visual Studio Code. Det innebär att du bör **Logga in på Azure lokalt** via någon av dessa metoder för att ställa in autentiseringsuppgifter för exemplet.

Om du använder Visual Studio eller Visual Studio Code för att köra kod exemplet, se till att du är inloggad i redigeraren med samma autentiseringsuppgifter för Azure som du vill använda för att få åtkomst till din Azure Digital-instansen.

Annars kan du [installera den lokala **Azure CLI**](/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true), starta en kommando tolk på datorn och köra `az login` kommandot för att logga in på ditt Azure-konto. När du sedan kör kod exemplet bör du logga in automatiskt.