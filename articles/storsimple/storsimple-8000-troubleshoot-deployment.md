---
title: Felsöka distributionsproblem i StorSimple 8000-serien | Microsoft Docs
description: Beskriver hur du diagnostiserar och korrigerar fel som uppstår när du distribuerar först StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 61719d482a4db1c737bbe38277f2ac3b2d684b63
ms.sourcegitcommit: 4597964eba08b7e0584d2b275cc33a370c25e027
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 07/02/2018
ms.locfileid: "37342444"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Felsöka distributionsproblem för StorSimple-enhet
## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsinformation för din Microsoft Azure StorSimple-distribution. Den beskriver vanliga problem, möjliga orsaker och rekommenderade åtgärder för att hjälpa dig att lösa problem som kan uppstå när du konfigurerar StorSimple. 

Den här informationen gäller både den fysiska enheten för StorSimple 8000-serien och StorSimple Cloud Appliance.

> [!NOTE]
> Konfigurationsrelaterade problem med enheter som du kan stöta på kan inträffa när du distribuerar enheten för första gången eller de kan inträffa senare, när enheten är i drift. Den här artikeln handlar om att felsöka distributionsproblem för första gången. Om du vill felsöka en operational enhet går du till [använda diagnostikverktyget för att felsöka en operational enhet](storsimple-8000-diagnostics.md).

Den här artikeln beskriver verktyg för felsökning av StorSimple-distributioner också och innehåller en stegvis Felsökningsexempel.

## <a name="first-time-deployment-issues"></a>Första gången distributionsproblem
Om du stöter på ett problem när du distribuerar din enhet för första gången, Tänk på följande:

* Om du felsöker en fysisk enhet, se till att maskinvaran har installerats och konfigurerats enligt beskrivningen i [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
* Kontrollera krav för distribution. Se till att du har all information som beskrivs i den [checklista för distributionskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Granska viktig information StorSimple för att se om problemet som beskrivs. Viktig information innehåller lösningar för kända problem. 

Under distributionen av enheten problem de vanligaste som användare ansikte inträffar när de kör installationsguiden och när de registrerar enheten via Windows PowerShell för StorSimple. (Du använder Windows PowerShell för StorSimple att registrera och konfigurera din StorSimple-enhet. Mer information om enhetsregistrering finns i [steg3: konfigurera och registrera din enhet via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

I följande avsnitt kan hjälpa dig att lösa problem som uppstår när du konfigurerar StorSimple-enhet för första gången.

## <a name="first-time-setup-wizard-process"></a>Installationsguiden för första gången
Följande steg sammanfattar installationsguiden. Mer detaljerad installationsinformation finns i [distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

1. Kör den [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) cmdlet för att starta installationsguiden som vägleder dig genom de återstående steg. 
2. Konfigurera nätverket: installationsguiden kan du konfigurera nätverksinställningar för DATA 0-nätverksgränssnittet på din StorSimple-enhet. Inställningarna omfattar följande:
   * Virtuell IP (VIP), nätmask och gateway – den [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet körs i bakgrunden. Den konfigurerar IP-adress, nätmask och gateway för DATA 0-nätverksgränssnittet på din StorSimple-enhet.
   * Primär DNS-server – den [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet körs i bakgrunden. Konfigurerar den DNS-inställningarna för din StorSimple-lösning.
   * NTP-server – den [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet körs i bakgrunden. Konfigurerar den NTP-serverinställningarna för din StorSimple-lösning.
   * Valfritt webbproxy – den [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) cmdlet körs i bakgrunden. Den anger och aktiverar webbproxykonfigurationen för din StorSimple-lösning.
3. Ställa in lösenordet: nästa steg är att ställa in enhetens administratörslösenord.
   Administratörslösenord för enheten används för att logga in på din enhet. Enheten standardlösenord är **Password1**.
        
     > [!IMPORTANT]
     > Lösenord som samlats in före registreringen, men tillämpas endast när du har registrerat enheten. Om det uppstår ett fel att använda ett lösenord, uppmanas du att ange lösenordet igen förrän du lösenorden (som uppfyller krav på komplexitet) som ska samlas in.
     
4. Registrera enheten: det sista steget är att registrera enheten med StorSimple Device Manager-tjänsten körs i Microsoft Azure. Registreringen kräver att du [hämta tjänstregistreringsnyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) från Azure-portalen och tillhandahålla den i installationsguiden. **När enheten har registrerats, får en krypteringsnyckeln för tjänstdata du. Var noga med att spara den här krypteringsnyckeln på en säker plats eftersom den kommer att behöva registrera alla efterföljande enheter med tjänsten.**

## <a name="common-errors-during-device-deployment"></a>Vanliga fel under distributionen av enhet
I tabellerna nedan listas vanliga fel som kan uppstå när du:

* Ställ in de nätverksinställningar som krävs.
* Konfigurera proxyinställningar för valfri webbtjänst.
* Konfigurera administratörslösenord för enheten.
* Registrera enheten.

## <a name="errors-during-the-required-network-settings"></a>Fel under nätverksinställningarna som krävs
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Det här kommandot kan endast köras på den aktiva kontrollenheten. |Konfigurationen utfördes på den passiva styrenheten. |Kör det här kommandot från den aktiva styrenheten. Mer information finns i [identifiera aktiv styrenhet på din enhet](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Enheten är inte klar. |Det finns problem med nätverksanslutningen på DATA 0. |Kontrollera den fysiska nätverksanslutningen på DATA 0. |
| 3 |Invoke-HcsSetupWizard: Det finns en IP-adresskonflikt med ett annat system i nätverket (undantag från HRESULT: 0x80070263). |IP-Adressen som angavs för DATA 0 har redan används av ett annat system. |Ange en ny IP-adress som inte används. |
| 4 |Invoke-HcsSetupWizard: Det gick inte att en klusterresurs. (Undantag från HRESULT: 0x800713AE). |Duplicera VIP. Den angivna IP-Adressen används redan. |Ange en ny IP-adress som inte används. |
| 5 |Invoke-HcsSetupWizard: Ogiltig IPv4-adress. |IP-adress har angetts i ett felaktigt format. |Kontrollera formatet och ange IP-adressen igen. Mer information finns i [Ipv4-adresser][1]. |
| 6 |Invoke-HcsSetupWizard: Ogiltig IPv6-adress. |IP-adress har angetts i ett felaktigt format. |Kontrollera formatet och ange IP-adressen igen. Mer information finns i [IPv6-adressering][2]. |
| 7 |Invoke-HcsSetupWizard: Det finns inga fler slutpunkter tillgängliga från slutpunktsmappningen. (Undantag från HRESULT: 0x800706D9) |Kluster-funktionen fungerar inte. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fel under valfritt web proxy-inställningar
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ogiltig parameter (undantag från HRESULT: 0x80070057) |En av parametrarna som anges för att proxyinställningarna är inte giltig. |URI: N anges inte i rätt format. Använd följande format: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: RPC-servern är inte tillgänglig (undantag från HRESULT: 0x800706ba) |Den grundläggande orsaken är en av följande:<ol><li>Klustret är inte igång.</li><li>Den passiva styrenheten inte kan kommunicera med den aktiva kontrollenheten och kommandot körs från passiv styrenhet.</li></ol> |Beroende på den bakomliggande orsaken:<ol><li>[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att se till att klustret är igång.</li><li>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från den passiva styrenheten behöver så att den passiva styrenheten kan kommunicera med den aktiva kontrollenheten. Behöver du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om den här anslutningen är bruten.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC-anropet misslyckades (undantag från HRESULT: 0x800706be) |Klustret har stoppats. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att se till att klustret är igång. |
| 4 |Invoke-HcsSetupWizard: Det gick inte att hitta klusterresursen (undantag från HRESULT: 0x8007138f) |Klusterresursen hittades inte. Detta kan inträffa när installationen inte har rätt. |Du kan behöva återställa enheten till fabriksinställningarna. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) att skapa en klusterresurs. |
| 5 |Invoke-HcsSetupWizard: Klusterresursen inte online (undantag från HRESULT: 0x8007138c) |Klusterresurser är inte online. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-related-to-device-administrator-password"></a>Fel som rör enhetens administratörslösenord
Enheten standardlösenord för administratören är **Password1**. Det här lösenordet upphör att gälla efter den första inloggningen; Därför kommer du behöva använda installationsguiden för att ändra den. Du måste ange ett nytt administratörslösenord för enheten när du registrerar enheten för första gången. 

Kontrollera att ditt lösenord uppfyller följande krav:

* Lösenordet ska vara mellan 8 och 15 tecken långt.
* Lösenord måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och specialtecken. 
* Lösenordet får inte vara samma som de senaste 24 lösenord.

Tänk också på att lösenord upphör att gälla varje år och kan ändras endast när du har registrerat enheten. Om registreringen misslyckas av någon anledning, ändras inte lösenorden.

Mer information om enhetens administratörslösenord går du till [använda StorSimple Device Manager-tjänsten för att ändra lösenordet för StorSimple](storsimple-8000-change-passwords.md).

En eller flera av följande fel kan uppstå när du konfigurerar enhetsadministratören och StorSimple Snapshot Manager-lösenord.

| Nej. | Felmeddelande | Rekommenderad åtgärd |
| --- | --- | --- |
| 1 |Lösenordet överskrider den maximala längden. |Lösenordet måste vara mellan 8 och 15 tecken långt. |
| 2 |Lösenordet uppfyller inte den tillåtna längden. |Lösenordet måste vara mellan 8 och 15 tecken långt.|
| 3 |Lösenordet måste innehålla gemener. |Lösenord måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och specialtecken. Se till att ditt lösenord uppfyller dessa krav. |
| 4 |Lösenordet måste innehålla numeriska tecken. |Lösenord måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och specialtecken. Se till att ditt lösenord uppfyller dessa krav. |
| 5 |Lösenordet måste innehålla specialtecken. |Lösenord måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och specialtecken. Se till att ditt lösenord uppfyller dessa krav. |
| 6 |Lösenordet måste innehålla 3 av följande teckentyper av 4: versaler, gemener, siffror och specialtecken. |Lösenordet innehåller inte de nödvändiga typerna av tecken. Se till att ditt lösenord uppfyller dessa krav. |
| 7 |Parametern matchar inte bekräftelsen. |Se till att ditt lösenord uppfyller alla krav och att du angett det korrekt. |
| 8 |Lösenordet får inte matcha standard. |Standardlösenordet är *Password1*. Du behöver ändra det här lösenordet när du loggar in för första gången. |
| 9 |Lösenordet som du har angett matchar inte lösenordet för enheten. Ange lösenordet på nytt. |Kontrollera lösenordet och ange dem igen. |

Lösenord har samlats in innan enheten är registrerad, men tillämpas endast efter en lyckad registrering. Återställningsarbetsflöde lösenord kräver att enheten registreras.

> [!IMPORTANT]
> I allmänhet om det inte går att ett försök att använda ett lösenord, försöker programmet flera gånger samla in lösenordet tills den lyckas. I sällsynta fall kan kan inte lösenordet användas. I så fall kan du registrera enheten och fortsätta, men lösenorden ändras inte. Du kan ändra administratörslösenord för enheten efter registreringen i Azure Portal.


Du kan återställa lösenordet i Azure-portalen via StorSimple Device Manager-tjänsten. Mer information går du till [ändra enhetens administratörslösenord](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Fel vid enhetsregistrering
Du kan använda StorSimple Device Manager-tjänsten som körs i Microsoft Azure för att registrera enheten. Du kan stöta på ett eller flera av följande problem under enhetsregistreringen.

| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Fel 350027: Det gick inte att registrera enheten med StorSimple Device Manager. | |Vänta några minuter och försök sedan igen. Om problemet kvarstår [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Fel 350013: Ett fel har uppstått i registrera enheten. Detta kan bero på felaktig tjänstregistreringsnyckel. | |Registrera enheten igen med rätt tjänstregistreringsnyckel. Mer information finns i [hämta nyckel för tjänstregistrering.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Fel 350063: Autentisering till StorSimple Device Manager-tjänsten men registreringen misslyckades. Försök igen efter en stund. |Det här felet indikerar att autentisering med ACS har passerat, men inte det gick att registrera anropet till tjänsten. Detta kan bero på ett glapp sporadiska nätverk. |Om problemet kvarstår, så [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fel 350049: Det gick inte att nå tjänsten under registreringen. |När anropet görs till tjänsten, tas ett webbundantag emot. I vissa fall kan få detta fasta genom att försöka igen senare. |Kontrollera din IP-adress och DNS-namn och försök sedan igen. Om problemet kvarstår [kontakta Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Fel 350031: Enheten har redan registrerats. | |Ingen åtgärd krävs. |
| 6 |Fel 350016: Det gick inte att registrera enheten. | |Kontrollera att Registreringsnyckeln är korrekt. |
| 7 |Invoke-HcsSetupWizard: Ett fel uppstod när du registrerar din enhet; Detta kan bero på felaktig IP-adress eller DNS-namn. Kontrollera dina nätverksinställningar och försök igen. Om problemet kvarstår [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Fel 350050) |Se till att enheten kan pinga utanför nätverket. Om du inte har anslutning till ett externt nätverk, misslyckas registreringen med det här felet. Det här felet kan vara en kombination av en eller flera av följande:<ul><li>Felaktig IP</li><li>Felaktig undernät</li><li>Felaktig gateway</li><li>Felaktig DNS-inställningar</li></ul> |Hittar du i den [stegvis Felsökningsexempel](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FBE2]. Försök igen om en stund. Kontakta Microsoft Support om problemet kvarstår. |Det här är ett allmänt fel som genereras för alla användare osynligt fel från tjänsten eller agent. Den vanligaste orsaken kan vara att ACS-autentisering misslyckades. En möjlig orsak till felet är att det finns problem med NTP-serverkonfigurationen och tid på enheten är inte korrekt. |Korrigera tiden (om det finns problem) och försök sedan igen för registrering. Om du använder kommandot Set-HcsSystem - Timezone versaler varje ord i tidszonen (till exempel ”Pacific Standard Time”) om du vill ändra tidszonen.  Om problemet kvarstår [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |
| 9 |Varning: Det gick inte att aktivera enheten. Enhetsadministratören och StorSimple Snapshot Manager-lösenord har inte ändrats. |Om registreringen misslyckas ändras inte enhetsadministratören och StorSimple Snapshot Manager-lösenord. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Verktyg för felsökning av StorSimple-distributioner
StorSimple innehåller flera verktyg som du kan använda för att felsöka din StorSimple-lösning. Exempel på dessa är:

* Stöd för paket och enhetsloggar.
* Cmdlets som utformats speciellt för felsökning.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Stöd för paket och enhetsloggar som är tillgängliga för felsökning
Ett supportpaket innehåller alla relevanta loggar som kan hjälpa Microsoft Support-teamet med att felsöka problem med. Du kan använda Windows PowerShell för StorSimple för att generera ett stöd för krypterade paket som du sedan kan dela med supportpersonal.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Visa loggarna eller innehållet i supportpaketet
1. Använd Windows PowerShell för StorSimple för att generera ett supportpaket enligt beskrivningen i [skapa och hantera ett stödpaket](storsimple-8000-create-manage-support-package.md).
2. Ladda ned den [dekryptering skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på din klientdator.
3. Använd det här [stegvisa anvisningar](storsimple-8000-create-manage-support-package.md#edit-a-support-package) att öppna och dekryptera supportpaketet.
4. Dekrypterade support paketet loggarna är i etw/etvx format. Du kan utföra följande steg om du vill visa filerna i Windows Loggboken:
   
   1. Kör den **eventvwr** på din Windows-klient. Detta startar Loggboken.
   2. I den **åtgärder** fönstret klickar du på **Öppna sparad loggfil** och pekar på filerna i etvx/etw-format (supportpaket). Du kan nu visa filen. När du öppnar filen ser du högerklicka och spara filen som text.
      
      > [!IMPORTANT]
      > Du kan också använda den **Get-WinEvent** cmdlet för att öppna de här filerna i Windows PowerShell. Mer information finns i [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) i Windows PowerShell-cmdlet-referensdokumentationen.
     
5. När loggarna öppnar i Loggboken, leta efter följande loggar som innehåller problem som rör enhetskonfigurationen:
   
   * hcs_pfconfig/Operational Log
   * hcs_pfconfig/Config
6. Sök efter strängar som är relaterade till de cmdletar som anropas av installationsguiden i loggfilerna. Se [installationsguiden för första gången](#first-time-setup-wizard-process) en lista över dessa cmdletar.
7. Om det inte går att ta reda på orsaken till problemet, kan du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Följ stegen i [skapa en supportbegäran](storsimple-8000-contact-microsoft-support.md#create-a-support-request) när du kontaktar Microsoft Support om du behöver hjälp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet: ar tillgängliga för felsökning
Använd följande Windows PowerShell-cmdletar för att identifiera anslutningsfel.

* `Get-NetAdapter`: Använd denna cmdlet för att identifiera hälsotillståndet för nätverksgränssnitt.
* `Test-Connection`: Använd denna cmdlet för att kontrollera nätverksanslutningen i och utanför nätverket.
* `Test-HcsmConnection`: Använd denna cmdlet för att kontrollera anslutningen för en enhet som har registrerats.
* `Sync-HcsTime`: Använd denna cmdlet för att visa enhetstiden och framtvinga en tidssynkronisering med NTP-servern.
* `Enable-HcsPing` och `Disable-HcsPing`: använda dessa cmdletar för att tillåta värdarna att pinga nätverksgränssnitt på StorSimple-enheten. Som standard svarar StorSimple-nätverksgränssnitten inte på ping-begäran.
* `Trace-HcsRoute`: Använd denna cmdlet som ett verktyg för spårning av väg. Den skickar paket till varje router på vägen till slutmålet under en viss tidsperiod och beräknar resultatet baserat på de paket som returneras från varje hopp. Eftersom `Trace-HcsRoute` visar graden av paketförlust vid varje router eller en länk, kan du identifiera vilka routrar eller länkar kan orsaka problem med nätverket.
* `Get-HcsRoutingTable`: Använd denna cmdlet för att visa lokala IP-routningstabell.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Felsöka med cmdleten Get-NetAdapter
När du konfigurerar nätverksgränssnitt för en distribution för första gången enheten är maskinvarustatus inte tillgänglig i StorSimple Device Manager-tjänsten Användargränssnittet eftersom enheten inte har ännu registrerats med tjänsten. Dessutom kan den **hälsotillstånd för maskinvara** bladet alltid korrekt avspeglar inte tillståndet för enheten, särskilt om det finns problem som påverkar Tjänstsynkronisering. I sådana fall kan du använda den `Get-NetAdapter` cmdlet för att fastställa hälsotillstånd och status för dina nätverksgränssnitt.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Se en lista över alla nätverkskort på din enhet
1. Starta Windows PowerShell för StorSimple och skriv sedan `Get-NetAdapter`. 
2. Använda utdata från den `Get-NetAdapter` cmdlet och de följande riktlinjerna för att förstå statusen för ett nätverksgränssnitt.
   
   * Om gränssnittet är felfria och aktiverad, den **ifIndex** status visas som **upp**.
   * Om gränssnittet är felfri men är inte anslutna (med en nätverkskabel), den **ifIndex** visas som **inaktiverad**.
   * Om gränssnittet är felfri men är inte aktiverad på **ifIndex** status visas som **NotPresent**.
   * Om gränssnittet inte finns, visas inte i den här listan. StorSimple Device Manager-tjänsten Användargränssnittet kommer fortfarande att ha det här gränssnittet i ett felaktigt tillstånd.

Mer information om hur du använder den här cmdleten, gå till [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) i Windows PowerShell cmdlet-referens.

I följande avsnitt visas exempel på utdata från den `Get-NetAdapter` cmdlet.

 I dessa exempel kontrollenhet 0 var den passiva styrenheten och är konfigurerad på följande sätt:

* DATA 0, 1 av DATA, DATA 2 och DATA 3-nätverket gränssnitt som fanns på enheten.
* DATA 4 och 5 DATA nätverkskort inte finns några; Därför kan visas de inte i utdata.
* DATA 0 har aktiverats.

Kontrollenhet 1 var den aktiva kontrollenheten och är konfigurerad på följande sätt:

* DATA 0, DATA 1, DATA 2, DATA 3, 4 för DATA och DATA 5 nätverk gränssnitt som fanns på enheten.
* DATA 0 har aktiverats.

**Exempel på utdata – kontrollenhet 0**

Följande är utdata från kontrollenhet 0 (den passiva styrenheten). DATA 1, DATA 2 och DATA 3 är inte ansluten. DATA 4 och 5 för DATA visas inte eftersom de inte finns på enheten.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Exempel på utdata – kontrollenhet 1**

Följande är utdata från kontrollenhet 1 (den aktiva kontrollenheten). Endast den DATA 0 nätverksgränssnitt på enheten har konfigurerats och fungerar.

     Controller1>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
     Ethernet 2           HCS VNIC                                    13       Up
     DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
     DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Felsöka med cmdleten Test-Connection
Du kan använda den `Test-Connection` cmdlet för att fastställa om din StorSimple-enhet kan ansluta till det externa nätverket. Om alla nätverksparametrar, inklusive DNS, är korrekt konfigurerade i guiden, kan du använda den `Test-Connection` cmdlet för att pinga en känd adress utanför nätverket, till exempel outlook.com.

Du bör aktivera ping för felsökning av anslutningsproblem med denna cmdlet om ping har inaktiverats.

Se följande exempel på utdata från den `Test-Connection` cmdlet.

> [!NOTE]
> I det här första exemplet konfigureras enheten med en felaktig DNS. I det andra exemplet är DNS korrekt.

**Exempel på utdata – felaktig DNS**

I följande exempel och finns det inga utdata för IPV4 och IPV6-adresser, vilket betyder att DNS inte är löst. Det innebär att det finns ingen nätverksanslutning till det externa nätverket och en korrekt DNS måste anges.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exempel på utdata – rätt DNS**

I följande exempel och returnerar DNS IPV4-adress, som anger att DNS är korrekt konfigurerad. Detta bekräftar att det finns anslutning till det externa nätverket.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Felsöka med cmdleten Test-HcsmConnection
Använd den `Test-HcsmConnection` cmdlet: en för en enhet som redan är ansluten till och registreras med StorSimple Device Manager-tjänsten. Denna cmdlet kan du kontrollera anslutningen mellan en registrerad enhet och motsvarande StorSimple Device Manager-tjänsten. Du kan köra det här kommandot på Windows PowerShell för StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Att köra cmdleten Test-HcsmConnection
1. Kontrollera att enheten är registrerad.
2. Kontrollera enhetens status. Om enheten har inaktiverats i underhållsläge eller offline, kanske du ser något av följande fel:
   
   * ErrorCode.CiSDeviceDecommissioned – detta anger att enheten har inaktiverats.
   * ErrorCode.DeviceNotReady – detta anger att enheten är i underhållsläge.
   * ErrorCode.DeviceNotReady – detta anger att enheten inte är online.
3. Kontrollera att StorSimple Device Manager-tjänsten körs (Använd den [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) cmdlet). Om tjänsten inte körs, kan du se följande fel:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – detta anger att ett undantag uppstod när du körde Get-ClusterResource.
4. Kontrollera Access Control Service (ACS)-token. Om den genererar en webbundantag kan det vara resultatet av ett problem med gateway, en saknas proxyautentisering, en felaktig DNS eller ett autentiseringsfel. Du kan se följande fel:
   
   * ErrorCode.CiSApplianceGateway – detta anger att ett HttpStatusCode.BadGateway undantag: matchare namntjänsten gick inte att matcha värdnamnet.
   * ErrorCode.CiSApplianceProxy – detta anger att ett HttpStatusCode.ProxyAuthenticationRequired-undantag (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern.
   * ErrorCode.CiSApplianceDNSError – detta anger att ett WebExceptionStatus.NameResolutionFailure undantag: matchare namntjänsten gick inte att matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta anger att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om det inget genereras ett webbundantag, söka efter ErrorCode.CiSApplianceFailure. Detta visar att installationen misslyckades.
5. Kontrollera tjänsten molnanslutning. Om tjänsten genererar en webbundantag, kan du se följande fel:
   
   * ErrorCode.CiSApplianceGateway – detta anger att ett HttpStatusCode.BadGateway undantag: ett mellanliggande proxyserver tog emot en felaktig begäran från en annan proxy eller från den ursprungliga servern.
   * ErrorCode.CiSApplianceProxy – detta anger att ett HttpStatusCode.ProxyAuthenticationRequired-undantag (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern.
   * ErrorCode.CiSApplianceDNSError – detta anger att ett WebExceptionStatus.NameResolutionFailure undantag: matchare namntjänsten gick inte att matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta anger att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om det inget genereras ett webbundantag, söka efter ErrorCode.CiSApplianceSaasServiceError. Detta tyder på problem med StorSimple Device Manager-tjänsten.
6. Kontrollera Azure Service Bus-anslutningen. ErrorCode.CiSApplianceServiceBusError anger att enheten inte kan ansluta till en Service Bus.

Loggfiler CiSCommandletLog0Curr.errlog och CiSAgentsvc0Curr.errlog innehåller mer information, till exempel information om undantag.

Mer information om hur du använder cmdlet: en går du till [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) i Windows PowerShell-referensdokumentation.

> [!IMPORTANT]
> Du kan köra denna cmdlet för både aktivt och passiva styrenheten.

Se följande exempel på utdata från den `Test-HcsmConnection` cmdlet.

**Exempel på utdata – har registrerad enhet som kör StorSimple uppdatering 3**

      Controller1>Test-HcsmConnection

      Checking device registration state  ... Success
      Device registered successfully

      Checking primary NTP server [time.windows.com] ... Success

      Checking web proxy  ... NOT SET

      Checking primary IPv4 DNS server [10.222.118.154] ... Success
      Checking primary IPv6 DNS server  ... NOT SET
      Checking secondary IPv4 DNS server [10.222.120.24] ... Success
      Checking secondary IPv6 DNS server  ... NOT SET

      Checking device online  ... Success

      Checking device authentication  ... This will take a few minutes.
      Checking device authentication  ... Success

      Checking connectivity from device to service  ... This will take a few minutes.

      Checking connectivity from device to service  ... Success

      Checking connectivity from service to device  ... Success

      Checking connectivity to Microsoft Update servers  ... Success
      Controller1>

**Exempel på utdata – offline enhet** 

Det här exemplet är från en enhet som har statusen **Offline** i Azure-portalen.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Enheten kunde inte ansluta med hjälp av den aktuella webbproxykonfigurationen. Detta kan vara ett problem med webbproxykonfigurationen eller ett problem med nätverksanslutningen. I det här fallet bör du se till att webbproxyinställningarna är korrekta och att webbproxyservrarna är online och kan nås.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Felsöka med cmdleten Sync-HcsTime
Använd denna cmdlet för att visa enhetens klocka. Om enhetens klocka har en förskjutning med NTP-servern, kan du sedan använda denna cmdlet för att framtvinga synkronisera tiden med NTP-servern.
- Om förskjutningen mellan enheten och NTP-server är större än 5 minuter, visas en varning. 
- Om förskjutningen överskrider 15 minuter, kommer enheten går offline. Du kan fortfarande använda denna cmdlet för att framtvinga en tidssynkronisering. 
- Men om förskjutningen överskrider 15 timmar, kommer sedan du inte att tvinga synkronisering, visas tid och ett felmeddelande.

**Exempel på utdata – Tvingad tidssynkronisering med Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Felsöka med cmdlet: Enable-HcsPing och inaktivera HcsPing
Använd dessa cmdlets för att säkerställa att nätverksgränssnitt på enheten svarar på ICMP-ping-begäranden. Som standard svarar StorSimple-nätverksgränssnitten inte på ping-begäran. Med denna cmdlet är det enklaste sättet att veta om enheten är online och kan nås.

**Exempel på utdata – aktivera HcsPing och inaktivera HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Felsöka med cmdleten Trace-HcsRoute
Använd denna cmdlet som ett verktyg för spårning av väg. Den skickar paket till varje router på vägen till slutmålet under en viss tidsperiod och beräknar resultatet baserat på de paket som returneras från varje hopp. Eftersom cmdlet visar hur mycket av paketförlust vid varje router eller länk, kan du identifiera vilka routrar eller länkar kan orsaka problem med nätverket.

**Exempel på utdata som visar hur du spåra ett paket med Trace-HcsRoute**

     Controller0>Trace-HcsRoute -Target 10.126.174.25

     Tracing route to contoso.com [10.126.174.25]
     over a maximum of 30 hops:
       0  HCSNode0 [10.126.173.90]
       1  contoso.com [10.126.174.25]

     Computing statistics for 25 seconds...
                 Source to Here   This Node/Link
     Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
       0                                           HCSNode0 [10.126.173.90]
                                     0/ 100 =  0%   |
       1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
      [10.126.174.25]

     Trace complete.

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Felsöka med cmdleten Get-HcsRoutingTable
Använd denna cmdlet om du vill visa routningstabellen för StorSimple-enheten. En routningstabell är en uppsättning regler som kan hjälpa dig att avgöra var datapaket som skickas över ett nätverk för Internet Protocol (IP) dirigeras.

Routningstabellen visar gränssnitt och den gateway som dirigerar data till de angivna nätverk. Du får också routning mått som är beslutsfattare för sökvägen för att nå ett visst mål. Nedre routning mått, desto högre prioritet.

Till exempel om du har 2 nätverksgränssnitten DATA 2 och DATA 3, ansluten till Internet. Om routning mått för DATA 2 och DATA 3 är 15 och 261, är det föredragna gränssnittet som används för att nå Internet med DATA 2 med lägre routning mått.

Om du kör uppdatering 1 på StorSimple-enheten har högsta prioritet för molntrafiken DATA 0-nätverksgränssnittet. Detta innebär att även om det finns andra moln-aktiverat gränssnitt, moln-trafik dirigeras via DATA 0.

Om du kör den `Get-HcsRoutingTable` cmdlet utan att ange parametrar (som visas i följande exempel), kommer cmdleten utdata routningstabeller för både IPv4 och IPv6. Du kan också ange `Get-HcsRoutingTable -IPv4` eller `Get-HcsRoutingTable -IPv6` att hämta en relevanta routningstabell.

      Controller0>
      Controller0>Get-HcsRoutingTable
      ===========================================================================
      Interface List
       14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
       12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
       13...28 18 78 bc 4b 85 ......HCS VNIC
        1...........................Software Loopback Interface 1
       21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
       22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
      ===========================================================================

      IPv4 Route Table
      ===========================================================================
      Active Routes:
      Network Destination        Netmask          Gateway       Interface  Metric
                0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
              127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
              127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
        127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
            169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
          169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
        169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
          192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
        192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
        192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
              224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
              224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
              224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
        255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
        255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
        255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
      ===========================================================================
      Persistent Routes:
        Network Address          Netmask  Gateway Address  Metric
                0.0.0.0          0.0.0.0  192.168.111.100       5
      ===========================================================================

      IPv6 Route Table
      ===========================================================================
      Active Routes:
       If Metric Network Destination      Gateway
        1    306 ::1/128                  On-link
       13    276 fd99:4c5b:5525:d80b::/64 On-link
       13    276 fd99:4c5b:5525:d80b::1/128
                                          On-link
       13    276 fd99:4c5b:5525:d80b::3/128
                                          On-link
       13    276 fe80::/64                On-link
       12    261 fe80::/64                On-link
       13    276 fe80::17a:4eba:7c80:727f/128
                                          On-link
       12    261 fe80::fc97:1a53:e81a:3454/128
                                          On-link
        1    306 ff00::/8                 On-link
       13    276 ff00::/8                 On-link
       12    261 ff00::/8                 On-link
       14    266 ff00::/8                 On-link
      ===========================================================================
      Persistent Routes:
        None

      Controller0>

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Stegvis Felsökningsexempel för StorSimple
I följande exempel visas stegvisa felsökning av en StorSimple-distribution. I exemplet misslyckas enhetsregistreringen med ett felmeddelande om att nätverksinställningarna eller DNS-namn är felaktigt.

Returneras följande felmeddelande är:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Felet kan bero på något av följande:

* Felaktig maskinvaruinstallation
* Felaktiga nätverksgränssnitt
* Felaktig IP-adress, nätmask, gateway, primär DNS-server eller webbproxy
* Felaktig registreringsnyckel
* Felaktiga brandväggsinställningar

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Hitta och åtgärda problemet med enheten registreringen
1. Kontrollera din enhetskonfiguration: kör på den aktiva kontrollenheten `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Installationsguiden måste köras på den aktiva kontrollenheten. Titta på popup-meddelandet som visas i seriekonsolen för att kontrollera att du är ansluten till den aktiva styrenheten. Popup-meddelandet anger om du är ansluten till kontrollenhet 0 eller kontrollenhet 1 och om styrenheten är aktiva eller passiva. Mer information går du till [identifiera aktiv styrenhet på din enhet](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Kontrollera att enheten är korrekt kabelansluten: Kontrollera nätverket kablar på enheten tillbaka plana. Anslutningarna är specifik för den nuvarande enhetsmodellen. Mer information går du till [installerar enheten StorSimple 8100](storsimple-8100-hardware-installation.md) eller [installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Om du använder 10 GbE-nätverksportar, behöver använda de tillhandahållna QSFP SFP-kort och SFP-kablar. Mer information finns i den [lista över kablar, växlar och mottagarna som rekommenderas för 10 GbE-portar](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Kontrollera hälsotillståndet för nätverksgränssnittet:
   
   * Använd cmdleten Get-NetAdapter för att identifiera hälsotillståndet för nätverksgränssnitt för DATA 0. 
   * Om länken inte fungerar den **ifindex** status anger att gränssnittet är igång. Du måste sedan kontrollera nätverksanslutningen för port till installationen och till växeln. Du måste också utesluta felaktiga kablar. 
   * Om du misstänker att DATA 0-porten på den aktiva styrenheten har misslyckats, du kan kontrollera detta genom att ansluta till DATA 0-porten på kontrollenhet 1. Koppla bort nätverkskabeln från baksidan av enheten från kontrollenhet 0 för att bekräfta detta genom att kabeln till kontrollenhet 1 och kör sedan cmdleten Get-NetAdapter igen.
     Om DATA 0-port på en domänkontrollant misslyckas, [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Du kan behöva ersätta kontrollanten på datorn.
4. Kontrollera anslutningen till växeln:
   
   * Se till att DATA 0 nätverksgränssnitt på kontrollenhet 0 och controller 1 i din primära hölje är i samma undernät. 
   * Kontrollera hub eller router. Normalt bör du ansluta båda styrenheterna på samma hubb eller router. 
   * Se till att de växlar som du använder för anslutningen har DATA 0 för båda styrenheterna i samma vLAN.
5. Eliminera eventuella användarfel:
   
   * Kör installationsguiden igen (kör **Invoke-HcsSetupWizard**), och ange värden igen för att se till att det inte finns några fel. 
   * Kontrollera registreringen nyckel som används. Samma Registreringsnyckeln kan användas för att ansluta flera enheter till en StorSimple Device Manager-tjänsten. Stegen nedan i [hämta tjänstregistreringsnyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) så att du använder rätt Registreringsnyckeln.
     
     > [!IMPORTANT]
     > Om du har flera tjänster som körs, kommer du behöva se till att Registreringsnyckeln för på lämplig tjänst används för att registrera enheten. Om du har registrerat en enhet med fel StorSimple Device Manager-tjänsten, du behöver du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Du kan behöva utföra en fabriksåterställning av enheten (vilket kan resultera i dataförlust) till och sedan ansluta den till den avsedda användningen.
     > 
     > 
6. Använd cmdleten Test-Connection för att verifiera att du har en anslutning till det externa nätverket. Mer information går du till [felsöka med cmdleten Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Kontrollera brandväggen störningar. Om du har verifierat att de virtuella IP (VIP), undernät, gateway och DNS-inställningarna är korrekta, och du fortfarande ser anslutningsproblem sedan är det möjligt att din brandvägg blockerar kommunikationen mellan enheten och det externa nätverket. Du måste kontrollera att portarna 80 och 443 är tillgängliga på StorSimple-enheten för utgående kommunikation. Mer information finns i [nätverkskrav för StorSimple-enheten](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Titta på loggarna. Gå till [stödja paket och enhetsloggar som är tillgängliga för att felsöka](#support-packages-and-device-logs-available-for-troubleshooting).
9. Om föregående steg inte löser problemet, [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.

## <a name="next-steps"></a>Nästa steg
[Lär dig att använda diagnostikverktyget för att felsöka en StorSimple-enhet](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
