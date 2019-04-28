---
title: Ny autentisering för StorSimple Virtual Array | Microsoft Docs
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
ms.date: 06/28/2018
ms.author: alkohli
ms.openlocfilehash: 080f49ca1078858462264f229e9acfee6fad17d1
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "61387666"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd ny autentisering för din StorSimple

## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera StorSimple Virtual Array. Hittills har StorSimple Device Manager-tjänsten används en Access Control service (ACS) för att autentisera tjänsten till din StorSimple-enhet. Mekanismen för ACS kommer snart inaktuell och ersatts av en Azure Active Directory (AAD)-autentisering.

Informationen i den här artikeln gäller för båda StorSimple 1200-serien virtuella matriser endast. Den här artikeln innehåller information om AAD-autentisering och associerade nya tjänstregistreringsnyckeln och ändringar av brandväggsregler som gäller för StorSimple-enheter.

AAD-autentisering sker i StorSimple Virtual Array (modell 1200) som kör Update 1 eller senare.

På grund av introduktionen av AAD-autentisering sker ändringar i:

- URL-mönster för brandväggsregler.
- Nyckel för tjänstregistrering.

Dessa ändringar beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>Ändringar av URL: en för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering, måste alla användare inkludera de nya URL: er för autentisering i sina brandväggsregler.

Om du använder StorSimple Virtual Array, se till att följande URL finns i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponenten/funktioner         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | Amerikanska myndigheter |AAD-autentiseringstjänst      |

För en fullständig lista över URL: en mönster för StorSimple Virtual Array, [URL-mönster för brandväggsregler](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Om URL: en för autentisering inte ingår i brandväggsreglerna utöver utfasningsdatum, se användarna en kritisk varning som deras StorSimple-enheten inte kunde autentisera med tjänsten. Tjänsten kommer inte att kunna kommunicera med enheten. Om användarna ser den här aviseringen, som de behöver inkludera den nya URL: en för autentisering. Mer information om aviseringen, går du till [använda aviseringar för att övervaka din StorSimple-enhet](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Ändringar för version och autentisering av enheten

Om du använder en StorSimple Virtual Array, Använd följande tabell för att fastställa vilken åtgärd som du behöver ta baserat på enhetens programvaruversion som du kör.

| Om enheten körs  | Vidta följande åtgärd                                    |
|----------------------------|--------------------------------------------------------------|
| Uppdatering 1.0 eller senare och är offline. <br> Du ser en avisering som att URL: en inte är godkänd.| 1. Ändra brandväggsregler för att inkludera autentiserings-URL. Se [autentisering URL: er](#url-changes-for-aad-authentication). <br> 2. [Hämta nyckel för AAD-registrering från tjänsten](#aad-based-registration-keys). <br> 3. Utför steg 1-5 för att [Anslut till Windows PowerShell-gränssnittet på den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Använd `Invoke-HcsReRegister` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatering 1.0 eller senare och enheten är online.| Ingen åtgärd krävs.                                       |
| Uppdatera version 0.6 eller tidigare och enheten är offline. | 1. [Hämta version 1.0 via katalogserver](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Använd version 1.0 via det lokala webbgränssnittet](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Hämta nyckel för AAD-registrering från tjänsten](#aad-based-registration-keys). <br>4. Utför steg 1-5 för att [Anslut till Windows PowerShell-gränssnittet på den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Använd `Invoke-HcsReRegister` cmdlet för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatera version 0.6 eller tidigare och enheten är online | Ändra brandväggsregler för att inkludera autentiserings-URL.<br> Installera version 1.0 via Azure portal. |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Från och version 1.0 för StorSimple Virtual Array, ny AAD-baserade registrering nycklar används. Du kan använda tangenterna registrering för att registrera din StorSimple Device Manager-tjänsten med enheten.

Du kan inte använda nya registreringsnycklar för AAD-tjänsten om du använder en StorSimple Virtual array som kör Update version 0.6 eller tidigare. Du måste återskapa tjänstregistreringsnyckeln. När du återskapar en nyckel används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya AAD-Registreringsnyckeln upphör att gälla efter tre dagar.
- AAD-registrering nycklar fungerar bara med StorSimple 1200-serien virtuella matriser som kör uppdatering 1 eller senare. AAD-registreringsnyckel från en enhet i StorSimple 8000-serien fungerar inte.
- AAD-registreringsnycklar är längre än de motsvarande ACS-nycklarna för registrering.

Utför följande steg för att generera en nyckel för tjänstregistrering AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Att generera nyckeln för tjänstregistrering AAD

1. I **StorSimple Device Manager**går du till **Management &gt;**  **nycklar**.
    
    ![Gå till nycklar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klicka på **skapa nycklar**.

    ![Klicka på Återskapa](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta att återskapa](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du distribuerar [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
