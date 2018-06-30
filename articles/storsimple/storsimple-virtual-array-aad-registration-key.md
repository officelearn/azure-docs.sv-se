---
title: Ny autentisering för virtuell StorSimple-matriser | Microsoft Docs
description: Beskriver hur du använder AAD-baserad autentisering för tjänsten, generera en ny registreringsnyckel och utföra manuell registrering av enheter.
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
ms.openlocfilehash: e33a3f843017ec24f3a79701fac9a62e15b4f9ba
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/29/2018
ms.locfileid: "37109196"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd den nya autentiseringen för din StorSimple

## <a name="overview"></a>Översikt

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Hittills har StorSimple enheten Manager-tjänsten används en Access Control service (ACS) för att autentisera tjänsten till din StorSimple-enhet. Mekanismen för ACS kommer snart föråldrad och ersättas med en Azure Active Directory (AAD) för autentisering.

Informationen i den här artikeln gäller både StorSimple 1200 serien virtuella matriser endast. Den här artikeln innehåller information om AAD-autentisering och associerade ny nyckel för tjänstregistrering och ändringar för brandväggsregler som är tillämpliga på StorSimple-enheter.

AAD-autentisering sker i StorSimple virtuell matriser (model 1200) kör uppdatering 1 eller senare.

På grund av att AAD-autentisering dataändringar:

- URL-mönster för brandväggsregler.
- Nyckel för tjänstregistrering.

De här ändringarna beskrivs i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>Ändringar av URL: en för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering, måste alla användare inkludera de nya URL: er för autentisering i sina brandväggsregler.

Om du använder virtuella StorSimple-matris, kan du kontrollera att följande URL ingår i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponenten/funktioner         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure Public |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | Amerikanska myndigheter |AAD-autentiseringstjänst      |

För en fullständig lista över URL: en mönster för virtuell StorSimple-matriser, gå till [URL-mönster för brandväggsregler](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Om URL: en för autentisering inte finns med i brandväggsreglerna utöver utfasningsdatum användarna kan se en kritisk varning som sin StorSimple-enhet inte kunde autentisera med tjänsten. Tjänsten kommer inte att kunna kommunicera med enheten. Om användarna kan se den här aviseringen, behöver de ta den nya URL för autentisering. Mer information om aviseringen, gå till [Använd aviseringar för att övervaka din StorSimple-enhet](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Ändringar av enheten version och autentisering

Om du använder en virtuell StorSimple-matris, Använd följande tabell för att avgöra vad du behöver vidta baserat på enheten programvaruversion du kör.

| Om enheten körs  | Vidta följande åtgärd                                    |
|----------------------------|--------------------------------------------------------------|
| Uppdatering 1.0 eller senare och är offline. <br> Du ser en varning att URL: en inte är godkända.| 1. Ändra brandväggsregler för att inkludera autentiserings-URL. Se [autentisering URL: er](#url-changes-for-aad-authentication). <br> 2. [Hämta AAD-registreringsnyckel från tjänsten](#aad-based-registration-keys). <br> 3. Utför steg 1-5 för att [Anslut till Windows PowerShell-gränssnittet i virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. Använd `Invoke-HcsReRegister` för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatering 1.0 eller senare och enheten är online.| Ingen åtgärd krävs.                                       |
| Uppdatera 0,6 eller tidigare och enheten är offline. | 1. [Ladda ned uppdatering 1.0 via katalogserver](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix).<br>2. [Tillämpa uppdatering 1.0 via lokala webbgränssnittet](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Hämta AAD-registreringsnyckel från tjänsten](#aad-based-registration-keys). <br>4. Utför steg 1-5 för att [Anslut till Windows PowerShell-gränssnittet i virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. Använd `Invoke-HcsReRegister` för att registrera enheten via Windows PowerShell. Ange den nyckel som du fick i föregående steg.|
| Uppdatera 0,6 eller tidigare och enheten är online | Ändra brandväggsregler för att inkludera autentiserings-URL.<br> Installera uppdatering 1.0 via Azure-portalen. |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Från uppdatering 1.0 för StorSimple virtuell matriser, nya AAD-baserade registreringen nycklar används. Du kan använda tangenterna registrering för att registrera din StorSimple Device Manager-tjänst med enheten.

Du kan inte använda nya registreringsnycklar för AAD-tjänsten om du använder en virtuell StorSimple-matriser som kör uppdatering 0,6 eller tidigare. Du måste återskapa nyckeln för tjänstregistrering. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Ny AAD-registreringsnyckel upphör att gälla efter tre dagar.
- AAD-registrering nycklar fungerar bara med StorSimple 1200-serien virtuella matriser körs Update 1 eller senare. AAD-registreringsnyckel från en enhet för StorSimple 8000-serien fungerar inte.
- AAD-registreringsnycklar är längre än de motsvarande ACS-registreringsnycklar.

Utför följande steg för att generera en nyckel för tjänstregistrering AAD.

#### <a name="to-generate-the-aad-service-registration-key"></a>Att generera nyckeln för tjänstregistrering AAD

1. I **StorSimple Enhetshanteraren**, gå till **Management &gt;**  **nycklar**.
    
    ![Gå till nycklar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klicka på **skapa nycklar**.

    ![Klicka på Återskapa](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Äldre nyckel fungerar inte längre.

    ![Bekräfta Generera](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Nästa steg

* Mer information om hur du distribuerar [virtuella StorSimple-matris](storsimple-virtual-array-deploy1-portal-prep.md)
