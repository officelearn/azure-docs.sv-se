---
title: Konfigurera webbproxy för enhet i StorSimple 8000-serien | Microsoft Docs
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
ms.openlocfilehash: 956cf45eb9e246f2e1f917f2bf487ac14deba90e
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 05/06/2019
ms.locfileid: "65204253"
---
# <a name="configure-web-proxy-for-your-storsimple-device"></a>Konfigurera webbproxy för din StorSimple-enhet

## <a name="overview"></a>Översikt

Den här självstudien beskrivs hur du använder Windows PowerShell för StorSimple för att konfigurera och visa Webbproxyinställningar för StorSimple-enheten. Webbproxyinställningarna som används av StorSimple-enheten vid kommunikation med molnet. En proxyserver används för att lägga till ytterligare en säkerhetsnivå, filtrera innehåll, cache för att enkelt bandbreddskrav eller även hjälp med analytics.

Riktlinjerna i den här självstudien gäller enbart för fysiska enheter i StorSimple 8000-serien. Webbproxykonfiguration stöds inte på StorSimple-Molninstallationen (8010 och 8020).

Webbproxy är en _valfritt_ konfigurationen för din StorSimple-enhet. Du kan konfigurera webbproxy endast via Windows PowerShell för StorSimple. Konfigurationen är en tvåstegsprocess:

1. Du måste först konfigurera Webbproxyinställningar via installationsguiden eller Windows PowerShell för StorSimple cmdlets.
2. Du kan sedan aktivera konfigurerade Webbproxyinställningar via Windows PowerShell för StorSimple cmdlets.

När webbproxykonfigurationen är klar kan du visa konfigurerade Webbproxyinställningar i både Microsoft Azure StorSimple Device Manager-tjänsten och Windows PowerShell för StorSimple.

När du har läst den här självstudiekursen kommer du att kunna:

* Konfigurera webbproxy med hjälp av installationsguiden och cmdletar.
* Aktivera webbproxy med hjälp av cmdlet: ar.
* Visa Webbproxyinställningar i Azure-portalen.
* Felsöka fel under webbproxykonfigurationen.


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple

Du kan använda något av följande för att konfigurera inställningar för proxy:

* Installationsguiden att guida dig genom konfigurationsstegen.
* Cmdlets i Windows PowerShell för StorSimple.

Var och en av dessa metoder beskrivs i följande avsnitt.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installationsguiden

Använd installationsguiden för att guida dig genom stegen för webbproxykonfigurationen. Utför följande steg för att konfigurera webbproxy på din enhet.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Konfigurera webbproxy via installationsguiden

1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst** och ge den **enhetens administratörslösenord**. Skriv följande kommando för att starta guiden en installationsprogrammet:
   
    `Invoke-HcsSetupWizard`
2. Om det här är första gången du använder installationsguiden för registrering av enheten, måste du konfigurera alla nödvändiga nätverksinställningarna tills du når webbproxykonfigurationen. Om enheten redan är registrerad, accepterar du alla de konfigurerade nätverksinställningarna tills du når webbproxykonfigurationen. I installationsguiden när du uppmanas att konfigurera inställningar för proxy, skriver **Ja**.
3. För den **Webbadress till webbproxy**anger IP-adressen eller det fullständigt kvalificerade domännamnet (FQDN) på webbservern för proxy och TCP-port number som du vill att din enhet för att använda vid kommunikation med molnet. Använd följande format:
   
    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`
   
    Som standard anges numret för TCP-port 8080.
4. Välj autentiseringstypen som **NTLM**, **grundläggande**, eller **ingen**. Basic är den minst säkra autentiseringen för proxyserverkonfigurationen. NT LAN Manager (NTLM) är en mycket säker och komplexa autentiseringsprotokoll som använder ett trevägs meddelandesystem (ibland fyra om det krävs ytterligare integriteten) att autentisera en användare. Standardautentisering är NTLM. Mer information finns i [grundläggande](https://hc.apache.org/httpclient-3.x/authentication.html) och [NTLM-autentisering](https://hc.apache.org/httpclient-3.x/authentication.html). 
   
   > [!IMPORTANT]
   > **Enheten Övervakningsdiagrammen fungerar inte när grundläggande eller NTLM-autentisering är aktiverat i proxyserverkonfiguration för enheten i StorSimple Device Manager-tjänsten. Du måste se till att autentiseringen är inställd på Ingen för Övervakningsdiagrammen ska fungera.**
  
5. Om du har aktiverat autentisering anger du en **Web Proxyanvändarnamnet** och en **Web Proxylösenord**. Du måste också bekräfta lösenordet.
   
    ![Konfigurera webbproxy på StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Om du registrerar din enhet för första gången, kan du fortsätta med registreringen. Om enheten har redan registrerats, avslutar guiden. De konfigurerade inställningarna har sparats.

Webbproxy är nu aktiverat. Du kan hoppa över den [aktivera webbproxy](#enable-web-proxy) steg och gå direkt till [visa Webbproxyinställningar i Azure-portalen](#view-web-proxy-settings-in-the-azure-portal).

## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Konfigurera webbproxy via Windows PowerShell för StorSimple cmdlets

Ett annat sätt att konfigurera inställningar för proxy är via Windows PowerShell för StorSimple cmdlets. Utför följande steg om du vill konfigurera webbproxy.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Konfigurera webbproxy via cmdlet: ar
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, anger den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`
   
    Ange och bekräfta lösenordet när du tillfrågas.
   
    ![Konfigurera webbproxy på StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

Webbproxy har nu konfigurerats och måste vara aktiverat.

## <a name="enable-web-proxy"></a>Aktivera webbproxy

Webbproxy är inaktiverad som standard. När du har konfigurerat Webbproxyinställningar på StorSimple-enheten kan du använda Windows PowerShell för StorSimple för att aktivera Webbproxyinställningar.

> [!NOTE]
> **Det här steget krävs inte om du använde guiden för att konfigurera webbproxy. Webbproxy aktiveras automatiskt som standard efter en installationsprogrammet guidesessionen.**


Utför följande steg i Windows PowerShell för StorSimple att aktivera webbproxy på din enhet:

#### <a name="to-enable-web-proxy"></a>Aktivera webbproxy
1. I menyn för seriekonsolen väljer du alternativ 1, **logga in med fullständig åtkomst**. När du uppmanas, anger den **enhetens administratörslösenord**. Standardlösenordet är `Password1`.
2. Skriv följande i kommandotolken:
   
    `Enable-HcsWebProxy`
   
    Du har nu aktiverats webbproxykonfigurationen på StorSimple-enheten.
   
    ![Konfigurera webbproxy på StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-portal"></a>Visa Webbproxyinställningar i Azure portal

Webbproxyinställningarna konfigureras via Windows PowerShell-gränssnittet och kan inte ändras från i portalen. Du kan dock se inställningarna konfigurerade i portalen. Utför följande steg om du vill visa webbproxy.

#### <a name="to-view-web-proxy-settings"></a>Visa Webbproxyinställningar
1. Gå till **StorSimple Device Manager-tjänsten > enheter**. Markera och klicka på en enhet och gå sedan till **Enhetsinställningar > nätverk**.

    ![Klicka på nätverk](./media/storsimple-8000-configure-web-proxy/view-web-proxy-1.png)

2. I den **nätverksinställningar** bladet klickar du på den **webbproxy** panelen.

    ![Klicka på webbproxy](./media/storsimple-8000-configure-web-proxy/view-web-proxy-2.png)

3. I den **webbproxy** bladet granska konfigurerade Webbproxyinställningar på StorSimple-enheten.
   
    ![Visa Webbproxyinställningar](./media/storsimple-8000-configure-web-proxy/view-web-proxy-3.png)


## <a name="errors-during-web-proxy-configuration"></a>Fel under webbproxykonfigurationen

Om webbproxyinställningarna är felaktigt konfigurerade visas felmeddelanden för användaren i Windows PowerShell för StorSimple. I följande tabell beskrivs några av följande felmeddelanden, troliga orsaker och rekommenderade åtgärder.

| Serienummer. | HRESULT-fel kod | Möjliga underliggande orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1. |0x80070001 |Kommandot körs från den passiva styrenheten och kan inte kommunicera med den aktiva kontrollenheten. |Kör kommandot på den aktiva kontrollenheten. Du måste åtgärda anslutningen mellan passiva och aktiva kontrollenheten för att köra kommandot från den passiva styrenheten. Du måste kontaktar Microsoft Support om den här anslutningen är bruten. |
| 2. |0x800710dd - åtgärds-ID: n är inte giltig |Proxyinställningar stöds inte på StorSimple-Molninstallationen. |Proxyinställningar stöds inte på StorSimple-Molninstallationen. Dessa kan endast konfigureras på en fysisk StorSimple-enhet. |
| 3. |0x80070057 - ogiltig parameter |En av parametrarna som anges för att proxyinställningarna är inte giltig. |URI: N anges inte i rätt format. Använd följande format: `http://<IP address or FQDN of the web proxy server>:<TCP port number>` |
| 4. |0x800706ba - RPC-servern är inte tillgänglig |Den grundläggande orsaken är en av följande:</br></br>Klustret är inte igång. </br></br>DataPath tjänsten körs inte.</br></br>Kommandot körs från passiv styrenhet och kan inte kommunicera med den aktiva kontrollenheten. |Engagera Microsoft Support så att klustret är igång och datapath-tjänsten körs.</br></br>Kör kommandot från den aktiva styrenheten. Om du vill att köra kommandot från den passiva styrenheten, måste du kontrollera att den passiva styrenheten kan kommunicera med den aktiva kontrollenheten. Du måste kontaktar Microsoft Support om den här anslutningen är bruten. |
| 5. |0X800706be - RPC-anrop misslyckades |Klustret har stoppats. |Engagera Microsoft Support så att klustret är igång. |
| 6. |0x8007138f - klusterresursen hittades inte |Plattformen tjänstresurs kluster hittades inte. Detta kan inträffa när installationen inte har rätt. |Du kan behöva utföra en fabriksåterställning på enheten. Du kan behöva skapa en resurs för plattformen. Kontakta Microsoft-supporten om du vill ha hjälp. |
| 7. |0x8007138c - klusterresursen inte online |Plattform eller datapath klusterresurser är inte online. |Kontakta Microsoft Support för att säkerställa att tjänstresurs datapath och plattform är online. |

> [!NOTE]
> * Listan ovan av felmeddelanden är inte komplett.
> * Fel som rör Webbproxyinställningar visas inte i Azure-portalen i StorSimple Device Manager-tjänsten. Om det finns ett problem med webbproxy när konfigurationen är klar, enhetens status ändras till **Offline** i den klassiska portalen. |

## <a name="next-steps"></a>Nästa steg
* Om du får problem när du distribuerar din enhet eller konfigurera proxyinställningar för webb, se [felsöka distributionen av StorSimple-enhet](storsimple-troubleshoot-deployment.md).
* Information om hur du använder StorSimple Device Manager-tjänsten, går du till [använda StorSimple Device Manager-tjänsten för att administrera din StorSimple-enhet](storsimple-8000-manager-service-administration.md).

