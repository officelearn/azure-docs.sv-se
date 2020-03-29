---
title: Ny autentisering för StorSimple Virtual Arrays
description: I artikeln beskrivs hur du angenordningsbaserad AAD-autentisering för din tjänst, genererar ny registreringsnyckel och utför manuell registrering av enheterna.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 07/25/2019
ms.author: alkohli
ms.openlocfilehash: 89f367e866c1a794f4359c76b8b8a8a9cfefd50d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "76273811"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd den nya autentiseringen för din StorSimple

## <a name="overview"></a>Översikt

[!INCLUDE [storsimple-virtual-array-eol-banner](../../includes/storsimple-virtual-array-eol-banner.md)]

StorSimple Device Manager-tjänsten körs i Microsoft Azure och ansluter till flera virtuella StorSimple-matriser. Hittills har StorSimple Device Manager-tjänsten använt en ÅTKOMSTKONTROLL-tjänst (ACS) för att autentisera tjänsten till din StorSimple-enhet. ACS-mekanismen kommer snart att vara inaktuell och ersättas av en Azure Active Directory -autentisering (AAD).

Informationen i den här artikeln gäller endast för både virtuella matriser i StorSimple 1200-serien. I den här artikeln beskrivs information om AAD-autentiseringen och den tillhörande nya tjänstregistreringsnyckeln och ändringar av brandväggsreglerna som gäller för StorSimple-enheterna.

AAD-autentiseringen sker i StorSimple Virtual Arrays (modell 1200) som kör uppdatering 1 eller senare.

På grund av införandet av AAD-autentiseringen sker ändringar i:

- URL-mönster för brandväggsregler.
- Tjänstens registreringsnyckel.

Dessa ändringar diskuteras i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>URL-ändringar för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering måste alla användare inkludera de nya autentiserings-URL:erna i sina brandväggsregler.

Om du använder StorSimple Virtual Array kontrollerar du att följande URL ingår i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponent/funktionalitet         |
|------------------------------------|-------|---------------------------------|
| `https://login.windows.net`        | Azure, offentlig |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | US Government |AAD-autentiseringstjänst      |

En fullständig lista över URL-mönster för StorSimple-virtuella matriser finns [i URL-mönster för brandväggsregler](storsimple-ova-system-requirements.md#url-patterns-for-firewall-rules).

Om autentiserings-URL:en inte ingår i brandväggsreglerna efter utfasningsdatumet ser användarna en kritisk avisering om att deras StorSimple-enhet inte kunde autentisera med tjänsten. Tjänsten kommer inte att kunna kommunicera med enheten. Om användarna ser den här aviseringen måste de inkludera den nya autentiserings-URL:en. Mer information om aviseringen finns i [Använd aviseringar för att övervaka din StorSimple-enhet](storsimple-virtual-array-manage-alerts.md#networking-alerts).

## <a name="device-version-and-authentication-changes"></a>Enhetsversion och autentiseringsändringar

Om du använder en StorSimple Virtual Array använder du följande tabell för att avgöra vilken åtgärd du behöver vidta baserat på den enhetsprogramversion du kör.

| Om enheten körs  | Vidta följande åtgärder                                    |
|----------------------------|--------------------------------------------------------------|
| Uppdatera 1.0 eller senare och är offline. <br> Du ser en avisering om att webbadressen inte är vitlistad.| 1. Ändra brandväggsreglerna så att de innehåller autentiserings-URL:en. Se [url:er för autentisering](#url-changes-for-aad-authentication). <br> 2. [Få AAD registreringsnyckeln från tjänsten](#aad-based-registration-keys). <br> 3. Utför steg 1-5 för att [ansluta till Windows PowerShell-gränssnittet i den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br> 4. `Invoke-HcsReRegister` Använd cmdlet för att registrera enheten via Windows PowerShell. Leverera nyckeln du fick i föregående steg.|
| Uppdatera 1.0 eller senare och enheten är online.| Ingen åtgärd krävs.                                       |
| Uppdatera 0,6 eller tidigare och enheten är offline. | 1. [Ladda ner Uppdatering 1.0 via katalogserver .](storsimple-virtual-array-install-update-1.md#download-the-update-or-the-hotfix)<br>2. [Installera uppdatering 1.0 via det lokala webbgränssnittet](storsimple-virtual-array-install-update-1.md#install-the-update-or-the-hotfix).<br>3. [Få AAD registreringsnyckel från tjänsten](#aad-based-registration-keys). <br>4. Utför steg 1-5 för att [ansluta till Windows PowerShell-gränssnittet i den virtuella matrisen](storsimple-virtual-array-deploy2-provision-hyperv.md#step-2-provision-a-virtual-array-in-hypervisor).<br>5. `Invoke-HcsReRegister` Använd cmdlet för att registrera enheten via Windows PowerShell. Leverera nyckeln du fick i föregående steg.|
| Uppdatera 0,6 eller tidigare och enheten är online | Ändra brandväggsreglerna så att de innehåller autentiserings-URL:en.<br> Installera uppdatering 1.0 via Azure-portalen. |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Med början av uppdatering 1.0 för StorSimple Virtual Arrays används nya AAD-baserade registreringsnycklar. Du använder registreringsnycklarna för att registrera tjänsten StorSimple Device Manager med enheten.

Du kan inte använda de nya AAD-tjänstregistreringsnycklarna om du använder en StorSimple Virtual Arrays som kör Uppdatering 0.6 eller tidigare. Du måste återskapa tjänstregistreringsnyckeln. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya AAD-registreringsnyckeln upphör att gälla efter 3 dagar.
- AAD-registreringsnycklarna fungerar bara med virtuella matriser i StorSimple 1200-serien som kör uppdatering 1 eller senare. AAD-registreringsnyckeln från en StorSimple 8000-serieenhet fungerar inte.
- AAD-registreringsnycklarna är längre än motsvarande ACS-registreringsnycklar.

Utför följande steg för att generera en AAD-tjänstregistreringsnyckel.

#### <a name="to-generate-the-aad-service-registration-key"></a>Så här genererar du registreringsnyckeln för AAD-tjänsten

1. Gå till **Hanteringsnycklar**i **StorSimple** ** &gt; Enhetshanteraren** .
    
    ![Gå till Nycklar](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key1.png)

2. Klicka på **Generera nyckel**.

    ![Klicka på återskapa](./media/storsimple-virtual-array-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta regenerering](./media/storsimple-virtual-array-aad-registration-key/aad-registration-key2.png)

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar [StorSimple Virtual Array](storsimple-virtual-array-deploy1-portal-prep.md)
