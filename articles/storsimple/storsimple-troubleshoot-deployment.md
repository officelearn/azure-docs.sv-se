---
title: "Felsöka distributionsproblem StorSimple | Microsoft Docs"
description: "Beskriver hur du diagnostisera och åtgärda fel som uppstår när du distribuerar först StorSimple."
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: 
ms.assetid: f8352eaa-193c-42d1-8818-0b8e02d8485d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/18/2016
ms.author: alkohli
ms.openlocfilehash: 230a652ceca8b4643d1984d81383c6628b8e1f5f
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Felsöka distributionsproblem för StorSimple-enhet
## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsinformation för din Microsoft Azure StorSimple-distribution. Här beskrivs vanliga problem, möjliga orsaker och rekommenderade steg för att hjälpa dig att lösa problem som kan uppstå när du konfigurerar StorSimple. Den här informationen gäller både den lokala fysiska StorSimple-enheten och den virtuella StorSimple-enheten.

> [!NOTE]
> Enheten configuration-relaterade problem som du kan stöta kan uppstå när du distribuerar enheten för första gången eller de kan inträffa senare, när enheten är i drift. Den här artikeln fokuserar på att felsöka distributionsproblem för första gången. Om du vill felsöka en operativa enhet, gå till [felsökning av problem med operativa enheter](storsimple-troubleshoot-operational-device.md).
> 
> 

Den här artikeln beskriver verktyg för felsökning av StorSimple distributioner också och innehåller en stegvis Felsökningsexempel.

## <a name="first-time-deployment-issues"></a>Första gången distributionsproblem
Om du stöter på ett problem när du distribuerar din enhet för första gången, Tänk på följande:

* Om du felsöker en fysisk enhet, kontrollera att maskinvaran har installerats och konfigurerats enligt beskrivningen i [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).
* Kontrollera förutsättningarna för distribution. Se till att du har all information som beskrivs i den [checklista för distributionskonfiguration](storsimple-deployment-walkthrough.md#deployment-configuration-checklist).
* Granska StorSimple viktig information för att se om problemet beskrivs. Viktig information innehåller lösningar för kända problem. 

Under distributionen av enheten problem de vanligaste som användare står inför uppstå när de kör installationsguiden och när de registrerar enheten via Windows PowerShell för StorSimple. (Du använder Windows PowerShell för StorSimple att registrera och konfigurera din StorSimple-enhet. Mer information om enhetsregistrering finns [steg3: konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-deployment-walkthrough.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

I följande avsnitt kan hjälpa dig att lösa problem som kan uppstå när du konfigurerar StorSimple-enhet för första gången.

## <a name="first-time-setup-wizard-process"></a>Installationsguiden för första gången
Följande steg sammanfattar installationsguiden. Detaljerad installationsinformation finns [distribuera din lokala StorSimple-enhet](storsimple-deployment-walkthrough.md).

1. Kör den [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) att starta installationsguiden som hjälper dig att återstående steg. 
2. Konfigurera nätverket: installationsguiden kan du konfigurera nätverksinställningar för DATA 0-nätverksgränssnittet på din StorSimple-enhet. Dessa inställningar inkluderar följande:
   * Virtuella IP-adresser (VIP), nätmask och gateway – den [Set HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) cmdlet körs i bakgrunden. Konfigurerar den IP-adress, nätmask och gateway för DATA 0-nätverksgränssnittet på din StorSimple-enhet.
   * Primär DNS-server – den [Set HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) cmdlet körs i bakgrunden. Den konfigurerar DNS-inställningarna för din StorSimple-lösning.
   * NTP-server – den [Set HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) cmdlet körs i bakgrunden. Konfigurerar den NTP-serverinställningar för din StorSimple-lösning.
   * Valfria webbproxy – den [Set HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) cmdlet körs i bakgrunden. Den anger och aktiverar webbproxykonfiguration för din StorSimple-lösning.
3. Ställ in lösenorden: nästa steg är att ställa in enhetsadministratör och StorSimple Snapshot Manager lösenord. Om du kör uppdatering 1 sedan uppmanas inte du att ställa in lösenordet för StorSimple Snapshot Manager.
   
   * Enhetens administratörslösenord används för att logga in på din enhet. Enheten standardlösenord är **Password1**.
   * Lösenordet för StorSimple Snapshot Manager krävs när du konfigurerar en enhet för att använda StorSimple Snapshot Manager. Du måste du först ställa in lösenordet i installationsguiden och du kan sedan ange och ändra från StorSimple Manager-tjänsten. Det här lösenordet autentiserar enheten med StorSimple Snapshot Manager.
     
     > [!IMPORTANT]
     > Samlas in innan registrering av lösenord, men används bara när du har registrerat enheten. Om det finns ett fel att använda ett lösenord, uppmanas du att ange lösenordet igen förrän lösenorden (som uppfyller krav på komplexitet) samlas in.
     > 
     > 
4. Registrera enheten: det sista steget är att registrera enheten med StorSimple Manager-tjänsten körs i Microsoft Azure. Registrering kräver att du [hämta nyckel för tjänstregistrering](storsimple-manage-service.md#get-the-service-registration-key) från den klassiska Azure-portalen och ange i installationsguiden. När enheten har registrerats tillhandahålls en krypteringsnyckel för tjänstdata. Glöm inte att spara den här krypteringsnyckeln på en säker plats eftersom den kommer att behöva registrera alla efterföljande enheter med tjänsten.

## <a name="common-errors-during-device-deployment"></a>Vanliga fel under distributionen av enheten
I tabellerna nedan listas vanliga fel som kan uppstå när du:

* Konfigurera nödvändiga nätverksinställningarna.
* Konfigurera proxyinställningar för valfri webbserver.
* Ställ in enhetsadministratören och StorSimple Snapshot Manager lösenord. 
* Registrera enheten. 

## <a name="errors-during-the-required-network-settings"></a>Fel under nödvändiga nätverksinställningarna
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Anropa HcsSetupWizard: Det här kommandot kan endast köras på den aktiva styrenheten. |Konfigurationen utfördes på den passiva styrenheten. |Kör kommandot från den aktiva styrenheten. Mer information finns i [identifiera en domänkontrollant för en aktiv på enheten](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Anropa HcsSetupWizard: Enheten är inte klar. |Det finns problem med nätverksanslutningen på DATA 0. |Kontrollera den fysiska nätverksanslutningen på DATA 0. |
| 3 |Anropa HcsSetupWizard: Det finns en IP-adresskonflikt med ett annat system i nätverket (undantag från HRESULT: 0x80070263). |IP-Adressen som angetts för DATA 0 var redan används av en annan dator. |Ange en ny IP-adress som inte används. |
| 4 |Anropa HcsSetupWizard: Det gick inte att en klusterresurs. (Undantag från HRESULT: 0x800713AE). |Duplicera VIP. Den angivna IP-Adressen används redan. |Ange en ny IP-adress som inte används. |
| 5 |Anropa HcsSetupWizard: Ogiltig IPv4-adress. |IP-adress har angetts i ett felaktigt format. |Kontrollera formatet och ange IP-adressen igen. Mer information finns i [Ipv4-adresser][1]. |
| 6 |Anropa HcsSetupWizard: Ogiltig IPv6-adress. |IP-adress har angetts i ett felaktigt format. |Kontrollera formatet och ange IP-adressen igen. Mer information finns i [IPv6-adressering][2]. |
| 7 |Anropa HcsSetupWizard: Det finns inga fler slutpunkter från slutpunktsavbildaren. (Undantag från HRESULT: 0x800706D9) |Funktionen kluster fungerar inte. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fel under valfri web proxy-inställningar
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Anropa HcsSetupWizard: Ogiltig parameter (undantag från HRESULT: 0x80070057) |En av de angivna parametrarna för proxyinställningarna är inte giltig. |URI: N har inte angetts i rätt format. Använd följande format: http://*<IP address or FQDN of the web proxy server>*:*<TCP port number>* |
| 2 |Anropa HcsSetupWizard: RPC-servern är inte tillgänglig (undantag från HRESULT: 0x800706ba) |Den grundläggande orsaken är en av följande:<ol><li>Klustret är inte upp.</li><li>Den passiva domänkontrollanten inte kan kommunicera med den aktiva styrenheten och kommandot körs från passiva domänkontrollant.</li></ol> |Beroende på den bakomliggande orsaken:<ol><li>[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) se till att klustret är igång.</li><li>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från passiva domänkontrollant måste så att passiva styrenhet kan kommunicera med den aktiva styrenheten. Du behöver [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) om den här anslutningen har brutits.</li></ol> |
| 3 |Anropa HcsSetupWizard: RPC-anrop misslyckades (undantag från HRESULT: 0x800706be) |Klustret har stoppats. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) se till att klustret är igång. |
| 4 |Anropa HcsSetupWizard: Klusterresursen gick inte att hitta (undantag från HRESULT: 0x8007138f) |Klusterresursen hittades inte. Detta kan hända när installationen inte var korrekt. |Du kan behöva återställa enheten till fabriksinställningarna. [Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) att skapa en klusterresurs. |
| 5 |Anropa HcsSetupWizard: Klusterresursen inte online (undantag från HRESULT: 0x8007138c) |Klusterresurser är inte online. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-related-to-device-administrator-and-storsimple-snapshot-manager-passwords"></a>Fel som rör enhetsadministratör och lösenord för StorSimple Snapshot Manager
Standard-enhetens administratörslösenord är **Password1**. Det här lösenordet upphör att gälla efter den första inloggningen; Därför behöver du använda guiden för att ändra den. Du måste ange ett nytt administratörslösenord för enheten när du registrerar enheten för första gången. 

Om du använder StorSimple Snapshot Manager-program som körs på Windows Server-värden för att hantera enheten, måste du också ange ett lösenord för StorSimple Snapshot Manager under registreringen för första gången. 

Kontrollera att lösenorden uppfyller följande krav:

* Administratörslösenord för enheten ska vara mellan 8 och 15 tecken långt.
* Lösenordet för StorSimple Snapshot Manager ska vara 14 eller 15 tecken långt.
* Lösenord måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och särskilda. 
* Lösenordet får inte vara samma som de senaste 24 lösenord.

Dessutom, Tänk på att lösenord upphör varje år och kan ändras endast när du har registrerat enheten. Om registreringen misslyckas av någon anledning, ändras inte lösenord. Mer information om enhetsadministratör och StorSimple Snapshot Manager lösenord går du till [använda StorSimple Manager-tjänsten för att ändra din StorSimple-lösenord](storsimple-change-passwords.md).

En eller flera av följande fel kan uppstå när du ställer in enhetsadministratören och StorSimple Snapshot Manager lösenord.

| Nej. | Felmeddelande | Rekommenderad åtgärd |
| --- | --- | --- |
| 1 |Lösenordet överskrider den maximala längden. |Använda ett lösenord som uppfyller dessa krav:<ul><li>Administratörslösenord för enheten måste vara mellan 8 och 15 tecken långt.</li><li>Lösenordet för StorSimple Snapshot Manager måste vara 14 eller 15 tecken långt.</li></ul> |
| 2 |Lösenordet uppfyller inte den tillåtna längden. |Använda ett lösenord som uppfyller dessa krav:<ul><li>Administratörslösenord för enheten måste vara mellan 8 och 15 tecken långt.</li><li>Lösenordet för StorSimple Snapshot Manager måste vara 14 eller 15 tecken långt.</lu></ul> |
| 3 |Lösenordet måste innehålla gemener. |Lösenordet måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och särskilda. Kontrollera att lösenordet uppfyller dessa krav. |
| 4 |Lösenordet måste innehålla numeriska tecken. |Lösenordet måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och särskilda. Kontrollera att lösenordet uppfyller dessa krav. |
| 5 |Lösenordet måste innehålla specialtecken. |Lösenordet måste innehålla 3 av följande teckentyper av 4: gemener, versaler, siffror och särskilda. Kontrollera att lösenordet uppfyller dessa krav. |
| 6 |Lösenordet måste innehålla 3 av följande 4 teckentyper: versaler, gemener, siffror och särskilda. |Lösenordet innehåller inte de nödvändiga typerna av tecken. Kontrollera att lösenordet uppfyller dessa krav. |
| 7 |Parametern matchar inte bekräftelsen. |Kontrollera att lösenordet uppfyller alla krav och att du angett rätt. |
| 8 |Lösenordet får inte matcha standardvärdet. |Standardlösenordet är *Password1*. Du måste ändra lösenordet när du loggar in för första gången. |
| 9 |Lösenordet som du har angett överensstämmer inte med lösenordet. Ange lösenordet på nytt. |Kontrollera lösenordet och ange den igen. |

Lösenord har samlats in innan enheten är registrerad, men tillämpas endast efter att registreringen har lyckats. Återställningsarbetsflöde lösenord kräver att enheten registreras. 

> [!IMPORTANT]
> I allmänhet om ett försök att använda ett lösenord inte försöker programmet flera gånger samla in lösenordet tills den lyckas. I sällsynta fall kan inte lösenordet användas. I så fall kan du registrera enheten och fortsätta, men lösenorden ändras inte. Du får inga indikation om vilken lösenord inte har ändrats, kommer enhetens administratörslösenord eller lösenordet för StorSimple Snapshot Manager. Om detta inträffar rekommenderar vi att du ändrar båda lösenorden.
> 
> 

Du kan återställa lösenord i den klassiska Azure-portalen via StorSimple Manager-tjänsten. Mer information finns: 

* [Ändra enhetens administratörslösenord](storsimple-change-passwords.md#change-the-device-administrator-password).
* [Ändra lösenordet för StorSimple Snapshot Manager](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password).

## <a name="errors-during-device-registration"></a>Fel vid enhetsregistrering
Du kan använda StorSimple Manager-tjänsten körs i Microsoft Azure för att registrera enheten. Du kan stöta på ett eller flera av följande problem under registrering av enheten.

| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Fel 350027: Det gick inte att registrera enheten med StorSimple Manager. | |Vänta några minuter och försök sedan igen. Om problemet kvarstår [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md). |
| 2 |Fel 350013: Ett fel har uppstått i registrera enheten. Detta kan bero på felaktig nyckel för tjänstregistrering. | |Registrera enheten igen med rätt tjänstregistreringsnyckel. Mer information finns i [hämta nyckel för tjänstregistrering.](storsimple-manage-service.md#get-the-service-registration-key) |
| 3 |Fel 350063: Autentisering till StorSimple Manager-tjänsten överförts men registreringen misslyckades. Försök igen efter en stund. |Det här felet indikerar att autentisering med ACS har passerat men inte det gick att registrera anropet till tjänsten. Detta kan bero på ett tekniskt problem sporadiska nätverk. |Om problemet kvarstår bör du [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md). |
| 4 |Fel 350049: Det gick inte att nå tjänsten under registreringen. |När anropet görs till tjänsten, tas ett webbundantag emot. I vissa fall kan få detta fast med du försöker igen senare. |Kontrollera IP-adress och DNS-namn och försök sedan igen. Om problemet kvarstår [kontaktar Microsoft Support.](storsimple-contact-microsoft-support.md) |
| 5 |Fel 350031: Enheten har redan registrerats. | |Ingen åtgärd krävs. |
| 6 |Fel 350016: Det gick inte att registrering av enheten. | |Kontrollera att rätt nyckel för registrering. |
| 7 |Anropa HcsSetupWizard: Ett fel uppstod vid registrering av enheten; Detta kan bero på felaktig IP-adress eller DNS-namn. Kontrollera nätverksinställningarna och försök igen. Om problemet kvarstår [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md). (Fel 350050) |Se till att enheten kan pinga utanför nätverket. Om du inte har anslutning till ett externt nätverk, misslyckas registreringen med det här felet. Det här felet kan vara en kombination av en eller flera av följande:<ul><li>Felaktigt IP</li><li>Felaktig undernät</li><li>Felaktig gateway</li><li>Felaktiga DNS-inställningar</li></ul> |Hittar du i den [stegvis Felsökningsexempel](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Anropa HcsSetupWizard: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FBE2]. Försök igen om en stund. Kontakta Microsoft Support om problemet kvarstår. |Detta är ett allmänt fel uppstod för alla användare osynliga fel från tjänsten eller agent. Den vanligaste orsaken kan vara att ACS-autentiseringen misslyckades. En möjlig orsak till felet är att det finns problem med NTP-serverkonfiguration och tid på enheten är inte korrekt. |Korrigera tiden (om det finns problem) och försök sedan igen för registrering. Om du använder kommandot Set-HcsSystem - tidszonen för att justera tidszonen versal varje ord i tidszonen (till exempel ”Pacific Standard Time”).  Om problemet kvarstår [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. |
| 9 |Varning: Det gick inte att aktivera enheten. Enhetsadministratören och StorSimple Snapshot Manager lösenord har inte ändrats. |Om registreringen misslyckas ändras inte enhetsadministratören och lösenord för StorSimple Snapshot Manager. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Verktyg för felsökning av StorSimple-distributioner
StorSimple innehåller flera verktyg som du kan använda för att felsöka din StorSimple-lösning. Exempel på dessa är:

* Stöd för paket och enhetsloggar 
* Cmdlets som utformats speciellt för felsökning 

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Stöd för paket och tillgängliga enhetsloggar för felsökning
Ett supportpaket innehåller de relevanta loggar som kan hjälpa Microsoft Support-teamet med felsökning av enhetsproblem med. Du kan använda Windows PowerShell för StorSimple för att generera ett stöd för krypterade paket som du sedan kan dela med supporttekniker.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Att visa loggarna eller innehållet i paketet stöd
1. Använda Windows PowerShell för StorSimple för att generera ett supportpaket enligt beskrivningen i [skapa och hantera ett stödpaket](storsimple-create-manage-support-package.md).
2. Hämta den [dekryptering skriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klientdatorn.
3. Använd den här [stegvisa anvisningar](storsimple-create-manage-support-package.md#edit-a-support-package) att öppna och dekryptera support-paketet.
4. Stöd för krypterade paket loggar har etw/etvx format. Du kan utföra följande steg för att visa dessa filer i Windows Loggboken:
   
   1. Kör den **eventvwr** på Windows-klienter. Detta startar Loggboken.
   2. I den **åtgärder** rutan klickar du på **Öppna sparad loggfil** och peka på loggfilerna etvx/etw-format (support-paket). Du kan nu visa filen. När du öppnar filen kan du högerklicka på och spara filen som text.
      
      > [!IMPORTANT]
      > Du kan också använda den **Get-WinEvent** för att öppna filerna i Windows PowerShell. Mer information finns i [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) i Windows PowerShell-cmdlet-referensdokumentationen.
      > 
      > 
5. När öppna loggarna i Loggboken och leta efter följande loggar som innehåller problem relaterade till enhetskonfigurationen:
   
   * hcs_pfconfig/Operational logg
   * hcs_pfconfig/Config
6. Sök efter strängar som rör cmdletar som anropas av installationsguiden i loggfilerna. Se [installationsguiden för första gången](#first-time-setup-wizard-process) för en lista över dessa cmdlets. 
7. Om det inte går att ta reda på orsaken till problemet, kan du [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. Följ stegen i [skapa en supportbegäran](storsimple-contact-microsoft-support.md#create-a-support-request) när du kontaktar Microsoft Support för hjälp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlet: ar tillgängliga för felsökning
Använd följande Windows PowerShell-cmdlets för att identifiera anslutningsfel.

* `Get-NetAdapter`: Använd denna cmdlet för att identifiera hälsotillståndet för nätverksgränssnitt. 
* `Test-Connection`: Använd denna cmdlet för att kontrollera nätverksanslutningen i och utanför nätverket.
* `Test-HcsmConnection`: Använd denna cmdlet för att kontrollera anslutningen till en enhet som har registrerats.

Om du kör uppdatering 1 på StorSimple-enheten finns även följande diagnostiska cmdlets.

* `Sync-HcsTime`: Använd denna cmdlet för att visa tid och framtvinga tidssynkronisering med NTP-server.
* `Enable-HcsPing`och `Disable-HcsPing`: använda dessa cmdletar för att tillåta värdarna att pinga nätverksgränssnitt på StorSimple-enheten. Som standard svarar nätverksgränssnitt StorSimple inte på ping-begäran.
* `Trace-HcsRoute`: Använd den här cmdleten som en route spårning för. Den skickar paket till varje router på väg till slutmålet under en viss tidsperiod och beräknar sedan resultatet baserat på de paket som returneras från varje hopp. Eftersom `Trace-HcsRoute` visar hur mycket av paketförlust vid varje router eller en länk som du kan identifiera vilka routrar eller länkar som orsakar problem med nätverket. 
* `Get-HcsRoutingTable`: Använd denna cmdlet ska visas den lokala routningstabellen.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Felsöka med cmdleten Get-NetAdapter
När du konfigurerar nätverksgränssnitt för en distribution för första gången enheten är maskinvarustatus inte tillgängliga i Användargränssnittet för StorSimple Manager-tjänsten eftersom enheten inte har ännu registrerats med tjänsten. Dessutom kanske sidan maskinvarustatus inte alltid korrekt återspeglar status för enheten, särskilt om det finns problem som påverkar Tjänstsynkronisering. I sådana fall måste du använda den `Get-NetAdapter` för att fastställa hälsa och status för din nätverksgränssnitt.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Visa en lista över alla nätverkskort på din enhet
1. Starta Windows PowerShell för StorSimple och skriv sedan `Get-NetAdapter`. 
2. Använda utdata från den `Get-NetAdapter` cmdlet och följande riktlinjer för att förstå statusen för din nätverksgränssnitt.
   
   * Om gränssnittet är felfri och aktiverad, den **ifIndex** status visas som **in**.
   * Om gränssnittet är felfri men är inte anslutna (med en nätverkskabel), den **ifIndex** visas som **inaktiverade**.
   * Om gränssnittet är felfri men är inte aktiverad på **ifIndex** status visas som **NotPresent**.
   * Om gränssnittet inte finns, visas inte i den här listan. Användargränssnittet för StorSimple Manager-tjänsten fortfarande visas det här gränssnittet i ett felaktigt tillstånd.

Mer information om hur du använder den här cmdleten, gå till [GetNetAdapter](https://technet.microsoft.com/library/jj130867.aspx) i Windows PowerShell-cmdlet-referens. 

Följande avsnitt visar exempel på utdata från den `Get-NetAdapter` cmdlet. 

 I de här exemplen styrenhet 0 är passiva domänkontrollant och har konfigurerats på följande sätt:

* DATA 0, DATA 1, DATA 2 och DATA 3 nätverk gränssnitt som fanns på enheten.
* DATA 4 och 5 DATA nätverkskort inte finns några; Därför kan visas de inte i utdata.
* DATA 0 har aktiverats.

Kontrollant 1 är den aktiva styrenheten och har konfigurerats på följande sätt:

* DATA 0, DATA 1, DATA 2, DATA 3, 4 DATA och DATA 5 nätverk gränssnitt som fanns på enheten.
* DATA 0 har aktiverats.

**Exempel på utdata-styrenhet 0**

Följande är utdata från styrenhet 0 (passiv styrenhet). DATA 1, DATA 2 och DATA 3 är inte ansluten. DATA 4 och 5 DATA visas inte eftersom de inte finns på enheten. 

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Exempel på utdata – kontrollant 1**

Följande är utdata från kontrollant 1 (den aktiva styrenheten). Endast den DATA 0 nätverkskort på enheten har konfigurerats och fungerar.

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
Du kan använda den `Test-Connection` för att fastställa om din StorSimple-enhet kan ansluta till det externa nätverket. Om alla nätverksparametrar, inklusive DNS, har konfigurerats korrekt i guiden, kan du använda den `Test-Connection` cmdlet för att pinga en känd adress utanför nätverket, till exempel outlook.com. 

Du bör aktivera ping för felsökning av anslutningsproblem med denna cmdlet om ping har inaktiverats.

Se följande exempel på utdata från den `Test-Connection` cmdlet. 

> [!NOTE]
> Enheten är konfigurerad med en felaktig DNS i det första exemplet. I det andra exemplet är DNS korrekt.
> 
> 

**Exempel på utdata – felaktig DNS**

I följande exempel finns inga utdata för IPV4 och IPV6-adresser, vilket indikerar att DNS-servern inte är löst. Detta innebär att ingen anslutning till det externa nätverket och en korrekt DNS måste anges. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exempel på utdata – rätt DNS**

I följande exempel returnerar DNS IPV4-adress, som anger att DNS är korrekt konfigurerad. Det här bekräftar att det finns en anslutning till det externa nätverket. 

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Felsöka med cmdleten Test-HcsmConnection
Använd den `Test-HcsmConnection` cmdlet för en enhet som redan är ansluten till och registrerad med StorSimple Manager-tjänsten. Denna cmdlet hjälper dig att kontrollera anslutningen mellan en registrerad enhet och motsvarande StorSimple Manager-tjänsten. Du kan köra det här kommandot på Windows PowerShell för StorSimple. 

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Att köra cmdleten Test-HcsmConnection
1. Kontrollera att enheten är registrerad.
2. Kontrollera enhetens status. Om enheten har inaktiverats i underhållsläge eller offline, kan du se följande fel: 
   
   * ErrorCode.CiSDeviceDecommissioned – detta anger att enheten har inaktiverats.
   * ErrorCode.DeviceNotReady – detta anger att enheten är i underhållsläge.
   * ErrorCode.DeviceNotReady – detta anger att enheten inte är online.
3. Kontrollera att StorSimple Manager-tjänsten körs (använda den [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) cmdlet). Om tjänsten inte körs kan du se följande fel:
   
   * ErrorCode.CiSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – detta anger att ett undantag uppstod när du körde Get-ClusterResource.
4. Kontrollera token Access Control Service (ACS). Om det genererar ett webbundantag kan det vara resultatet av ett gateway-problem, saknas proxyautentisering, en felaktig DNS- eller ett autentiseringsfel. Du kan se följande fel:
   
   * ErrorCode.CiSApplianceGateway – detta anger ett HttpStatusCode.BadGateway undantag: lösartjänsten namn gick inte att matcha värdnamnet. 
   * ErrorCode.CiSApplianceProxy – detta anger ett HttpStatusCode.ProxyAuthenticationRequired undantag (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern. 
   * ErrorCode.CiSApplianceDNSError – detta anger ett WebExceptionStatus.NameResolutionFailure undantag: lösartjänsten namn gick inte att matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta anger att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om det inget genereras ett webbundantag, kontrollera om ErrorCode.CiSApplianceFailure. Detta anger att anordningen misslyckades.
5. Kontrollera cloud service-anslutning. Om tjänsten genererar ett webbundantag, kan du se följande fel:
   
   * ErrorCode.CiSApplianceGateway – detta anger ett HttpStatusCode.BadGateway undantag: en mellanliggande proxyserver tog emot en felaktig begäran från en annan proxy eller från den ursprungliga servern.
   * ErrorCode.CiSApplianceProxy – detta anger ett HttpStatusCode.ProxyAuthenticationRequired undantag (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern. 
   * ErrorCode.CiSApplianceDNSError – detta anger ett WebExceptionStatus.NameResolutionFailure undantag: lösartjänsten namn gick inte att matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta anger att tjänsten returnerade ett autentiseringsfel, men det finns en anslutning.
     
     Om det inget genereras ett webbundantag, kontrollera om ErrorCode.CiSApplianceSaasServiceError. Det här tyder på problem med StorSimple Manager-tjänsten.
6. Kontrollera Azure Service Bus-anslutningen. ErrorCode.CiSApplianceServiceBusError anger att enheten inte kan ansluta till Service Bus.

CiSCommandletLog0Curr.errlog loggfiler och CiSAgentsvc0Curr.errlog har mer information, till exempel undantagsinformation. 

Mer information om hur du använder cmdlet, gå till [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) refererar dokumentationen i Windows PowerShell.

> [!IMPORTANT]
> Du kan köra denna cmdlet för både aktiva och passiva domänkontrollant. 
> 
> 

Se följande exempel på utdata från den `Test-HcsmConnection` cmdlet. 

**Exempel på utdata – registrerades enhet som kör StorSimple Release (juli 2014)**

Det första exemplet är från en enhet som har registrerats med StorSimple Manager-tjänsten och har inga problem med nätverksanslutningen. 

     Controller1>Test-HcsmConnection -verbose
     Checking device state  ... Success.
     Device registered successfully
     Checking device authentication.  ... This operation will take few minutes to complete....
     Checking device authentication.  ... Success.
     Checking connectivity from device to StorSimple Manager service.  ... This operation will take few minutes to complete....
     Checking connectivity from device to StorSimple Manager service.  ... Success.
     Checking connectivity from StorSimple Manager service to StorSimple device. .... Success.
     Controller1>

**Exempel på utdata – registrerades enhet som kör StorSimple uppdatering 1**

Om du kör uppdatering 1 på din StorSimple-enhet behöver du inte köra det med växeln utförlig.

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

**Exempel på utdata – offline enhet som kör StorSimple Release (juli 2014)**

Det här exemplet är från en enhet som har statusen **Offline** i den klassiska Azure-portalen.

     Checking device state: Success 
     Device is registered successfully 
     Checking connectivity from device to SaaS.. Failure

Enheten kunde inte ansluta med hjälp av den aktuella webbproxykonfigurationen. Detta kan bli ett problem med webbproxykonfigurationen eller ett problem med nätverksanslutningen. I det här fallet bör du se till att din webbproxyinställningarna är korrekta och webbproxyservrarna är online och kan nås. 

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Felsöka med cmdleten synkronisering HcsTime
Använd denna cmdlet för att visa enheter. Om enheten tid har en förskjutning med NTP-server kan använda du sedan denna cmdlet för att framtvinga synkronisera tiden med NTP-server. Om förskjutningen mellan enheten och NTP-server är större än 5 minuter, visas en varning. Om förskjutningen överskrider 15 minuter, kommer enheten går offline. Du kan fortfarande använda denna cmdlet för att framtvinga tidssynkronisering. Men om förskjutningen överskrider 15 timmar, kommer sedan du inte kunna force-sync tid och ett felmeddelande visas.

**Exempel på utdata – framtvingad tidssynkronisering med synkronisering HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Felsöka med cmdlets HcsPing aktivera och inaktivera HcsPing
Du kan använda dessa cmdletar för att säkerställa att nätverksgränssnitt på enheten svarar på ICMP-ping-begäranden. Som standard svarar nätverksgränssnitt StorSimple inte på ping-begäran. Använder denna cmdlet är det enklaste sättet att veta om enheten är online och kan nås.  

**Exempel på utdata – HcsPing aktivera och inaktivera HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Felsöka med Trace-HcsRoute cmdlet
Använd denna cmdlet som ett verktyg för spårning av vägen. Den skickar paket till varje router på väg till slutmålet under en viss tidsperiod och beräknar sedan resultatet baserat på de paket som returneras från varje hopp. Eftersom cmdlet visar hur mycket av paketförlust vid varje router eller länk, kan du identifiera vilka routrar eller länkar som orsakar problem med nätverket.

**Exempel på utdata visar hur du spåra ett paket med Trace-HcsRoute**

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
Använd denna cmdlet om du vill visa routningstabellen för din StorSimple-enhet. En routningstabell är en uppsättning regler som kan hjälpa dig att avgöra var datapaket som skickas över ett nätverk för IP (Internet Protocol) dirigeras. 

Routningstabellen visar gränssnitt och den gateway som skickar data till de angivna nätverk. Det ger även routning måttet som är beslutsfattare för sökvägen för att nå en viss destination. Ju lägre routning måttet, desto högre prioritet. 

Till exempel om du har 2 nätverksgränssnitten DATA 2 och DATA 3 ansluten till Internet. Om routning mätvärden för DATA 2 och DATA 3 är 15 och 261, är DATA 2 med lägre routning mått prioriterade gränssnittet används för att komma åt Internet.

Om du kör uppdatering 1 på StorSimple-enheten har högsta prioritet för molnet trafik DATA 0-nätverksgränssnittet. Detta innebär att även om det finns andra moln-aktiverat gränssnitt, molntrafiken ska skickas via DATA 0. 

Om du kör den `Get-HcsRoutingTable` cmdlet utan att ange eventuella parametrar (som visas i följande exempel), kommer cmdleten utdata routningstabeller för både IPv4 och IPv6. Du kan också ange `Get-HcsRoutingTable -IPv4` eller `Get-HcsRoutingTable -IPv6` att hämta en relevant routningstabell.

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

Returnerade felmeddelandet är:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Felet kan orsakas av något av följande:

* Installation av felaktig maskinvara
* Felaktig nätverksgränssnitt
* Felaktigt IP-adress, nätmask, gateway, primär DNS-server eller webbproxy
* Felaktig registreringsnyckel
* Felaktiga brandväggsinställningar

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Leta upp och åtgärda problemet med enheten registreringen
1. Kontrollera enhetskonfigurationen av: kör på den aktiva styrenheten `Invoke-HcsSetupWizard`.
   
   > [!NOTE]
   > Installationsguiden måste köras på den aktiva styrenheten. Titta på banderollen presenteras i seriekonsolen för att kontrollera att du är ansluten till den aktiva styrenheten. Popup-meddelandet anger om du är ansluten till styrenhet 0 eller 1-styrenheten och om registeransvarige är aktiva eller passiva. Mer information finns på [identifiera en domänkontrollant för en aktiv på enheten](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device).
   > 
   > 
2. Kontrollera att enheten är korrekt kabelansluten: Kontrollera nätverket kablar på enheten tillbaka plana. Anslutningarna är specifik för enhetsmodellen. Mer information finns på [installerar StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [installera din StorSimple-8600-enhet](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Om du använder 10 GbE-nätverksportar, behöver du använda den angivna QSFP SFP-kort och SFP-kablar. Mer information finns i [lista över kablar och växlar mottagarna rekommenderas för 10 GbE-portar](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
   > 
   > 
3. Kontrollera hälsotillståndet för nätverksgränssnittet:
   
   * Använd cmdleten Get-NetAdapter för att identifiera hälsotillståndet för nätverksgränssnitten för DATA 0. 
   * Om länken inte fungerar i **ifindex** status anger att gränssnittet är igång. Du måste sedan kontrollera nätverksanslutningen för porten till enhet och till växeln. Du måste också utesluta felaktiga kablar. 
   * Om du misstänker att DATA 0 port på den aktiva styrenheten misslyckades, du kan kontrollera detta genom att ansluta till DATA 0-port på en domänkontrollant 1. Koppla från den seriella kabeln på baksidan av enheten från styrenhet 0 för att bekräfta detta, Anslut kabeln till kontrollant 1 och kör sedan cmdleten Get-NetAdapter igen. 
     Om DATA 0-port på en domänkontrollant misslyckas, [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. Du kan behöva ersätta domänkontrollant på datorn.
4. Kontrollera anslutningen till växeln:
   
   * Se till att DATA 0 nätverksgränssnitt på styrenhet 0 och kontrollant 1 i din primära hölje är i samma undernät. 
   * Kontrollera navet eller routern. Normalt bör du ansluta båda domänkontrollanter på samma hubb eller routern. 
   * Se till att de växlar som du använder för anslutningen har DATA 0 för både domänkontrollanter i samma vLAN.
5. Ta bort användarfel:
   
   * Kör installationsguiden igen (kör **Invoke-HcsSetupWizard**), och ange värden igen för att se till att det inte finns några fel. 
   * Kontrollera registreringen nyckel som används. Samma Registreringsnyckeln kan användas för att ansluta flera enheter till en StorSimple Manager-tjänsten. Använd proceduren i [hämta nyckel för tjänstregistrering](storsimple-manage-service.md#get-the-service-registration-key) så att du använder rätt Registreringsnyckeln.
     
     > [!IMPORTANT]
     > Om du har flera tjänster som körs, behöver du se till att Registreringsnyckeln för tjänsten används för att registrera enheten. Om du har registrerat en enhet med fel StorSimple Manager-tjänsten, behöver du [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. Du kan behöva utföra en fabriksåterställning på enheten (vilket kan leda till förlust av data) och sedan ansluta till den avsedda användningen.
     > 
     > 
6. Använda cmdleten Test-Connection för att verifiera att du har anslutning till det externa nätverket. Mer information finns på [felsökning med cmdleten Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Kontrollera brandväggen störningar. Om du har verifierat att virtuella IP-adresser (VIP), undernät, gateway och DNS-inställningarna är korrekta, och du fortfarande se anslutningsproblem sedan är det möjligt att din brandvägg blockerar kommunikation mellan enheten och det externa nätverket. Du måste kontrollera att portarna 80 och 443 är tillgängliga på din StorSimple-enhet för utgående kommunikation. Mer information finns i [nätverkskrav för din StorSimple-enhet](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Titta på loggfilerna. Gå till [stödja paket och tillgängliga enhetsloggar för felsökning](#support-packages-and-device-logs-available-for-troubleshooting).
9. Om de föregående stegen inte löser problemet, [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) om du behöver hjälp.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du felsöker en operativa enhet](storsimple-troubleshoot-operational-device.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
