---
author: baanders
description: ta med fil för Azure Digitals-CITES kända problem med Cloud Shell autentisering
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: bd3a192ed493a886da427cb6d89239a4eb8e4d17
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/09/2020
ms.locfileid: "89290113"
---
>[!NOTE]
>Det finns för närvarande ett **känt problem** i Cloud Shell som påverkar dessa kommando grupper: `az dt route` , `az dt model` , `az dt twin` .
>
>Du kan lösa problemet genom att antingen köra `az login` i Cloud Shell innan du kör kommandot eller använda den [lokala CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : en i stället för Cloud Shell. Mer information finns i [*fel sökning: kända problem i Azure Digitals*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).