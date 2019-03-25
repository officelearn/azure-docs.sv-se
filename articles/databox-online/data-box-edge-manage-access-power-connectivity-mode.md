---
title: Microsoft Azure Data Box Edge Enhetsåtkomst, kraft och anslutningsläget | Microsoft Docs
description: Beskriver hur du hanterar åtkomst, kraft och anslutningsläget för Azure Data Box Edge-enhet som hjälper dig att överföra data till Azure
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/21/2019
ms.author: alkohli
ms.openlocfilehash: e0b8b35c654f0716fae1e6ab785f57dcf04e1a5a
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400890"
---
# <a name="manage-access-power-and-connectivity-mode-for-your-azure-data-box-edge-preview"></a>Hantera åtkomst, power och anslutningsläget för din Azure Data Box Edge (förhandsversion)

Den här artikeln beskriver hur du hanterar åtkomst, kraft och anslutningen läge för din Azure Data Box Edge. Dessa åtgärder utförs via det lokala webbgränssnittet eller Azure-portalen.

I den här artikeln kan du se hur du:

> [!div class="checklist"]
> * Hantera Enhetsåtkomst
> * Hantera anslutningsläget
> * Hantera power

> [!IMPORTANT]
> Data Box Edge är i förhandsversion. Granska [Azures användningsvillkor för förhandsversionen](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) innan du beställer och distribuerar den här lösningen.

## <a name="manage-device-access"></a>Hantera Enhetsåtkomst

Åtkomst till din Data Box Edge-enhet är kontrolleras med hjälp av enhetens lösenord. Du kan ändra lösenordet via det lokala webbgränssnittet. Du kan också återställa lösenordet för enheten i Azure-portalen.

### <a name="change-device-password"></a>Ändra enhetens lösenord

Följ dessa steg i det lokala Användargränssnittet för att ändra lösenordet för enheten.

1. I det lokala webbgränssnittet går du till **Underhåll > lösenordsändring**.
2. Ange det aktuella lösenordet och sedan det nya lösenordet. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Bekräfta det nya lösenordet.

    ![Ändra lösenord](media/data-box-edge-manage-access-power-connectivity-mode/change-password-1.png)

3. Välj **ändra lösenord**.
 
### <a name="reset-device-password"></a>Återställ enhetslösenord

Återställ arbetsflöde kräver inte användare att komma ihåg det gamla lösenordet och är användbart när lösenordet går förlorat. Det här arbetsflödet utförs i Azure-portalen.

1. I Azure-portalen går du till **översikt > Återställ administratörslösenord**.

    ![Återställa lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-1.png)


2. Ange det nya lösenordet och bekräftar det sedan. Det angivna lösenordet måste vara mellan 8 och 16 tecken. Lösenordet måste innehålla 3 av följande tecken: versaler, gemener, siffror och specialtecken. Välj **återställa**.

    ![Återställa lösenord](media/data-box-edge-manage-access-power-connectivity-mode/reset-password-2.png)

## <a name="manage-connectivity-mode"></a>Hantera anslutningsläget

Förutom de fullständigt anslutna standardläget, kan enheten också köra i delvis ansluten eller helt frånkopplad läge. Var och en av dessa lägen beskrivs nedan:

- **Anslutas fullständigt** – detta är normalt standardläget där enheten fungerar. Både molnöverföring och hämtning av data är aktiverat i det här läget. Du kan använda Azure portal eller det lokala webbgränssnittet för att hantera enheten.

- **Delvis frånkopplad** – i det här läget enheten kan inte överföra eller hämta alla dela data men kan hanteras via Azure portal.

    Det här läget används vanligtvis när du är i ett bevakat satelliten nätverk och målet är att minimera bandbreddsanvändning i nätverket. Minimal nätverksförbrukning kan fortfarande ha åtgärder för enhetsövervakning.

- **Frånkopplad** – i det här läget enheten är helt frånkopplad från molnet och både i molnet överföringar och hämtningar har inaktiverats. Enheten kan bara hanteras via det lokala webbgränssnittet.

    Det här läget används vanligtvis när du vill koppla från enheten.

Följ dessa steg om du vill ändra Enhetsläge:

1. I det lokala webbgränssnittet på din enhet, går du till **Configuration > Molninställningar**.
2. Välj det läge som du vill använda enheten i den nedrullningsbara listan. Du kan välja från **anslutas fullständigt**, **delvis ansluten**, och **helt frånkopplad**. Om du vill köra enheten i delvis frånkopplat läge, aktivera **hantering av Azure portal**.

    ![Anslutningsläge](media/data-box-edge-manage-access-power-connectivity-mode/connectivity-mode.png)
 
## <a name="manage-power"></a>Hantera power

Du kan stänga av eller starta om den fysiska enheten med hjälp av det lokala webbgränssnittet. Vi rekommenderar att innan du startar om ta resurser offline på data-server och sedan på enheten. Den här åtgärden minimerar risk att data skadas.

1. I det lokala webbgränssnittet går du till **Underhåll > energiinställningar**.
2. Välj **avstängning** eller **starta om** beroende på vad du vill göra.

    ![Energisparinställningar](media/data-box-edge-manage-access-power-connectivity-mode/shut-down-restart-1.png)

3. När du uppmanas att bekräfta väljer **Ja** att fortsätta.

> [!NOTE]
> Om du stänger av den fysiska enheten kommer du behöva på strömknappen på enheten för att aktivera den.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hantera filresurser](data-box-edge-manage-shares.md).