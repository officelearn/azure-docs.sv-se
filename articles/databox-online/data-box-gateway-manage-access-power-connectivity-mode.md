---
title: Microsoft Azure Data Box Gateway Enhetsåtkomst, kraft och anslutningsläget | Microsoft Docs
description: Beskriver hur du hanterar åtkomst, kraft och anslutningsläget för Azure Data Box Gateway-enheten som hjälper dig att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: gateway
ms.topic: article
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: 0ad94799320e25d88f616117f1bfcf9f0513aadf
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873027"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-gateway-preview"></a>Hantera åtkomst, kraft och anslutningsläget för din Azure Data Box-Gateway (förhandsversion)

Den här artikeln beskriver hur du hanterar åtkomst, kraft och anslutningen läge för din Azure Data Box-Gateway. Dessa åtgärder utförs via det lokala webbgränssnittet eller Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera Enhetsåtkomst
> * Hantera anslutningsläget
> * Hantera power

> [!IMPORTANT]
> Data Box Gateway är en förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.

## <a name="manage-device-access"></a>Hantera Enhetsåtkomst

Åtkomst till din Data Box-gatewayenhet styrs med hjälp av ett administratörslösenord för enheten. Du kan ändra administratörslösenordet via det lokala webbgränssnittet. Du kan också återställa administratörslösenordet för enheten i Azure-portalen.

### <a name="change-device-administrator-password"></a>Ändra enhetens administratörslösenord

Följ dessa steg i det lokala Användargränssnittet för att ändra enhetens administratörslösenord.

1. I det lokala webbgränssnittet går du till **Underhåll > lösenordsändring**.
2. Ange det aktuella lösenordet och sedan det nya lösenordet. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Bekräfta det nya lösenordet.

    ![Ändra lösenord](media/data-box-gateway-manage-access-power-connectivity-mode/change-password-1.png)

3. Klicka på **ändra lösenord**.
 
### <a name="reset-device-administrator-password"></a>Återställa administratörslösenord för enheten

Återställ arbetsflöde kräver inte användare att komma ihåg det gamla lösenordet och är användbart när lösenordet går förlorat. Det här arbetsflödet utförs i Azure-portalen.

1. I Azure-portalen går du till **översikt > Återställ administratörslösenord**.

    ![Återställa lösenord](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-1.png)

 
2. Ange det nya lösenordet och bekräftar det sedan. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Klicka på **återställa**.

    ![Återställa lösenord](media/data-box-gateway-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Hantera anslutningsläget

Förutom de normala standardläget, kan enheten också köra i delvis frånkopplade eller frånkopplat läge. Var och en av dessa lägen beskrivs nedan:

- **Delvis frånkopplad** – i det här läget enheten kan inte ladda upp några data till filresurser men kan hanteras via Azure portal.

    Det här läget används vanligtvis när du är i ett bevakat satelliten nätverk och målet är att minimera bandbreddsanvändning i nätverket. Minimal nätverksförbrukning kan fortfarande ha åtgärder för enhetsövervakning.

- **Frånkopplad** – i det här läget enheten är helt frånkopplad från molnet och både i molnet överföringar och hämtningar har inaktiverats. Enheten kan bara hanteras via det lokala webbgränssnittet.

    Det här läget används vanligtvis när du vill koppla från enheten.

Följ dessa steg om du vill ändra Enhetsläge:

1. I det lokala webbgränssnittet på din enhet, går du till **Configuration > Molninställningar**.
2. Inaktivera den **Cloud överföring och hämtning**.
3. Om du vill köra enheten i delvis frånkopplat läge, aktivera **hantering av Azure portal**.

    ![Anslutningsläge](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-1.png)
 
4. Om du vill köra enheten i frånkopplat läge, inaktivera **hantering av Azure portal**. Enheten kan nu endast hanteras via det lokala webbgränssnittet.

    ![Anslutningsläge](media/data-box-gateway-manage-access-power-connectivity-mode/connectivity-mode-2.png)

## <a name="manage-power"></a>Hantera power

Du kan stänga av eller starta om fysiska och virtuella enheten med det lokala webbgränssnittet. Innan du startar om rekommenderar vi att du tar ned resurserna offline på värden och sedan enheten. Den här åtgärden minimerar risk att data skadas.

1. I det lokala webbgränssnittet går du till **Underhåll > energiinställningar**.
2. Klicka på **avstängning** eller **starta om** beroende på vad du vill göra.

    ![Energisparinställningar](media/data-box-gateway-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta klickar du på **Ja** att fortsätta.

> [!NOTE]
> Om du stänger av den virtuella enheten behöver du starta enheten via hypervisor-hantering.
