---
title: Konfigurera en proxyserver för StorSimple 8000-serien enheten | Microsoft Docs
description: Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera inställningar för proxy för din StorSimple-enhet.
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
ms.openlocfilehash: 1109e44ed9c6aa8a0f7305b8a50410316711589c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874999"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurera en proxyserver för din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudiekursen beskrivs hur du använder Windows PowerShell för StorSimple för att konfigurera och visa web proxy-inställningar för din StorSimple-enhet. Webbproxyinställningarna som används av StorSimple-enhet när du kommunicerar med molnet. En proxyserver används för att lägga till ytterligare en säkerhetsnivå, filter innehåll cache bandbreddskraven för enkel eller även hjälp med analytics.

Riktlinjerna i den här kursen gäller endast StorSimple 8000-serien fysiska enheter. Webbproxykonfiguration stöds inte på StorSimple moln-enhet (8010 och 8020).

Webbproxyn är en _valfria_ konfigurationen för din StorSimple-enhet. Du kan konfigurera webbproxy endast via Windows PowerShell för StorSimple. Konfigurationen är en tvåstegsprocess:

1. Du måste först konfigurera proxyinställningar för webben via installationsguiden eller Windows PowerShell för StorSimple-cmdlets.
2. Sen kan du aktivera konfigurerade webbproxyinställningarna via Windows PowerShell för StorSimple-cmdlets.

När webbproxykonfigurationen är klar kan visa du konfigurerade webbproxyinställningarna i både tjänsten Microsoft Azure StorSimple Device Manager och Windows PowerShell för StorSimple.

När du har läst den här självstudiekursen kommer du att kunna:

* Konfigurera webbproxy med hjälp av installationsguiden och cmdlet: ar.
* Aktivera webbproxy med hjälp av cmdlet: ar.
* Visa webbproxyinställningarna i Azure-portalen.
* Felsök fel under webbproxykonfigurationen.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple

Du kan använda något av följande för att konfigurera inställningar för proxy:

* Installationsguiden för att hjälpa dig genom konfigurationsstegen.
* Cmdlets i Windows PowerShell för StorSimple.

Var och en av dessa metoder beskrivs i följande avsnitt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installationsguiden

Använd installationsguiden för att hjälpa dig genom stegen för webbproxykonfigurationen. Utför följande steg om du vill konfigurera webbproxy på enheten.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Så här konfigurerar du webbproxy via installationsguiden

1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst** och ange den **enhetens administratörslösenord**. Skriv följande kommando för att starta en session med guiden installationen:
   
    `Invoke-HcsSetupWizard`
2. Om det här är första gången som du har använt installationsguiden för registrering av enheten, måste du konfigurera alla nödvändiga nätverksinställningarna tills du når webbproxykonfigurationen. Om enheten redan är registrerad, accepterar du alla konfigurerade nätverksinställningarna tills du når webbproxykonfigurationen. I guiden när du uppmanas att konfigurera inställningar för proxy, skriver **Ja**.
3. För den **URL för Web Proxy**, ange IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) för din webbproxyserver och TCP-port number som du vill att din enhet för att använda vid kommunikation med molnet. Använd följande format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Som standard, har TCP-portnummer 8080 angetts.
4. Välj autentiseringstyp av som **NTLM**, **grundläggande**, eller **ingen**. Grundläggande är den minst säkra autentiseringen för proxyserverkonfigurationen. NT LAN Manager (NTLM) är ett mycket säkert och komplexa autentiseringsprotokoll som använder ett trevägs meddelandesystem (ibland fyra om det krävs ytterligare integritet) att autentisera en användare. Standardautentisering är NTLM. Mer information finns i [grundläggande](http://hc.apache.org/httpclient-3.x/authentication.html) och [NTLM-autentisering](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i Enhetshanteraren för StorSimple-tjänsten. För övervakning diagram ska fungera, måste du se till att autentiseringen är inställd på NONE.**
  
5. Om du har aktiverat autentiseringen, ange en **Web Proxyanvändarnamnet** och en **Web Proxylösenord**. Du måste bekräfta lösenordet.
   
    ![Konfigurera webbproxy på StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Om du registrerar din enhet för första gången du fortsätta med registreringen. Om enheten redan har registrerats avslutar guiden. De konfigurerade inställningarna sparas.

Webbproxy har nu aktiverats. Du kan hoppa över den [aktivera webbproxy](#enable-web-proxy) steget och gå direkt till [visa webbproxyinställningarna i Azure portal](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple-cmdlets

Ett annat sätt att konfigurera inställningar för proxy är via Windows PowerShell för StorSimple-cmdlets. Utför följande steg om du vill konfigurera webbproxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Så här konfigurerar du webbproxy via cmdlets
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ange och bekräfta lösenordet när du tillfrågas.
   
    ![Konfigurera webbproxy på StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webbproxyn är nu konfigurerad och måste vara aktiverat.

## <a name="enable-web-proxy"></a>Aktivera webbproxy

Webbproxyn är inaktiverad som standard. När du har konfigurerat webbproxyinställningarna på din StorSimple-enhet kan du använda Windows PowerShell för StorSimple för att aktivera webbproxyinställningarna.

> [!NOTE]
> **Det här steget är inte nödvändigt om du använde guiden för att konfigurera webbproxy. Webbproxy aktiveras automatiskt som standard efter en installationsprogrammet guidesessionen.**


Utför följande steg i Windows PowerShell för StorSimple att aktivera webbproxy på din enhet:

#### <a name="to-enable-web-proxy"></a>Så här aktiverar du webbproxy
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Enable-HcsWebProxy`
   
    Du har nu aktiverats webbproxykonfigurationen på StorSimple-enheten.
   
    ![Konfigurera webbproxy på StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Visa webbproxyinställningarna i Azure-portalen

Webbproxyinställningarna konfigureras via Windows PowerShell-gränssnittet och kan inte ändras från i portalen. Du kan dock visa de konfigurerade inställningarna i portalen. Utför följande steg om du vill visa webbproxy.

#### <a name="to-view-web-proxy-settings"></a>Visa web proxy-inställningar
1. Gå till **StorSimple Enhetshanteraren service > enheter**. Markera och klicka på en enhet och gå sedan till **Enhetsinställningar > nätverk**.

    ![Klicka på nätverk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. I den **nätverksinställningar** bladet, klickar du på den **Web proxy** panelen.

    ![Klicka på webbproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. I den **Web proxy** bladet granska konfigurerade web proxy-inställningar på din StorSimple-enhet.
   
    ![Visa web proxy-inställningar](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fel under webbproxykonfigurationen

Om webbproxyinställningarna är felaktigt konfigurerade visas felmeddelanden för användaren i Windows PowerShell för StorSimple. I följande tabell beskrivs några av dessa meddelanden, troliga orsaker och rekommenderade åtgärder.

| Serienummer. | HRESULT-felkoden | Möjliga underliggande orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Kommandot körs från den passiva domänkontrollanten och kan inte kommunicera med den aktiva styrenheten. |Kör kommandot på den aktiva styrenheten. För att köra kommandot från passiva domänkontrollant, måste du åtgärda anslutningen mellan passiva och aktiva styrenhet. Du måste kontaktar Microsoft Support om den här anslutningen har brutits. |
| 2. |0x800710dd - Åtgärdsidentifieraren är inte giltig |Proxyinställningar stöds inte på StorSimple-enhet för molnet. |Proxyinställningar stöds inte på StorSimple-enhet för molnet. De kan endast konfigureras på fysiska StorSimple-enheten. |
| 3. |0x80070057 - ogiltig parameter |En av de angivna parametrarna för proxyinställningarna är inte giltig. |URI: N har inte angetts i rätt format. Använd följande format:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC-servern är inte tillgänglig |Den grundläggande orsaken är en av följande:</br></br>Klustret är inte in. </br></br>DataPath-tjänsten körs inte.</br></br>Kommandot körs från passiva domänkontrollant och kan inte kommunicera med den aktiva styrenheten. |Använd Microsoft-supporten så att klustret fungerar och datapath-tjänsten körs.</br></br>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från passiva domänkontrollant måste du kontrollera att den passiva styrenheten kan kommunicera med den aktiva styrenheten. Du måste kontaktar Microsoft Support om den här anslutningen har brutits. |
| 5. |0X800706be - RPC-anrop misslyckades |Klustret har stoppats. |Använd Microsoft-supporten att se till att klustret är igång. |
| 6. |0x8007138f - resurs inte hittas |Klusterresursen för Platform-tjänsten hittades inte. Detta kan hända när installationen inte har rätt. |Du kan behöva utföra en fabriksåterställning på enheten. Du kan behöva skapa en resurs för plattformen. Kontakta Microsofts Support för nästa steg. |
| 7. |0x8007138c - klusterresursen inte online |Plattform eller datapath klusterresurser är inte online. |Kontakta Microsoft Support för att säkerställa att tjänstresurs datapath och plattformen är online. |

> [!NOTE]
> * Listan ovan av felmeddelanden är inte komplett.
> * Fel som rör webbproxyinställningarna visas inte i Azure-portalen i Enhetshanteraren för StorSimple-tjänsten. Om det finns ett problem med webbproxy när konfigurationen är klar, enhetens status ändras till **Offline** i den klassiska portalen. |

## <a name="next-steps"></a>Nästa steg
* Om du får problem när du distribuerar din enhet eller konfigurera webbproxyinställningarna [felsöka distribution din StorSimple-enheten](storsimple-troubleshoot-deployment.md).
* Information om hur du använder tjänsten StorSimple Enhetshanteraren, gå till [använda Enhetshanteraren för StorSimple-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

