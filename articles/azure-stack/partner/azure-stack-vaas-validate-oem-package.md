---
title: Verifiera OEM-tillverkare (original equipment manufacturer)-paket i Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Lär dig hur du kontrollerar OEM-tillverkare (original equipment manufacturer)-paket med verifiering som en tjänst.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/24/2018
ms.author: mabrigg
ms.reviewer: johnhas
ms.openlocfilehash: a08f439780e0080d8da2cde1531e1580dbdad14f
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/20/2018
ms.locfileid: "40235478"
---
# <a name="validate-oem-packages"></a>Verifiera OEM-paket

[!INCLUDE[Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Du kan testa ett nytt OEM-paket när det har skett en ändring av inbyggd programvara eller drivrutiner för en färdiga lösningen verifiering. När paketet har passerat testet, är det signerat av Microsoft. Testet måste innehålla det uppdaterade OEM-tilläggspaket med drivrutiner och inbyggd programvara som har klarat logotyp för Windows Server och datorer tester.

Alla testerna är klara inom 24 timmar med resultatet av **lyckades**. Om något av testerna har ett resultat av **misslyckades**, rapportera ett fel i [Microsoft Collaborate](https://aka.ms/collaborate) och meddela Microsoft genom att skicka ett e-postmeddelande till [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com).

## <a name="get-your-oem-package-signed"></a>Hämta din OEM-paket signerade

1. Kontrollera att den aktuella månatliga uppdateringen har installerats. Den senaste versionen finns i den senaste versionen i [dokumentation om Azure Stack-Operator > Översikt > viktig information om](https://docs.microsoft.com/azure/azure-stack/) .

    Microsoft-programuppdateringar till Azure Stack betecknas med en namngivningskonvention, till exempel 1803 som anger uppdateringen för mars 2018. Information om Azure Stack-uppdateringsprincip takt och viktig information som är tillgängliga, se [Azure Stack hanteringsprincip](https://docs.microsoft.com/azure/azure-stack/azure-stack-servicing-policy).

1. Kontrollera hälsostatus för system genom att köra **Test AzureStack** som beskrivs i [kör ett verifieringstest för Azure Stack. Åtgärda eventuella varningar och fel innan du startar alla tester.

2. I den [verifiering portal](https://azurestackvalidation.com), Välj en befintlig lösning. Om du inte har lagt till din lösning, se [lägga till en ny lösning](azure-stack-vaas-validate-solution-new.md#add-a-new-solution).

3. Välj **starta** på den **paketet verifieringar** panelen för att starta ett nytt arbetsflöde.

    ![Paketet verifieringar](media/image3.png)

4.  Ange en diagnostik anslutningssträngen. Anvisningar finns i [ställa in ett lagringskonto](azure-stack-vaas-set-up-account.md).

    En OEM-tillägg-paketet måste anges för varje Paketvalideringen kör. Ange OEM-paket som har installerats på lösningen i Azure Stack-distributionen. Anvisningar finns i [skapa ett Azure storage-blob för att lagra loggar](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    En JSON-fil med miljövariablerna som måste användas för att slutföra indata för obligatoriska fält för körningen att undvika fel i datainmatningen. Anvisningar finns i [hämta en fil i en Azure Stack-distribution](azure-stack-vaas-parameters.md).

5. Köra testerna.

6. När alla tester har slutförts, skicka namnet på din lösning och paketet verifieras vid [ vaashelp@microsoft.com ](mailto:vaashelp@microsoft.com) till Begär signering av paketet.

## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
