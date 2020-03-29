---
title: Azure AD-autentisering för StorSimple 8000 i Enhetshanteraren
description: I artikeln beskrivs hur du angenordningsbaserad AAD-autentisering för din tjänst, genererar ny registreringsnyckel och utför manuell registrering av enheterna.
author: alkohli
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: b16132c24d35ee2c9902fa2b21c44416d8376b4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470912"
---
# <a name="use-the-new-authentication-for-your-storsimple"></a>Använd den nya autentiseringen för din StorSimple

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Översikt

Tjänsten StorSimple Device Manager körs i Microsoft Azure och ansluter till flera StorSimple-enheter. Hittills har StorSimple Device Manager-tjänsten använt en ÅTKOMSTKONTROLL-tjänst (ACS) för att autentisera tjänsten till din StorSimple-enhet. ACS-mekanismen kommer snart att vara inaktuell och ersättas av en Azure Active Directory -autentisering (AAD). Mer information finns i följande meddelanden för ACS-utfasning och användning av AAD-autentisering.

- [Framtiden för Azure ACS är Azure Active Directory](https://cloudblogs.microsoft.com/enterprisemobility/2015/02/12/the-future-of-azure-acs-is-azure-active-directory/)
- [Kommande ändringar i Microsoft Access Control Service](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

I den här artikeln beskrivs information om AAD-autentiseringen och den tillhörande nya tjänstregistreringsnyckeln och ändringar av brandväggsreglerna som gäller för StorSimple-enheterna. Informationen i den här artikeln gäller endast för Enheter i StorSimple 8000-serien.

AAD-autentiseringen sker i StorSimple 8000-seriens enhet som kör uppdatering 5 eller senare. På grund av införandet av AAD-autentiseringen sker ändringar i:

- URL-mönster för brandväggsregler.
- Tjänstens registreringsnyckel.

Dessa ändringar diskuteras i detalj i följande avsnitt.

## <a name="url-changes-for-aad-authentication"></a>URL-ändringar för AAD-autentisering

För att säkerställa att tjänsten använder AAD-baserad autentisering måste alla användare inkludera de nya autentiserings-URL:erna i sina brandväggsregler.

Om du använder StorSimple 8000-serien kontrollerar du att följande WEBBADRESS ingår i brandväggsreglerna:

| URL-mönster                         | Molnet | Komponent/funktionalitet         |
|------------------------------------|-------|----------------------------------|
| `https://login.windows.net`        | Azure, offentlig |AAD-autentiseringstjänst      |
| `https://login.microsoftonline.us` | US Government |AAD-autentiseringstjänst      |

En fullständig lista över URL-mönster för StorSimple 8000-serieenheter finns [i URL-mönster för brandväggsregler](storsimple-8000-system-requirements.md#url-patterns-for-firewall-rules).

Om autentiserings-URL:en inte ingår i brandväggsreglerna efter utfasningsdatumet och enheten kör uppdatering 5, ser användarna en URL-avisering. Användarna måste inkludera den nya autentiserings-URL:en. Om enheten kör en version före uppdatering 5 ser användarna en pulsslagsvarning. I varje enskilt fall kan StorSimple-enheten inte autentisera med tjänsten och tjänsten kan inte kommunicera med enheten.

## <a name="device-version-and-authentication-changes"></a>Enhetsversion och autentiseringsändringar

Om du använder en StorSimple 8000-serie enhet använder du följande tabell för att avgöra vilken åtgärd du behöver vidta baserat på den enhetsprogramversion du kör.

| Om enheten körs| Vidta följande åtgärder                                    |
|--------------------------|------------------------|
| Uppdatera 5 eller senare och enheten är offline. <br> Du ser en avisering om att webbadressen inte är vitlistad.|1. Ändra brandväggsreglerna så att de innehåller autentiserings-URL:en. Se [url:er för autentisering](#url-changes-for-aad-authentication).<br>2. [Få AAD registreringsnyckeln från tjänsten](#aad-based-registration-keys).<br>3. [Anslut till Windows PowerShell-gränssnittet för StorSimple 8000-serien](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console).<br>4. `Redo-DeviceRegistration` Använd cmdlet för att registrera enheten via Windows PowerShell. Leverera nyckeln du fick i föregående steg.|
| Uppdatera 5 eller senare och enheten online.| Ingen åtgärd krävs.                                       |
| Uppdatering 4 eller tidigare och enheten är offline. |1. Ändra brandväggsreglerna så att de innehåller autentiserings-URL:en.<br>2. [Ladda ner Uppdatering 5 via katalogserver .](storsimple-8000-install-update-5.md#download-updates-for-your-device)<br>3. [Installera uppdatering 5 med snabbkorrigeringsmetoden](storsimple-8000-install-update-5.md#install-update-5-as-a-hotfix).<br>4. [Få AAD registreringsnyckeln från tjänsten](#aad-based-registration-keys).<br>5. [Anslut till Windows PowerShell-gränssnittet för StorSimple 8000-serien](storsimple-8000-deployment-walkthrough-u2.md#use-putty-to-connect-to-the-device-serial-console). <br>6. `Redo-DeviceRegistration` Använd cmdlet för att registrera enheten via Windows PowerShell. Leverera nyckeln du fick i föregående steg.|
| Uppdatering 4 eller tidigare och enheten är online. |Ändra brandväggsreglerna så att de innehåller autentiserings-URL:en.<br> Installera uppdatering 5 via Azure-portalen.              |
| Fabriksåterställning till en version före uppdatering 5.      |Portalen visar en AAD-baserad registreringsnyckel medan enheten kör äldre programvara. Följ stegen i föregående scenario för när enheten kör Uppdatering 4 eller tidigare.              |

## <a name="aad-based-registration-keys"></a>AAD-baserade registreringsnycklar

Start uppdatering 5 för StorSimple 8000-seriens enheter, nya AAD-baserade registreringsnycklar används. Du använder registreringsnycklarna för att registrera tjänsten StorSimple Device Manager med enheten.

Du kan inte använda de nya AAD-tjänstregistreringsnycklarna om du använder en StorSimple 8000-serieenhet som kör Uppdatering 4 eller tidigare (inkluderar att en äldre enhet aktiveras nu).
I det här fallet måste du återskapa tjänsten registreringsnyckel. När du återskapar nyckeln används den nya nyckeln för att registrera alla efterföljande enheter. Den gamla nyckeln är inte längre giltig.

- Den nya AAD-registreringsnyckeln upphör att gälla efter 3 dagar.
- AAD-registreringsnycklarna fungerar bara med StorSimple 8000-serieenheter som kör Uppdatering 5 eller senare.
- AAD-registreringsnycklarna är längre än motsvarande ACS-registreringsnycklar.

Utför följande steg för att generera en AAD-tjänstregistreringsnyckel.

#### <a name="to-generate-the-aad-service-registration-key"></a>Så här genererar du registreringsnyckeln för AAD-tjänsten

1. Gå till **Hanteringsnycklar**i **StorSimple** ** &gt; Enhetshanteraren** . Du kan också använda sökfältet för att söka efter _Nycklar_.
    
2. Klicka på **Generera nyckel**.

    ![Klicka på återskapa](./media/storsimple-8000-aad-registration-key/aad-click-generate-registration-key.png)

3. Kopiera den nya nyckeln. Den äldre nyckeln fungerar inte längre.

    ![Bekräfta regenerering](./media/storsimple-8000-aad-registration-key/aad-registration-key2.png)

    > [!NOTE] 
    > Om du skapar en StorSimple Cloud Appliance på tjänsten som är registrerad på din StorSimple 8000-serieenhet ska du inte generera en registreringsnyckel medan skapandet pågår. Vänta tills skapandet har slutförts och generera sedan registreringsnyckeln.

## <a name="next-steps"></a>Nästa steg

* Läs mer om hur du distribuerar [StorSimple 8000-seriens enhet](storsimple-8000-deployment-walkthrough-u2.md).

