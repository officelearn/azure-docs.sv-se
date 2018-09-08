---
title: Verifiera programuppdateringar från Microsoft i Azure Stack-verifiering som en tjänst | Microsoft Docs
description: Lär dig hur du validerar programuppdateringar från Microsoft med verifiering som en tjänst.
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
ms.openlocfilehash: 6ef8c0486a694ac44c53375b24893812b10343e4
ms.sourcegitcommit: 2d961702f23e63ee63eddf52086e0c8573aec8dd
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/07/2018
ms.locfileid: "44158491"
---
# <a name="validate-software-updates-from-microsoft"></a>Verifiera programuppdateringar från Microsoft

[!INCLUDE [Azure_Stack_Partner](./includes/azure-stack-partner-appliesto.md)]

Microsoft släpper regelbundet uppdateringar av Azure Stack-programvaran. De här uppdateringarna tillhandahålls till Azure Stack delad engineering partners förväg som görs tillgänglig så att de kan verifiera uppdateringarna mot sina lösningar och lämna feedback till Microsoft.

## <a name="test-an-existing-solution"></a>Testa en befintlig lösning

1. Logga in på den [verifiering portal](https://azurestackvalidation.com).

2. Välj en befintlig lösning där den uppdaterade från Microsoft har distribuerats och välj **starta** på den **Paketvalideringen** panelen.

    ![Verifiera paketet](media/image3.png)

3. Ange namn för verifiering.

4. Ange Webbadressen till den OEM-paket som har installerats på lösningen vid tidpunkten för distribution. Använd URL: en för det paket som lagras på Azure blob-tjänsten. Mer information finns i [skapa ett Azure storage-blob för att lagra loggar](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

5. Välj **överför** att lägga till din distributionskonfigurationsfilen. Referera till den [validerar en ny lösning för Azure Stack](azure-stack-vaas-validate-solution-new.md) information om hur du laddar upp din konfigurationsfil för distribution.

6. Distributionskonfigurationsfilen måste sedan vara anpassad med rätt miljö parameterfilen, se [miljö parametrar](azure-stack-vaas-parameters.md#environment-parameters) för ytterligare information.

    > [!Note]   
    > Distributionskonfigurationsfilen kan anpassas ytterligare genom att lägga till vanliga parametrar. Mer information finns i [arbetsflödets gemensamma parametrar för Azure Stack-verifiering som en tjänst](azure-stack-vaas-parameters.md)

7. Användarnamn och lösenord för klientanvändare, tjänstadministratör och cloud-administratören måste anges manuellt.

8. Ange URL: en till Azure Storage-blob för att lagra diagnostikloggarna. Mer information finns i [skapa ett Azure storage-blob för att lagra loggar](azure-stack-vaas-set-up-account.md#create-an-azure-storage-blob-to-store-logs).

    > [!Note]  
    > Beskrivande taggar kan anges för att märka arbetsflödet.

10. Välj **skicka** spara arbetsflödet.

Lösningen arbetsflödet körs i ungefär 24 timmar. Lägg till en länk till eller en instruktion om schemaläggning av testerna. Radera i verktyget.

Hitta mer information om hur du övervakar förloppet för en verifiering kör, se [övervaka ett test ](azure-stack-vaas-monitor-test.md).

## <a name="next-steps"></a>Nästa steg

- Mer information om [Azure Stack-verifiering som en tjänst](https://docs.microsoft.com/azure/azure-stack/partner).
