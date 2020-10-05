---
author: msmbaldwin
ms.service: key-vault
ms.topic: include
ms.date: 09/03/2020
ms.author: msmbaldwin
ms.openlocfilehash: 9440a33858cb70389e86621ae76be9c08ec56d61
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/05/2020
ms.locfileid: "89482152"
---
1. Se till att du har ett [Azure-konto med en aktiv prenumeration](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

1. Installera [python 2.7 + eller 3.5.3 +](https://www.python.org/downloads).

1. Installera [Azure CLI](/cli/azure/install-azure-cli).

1. Följ instruktionerna för att [Konfigurera autentisering för lokal utveckling](/azure/developer/python/configure-local-development-environment?tabs=cmd#configure-authentication), där du skapar ett lokalt huvud namn för tjänsten och gör det tillgängligt för den Azure Key Vault klienten för python via miljövariabler. 

    När du kör kod direkt i Azure behövs inget separat tjänst huvud namn om appen använder hanterad identitet.

1. I en terminal-eller kommando tolk skapar du en lämplig projektmapp och skapar och aktiverar sedan en virtuell python-miljö enligt beskrivningen i [använda python-virtuella miljöer](/azure/developer/python/configure-local-development-environment?tabs=cmd#use-python-virtual-environments)

1. Installera Azure Active Directory identitets bibliotek:

    ```terminal
    pip install azure.identity
    ```
