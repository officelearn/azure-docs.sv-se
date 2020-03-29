---
title: Konfigurera webbproxy för StorSimple 8000-serien | Microsoft-dokument
description: Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera webbproxyinställningar för Din StorSimple-enhet.
services: storsimple
documentationcenter: ''
author: alkohli
manager: ''
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: alkohli
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "65204253"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurera webbproxy för StorSimple-enheten

## <a name="overview"></a>Översikt

I den här självstudien beskrivs hur du använder Windows PowerShell för StorSimple för att konfigurera och visa webbproxyinställningar för Din StorSimple-enhet. Webbproxyinställningarna används av StorSimple-enheten när du kommunicerar med molnet. En webbproxyserver används för att lägga till ytterligare ett lager av säkerhet, filtrera innehåll, cache för att underlätta bandbreddskrav eller till och med hjälp med analyser.

Vägledningen i den här självstudien gäller endast för fysiska enheter i StorSimple 8000-serien. Webbproxykonfiguration stöds inte på StorSimple Cloud Appliance (8010 och 8020).

Webbproxy är en _valfri_ konfiguration för Din StorSimple-enhet. Du kan bara konfigurera webbproxy via Windows PowerShell för StorSimple. Konfigurationen är en tvåstegsprocess enligt följande:

1. Du konfigurerar först webbproxyinställningar via installationsguiden eller Windows PowerShell för StorSimple-cmdlets.
2. Du aktiverar sedan de konfigurerade webbproxyinställningarna via Windows PowerShell för StorSimple-cmdlets.

När webbproxykonfigurationen är klar kan du visa de konfigurerade webbproxyinställningarna i både Tjänsten Microsoft Azure StorSimple Device Manager och Windows PowerShell för StorSimple.

Efter att ha läst den här guiden kommer du att kunna:

* Konfigurera webbproxy med hjälp av installationsguiden och cmdlets.
* Aktivera webbproxy med cmdlets.
* Visa webbproxyinställningar i Azure-portalen.
* Felsöka fel under webbproxykonfiguration.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple

Du använder något av följande för att konfigurera webbproxyinställningar:

* Installationsguiden som hjälper dig att följa konfigurationsstegen.
* Cmdlets i Windows PowerShell för StorSimple.

Var och en av dessa metoder diskuteras i följande avsnitt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installationsguiden

Använd installationsguiden för att guida dig igenom stegen för webbproxykonfiguration. Utför följande steg för att konfigurera webbproxy på enheten.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Så här konfigurerar du webbproxy via installationsguiden

1. I menyn för seriell konsol väljer du alternativ 1, **Logga in med full åtkomst** och ange lösenordet för **enhetsadministratören**. Skriv följande kommando för att starta en installationsguidesession:
   
    `Invoke-HcsSetupWizard`
2. Om det är första gången du använder installationsguiden för enhetsregistrering måste du konfigurera alla nödvändiga nätverksinställningar tills du når webbproxykonfigurationen. Om enheten redan är registrerad godkänner du alla konfigurerade nätverksinställningar tills du når webbproxykonfigurationen. Skriv **Ja**när du uppmanas att konfigurera inställningar för webbproxy i installationsguiden .
3. För **webbproxy-URL:en**anger du IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) för webbproxyservern och det TCP-portnummer som du vill att enheten ska använda när du kommunicerar med molnet. Använd följande format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Som standard anges TCP-portnummer 8080.
4. Välj autentiseringstyp som **NTLM**, **Basic**eller **Ingen**. Basic är den minst säkra autentiseringen för proxyserverkonfigurationen. NT LAN Manager (NTLM) är ett mycket säkert och komplext autentiseringsprotokoll som använder ett trevägsmeddelandesystem (ibland fyra om ytterligare integritet krävs) för att autentisera en användare. Standardautentiseringen är NTLM. Mer information finns i [Grundläggande](https://hc.apache.org/httpclient-3.x/authentication.html) och [NTLM-autentisering](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **I Tjänsten StorSimple Device Manager fungerar enhetsövervakningsdiagrammen inte när Basic- eller NTLM-autentisering är aktiverad i enhetens proxyserverkonfiguration. För att övervakningsdiagrammen ska fungera måste du se till att autentiseringen är inställd på NONE.**
  
5. Om du har aktiverat autentiseringen anger du ett **användarnamn för webbproxy** och ett **webbproxylösenord**. Du måste också bekräfta lösenordet.
   
    ![Konfigurera webbproxy på StorSimple-enhet1](./media/storsimple-configure-web-proxy/IC751830.png)

Om du registrerar enheten för första gången fortsätter du med registreringen. Om enheten redan har registrerats avslutas guiden. De konfigurerade inställningarna sparas.

Webbproxy är nu aktiverat. Du kan hoppa över steget [Aktivera webbproxy](#enable-web-proxy) och gå direkt till [Visa webbproxyinställningar i Azure-portalen](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple-cmdlets

Ett alternativt sätt att konfigurera webbproxyinställningar är via Windows PowerShell för StorSimple-cmdlets. Utför följande steg för att konfigurera webbproxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Så här konfigurerar du webbproxy via cmdlets
1. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**. Ange **lösenordet för enhetsadministratören**när du uppmanas att göra det. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ange och bekräfta lösenordet när du uppmanas att göra det.
   
    ![Konfigurera webbproxy på StorSimple-enhet3](./media/storsimple-configure-web-proxy/IC751831.png)

Webbproxyn är nu konfigurerad och måste aktiveras.

## <a name="enable-web-proxy"></a>Aktivera webbproxy

Webbproxyn är inaktiverad som standard. När du har konfigurerat webbproxyinställningarna på StorSimple-enheten använder du Windows PowerShell för StorSimple för att aktivera webbproxyinställningarna.

> [!NOTE]
> **Det här steget krävs inte om du använde installationsguiden för att konfigurera webbproxy. Webbproxy aktiveras automatiskt som standard efter en installationsguidesession.**


Gör följande i Windows PowerShell för StorSimple för att aktivera webbproxy på enheten:

#### <a name="to-enable-web-proxy"></a>Så här aktiverar du webbproxy
1. I den seriella konsolmenyn väljer du alternativ 1, **Logga in med full åtkomst**. Ange **lösenordet för enhetsadministratören**när du uppmanas att göra det. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Enable-HcsWebProxy`
   
    Du har nu aktiverat webbproxykonfigurationen på din StorSimple-enhet.
   
    ![Konfigurera webbproxy på StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Visa webbproxyinställningar i Azure-portalen

Webbproxyinställningarna konfigureras via Windows PowerShell-gränssnittet och kan inte ändras inifrån portalen. Du kan dock visa dessa konfigurerade inställningar i portalen. Gör följande för att visa webbproxy.

#### <a name="to-view-web-proxy-settings"></a>Så här visar du inställningar för webbproxy
1. Navigera till **Tjänsten StorSimple Device Manager > Devices**. Markera och klicka på en enhet och gå sedan till **Enhetsinställningar > Nätverk**.

    ![Klicka på Nätverk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. Klicka på panelen **Webbproxy** i bladet **Nätverksinställningar.**

    ![Klicka på webbproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. Granska de konfigurerade webbproxyinställningarna på StorSimple-enheten i **webbproxybladet.**
   
    ![Visa inställningar för webbproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fel under webbproxykonfiguration

Om webbproxyinställningarna är felaktigt konfigurerade visas felmeddelanden för användaren i Windows PowerShell för StorSimple. I följande tabell beskrivs några av dessa felmeddelanden, de troliga orsakerna och rekommenderade åtgärder.

| Seriellt nej. | HRESULT-felkod | Möjlig grundorsak | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Kommandot körs från den passiva styrenheten och det kan inte kommunicera med den aktiva styrenheten. |Kör kommandot på den aktiva styrenheten. Om du vill köra kommandot från den passiva styrenheten måste du åtgärda anslutningen från passiv till aktiv styrenhet. Du måste aktivera Microsoft Support om den här anslutningen är bruten. |
| 2. |0x800710dd - Åtgärdsidentifieraren är ogiltig |Proxyinställningar stöds inte på StorSimple Cloud Appliance. |Proxyinställningar stöds inte på StorSimple Cloud Appliance. Dessa kan endast konfigureras på en storsimaple fysisk enhet. |
| 3. |0x80070057 - Ogiltig parameter |En av parametrarna för proxyinställningarna är ogiltig. |URI:n är inte korrekt. Använd följande format:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC-server är inte tillgänglig |Grundorsaken är något av följande:</br></br>Klustret är inte uppe. </br></br>Datapath-tjänsten körs inte.</br></br>Kommandot körs från passiv styrenhet och det kan inte kommunicera med den aktiva styrenheten. |Aktivera Microsoft Support för att se till att klustret är igång och att datapath-tjänsten körs.</br></br>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från den passiva styrenheten måste du se till att den passiva styrenheten kan kommunicera med den aktiva styrenheten. Du måste aktivera Microsoft Support om den här anslutningen är bruten. |
| 5. |0x800706be - RPC-anropet misslyckades |Klustret är nere. |Engagera Microsoft Support för att se till att klustret är uppe. |
| 6. |0x8007138f - Klusterresursen hittades inte |Det gick inte att hitta klusterresursen för plattformstjänst. Detta kan inträffa när installationen inte var korrekt. |Du kan behöva göra en fabriksåterställning på enheten. Du kan behöva skapa en plattformsresurs. Kontakta Microsoft-supporten om du vill ha hjälp. |
| 7. |0x8007138c - Klusterresurs inte online |Plattforms- eller datapath-klusterresurser är inte online. |Kontakta Microsoft Support för att se till att datapath- och plattformstjänstresursen är online. |

> [!NOTE]
> * Listan över felmeddelanden ovan är inte uttömmande.
> * Fel relaterade till webbproxyinställningar visas inte i Azure-portalen i Tjänsten StorSimple Device Manager. Om det finns ett problem med webbproxy när konfigurationen är klar ändras enhetsstatusen till **Offline** i den klassiska portalen.|

## <a name="next-steps"></a>Efterföljande moment
* Om du får problem när du distribuerar enheten eller konfigurerar webbproxyinställningar läser du [Felsöka distributionen av StorSimple-enheten](storsimple-troubleshoot-deployment.md).
* Om du vill veta hur du använder Tjänsten StorSimple Device Manager går du till [Använd Tjänsten StorSimple Device Manager för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

