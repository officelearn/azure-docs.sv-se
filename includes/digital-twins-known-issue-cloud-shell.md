---
author: baanders
description: ta med fil för Azure Digitals-CITES kända problem med Cloud Shell autentisering
ms.service: digital-twins
ms.topic: include
ms.date: 9/1/2020
ms.author: baanders
ms.openlocfilehash: 574ca9d42565e897e49d6800e61bb2c33717891b
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/21/2020
ms.locfileid: "92320728"
---
>[!NOTE]
>Det finns för närvarande ett **känt problem** i Cloud Shell som påverkar dessa kommando grupper när de körs från *https://shell.azure.com* : `az dt route` , `az dt model` , `az dt twin` .
>
>För att lösa problemet kan du göra något av följande:
> * Kör `az login` i Cloud Shell innan du kör kommandot.
> * Öppna fönstret Cloud Shell i Azure Portal och slutför din Cloud Shell arbete därifrån.
>  :::image type="content" source="../articles/digital-twins/media/includes/portal-cloud-shell.png" alt-text="Vy av Azure Portal med ikonen &quot;Cloud Shell&quot; markerad och Cloud Shell visas längst ned i Portal fönstret":::
> * Använd den [lokala CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) : en i stället för Cloud Shell.
>
>Mer information om det här problemet finns i [*fel sökning: kända problem i Azure Digitals*](../articles/digital-twins/troubleshoot-known-issues.md#400-client-error-bad-request-in-cloud-shell).