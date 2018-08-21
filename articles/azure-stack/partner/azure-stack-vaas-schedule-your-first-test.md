---
title: Använda verifieringen som en tjänst för Azure Stack-portalen för att schemalägga första testet | Microsoft Docs
description: Använda verifieringen som en tjänst för Azure Stack-portalen för att schemalägga första testet.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: 0f681070df4b4b3384171c05edb3851abec2ab5c
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235515"
---
# <a name="quickstart-use-the-validation-as-a-service-portal-to-schedule-your-first-test"></a>Snabbstart: Använda verifieringen som en tjänst-portal för att schemalägga första testet

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du schemalägger första testet med verifiering som en (VaaS)-tjänstportalen för att kontrollera om maskinvaran. Lokal agent måste distribueras på Azure Stack-lösningen som ska verifieras innan du kör verifieringstester.

I den här snabbstarten du lägga till din lösning och kör testerna.

## <a name="prerequisites"></a>Förutsättningar

Innan du följer den här snabbstarten behöver du ha:
 - En verifiering som ett tjänstkonto. Anvisningar finns i [ställa in din validering som ett tjänstkonto](azure-stack-vaas-set-up-account.md).  
- Den lokala agenten är installerad på datorn. Anvisningar finns i [distribuera de lokala virtuella datorerna för agenten och testa](azure-stack-vaas-test-vm.md).

## <a name="add-a-new-solution"></a>Lägg till en ny lösning

1. Logga in på den [verifiering portal](https://azurestackvalidation.com).

    ![Logga in på portalen för verifiering](media/vaas_portalsignin.png)  

2. Välj **ny lösning**.
3. Ange ett namn för lösningen och välj **spara**.

## <a name="create-a-solution-validation-workflow"></a>Skapa ett arbetsflöde för verifiering av lösning

1. Välj lösningens namn.
2. Välj **hantera** på den **lösning verifieringar** panelen.

    ![Lösningen verifieringar](media/image2.png)

## <a name="create-a-solution-workflow"></a>Skapa ett arbetsflöde för lösningen

1. Välj **ny lösning validering**.
2. Skriv namnet på verifieringen.
3. Välj **minsta** eller **maximala**.  
    - **Minst**  
    Lösningen har konfigurerats med det minsta tillåtna antalet noder.  
    - **Maximalt**  
    Lösningen har konfigurerats med det högsta tillåtna antalet noder.
4. Lägg till din miljö parametrar. Mer information finns i [lägga till miljön parametrar](#add-environmental-parameters).
5. Lägg till din vanliga parametrar. Mer information finns i [lägga till parametrar för vanliga](#add-common-test-parameters).

    Beroende på test-definition testet kan kräva att du anger ett värde oberoende av de gemensamma parametrarna eller kan du åsidosätta vanliga parametervärdet.
6. Klicka på **skicka** att schemalägga testet.

## <a name="add-environmental-parameters"></a>Lägg till miljön parametrar

Lägg till följande parametrar i miljön:

| Pass testinformation | Krävs | Beskrivning |
| --- | --- | --- | --- |
| Azure Stack-version | Krävs | Azure Stack-version (till exempel 20170501.1) Nummervärdet måste vara en giltig Azure Stack bygger tal eller versionen, till exempel 1.0.170330.9 |
| Klient-ID:t | Krävs | Active Directory-klient-ID. Detta måste vara en GUID (till exempel ECA23256-6BA0-4F27-8E4D-AFB02F088363) |
| Region | Krävs | Distributionsregionen för Azure Stack |
| Klient Resource Manager-slutpunkten | Krävs | Slutpunkt för klient Azure Resource Manager-åtgärder (till exempel https://management.loc-ext.domain.com) |
| Administratören Resource Manager-slutpunkten | Krävs inte | Slutpunkt för klient Azure Resource Manager-åtgärder (till exempel https://management.loc-ext.domain.com) |
| Externa FQDN | Krävs inte | Externa kvalificerade fullständigt domännamn som används som suffix för slutpunkter. (till exempel local.azurestack.external eller redmond.contoso.com) |
| Antal noder | Krävs | Antalet noder i din lösning. |

## <a name="add-common-test-parameters"></a>Lägg till parametrarna för testning

Lägg till följande test parametrarna:

| Pass testinformation | Krävs | Beskrivning |
| --- | --- | --- |
| Klient-användarnamn | Krävs | Klient-användarnamn (till exempel tenant@contoso.onmicrosoft.com) |
| Klient-lösenord | Krävs | Lösenordet för klienten. |
| Tjänstadministratör användarnamn | Krävs inte | Klient-användarnamn (till exempel tenant@contoso.onmicrosoft.com) |
| Administratörslösenord för tjänsten | Krävs inte | Tjänstadministratör användarnamn (till exempel serviceadmin@contoso.onmicrosoft.com) |
| Molnadministratören användarnamn | Krävs inte | Azure Stack administratörskonto i domänen (till exempel contoso\cloudadmin) |
| Administratörslösenord för molnet | Krävs inte | |
|  Anslutningssträngen för diagnostik | Krävs inte | SAS-URI för Azure Storage-kontot på vilka felsökning kopieras loggfilerna vid testkörning. Se [skapa ett Azure storage-blob för att lagra loggar](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs). <br><br>Värdet för den **diagnostik anslutningssträngen** gemensamma parametern ska lagras av tjänsten och anges på Schemalägg tid att alla tester i arbetsflödet som använder den här parametern. När SAS-Webbadressen är inom 30 dagar har upphört att gälla, uppmanas du att ange en ny SAS-URL på sidan med vanliga parametrar. |
| Tagg - namn | Krävs inte |  Beskrivande taggar kan anges för att märka arbetsflödet. Det här är namnet på taggen. |
| Tagg - värde | Krävs inte | Beskrivande taggar kan anges för att märka arbetsflödet. Det här är värdet för taggen. |

## <a name="next-steps"></a>Nästa steg

- [Verifiera en ny Azure Stack-lösning](azure-stack-vaas-validate-solution-new.md)  
- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
