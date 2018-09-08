---
title: Verifiera en ny Azure Stack-lösning | Microsoft Docs
description: Lär dig hur du validerar en ny Azure Stack-lösning med verifiering som en tjänst.
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
ms.openlocfilehash: 1e908a8cf5576ce3bc3d58d1ef6f29d596ebc58b
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158185"
---
# <a name="validate-a-new-azure-stack-solution"></a>Verifiera en ny Azure Stack-lösning

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Lär dig hur du kan använder valideringsarbetsflödet lösning för certifiering nya lösningar i Azure Stack.

En lösning med Azure Stack är en maskinvara struktur (BoM) som hölls har kommit överens om med Microsoft och har klarat logotypen för Windows Server certifieringskrav. Du kan också använda valideringsarbetsflödet lösning när det har skett en ändring av maskinvara BoM som skulle orsaka en lösning för att klassificeras som *nya*. Om det finns frågor om vad som ska utlösa en **nya** eller **ny certifiering i enlighet** för en lösning för att kontakta på [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

Certifiera din lösning genom att köra arbetsflödet två gånger. Kör den en gång för den *minimalt* konfiguration som stöds. I den kör en gång för den *maximala* konfiguration. Microsoft certifierar lösningen om båda konfigurationerna klara alla tester.

Den här snabbstarten hjälper dig att gå på processen med att lägga till din lösning och köra tester.

## <a name="add-a-new-solution"></a>Lägg till en ny lösning

1. Logga in på den [verifiering portal](https://azurestackvalidation.com).
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
4. Välj **överför** och Lägg sedan till din distributionskonfigurationsfilen. Det här är ett valfritt steg. Du kan också lägga till parametrar för din genom att följa stegen i nästa avsnitt.

    > [!note]  
    > Du kan skapa konfigurationsfilen genom att lägga till parametrarna i miljön parametrarna en gemensam test parametrar avsnitt i gränssnittet. Du kan hämta den fil som skapats av tjänsten från Azure Stack-distributioner som ska verifieras. Anvisningar finns i [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md).

5. Lägg till din miljö parametrar. Mer information finns i [lägga till miljön parametrar](#add-environmental-parameters).
6. Lägg till din vanliga parametrar. Mer information finns i [lägga till parametrar för vanliga](#add-common-test-parameters).

    Beroende på test-definition testet kan kräva att du anger ett värde oberoende av de gemensamma parametrarna eller kan du åsidosätta vanliga parametervärdet.

7. Klicka på **skicka** att schemalägga testet.

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

- [Omboka eller avboka ett test](azure-stack-vaas-monitor-test.md#reschedule-a-test)
- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).