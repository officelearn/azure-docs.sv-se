---
title: "Konfigurera en proxyserver för en StorSimple-enhet | Microsoft Docs"
description: "Lär dig hur du använder Windows PowerShell för StorSimple för att konfigurera inställningar för proxy för din StorSimple-enhet."
services: storsimple
documentationcenter: 
author: alkohli
manager: carmonm
editor: 
ms.assetid: 6c2ca351-a7c6-4da6-ab5e-c081e6d08261
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/17/2016
ms.author: alkohli
ms.openlocfilehash: 66ee6ce15e51b14366eac0512c899d1c425c6092
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/11/2017
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurera en proxyserver för din StorSimple-enhet
## <a name="overview"></a>Översikt
Den här självstudiekursen beskrivs hur du använder Windows PowerShell för StorSimple för att konfigurera och visa web proxy-inställningar för din StorSimple-enhet. Webbproxyinställningarna som används av StorSimple-enhet när du kommunicerar med molnet. En proxyserver används för att lägga till ytterligare en säkerhetsnivå, filter innehåll cache bandbreddskraven för enkel eller även hjälp med analytics.

Webbproxyn är en valfri konfiguration för din StorSimple-enhet. Du kan konfigurera webbproxy endast via Windows PowerShell för StorSimple. Konfigurationen är en tvåstegsprocess:

1. Du måste först konfigurera proxyinställningar för webben via installationsguiden eller Windows PowerShell för StorSimple-cmdlets.
2. Sen kan du aktivera konfigurerade webbproxyinställningarna via Windows PowerShell för StorSimple-cmdlets.

När webbproxykonfigurationen är klar kan visa du konfigurerade webbproxyinställningarna i både Microsoft Azure StorSimple Manager-tjänsten och Windows PowerShell för StorSimple. 

När du har läst den här självstudiekursen kommer du att kunna:

* Konfigurera webbproxy med hjälp av installationsguiden och cmdlet: ar
* Aktivera webbproxy med hjälp av cmdlet: ar
* Visa web proxy-inställningar i den klassiska Azure-portalen
* Felsök fel under webbproxykonfigurationen

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple
Du kan använda något av följande för att konfigurera inställningar för proxy:

* Installationsguiden för att hjälpa dig genom konfigurationsstegen.
* Cmdlets i Windows PowerShell för StorSimple.

Var och en av dessa metoder beskrivs i följande avsnitt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installationsguiden
Du kan använda guiden för att hjälpa dig genom stegen för webbproxykonfigurationen. Utför följande steg om du vill konfigurera webbproxy på enheten.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Så här konfigurerar du webbproxy via installationsguiden
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst** och ange den **enhetens administratörslösenord**. Skriv följande kommando för att starta en session med guiden installationen:
   
    `Invoke-HcsSetupWizard`
2. Om du har använt installationsguiden för registrering av enheten behöver du konfigurera alla nödvändiga nätverksinställningarna tills du når webbproxykonfigurationen. Om enheten redan är registrerad kan du acceptera de konfigurerade nätverksinställningarna tills du når webbproxykonfigurationen. I guiden när du uppmanas att konfigurera inställningar för proxy, skriver **Ja**.
3. För den **URL för Web Proxy**, ange IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) för din webbproxyserver och TCP-port number som du vill att din enhet för att använda vid kommunikation med molnet. Använd följande format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Som standard, har TCP-portnummer 8080 angetts.
4. Välj autentiseringstyp av som **NTLM**, **grundläggande**, eller **ingen**. Grundläggande är den minst säkra autentiseringen för proxyserverkonfigurationen. NT LAN Manager (NTLM) är ett mycket säkert och komplexa autentiseringsprotokoll som använder ett trevägs meddelandesystem (ibland fyra om det krävs ytterligare integritet) att autentisera en användare. Standardautentisering är NTLM. Mer information finns i [grundläggande](http://hc.apache.org/httpclient-3.x/authentication.html) och [NTLM-autentisering](http://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Övervakning diagrammen enheten fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Manager-tjänsten. Om övervakning diagrammet ska fungera, behöver du se till att autentiseringen är inställd på NONE.**
   > 
   > 
5. Om du använder autentisering anger du en **Web Proxyanvändarnamnet** och en **Web Proxylösenord**. Du måste också bekräfta lösenordet.
   
    ![Konfigurera webbproxy på StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Om du registrerar din enhet för första gången du fortsätta med registreringen. Om enheten redan har registrerats avslutas i guiden. De konfigurerade inställningarna kommer att sparas.

Webbproxy nu aktiveras också. Du kan hoppa över den [aktivera webbproxy](#enable-web-proxy) steget och gå direkt till [visa web proxy-inställningar i den klassiska Azure-portalen](#view-web-proxy-settings-in-the-azure-classic-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple-cmdlets
Ett annat sätt att konfigurera inställningar för proxy är via Windows PowerShell för StorSimple-cmdlets. Utför följande steg om du vill konfigurera webbproxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Så här konfigurerar du webbproxy via cmdlets
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ange och bekräfta lösenordet när du uppmanas, enligt nedan.
   
    ![Konfigurera webbproxy på StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webbproxyn är nu konfigurerad och måste vara aktiverat.

## <a name="enable-web-proxy"></a>Aktivera webbproxy
Webbproxyn är inaktiverad som standard. När du konfigurerar webbproxyinställningarna på StorSimple-enheten måste du använda Windows PowerShell för StorSimple för att aktivera webbproxyinställningarna.

> [!NOTE]
> **Det här steget kommer krävs inte om du använder guiden för att konfigurera webbproxy. Webbproxy aktiveras automatiskt som standard efter en installationsprogrammet guidesessionen.**
> 
> 

Utför följande steg i Windows PowerShell för StorSimple att aktivera webbproxy på din enhet:

#### <a name="to-enable-web-proxy"></a>Så här aktiverar du webbproxy
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, ange den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande vid kommandotolken:
   
    `Enable-HcsWebProxy`
   
    Du har nu aktiverats webbproxykonfigurationen på StorSimple-enheten.
   
    ![Konfigurera webbproxy på StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Visa web proxy-inställningar i den klassiska Azure-portalen
Webbproxyinställningarna konfigureras via Windows PowerShell-gränssnittet och kan inte ändras från den klassiska portalen. Du kan dock visa de konfigurerade inställningarna i den klassiska portalen. Utför följande steg om du vill visa webbproxy.

#### <a name="to-view-web-proxy-settings"></a>Visa web proxy-inställningar
1. Gå till **StorSimple Manager-tjänsten > enheter**. Markera och klicka på en enhet och gå sedan till **konfigurera**.
2. Rulla ned på den **konfigurera** sidan till **Web proxyinställningar** avsnitt. Du kan visa de konfigurerade webbproxyinställningarna på StorSimple-enheten som visas nedan.
   
    ![Visa webbproxy i hanteringsportalen](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)

## <a name="errors-during-web-proxy-configuration"></a>Fel under webbproxykonfigurationen
Om webbproxyinställningarna har konfigurerats felaktigt, visas felmeddelanden för användaren i Windows PowerShell för StorSimple. I följande tabell beskrivs några av dessa meddelanden, troliga orsaker och rekommenderade åtgärder.

| Serienummer. | HRESULT-felkoden | Möjliga underliggande orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Kommandot körs från den passiva domänkontrollanten och kan inte kommunicera med den aktiva styrenheten. |Kör kommandot på den aktiva styrenheten. För att köra kommandot från passiva domänkontrollant, behöver du åtgärda anslutningen mellan passiva och aktiva styrenhet. Du måste genomföra Microsoft-supporten om den här anslutningen har brutits. |
| 2. |0x800710dd - Åtgärdsidentifieraren är inte giltig |Proxyinställningar stöds inte på virtuella StorSimple-enheten. |Proxyinställningar stöds inte på virtuella StorSimple-enheten. De kan endast konfigureras på fysiska StorSimple-enheten. |
| 3. |0x80070057 - ogiltig parameter |En av de angivna parametrarna för proxyinställningarna är inte giltig. |URI: N har inte angetts i rätt format. Använd följande format:`http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC-servern är inte tillgänglig |Den grundläggande orsaken är en av följande:</br></br>Klustret är inte in.</br></br>DataPath-tjänsten körs inte.</br></br>Kommandot körs från passiva domänkontrollant och kan inte kommunicera med den aktiva styrenheten. |Engagera du Microsoft Support så att klustret fungerar och datapath-tjänsten körs.</br></br>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från den passiva styrenheten behöver du kontrollera passiva styrenhet kan kommunicera med den aktiva styrenheten. Du måste genomföra Microsoft-supporten om den här anslutningen har brutits. |
| 5. |0X800706be - RPC-anrop misslyckades |Klustret har stoppats. |Engagera du Microsoft Support att se till att klustret är igång. |
| 6. |0x8007138f - resurs inte hittas |Klusterresursen för Platform-tjänsten hittades inte. Detta kan hända när installationen inte har rätt. |Du kan behöva utföra en fabriksåterställning på enheten. Du kan behöva skapa en resurs för plattformen. Kontakta Microsoft Support för nästa steg. |
| 7. |0x8007138c - klusterresursen inte online |Plattform eller datapath klusterresurser är inte online. |Kontakta Microsoft Support för att säkerställa att tjänstresurs datapath och plattformen är online. |

> [!NOTE]
> * Listan ovan av felmeddelanden är inte komplett. 
> * Fel som rör webbproxyinställningarna visas inte i den klassiska Azure-portalen i StorSimple Manager-tjänsten. Om det finns ett problem med webbproxy när konfigurationen är klar, enhetens status ändras till **Offline** i den klassiska portalen. |
> 
> 

## <a name="next-steps"></a>Nästa steg
* Om du får problem när du distribuerar din enhet eller konfigurera webbproxyinställningarna [felsöka distribution din StorSimple-enheten](storsimple-troubleshoot-deployment.md).
* Om du vill lära dig mer om att använda StorSimple Manager-tjänsten, gå till [använda StorSimple Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-manager-service-administration.md).

