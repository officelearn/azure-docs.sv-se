---
title: Använd ny autentisering för StorSimple 8000 Device Manager-tjänsten i Azure | Microsoft Docs
description: Beskriver hur du använder AAD-baserad autentisering för din tjänst, generera ny registreringsnyckel och utföra manuell registrering av enheter.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 01d36188c1684eae8303cb20ba0fd0c708ff91ba
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/18/2019
ms.locfileid: "58079923"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd ny autentisering för din StorSimple

## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Hittills har StorSimple Device Manager-tjänsten används en Access Control service (ACS) för att autentisera tjänsten till din StorSimple-enhet. Mekanismen för ACS kommer snart inaktuell och ersatts av en Azure Active Directory (AAD)-autentisering. Mer information går du till följande meddelanden om utfasning av ACS och användning av AAD-autentisering.

- [Framtiden för Azure ACS är Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Kommande ändringar i Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Den här artikeln innehåller information om AAD-autentisering och associerade nya tjänstregistreringsnyckeln och ändringar av brandväggsregler som gäller för StorSimple-enheter. Informationen i den här artikeln gäller endast StorSimple 8000 series-enheter.

AAD-autentisering sker i StorSimple 8000 series-enhet som kör uppdatering 5 eller senare. På grund av introduktionen av AAD-autentisering sker ändringar i:

- URL-mönster för brandväggsregler.
- Nyckel för tjänstregistrering.

Dessa ändringar beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>Ändringar av URL: en för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering, måste alla användare inkludera de nya URL: er för autentisering i sina brandväggsregler.

Om du använder StorSimple 8000-serien, kontrollerar du att följande URL ingår i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponenten/funktioner         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | Amerikanska myndigheter |AAD-autentiseringstjänst      |

För en fullständig lista över URL: en mönster för enheter i StorSimple 8000-serien, [URL-mönster för brandväggsregler](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Om URL: en för autentisering ingår inte i brandväggsreglerna utöver utfasningsdatum och enheten kör uppdatering 5, se användare en URL-avisering. Användarna måste du inkludera den nya URL: en för autentisering. Om enheten kör en version före uppdatering 5, se användarna en avisering om pulsslag. I båda fallen StorSimple-enheten inte kan autentisera med tjänsten och tjänsten är inte kan kommunicera med enheten.

## <a name="device-version-and-authentication-changes"></a>Ändringar för version och autentisering av enheten

Om du använder en enhet i StorSimple 8000-serien, Använd följande tabell för att fastställa vilken åtgärd som du behöver ta baserat på enhetens programvaruversion som du kör.

| Om enheten körs| Vidta följande åtgärd                                    |
|--------------------------|------------------------|
| Uppdatering 5 eller senare och enheten är offline. <br> Du ser en avisering som att URL: en inte är godkänd.|1. Ändra brandväggsregler för att inkludera autentiserings-URL. Se [autentisering URL: er](#url-changes-for-aad-authentication).<br>2. [Hämta nyckel för AAD-registrering från tjänsten](#aad-based-registration-keys).<br>3. [Ansluta till Windows PowerShell-gränssnittet för StorSimple 8000-serien enheten](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. Använd `Redo-DeviceRegistration` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatering 5 eller senare och enheten.| Ingen åtgärd krävs.                                       |
| Uppdatering 4 eller tidigare och enheten är offline. |1. Ändra brandväggsregler för att inkludera autentiserings-URL.<br>2. [Ladda ned uppdatering 5 via katalogserver](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>3. [Installera uppdatering 5 via metoden snabbkorrigering](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Hämta nyckel för AAD-registrering från tjänsten](#aad-based-registration-keys).<br>5. [Ansluta till Windows PowerShell-gränssnittet för StorSimple 8000-serien enheten](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. Använd `Redo-DeviceRegistration` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatering 4 eller tidigare och enheten är online. |Ändra brandväggsregler för att inkludera autentiserings-URL.<br> Installera uppdatering 5 via Azure portal.              |
| Fabriksåterställning till en version före uppdatering 5.      |Portalen visar en AAD-baserat registreringsnyckel medan enheten kör äldre programvara. Följ stegen i det föregående scenariot för när enheten kör uppdatering 4 eller tidigare.              |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Från och uppdatering 5 för StorSimple 8000-serieenheter, ny AAD-baserade registrering nycklar används. Du kan använda tangenterna registrering för att registrera din StorSimple Device Manager-tjänsten med enheten.

Du kan inte använda nya registreringsnycklar för AAD-tjänsten om du använder en enhet för StorSimple 8000-serien som kör uppdatering 4 eller tidigare (inklusive en äldre enheten som aktiveras nu).
I det här scenariot måste du återskapa tjänstregistreringsnyckeln. När du återskapar en nyckel används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya AAD-Registreringsnyckeln upphör att gälla efter tre dagar.
- AAD-registreringsnycklar fungerar endast med enheter i StorSimple 8000-serien som kör uppdatering 5 eller senare.
- AAD-registreringsnycklar är längre än de motsvarande ACS-nycklarna för registrering.

Utför följande steg för att generera en nyckel för tjänstregistrering AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Att generera nyckeln för tjänstregistrering AAD

1. I **StorSimple Device Manager**går du till **Management &gt;**  **nycklar**. Du kan också använda sökfältet för att söka efter _nycklar_.
    
2. Klicka på **skapa nycklar**.

    ![Klicka på Återskapa](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta att återskapa](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Om du skapar en StorSimple Cloud Appliance på tjänsten som registrerats för din enhet i StorSimple 8000-serien kan du inte generera en registreringsnyckel medan skapandet pågår. Vänta på att slutföra och generera nyckel för tjänstregistrering.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du distribuerar [enhet i StorSimple 8000-serien](storsimple-8000-deployment-walkthrough-u2.md).

