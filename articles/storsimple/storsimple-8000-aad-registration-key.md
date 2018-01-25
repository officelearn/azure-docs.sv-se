---
title: "Använd nya autentisering för StorSimple 8000 Enhetshanteraren i Azure | Microsoft Docs"
description: "Beskriver hur du använder AAD-baserad autentisering för tjänsten, generera en ny registreringsnyckel och utföra manuell registrering av enheter."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: 37f44538d94ed78509bbcb09e726dc34a9e92e95
ms.sourcegitcommit: 28178ca0364e498318e2630f51ba6158e4a09a89
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 01/24/2018
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd den nya autentiseringen för din StorSimple

## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Hittills har StorSimple enheten Manager-tjänsten används en Access Control service (ACS) för att autentisera tjänsten till din StorSimple-enhet. Mekanismen för ACS kommer snart föråldrad och ersättas med en Azure Active Directory (AAD) för autentisering. Mer information finns på följande meddelanden för ACS utfasningen och användning av AAD-autentisering.

- [Framtiden för Azure ACS är Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Kommande ändringar till Microsoft Access Control Service](https://azure.microsoft.com/en-in/blog/acs-access-control-service-namespace-creation-restriction/)

Den här artikeln innehåller information om AAD-autentisering och associerade ny nyckel för tjänstregistrering och ändringar för brandväggsregler som är tillämpliga på StorSimple-enheter. Informationen i den här artikeln gäller endast StorSimple 8000 series-enheter.

AAD-autentisering sker i StorSimple 8000-serieenhet som kör uppdatering 5 eller senare. På grund av att AAD-autentisering dataändringar:

- URL-mönster för brandväggsregler.
- Nyckel för tjänstregistrering.

De här ändringarna beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>Ändringar av URL: en för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering, måste alla användare inkludera de nya URL: er för autentisering i sina brandväggsregler.

Om du använder StorSimple 8000-serien, kan du kontrollera att följande URL ingår i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponenten/funktioner         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | Amerikanska myndigheter |AAD-autentiseringstjänst      |

För en fullständig lista över URL: en mönster för StorSimple 8000-serien enheter, gå till [URL-mönster för brandväggsregler](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Om enheten kör uppdatering 5 autentiserings-URL ingår inte i brandväggsreglerna utöver utfasningsdatum och användarna kan se en avisering om URL: en. Användarna måste inkludera den nya URL för autentisering. Om enheten kör en tidigare version än uppdatering 5 kan användarna kan se en pulsslagsavisering om. I varje fall StorSimple-enheten autentisera inte med tjänsten och tjänsten är inte kan kommunicera med enheten.

## <a name="device-version-and-authentication-changes"></a>Ändringar av enheten version och autentisering

Om du använder en StorSimple 8000-serien, Använd följande tabell för att avgöra vad du behöver vidta baserat på enheten programvaruversion du kör.

| Om enheten körs| Vidta följande åtgärd                                    |
|--------------------------|------------------------|--------------------|--------------------------------------------------------------|
| Uppdatering 5 eller senare och enheten är offline. <br> Du ser en varning att URL: en inte är godkända.| Ändra brandväggsregler för att inkludera autentiserings-URL.<br> Se [autentisering URL: er](#url-changes-for-aad-authentication). |
| Uppdateringen 5 eller senare och enheten online.| Ingen åtgärd krävs.                                       |
| Update 4 eller tidigare och enheten är offline. | Ändra brandväggsregler för att inkludera autentiserings-URL.<br>[Ladda ned uppdatering 5 via katalogserver](storsimple-8000-install-update-5.md#download-updates-for-your-device).<br>[Installera uppdatering 5 via metoden snabbkorrigering](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix). <br> [Hämta AAD-registreringsnyckel från tjänsten](#aad-based-registration-keys). <br> [Ansluta till Windows PowerShell-gränssnittet för StorSimple 8000-serien enheten](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>Använd `Redo-DeviceRegistration` för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Update 4 eller tidigare och enheten är online. |Ändra brandväggsregler för att inkludera autentiserings-URL.<br> Installera uppdatering 5 via Azure-portalen.              |
| Fabriksåterställning till en version före uppdatering 5.      |Portalen visar ett AAD-baserade registreringsnyckel medan enheten kör äldre program. Följ stegen i det föregående scenariot för när enheten kör Update 4 eller tidigare.              |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Från uppdatering 5 för StorSimple 8000-serien enheter, nya AAD-baserade registreringen nycklar används. Du kan använda tangenterna registrering för att registrera din StorSimple Device Manager-tjänst med enheten.

Du kan inte använda nya registreringsnycklar för AAD-tjänsten om du använder en StorSimple 8000-serieenhet som kör uppdatering 4 eller tidigare (inklusive en äldre enheten som aktiveras nu).
I det här scenariot måste du återskapa nyckeln för tjänstregistrering. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Ny AAD-registreringsnyckel upphör att gälla efter tre dagar.
- De AAD registreringsnycklar fungerar bara med StorSimple 8000-serien enheter som kör uppdatering 5 eller senare.
- AAD-registreringsnycklar är längre än de motsvarande ACS-registreringsnycklar.

Utför följande steg för att generera en nyckel för tjänstregistrering AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Att generera nyckeln för tjänstregistrering AAD

1. I **StorSimple Enhetshanteraren**, gå till **Management &gt;**  **nycklar**. Du kan också använda sökfältet för att söka efter _nycklar_.
    
2. Klicka på **skapa nycklar**.

    ![Klicka på Återskapa](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Äldre nyckel fungerar inte längre.

    ![Bekräfta Generera](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Om du skapar en StorSimple-enhet för molnet på tjänsten som registrerats för enheten StorSimple 8000-serien kan du inte generera en registreringsnyckel när den skapas. Vänta för att skapa att slutföra och generera nyckel för tjänstregistrering.

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du distribuerar [StorSimple 8000-serien enheten](storsimple-8000-deployment-walkthrough-u2.md).

