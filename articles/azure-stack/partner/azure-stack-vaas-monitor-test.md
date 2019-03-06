---
title: Övervaka och hantera tester i Azure Stack VaaS-portalen | Microsoft Docs
description: Övervaka och hantera tester i Azure Stack VaaS-portalen.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/04/2019
ms.author: mabrigg
ms.reviewer: johnhas
ms.lastreviewed: 11/26/2018
ROBOTS: NOINDEX
ms.openlocfilehash: d4854bd623bb11d50a712be3a5aea07db24c2951
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/06/2019
ms.locfileid: "57432251"
---
# <a name="monitor-and-manage-tests-in-the-vaas-portal"></a>Övervaka och hantera tester i VaaS-portalen

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Schemalägga testerna mot din Azure Stack-lösning, verifiering som en tjänst (VaaS) börjar efter rapporterar status för körning av test. Den här informationen är tillgänglig i portalen VaaS tillsammans med åtgärder som att ändra på tidsplanering och avbryter tester.

## <a name="navigate-to-the-workflow-tests-summary-page"></a>Gå till sidan arbetsflöde tester sammanfattning

1. Välj en befintlig lösning som har minst ett arbetsflöde på instrumentpanelen för lösningar.

    ![Arbetsflöde för paneler](media/tile_all-workflows.png)

1. Välj **hantera** på panelen arbetsflöde. Nästa sida visar en lista över de arbetsflöden som skapats för den valda lösningen.

1. Välj Arbetsflödesnamnet att öppna dess sammanfattning test.

## <a name="change-workflow-parameters"></a>Ändra parametrarna för arbetsflödet

Varje arbetsflödestyp av kan du redigera den [parametrarna för Webbtestet](azure-stack-vaas-parameters.md#test-parameters) anges under arbetsflödet skapats.

1. På sammanfattningssidan tester, Välj den **redigera** knappen.

1. Ange nya värden enligt [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md).

1. Välj **skicka** att spara värdena.

> [!NOTE]
> I den **testet** arbetsflöde, behöver du slutför test-markering och gå till granskningssidan innan du kan spara de nya parametervärdena.

### <a name="add-tests-test-pass-only"></a>Lägg till tester (endast testet)

I **testet** arbetsflöden, både den **lägga till tester** och **redigera** knappar kan du schemalägga nya test i arbetsflödet.

> [!TIP]
> Välj **lägga till tester** om du bara vill schemalägga nya test och behöver inte redigera parametrar för en **testet** arbetsflöde.

## <a name="managing-test-instances"></a>Hantering av test-instanser

För inofficiella körningar (d.v.s. den **testet** arbetsflöde), sammanfattningssidan testerna innehåller testerna schemalagda mot Azure Stack-lösning.

För officiella körningar (d.v.s. den **verifiering** arbetsflöden), sammanfattningssidan testerna innehåller testerna som krävs för att slutföra verifieringen av Azure Stack-lösning. Verifieringstester schemaläggs från den här sidan.

Varje schemalagd test-instans visas följande information:

| Kolumn | Beskrivning |
| --- | --- |
| Testnamn | Namnet och versionen av testet. |
| Kategori | Syftet med testet. |
| Skapad | Den tid då testet har schemalagts. |
| Startade | Den tid då testet startade körningen. |
| Varaktighet | Hur lång tid som testet kördes. |
| Status | Tillstånd eller resultatet av testet. Pre-Execution eller pågående statusen är: `Pending`, `Running`. Terminal statusen är: `Cancelled`, `Failed`, `Aborted`, `Succeeded`. |
| Agentnamn | Namnet på den agent som körde testet. |
| Totala åtgärder | Det totala antalet åtgärder försökte under testet. |
| Överförda åtgärder | Antal åtgärder som har skapats under testet. |
|  Misslyckade åtgärder | Antal åtgärder som misslyckades under testet. |

### <a name="actions"></a>Åtgärder

Varje test-instans Listar tillgängliga åtgärder som du kan vidta när du klickar på dess snabbmenyn **[...]**  i tabellen test-instanser.

#### <a name="view-information-about-the-test-definition"></a>Visa information om test-definition

Välj **visa information om** från snabbmenyn för att visa allmän information om test-definition. Detta är gemensam för varje test-instans med samma namn och version.

| Testa egenskapen | Beskrivning |
| -- | -- |
| Testnamn | Namnet på testet. |
| Testversion | Versionen av testet. |
| Utgivare | Utgivare av testet. |
| Kategori |  Syftet med testet. |
| Mål-tjänster | Azure Stack-tjänster som har testats. |
| Beskrivning | Beskrivning av testet. |
| Uppskattad varaktighet (minuter) | Förväntad körning av testet. |
| Länkar | Relevant information om test- eller kontaktpunkter. |

#### <a name="view-test-instance-parameters"></a>Visa parametrar för instans

Välj **Visa parametrar** från snabbmenyn för att visa de parametrar som angetts för test-instans på Schemalägg tid. Känsliga strängar som lösenord visas inte. Den här åtgärden är endast tillgänglig för tester som har schemalagts.

Det här fönstret innehåller följande metadata för alla test-instanser:

| Testa instansegenskap | Beskrivning |
| -- | -- |
| Testnamn | Namnet på testet. |
| Testversion | Versionen av testet. |
| Testa instans-ID | GUID som identifierar den specifika instansen av testet. |

#### <a name="view-test-instance-operations"></a>Visa test instans åtgärder

Välj **Visa åtgärder** från kontexten menyn för att visa detaljerad statusinformation för åtgärder som utförs under testet. Den här åtgärden är endast tillgänglig för tester som har schemalagts.

![Visa åtgärder](media/manage-test_context-menu-operations.png)

#### <a name="download-logs-for-a-completed-test-instance"></a>Hämta loggar för en slutförd test-instans

Välj **Hämtningsloggar** från snabbmenyn för att ladda ned en `.zip` fil på loggar utdata vid testkörning. Den här åtgärden är endast tillgänglig för tester som har slutfört, t.ex, ett test med statusen `Cancelled`, `Failed`, `Aborted`, eller `Succeeded`.

#### <a name="reschedule-a-test-instance-or-schedule-a-test"></a>Omboka en test-instans eller schemalägga ett test

Schemalägga testerna från sidan för hantering av beror på vilken typ av arbetsflödet testet körs under.

##### <a name="test-pass-workflow"></a>Testet arbetsflöde

I arbetsflödet testet **ändra på tidsplanering** en test-instans återanvänder samma uppsättning parametrar som den ursprungliga test-instansen och *ersätter* ursprungliga resultatet, inklusive dess loggar. Du måste ange känsliga strängar som lösenord när du schemalägger om.

1. Välj **omboka** från snabbmenyn för att öppna en kommandotolk för att ändra på tidsplanering för test-instans.

1. Ange eventuella tillämpliga parametrar.

1. Välj **skicka** omboka test-instans och ersätta den befintliga instansen.

##### <a name="validation-workflows"></a>Verifiering arbetsflöden

[!INCLUDE [azure-stack-vaas-workflow-validation-section_schedule](includes/azure-stack-vaas-workflow-validation-section_schedule.md)]

#### <a name="cancel-a-test-instance"></a>Avbryt en test-instans

En schemalagd test som kan avbrytas om dess status är `Pending` eller `Running`.  

1. Välj **Avbryt** från snabbmenyn för att öppna en kommandotolk för att avbryta test-instans.

1. Välj **skicka** annullera test-instans.

## <a name="next-steps"></a>Nästa steg

- [Felsöka verifiering som en tjänst](azure-stack-vaas-troubleshoot.md)
