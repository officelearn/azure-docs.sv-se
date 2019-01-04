---
title: Administration av lokalt webbgränssnitt med Microsoft Azure Data Box | Microsoft-Docs i data
description: Beskriver hur du använder det lokala webbgränssnittet för att administrera din Data Box-enhet
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: overview
ms.date: 12/27/2018
ms.author: alkohli
ms.openlocfilehash: c0793cd4adc5359aaa2282eb2505c257315c056f
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: HT
ms.contentlocale: sv-SE
ms.lasthandoff: 12/27/2018
ms.locfileid: "53793401"
---
# <a name="use-the-local-web-ui-to-administer-your-data-box"></a>Använda det lokala webbgränssnittet för att administrera din Data Box

Den här artikeln beskriver några av de konfigurations- och hanteringsuppgifter som kan utföras med Data Box. Du kan hantera Data Box via Azure-portalens användargränssnitt och det lokala webbgränssnittet för enheten. Den här artikeln fokuserar på de uppgifter som du kan utföra med det lokala webbgränssnittet.

Det lokala webbgränssnittet för Data Box används för den initiala konfigurationen av enheten. Du kan även använda det lokala webbgränssnittet för att stänga av eller starta om Data Box, köra diagnostiktest, uppdatera programvara, visa kopieringsloggar och generera ett loggpaket för Microsoft Support.

Den här artikeln innehåller följande självstudier:

- Generera ett supportpaket
- Stänga av eller starta om enheten
- Visa tillgänglig kapacitet på enheten
- Hoppa över validering av kontrollsummor 

## <a name="generate-support-package"></a>Generera supportpaket

Om det uppstår enhetsproblem kan du skapa ett supportpaket från systemloggarna. Microsoft Support använder det här paketet för att felsöka problemet. Generera ett supportpaket genom att utföra följande steg:

1. I det lokala webbgränssnittet går du till **Kontakta support** och klickar på **Skapa supportpaket**.

    ![Skapa supportpaket 1](media/data-box-local-web-ui-admin/create-support-package-1.png)

2. Ett supportpaket samlas in. Den här åtgärden tar några minuter.

    ![Skapa supportpaket 2](media/data-box-local-web-ui-admin/create-support-package-2.png)

3. När supportpaketet är klart klickar du på **Ladda ned supportpaket**. 

    ![Skapa supportpaket 4](media/data-box-local-web-ui-admin/create-support-package-4.png)

4. Bläddra och välj nedladdningsplats. Öppna mappen för att visa innehållet.

    ![Skapa supportpaket 5](media/data-box-local-web-ui-admin/create-support-package-5.png)


## <a name="shut-down-or-restart-your-device"></a>Stänga av eller starta om enheten

Du kan stänga av eller starta om din Data Box med hjälp av det lokala webbgränssnittet. Innan du startar om rekommenderar vi att du tar ned resurserna offline på värden och sedan enheten. Detta minskar risken för skadade data. Se till att datakopiering inte pågår när du stänger av enheten.

Utför följande steg för att stänga av din Data Box.

1. I det lokala webbgränssnittet går du till **Stäng av eller starta om**.
2. Klicka på **Stäng av**.

    ![Stänga av dataruta 1](media/data-box-local-web-ui-admin/shut-down-local-web-ui-1.png)

3. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.

    ![Stänga av dataruta 2](media/data-box-local-web-ui-admin/shut-down-local-web-ui-2.png)

När enheten är avstängd använder du strömknappen på frontpanelen för att starta enheten.

Utför följande steg för att starta om din Data Box.

1. I det lokala webbgränssnittet går du till **Stäng av eller starta om**.
2. Klicka på **Starta om**.

    ![Starta om dataruta 1](media/data-box-local-web-ui-admin/restart-local-web-ui-1.png)

3. När du uppmanas att bekräfta klickar du på **OK** för att fortsätta.

   Enheten stängs av och startar sedan om.

## <a name="view-available-capacity-of-the-device"></a>Visa tillgänglig kapacitet på enheten

Du kan använda enhetsinstrumentpanelen för att visa tillgänglig och använd kapacitet för enheten. 

1. I det lokala webbgränssnittet går du till **Visa instrumentpanel**.
2. Under **Anslut och kopiera** visas ledigt och använt utrymme på enheten.

    ![Visa tillgänglig kapacitet](media/data-box-local-web-ui-admin/verify-used-space-dashboard.png)


## <a name="skip-checksum-validation"></a>Hoppa över validering av kontrollsummor

En kontrollsumma genereras för dina data som standard när du förbereder för att skicka. Beroende på datatyp (små filstorlekar) kan prestandan i vissa sällsynta fall vara mycket långsam. I sådana fall kan du hoppa över kontrollsumman. 

Vi rekommenderar starkt att du inte inaktiverar kontrollsumman såvida inte prestanda påverkas allvarligt.

1. I det övre högra hörnet av det lokala webbgränssnittet på enheten går du till Inställningar.

    ![Inaktivera kontrollsumma](media/data-box-local-web-ui-admin/disable-checksum.png)

2. **Inaktivera** validering av kontrollsummor
3. Klicka på **Verkställ**.

## <a name="next-steps"></a>Nästa steg

- Lär dig hur du [hanterar Data Box via Azure-portalen](data-box-portal-admin.md).

