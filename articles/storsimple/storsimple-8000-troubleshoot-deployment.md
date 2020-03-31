---
title: Felsöka driftsättningsproblem i StorSimple 8000-serien | Microsoft-dokument
description: Beskriver hur du diagnostiserar och åtgärdar fel som uppstår när du först distribuerar StorSimple.
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
ms.openlocfilehash: f2b454e812db1eea686f82e92841163f1129b6c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267630"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Felsöka problem med distribution av StorSimple-enheter
## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsvägledning för din Microsoft Azure StorSimple-distribution. Den beskriver vanliga problem, möjliga orsaker och rekommenderade steg som hjälper dig att lösa problem som kan uppstå när du konfigurerar StorSimple. 

Den här informationen gäller både Den fysiska enheten i StorSimple 8000-serien och StorSimple Cloud Appliance.

> [!NOTE]
> Enhetskonfigurationsrelaterade problem som du kan ställas inför kan uppstå när du distribuerar enheten för första gången, eller så kan de uppstå senare när enheten är i drift. Den här artikeln fokuserar på felsökning av första gången distributionsproblem. Om du vill felsöka en fungerande enhet går du till [Använd diagnostikverktyget för att felsöka en fungerande enhet](storsimple-8000-diagnostics.md).

I den här artikeln beskrivs också verktygen för felsökning av StorSimple-distributioner och ett steg-för-steg-felsökningsexempel.

## <a name="first-time-deployment-issues"></a>Problem med första gångens distribution
Om du stöter på ett problem när du distribuerar enheten för första gången bör du tänka på följande:

* Om du felsöker en fysisk enhet kontrollerar du att maskinvaran har installerats och konfigurerats enligt beskrivningen i [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
* Kontrollera förutsättningarna för distribution. Kontrollera att du har all information som beskrivs i [checklistan för distributionskonfiguration](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Granska StorSimple-viktig information för att se om problemet beskrivs. Viktig information innehåller lösningar för kända installationsproblem. 

Under enhetsdistributionen uppstår de vanligaste problemen som användarna ställs inför när de kör installationsguiden och när de registrerar enheten via Windows PowerShell för StorSimple. (Du använder Windows PowerShell för StorSimple för att registrera och konfigurera StorSimple-enheten. Mer information om enhetsregistrering finns i [Steg 3: Konfigurera och registrera enheten via Windows PowerShell för StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Följande avsnitt kan hjälpa dig att lösa problem som uppstår när du konfigurerar StorSimple-enheten för första gången.

## <a name="first-time-setup-wizard-process"></a>Första gången installationsguiden process
I följande steg sammanfattas installationsguiden. Detaljerad inställningsinformation finns i [Distribuera din lokala StorSimple-enhet](storsimple-8000-deployment-walkthrough-u2.md).

1. Kör [cmdleten Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) för att starta installationsguiden som guidar dig genom de återstående stegen. 
2. Konfigurera nätverket: Med installationsguiden kan du konfigurera nätverksinställningar för DATA 0-nätverksgränssnittet på StorSimple-enheten. Dessa inställningar omfattar följande:
   * Virtuell IP (VIP), nätmask och gateway – [Cmdlet Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) körs i bakgrunden. Den konfigurerar IP-adressen, nätmasken och gatewayen för DATA 0-nätverksgränssnittet på Din StorSimple-enhet.
   * Primär [DNS-server – Cmdlet Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) körs i bakgrunden. Dns-inställningarna för storsimple-lösningen konfigureras.
   * NTP-server – [Cmdlet Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) körs i bakgrunden. Ntp-serverinställningarna konfigureras för StorSimple-lösningen.
   * Valfri webbproxy – [Cmdlet Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) körs i bakgrunden. Den ställer in och aktiverar webbproxykonfigurationen för din StorSimple-lösning.
3. Konfigurera lösenordet: nästa steg är att konfigurera lösenordet för enhetsadministratören.
   Enhetsadministratörens lösenord används för att logga in på enheten. Enheten standardlösenord är **Password1**.
        
     > [!IMPORTANT]
     > Lösenord samlas in före registreringen, men tillämpas först när du har registrerat enheten. Om ett lösenord inte har tillämpats uppmanas du att ange lösenordet igen tills de lösenord som krävs (som uppfyller komplexitetskraven) har samlats in.
     
4. Registrera enheten: det sista steget är att registrera enheten med StorSimple Device Manager-tjänsten som körs i Microsoft Azure. Registreringen kräver att du [hämtar tjänstregistreringsnyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) från Azure-portalen och anger den i installationsguiden. **När enheten har registrerats tillhandahålls du en krypteringsnyckel för tjänstdata. Var noga med att hålla den här krypteringsnyckeln på en säker plats eftersom det kommer att krävas för att registrera alla efterföljande enheter med tjänsten.**

## <a name="common-errors-during-device-deployment"></a>Vanliga fel under enhetsdistribution
I följande tabeller visas de vanliga fel som kan uppstå när du:

* Konfigurera de nätverksinställningar som krävs.
* Konfigurera valfria webbproxyinställningar.
* Konfigurera lösenordet för enhetsadministratören.
* Registrera enheten.

## <a name="errors-during-the-required-network-settings"></a>Fel under de nödvändiga nätverksinställningarna
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Det här kommandot kan bara köras på den aktiva styrenheten. |Konfigurationen utfördes på den passiva styrenheten. |Kör det här kommandot från den aktiva styrenheten. Mer information finns i [Identifiera en aktiv handkontroll på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: Enheten är inte klar. |Det finns problem med nätverksanslutningen på DATA 0. |Kontrollera den fysiska nätverksanslutningen på DATA 0. |
| 3 |Invoke-HcsSetupWizard: Det finns en IP-adresskonflikt med ett annat system i nätverket (Undantag från HRESULT: 0x80070263). |Den IP som angavs för DATA 0 användes redan av ett annat system. |Ange en ny IP som inte används. |
| 4 |Invoke-HcsSetupWizard: En klusterresurs misslyckades. (Undantag från HRESULT: 0x800713AE). |Duplicera VIP. Den angivna IP-adressen används redan. |Ange en ny IP som inte används. |
| 5 |Invoke-HcsSetupWizard: Ogiltig IPv4-adress. |IP-adressen anges i ett felaktigt format. |Kontrollera formatet och ange din IP-adress igen. Mer information finns i [Ipv4-adressering][1]. |
| 6 |Invoke-HcsSetupWizard: Ogiltig IPv6-adress. |IP-adressen anges i ett felaktigt format. |Kontrollera formatet och ange din IP-adress igen. Mer information finns i [Ipv6-adressering][2]. |
| 7 |Invoke-HcsSetupWizard: Det finns inga fler slutpunkter tillgängliga från slutpunktsmapparen. (Undantag från HRESULT: 0x800706D9) |Klusterfunktionen fungerar inte. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Fel under de valfria webbproxyinställningarna
| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: Ogiltig parameter (Undantag från HRESULT: 0x80070057) |En av parametrarna för proxyinställningarna är ogiltig. |URI:n finns inte i rätt format. Använd följande format: http://*\<IP-adress eller FQDN för webbproxyservern>: **\<TCP-portnummer>* |
| 2 |Invoke-HcsSetupWizard: RPC-server är inte tillgänglig (Undantag från HRESULT: 0x800706ba) |Grundorsaken är något av följande:<ol><li>Klustret är inte uppe.</li><li>Den passiva styrenheten kan inte kommunicera med den aktiva styrenheten och kommandot körs från passiv styrenhet.</li></ol> |Beroende på grundorsaken:<ol><li>[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att se till att klustret är uppe.</li><li>Kör kommandot från den aktiva styrenheten. Om du vill köra kommandot från den passiva styrenheten måste du se till att den passiva styrenheten kan kommunicera med den aktiva styrenheten. Du måste [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) om anslutningen är bruten.</li></ol> |
| 3 |Invoke-HcsSetupWizard: RPC-anropet misslyckades (Undantag från HRESULT: 0x800706be) |Klustret är nere. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att se till att klustret är uppe. |
| 4 |Invoke-HcsSetupWizard: Klusterresurs hittades inte (Undantag från HRESULT: 0x8007138f) |Det gick inte att hitta klusterresursen. Detta kan inträffa när installationen inte var korrekt. |Du kan behöva återställa enheten till fabriksinställningarna. [Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att skapa en klusterresurs. |
| 5 |Invoke-HcsSetupWizard: Klusterresurs är inte online (Undantag från HRESULT: 0x8007138c) |Klusterresurser är inte online. |[Kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |

## <a name="errors-related-to-device-administrator-password"></a>Fel relaterade till enhetsadministratörens lösenord
Standardlösenordet för enhetsadministratören är **Password1**. Det här lösenordet upphör att gälla efter den första inloggningen. Därför måste du använda installationsguiden för att ändra den. Du måste ange ett nytt lösenord för enhetsadministratören när du registrerar enheten för första gången. 

Se till att dina lösenord uppfyller följande krav:

* Lösenordet för enhetsadministratören bör vara mellan 8 och 15 tecken långt.
* Lösenord bör innehålla 3 av följande 4 teckentyper: gemener, versaler, numeriska och speciella. 
* Ditt lösenord kan inte vara detsamma som de senaste 24 lösenorden.

Tänk dessutom på att lösenorden upphör att gälla varje år och kan ändras först när du har registrerat enheten. Om registreringen misslyckas av någon anledning ändras inte lösenorden.

Mer information om lösenord för enhetsadministratör finns i [Använda Tjänsten StorSimple Device Manager för att ändra ditt StorSimple-lösenord](storsimple-8000-change-passwords.md).

Du kan stöta på ett eller flera av följande fel när du konfigurerar lösenorden för enhetsadministratören och StorSimple Snapshot Manager.

| Nej. | Felmeddelande | Rekommenderad åtgärd |
| --- | --- | --- |
| 1 |Lösenordet överskrider den maximala längden. |Lösenordet för enhetsadministratören måste vara mellan 8 och 15 tecken långt. |
| 2 |Lösenordet uppfyller inte önskad längd. |Lösenordet för enhetsadministratören måste vara mellan 8 och 15 tecken långt.|
| 3 |Lösenordet måste innehålla gemener. |Lösenord måste innehålla 3 av följande 4 teckentyper: gemener, versaler, numeriska och speciella. Se till att ditt lösenord uppfyller dessa krav. |
| 4 |Lösenordet måste innehålla numeriska tecken. |Lösenord måste innehålla 3 av följande 4 teckentyper: gemener, versaler, numeriska och speciella. Se till att ditt lösenord uppfyller dessa krav. |
| 5 |Lösenordet måste innehålla specialtecken. |Lösenord måste innehålla 3 av följande 4 teckentyper: gemener, versaler, numeriska och speciella. Se till att ditt lösenord uppfyller dessa krav. |
| 6 |Lösenordet måste innehålla 3 av följande 4 teckentyper: versaler, gemener, numeriska och speciella. |Ditt lösenord innehåller inte de teckentyper som krävs. Se till att ditt lösenord uppfyller dessa krav. |
| 7 |Parametern matchar inte bekräftelse. |Se till att ditt lösenord uppfyller alla krav och att du har angett det korrekt. |
| 8 |Ditt lösenord kan inte matcha standardinställningen. |Standardlösenordet är *Password1*. Du måste ändra det här lösenordet när du har loggat in för första gången. |
| 9 |Det angivna lösenordet matchar inte enhetslösenordet. Skriv lösenordet igen. |Kontrollera lösenordet och skriv det igen. |

Lösenord samlas in innan enheten registreras, men tillämpas endast efter lyckad registrering. Arbetsflödet för återställning av lösenord kräver att enheten registreras.

> [!IMPORTANT]
> I allmänhet, om ett försök att tillämpa ett lösenord misslyckas, försöker programvaran upprepade gånger att samla in lösenordet tills det lyckas. I sällsynta fall kan lösenordet inte tillämpas. I det här fallet kan du registrera enheten och fortsätta, men lösenorden kommer inte att ändras. Du kan ändra lösenordet för enhetsadministratören efter registreringen från Azure-portalen.


Du kan återställa lösenordet i Azure-portalen via StorSimple Device Manager-tjänsten. Mer information finns i [Ändra enhetsadministratörens lösenord](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Fel under enhetsregistreringen
Du använder Tjänsten StorSimple Device Manager som körs i Microsoft Azure för att registrera enheten. Du kan stöta på ett eller flera av följande problem under enhetsregistreringen.

| Nej. | Felmeddelande | Möjliga orsaker | Rekommenderad åtgärd |
| --- | --- | --- | --- |
| 1 |Fel 350027: Det gick inte att registrera enheten hos StorSimple-enhetshanteraren. | |Vänta några minuter och försök sedan igen. Om problemet kvarstår [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 2 |Fel 350013: Ett fel har uppstått när enheten registrerades. Detta kan bero på felaktig tjänstregistreringsnyckel. | |Registrera enheten igen med rätt serviceregistreringsnyckel. Mer information finns i [Hämta tjänstregistreringsnyckeln.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Fel 350063: Autentisering till StorSimple Enhetshanteraren-tjänsten gick men registreringen misslyckades. Försök igen efter en tid. |Det här felet indikerar att autentisering med ACS har skickats men att registeranropet som gjordes till tjänsten har misslyckats. Detta kan vara ett resultat av ett sporadiskt nätverk glitch. |Om problemet kvarstår kontaktar du [Microsoft Support](storsimple-8000-contact-microsoft-support.md). |
| 4 |Fel 350049: Tjänsten kunde inte nås under registreringen. |När samtalet görs till tjänsten tas ett webbundantag emot. I vissa fall kan detta åtgärdas genom att försöka igen åtgärden senare. |Kontrollera din IP-adress och DNS-namn och försök sedan igen. Om problemet kvarstår [kontaktar du Microsoft Support.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Fel 350031: Enheten har redan registrerats. | |Inga åtgärder behövs. |
| 6 |Fel 350016: Enhetsregistrering misslyckades. | |Kontrollera att registreringsnyckeln är korrekt. |
| 7 |Invoke-HcsSetupWizard: Ett fel uppstod när enheten registrerades. Detta kan bero på felaktig IP-adress eller DNS-namn. Kontrollera nätverksinställningarna och försök igen. Om problemet kvarstår [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md). (Fel 350050) |Se till att enheten kan pinga det externa nätverket. Om du inte har anslutning till externt nätverk kan registreringen misslyckas med det här felet. Det här felet kan vara en kombination av ett eller flera av följande:<ul><li>Felaktig IP</li><li>Felaktigt undernät</li><li>Felaktig gateway</li><li>Felaktiga DNS-inställningar</li></ul> |Se stegen i [exemplet med felsökning steg för steg](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: Den aktuella åtgärden misslyckades på grund av ett internt tjänstfel [0x1FBE2]. Försök igen efter någon gång. Om problemet kvarstår kontaktar du Microsoft Support. |Detta är ett allmänt fel som genereras för alla användare osynliga fel från tjänst eller agent. Den vanligaste orsaken kan vara att ACS-autentiseringen har misslyckats. En möjlig orsak till felet är att det finns problem med NTP-serverns konfiguration och att tiden på enheten inte är korrekt inställd. |Korrigera tiden (om det finns problem) och försök sedan igen registreringsåtgärden. Om du använder kommandot Set-HcsSystem -Timezone för att justera tidszonen aktiveras varje ord med versaler i tidszonen (till exempel "Stillahavsstandardtid").  Om problemet kvarstår [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. |
| 9 |Varning: Det gick inte att aktivera enheten. Lösenorden för enhetsadministratören och StorSimple Snapshot Manager har inte ändrats. |Om registreringen misslyckas ändras inte enhetsadministratören och StorSimple Snapshot Manager-lösenorden. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Verktyg för felsökning av StorSimple-distributioner
StorSimple innehåller flera verktyg som du kan använda för att felsöka din StorSimple-lösning. Exempel på dessa är:

* Stödpaket och enhetsloggar.
* Cmdlets speciellt utformade för felsökning.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Supportpaket och enhetsloggar tillgängliga för felsökning
Ett supportpaket innehåller alla relevanta loggar som kan hjälpa Microsoft Support-teamet med felsökningsenhetsproblem. Du kan använda Windows PowerShell för StorSimple för att generera ett krypterat supportpaket som du sedan kan dela med supportpersonal.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Så här visar du loggarna eller innehållet i supportpaketet
1. Använd Windows PowerShell för StorSimple för att generera ett supportpaket enligt beskrivningen i [Skapa och hantera ett supportpaket](storsimple-8000-create-manage-support-package.md).
2. Hämta [dekrypteringsskriptet](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalt på klientdatorn.
3. Använd den här [steg-för-steg-proceduren](storsimple-8000-create-manage-support-package.md#edit-a-support-package) för att öppna och dekryptera supportpaketet.
4. De dekrypterade supportpaketloggarna är i etw/etvx-format. Du kan utföra följande steg för att visa dessa filer i Windows Loggboken:
   
   1. Kör **kommandot eventvwr** på Din Windows-klient. Då startar Loggboken.
   2. Klicka på Öppna **sparad logg** i **åtgärdsfönstret** och peka på loggfilerna i etvx/etw-format (supportpaketet). Nu kan du visa filen. När du har öppnat filen kan du högerklicka och spara filen som text.
      
      > [!IMPORTANT]
      > Du kan också använda cmdleten **Get-WinEvent** för att öppna dessa filer i Windows PowerShell. Mer information finns i [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) i referensdokumentationen för Windows PowerShell-cmdlet.
     
5. När loggarna öppnas i Loggboken letar du efter följande loggar som innehåller problem relaterade till enhetskonfigurationen:
   
   * hcs_pfconfig/driftslogg
   * hcs_pfconfig/Config
6. Sök efter strängar relaterade till de cmdletar som anropas av installationsguiden i loggfilerna. Se [Första gången installationsguiden process](#first-time-setup-wizard-process) för en lista över dessa cmdlets.
7. Om du inte kan ta reda på orsaken till problemet kan du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Följ stegen i [Skapa en supportbegäran](storsimple-8000-contact-microsoft-support.md#create-a-support-request) när du kontaktar Microsoft Support för att få hjälp.

## <a name="cmdlets-available-for-troubleshooting"></a>Cmdlets tillgängliga för felsökning
Använd följande Windows PowerShell-cmdlets för att identifiera anslutningsfel.

* `Get-NetAdapter`: Använd den här cmdleten för att upptäcka hälsotillståndet för nätverksgränssnitt.
* `Test-Connection`: Använd den här cmdleten för att kontrollera nätverksanslutningen i och utanför nätverket.
* `Test-HcsmConnection`: Använd den här cmdleten för att kontrollera anslutningen till en enhet som har registrerats.
* `Sync-HcsTime`: Använd den här cmdleten för att visa enhetens tid och tvinga fram en tidssynkronisering med NTP-servern.
* `Enable-HcsPing`och `Disable-HcsPing`: Använd dessa cmdlets för att låta värdarna pinga nätverksgränssnitten på din StorSimple-enhet. Som standard svarar inte StorSimple-nätverksgränssnitten på ping-begäranden.
* `Trace-HcsRoute`: Använd den här cmdleten som ett ruttspårningsverktyg. Den skickar paket till varje router på väg till ett slutmål under en tidsperiod och beräknar sedan resultat baserat på de paket som returneras från varje hopp. Eftersom `Trace-HcsRoute` visar graden av paketförlust på en viss router eller länk kan du precisera vilka routrar eller länkar som kan orsaka nätverksproblem.
* `Get-HcsRoutingTable`: Använd den här cmdleten för att visa den lokala IP-routningstabellen.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Felsöka med Cmdlet Get-NetAdapter
När du konfigurerar nätverksgränssnitt för en första gången enhetsdistribution är maskinvarustatusen inte tillgänglig i tjänstgränssnittet för StorSimple Enhetshanteraren eftersom enheten ännu inte är registrerad hos tjänsten. Dessutom kanske **maskinvaruhälsobladet** inte alltid återspeglar enhetens tillstånd, särskilt inte om det finns problem som påverkar synkroniseringen av tjänsten. I dessa situationer kan `Get-NetAdapter` du använda cmdlet för att fastställa hälsotillstånd och status för nätverksgränssnitten.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Så här visar du en lista över alla nätverkskort på enheten
1. Starta Windows PowerShell för StorSimple `Get-NetAdapter`och skriv sedan . 
2. Använd cmdletens `Get-NetAdapter` utdata och följande riktlinjer för att förstå nätverksgränssnittets status.
   
   * Om gränssnittet är felfritt och aktiverat visas **ifIndex-statusen** som **Upp**.
   * Om gränssnittet är felfritt men inte är fysiskt anslutet (via en nätverkskabel) visas **ifIndex** som **Inaktiverat**.
   * Om gränssnittet är felfritt men inte aktiverat visas **ifIndex-statusen** **som NotPresent**.
   * Om gränssnittet inte finns visas det inte i den här listan. Tjänstgränssnittet i StorSimple Device Manager visar fortfarande gränssnittet i ett misslyckat tillstånd.

Mer information om hur du använder den här cmdleten finns i [Get-NetAdapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) i cmdletreferensen för Windows PowerShell.

Följande avsnitt visar exempel på `Get-NetAdapter` utdata från cmdleten.

 I dessa prover var styrenheten 0 den passiva styrenheten och konfigurerades på följande sätt:

* Data 0, DATA 1, DATA 2 och DATA 3 nätverksgränssnitt fanns på enheten.
* Nätverkskorten DATA 4 och DATA 5 fanns inte med. Därför är de inte listade i produktionen.
* DATA 0 aktiverades.

Controller 1 var den aktiva styrenheten och konfigurerades på följande sätt:

* Data 0, DATA 1, DATA 2, DATA 3, DATA 4 och DATA 5 nätverksgränssnitt fanns på enheten.
* DATA 0 aktiverades.

**Exempel på utdata – styrenhet 0**

Följande är utgången från styrenheten 0 (den passiva styrenheten). DATA 1, DATA 2 och DATA 3 är inte anslutna. DATA 4 och DATA 5 visas inte eftersom de inte finns på enheten.

     Controller0>Get-NetAdapter
     Name                 InterfaceDescription                        ifIndex  Status
     ------               --------------------                        -------  ----------
     DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
     DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
     Ethernet 2           HCS VNIC                                    13       Up
     DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
     DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up


**Exempel på utgång – styrenhet 1**

Följande är utgången från styrenheten 1 (den aktiva styrenheten). Endast data 0-nätverksgränssnittet på enheten är konfigurerat och fungerar.

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


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Felsöka med cmdlet för testanslutning
Du kan `Test-Connection` använda cmdleten för att avgöra om Din StorSimple-enhet kan ansluta till det externa nätverket. Om alla nätverksparametrar, inklusive DNS, är korrekt konfigurerade i installationsguiden kan du använda cmdleten `Test-Connection` för att pinga en känd adress utanför nätverket, till exempel outlook.com.

Du bör aktivera ping för att felsöka anslutningsproblem med den här cmdleten om ping är inaktiverad.

Se följande exempel på `Test-Connection` utdata från cmdleten.

> [!NOTE]
> I det första exemplet konfigureras enheten med en felaktig DNS. I det andra exemplet är DNS korrekt.

**Exempel på utdata – felaktig DNS**

I följande exempel finns det ingen utdata för IPV4- och IPV6-adresserna, vilket indikerar att DNS-et inte är löst. Detta innebär att det inte finns någon anslutning till det externa nätverket och en korrekt DNS måste levereras.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com
     HCSNODE0      outlook.com

**Exempel på utdata – korrekt DNS**

I följande exempel returnerar DNS IPV4-adressen, vilket anger att DNS:n är korrekt konfigurerad. Detta bekräftar att det finns anslutning till det externa nätverket.

     Source        Destination     IPV4Address      IPV6Address
     ------        -----------     -----------      -----------
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194
     HCSNODE0      outlook.com     132.245.92.194

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Felsöka med test-hcsmconnection-cmdlet
Använd `Test-HcsmConnection` cmdlet för en enhet som redan är ansluten till och registrerad med tjänsten StorSimple Device Manager. Den här cmdleten hjälper dig att verifiera anslutningen mellan en registrerad enhet och motsvarande StorSimple Enhetshanteraren-tjänst. Du kan köra det här kommandot i Windows PowerShell för StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Så här kör du test-hcsmconnection-cmdlet
1. Kontrollera att enheten är registrerad.
2. Kontrollera enhetens status. Om enheten är inaktiverad, i underhållsläge eller offline kan något av följande fel visas:
   
   * ErrorCode.CiSDeviceDecommissioned – detta indikerar att enheten är inaktiverad.
   * ErrorCode.DeviceNotReady – detta indikerar att enheten är i underhållsläge.
   * ErrorCode.DeviceNotReady – detta indikerar att enheten inte är online.
3. Kontrollera att Tjänsten StorSimple Device Manager körs (använd cmdleten [Get-ClusterResource).](https://technet.microsoft.com/library/ee461004.aspx) Om tjänsten inte körs kan följande fel visas:
   
   * ErrorCode.ciSApplianceAgentNotOnline
   * ErrorCode.CisPowershellScriptHcsError – detta indikerar att det fanns ett undantag när du körde Get-ClusterResource.
4. Kontrollera ACS-token (Access Control Service). Om ett webbundantag genereras kan det bero på ett gatewayproblem, en proxyautentisering som saknas, en felaktig DNS eller ett autentiseringsfel. Följande fel kan visas:
   
   * ErrorCode.CiSApplianceGateway – detta indikerar ett HttpStatusCode.BadGateway-undantag: namnmatchningstjänsten kunde inte matcha värdnamnet.
   * ErrorCode.CiSApplianceProxy – detta indikerar ett undantag från HttpStatusCode.ProxyAuthenticationRequired (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern.
   * ErrorCode.CiSApplianceDNSError – detta indikerar ett WebExceptionStatus.NameResolutionFailure-undantag: namnmatchningstjänsten kunde inte matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta indikerar att tjänsten returnerade ett autentiseringsfel, men det finns anslutning.
     
     Om det inte genererar ett webbundantag kontrollerar du om det finns felkod.ciSApplianceFailure. Detta indikerar att apparaten misslyckades.
5. Kontrollera molntjänstanslutningen. Om tjänsten genererar ett webbundantag kan följande fel visas:
   
   * ErrorCode.CiSApplianceGateway – detta indikerar ett HttpStatusCode.BadGateway-undantag: en mellanliggande proxyserver fick en felaktig begäran från en annan proxy eller från den ursprungliga servern.
   * ErrorCode.CiSApplianceProxy – detta indikerar ett undantag från HttpStatusCode.ProxyAuthenticationRequired (HTTP-statuskod 407): klienten kunde inte autentisera med proxyservern.
   * ErrorCode.CiSApplianceDNSError – detta indikerar ett WebExceptionStatus.NameResolutionFailure-undantag: namnmatchningstjänsten kunde inte matcha värdnamnet.
   * ErrorCode.CiSApplianceACSError – detta indikerar att tjänsten returnerade ett autentiseringsfel, men det finns anslutning.
     
     Om det inte genererar ett webbundantag kontrollerar du om det finns felkod.CiSApplianceSaasServiceError. Detta indikerar ett problem med Tjänsten StorSimple Device Manager.
6. Kontrollera Azure Service Bus-anslutningen. ErrorCode.CiSApplianceServiceBusError anger att enheten inte kan ansluta till servicebussen.

Loggfilerna CiSCommandletLog0Curr.errlog och CiSAgentsvc0Curr.errlog kommer att ha mer information, till exempel undantagsinformation.

Mer information om hur du använder cmdleten finns i [Test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) i windows PowerShell-referensdokumentationen.

> [!IMPORTANT]
> Du kan köra den här cmdleten för både den aktiva och den passiva styrenheten.

Se följande exempel på `Test-HcsmConnection` utdata från cmdleten.

**Exempelutdata – har registrerat enhet som kör StorSimple Update 3**

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

**Exempel på utdata – offlineenhet** 

Det här exemplet kommer från en enhet som har **statusen Offline** i Azure-portalen.

     Checking device registrationstate: Success
     Device is registered successfully
     Checking connectivity from device to SaaS.. Failure

Det gick inte att ansluta enheten med den aktuella webbproxykonfigurationen. Detta kan vara ett problem med webbproxykonfigurationen eller ett problem med nätverksanslutningen. I det här fallet bör du se till att webbproxyinställningarna är korrekta och att webbproxyservrarna är online och kan nås.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Felsöka med cmdleten Sync-HcsTime
Använd den här cmdleten för att visa enhetens tid. Om enhetstiden har en förskjutning med NTP-servern kan du sedan använda den här cmdleten för att tvinga synkronisera tiden med NTP-servern.
- Om förskjutningen mellan enheten och NTP-servern är större än 5 minuter visas en varning. 
- Om förskjutningen överstiger 15 minuter kopplas enheten från. Du kan fortfarande använda den här cmdleten för att tvinga fram en tidssynkronisering. 
- Men om förskjutningen överstiger 15 timmar, kommer du inte att kunna tvinga-synkronisera tiden och ett felmeddelande kommer att visas.

**Exempel på utdata – synkronisering av påtvingad tid med Sync-HcsTime**

     Controller0>Sync-HcsTime
     The current device time is 4/24/2015 4:05:40 PM UTC.

     Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
     [Y] Yes [N] No (Default is "Y"): Y
     Controller0>

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Felsöka med cmdletsen Enable-HcsPing och Disable-HcsPing
Använd dessa cmdlets för att säkerställa att nätverksgränssnitten på enheten svarar på ICMP-ping-begäranden. Som standard svarar inte StorSimple-nätverksgränssnitten på ping-begäranden. Att använda den här cmdleten är det enklaste sättet att veta om enheten är online och kan nås.

**Exempelutdata – Aktivera-HcsPing och inaktivera-HcsPing**

     Controller0>
     Controller0>Enable-HcsPing
     Successfully enabled ping.
     Controller0>
     Controller0>
     Controller0>Disable-HcsPing
     Successfully disabled ping.
     Controller0>

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Felsöka med Trace-HcsRoute cmdlet
Använd den här cmdleten som ett ruttspårningsverktyg. Den skickar paket till varje router på väg till ett slutmål under en tidsperiod och beräknar sedan resultat baserat på de paket som returneras från varje hopp. Eftersom cmdleten visar graden av paketförlust på en viss router eller länk kan du precisera vilka routrar eller länkar som kan orsaka nätverksproblem.

**Exempelutdata som visar hur du spårar flödet för ett paket med Trace-HcsRoute**

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
Använd den här cmdleten för att visa routningstabellen för StorSimple-enheten. En routningstabell är en uppsättning regler som kan hjälpa dig att avgöra var datapaket som reser över ett IP-nätverk (Internet Protocol) ska dirigeras.

Routningstabellen visar gränssnitten och gatewayen som dirigerar data till de angivna nätverken. Det ger också routningsmåttet som är beslutsfattaren för den väg som tas för att nå en viss destination. Ju lägre routningsmått, desto högre prioritet.

Om du till exempel har två nätverksgränssnitt, DATA 2 och DATA 3, anslutna till Internet. Om routningsmåtten för DATA 2 och DATA 3 är 15 respektive 261, är DATA 2 med det lägre routningsmåttet det gränssnitt som föredras för att nå Internet.

Om du kör Uppdatering 1 på Din StorSimple-enhet har ditt DATA 0-nätverksgränssnitt den högsta inställningen för molntrafiken. Detta innebär att även om det finns andra molnaktiverade gränssnitt, skulle molntrafiken dirigeras via DATA 0.

Om du `Get-HcsRoutingTable` kör cmdlet utan att ange några parametrar (som följande exempel visar), kommer cmdlet ut både IPv4 och IPv6 routning tabeller. Du kan också `Get-HcsRoutingTable -IPv4` ange `Get-HcsRoutingTable -IPv6` eller hämta en relevant routningstabell.

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

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Exempel på felsökning steg för steg
I följande exempel visas stegvis felsökning av en StorSimple-distribution. I exempelscenariot misslyckas enhetsregistreringen med ett felmeddelande om att nätverksinställningarna eller DNS-namnet är felaktiga.

Felmeddelandet som returneras är:

     Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
     +CategoryInfo: Not specified
     +FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand

Felet kan orsakas av något av följande:

* Felaktig maskinvaruinstallation
* Felaktiga nätverksgränssnitt
* Felaktig IP-adress, nätmask, gateway, primär DNS-server eller webbproxy
* Felaktig registreringsnyckel
* Felaktiga brandväggsinställningar

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Så här hittar och åtgärdar du enhetsregistreringsproblemet
1. Kontrollera enhetens konfiguration: kör `Invoke-HcsSetupWizard`den aktiva handkontrollen på den aktiva handkontrollen .
   
   > [!NOTE]
   > Installationsguiden måste köras på den aktiva styrenheten. Om du vill kontrollera att du är ansluten till den aktiva handkontrollen tittar du på banderollen som visas i seriekonsolen. Banderollen anger om du är ansluten till styrenheten 0 eller styrenhet 1 och om handkontrollen är aktiv eller passiv. Mer information finns i [Identifiera en aktiv handkontroll på enheten](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Kontrollera att enheten är korrekt kabelansluten: kontrollera nätverkskablarna på enhetens baksida. Kablaget är specifikt för enhetsmodellen. Mer information finns i [Installera din StorSimple 8100-enhet](storsimple-8100-hardware-installation.md) eller [Installera din StorSimple 8600-enhet](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Om du använder 10 GbE-nätverksportar måste du använda de medföljande QSFP-SFP-korten och SFP-kablarna. Mer information finns i [listan över kablar, växlar och sändtagare som rekommenderas för de 10 GbE-portarna](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Kontrollera nätverksgränssnittets hälsa:
   
   * Använd cmdleten Get-NetAdapter för att upptäcka hälsotillståndet för nätverksgränssnitten för DATA 0. 
   * Om länken inte fungerar anger **ifindex-statusen** att gränssnittet är nere. Du måste då kontrollera nätverksanslutningen av porten till apparaten och till växeln. Du måste också utesluta dåliga kablar. 
   * Om du misstänker att DATA 0-porten på den aktiva styrenheten har misslyckats kan du bekräfta detta genom att ansluta till DATA 0-porten på styrenhet 1. För att bekräfta detta kopplar du bort nätverkskabeln från enhetens baksida från styrenheten 0, ansluter kabeln till styrenheten 1 och kör sedan Get-NetAdapter-cmdleten igen.
     Om DATA 0-porten på en styrenhet misslyckas [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Du kan behöva byta ut handkontrollen på datorn.
4. Verifiera anslutningen till växeln:
   
   * Kontrollera att DATA 0-nätverksgränssnitt på styrenheten 0 och styrenheten 1 i din primära hölje finns i samma undernät. 
   * Kontrollera navet eller routern. Vanligtvis bör du ansluta båda styrenheterna till samma nav eller router. 
   * Se till att de växlar du använder för anslutningen har DATA 0 för båda styrenheterna i samma vLAN.
5. Eliminera eventuella användarfel:
   
   * Kör installationsguiden igen (kör **Invoke-HcsSetupWizard**) och ange värdena igen för att se till att det inte finns några fel. 
   * Kontrollera vilken registreringsnyckel som används. Samma registreringsnyckel kan användas för att ansluta flera enheter till en StorSimple Enhetshanteraren-tjänst. Använd proceduren i [Hämta tjänstregistreringsnyckeln](storsimple-8000-manage-service.md#get-the-service-registration-key) för att se till att du använder rätt registreringsnyckel.
     
     > [!IMPORTANT]
     > Om du har flera tjänster igång måste du se till att registreringsnyckeln för rätt tjänst används för att registrera enheten. Om du har registrerat en enhet med fel StorSimple Enhetshanteraren-tjänst måste du [kontakta Microsoft Support](storsimple-8000-contact-microsoft-support.md) för nästa steg. Du kan behöva utföra en fabriksåterställning av enheten (vilket kan resultera i dataförlust) för att sedan ansluta den till den avsedda tjänsten.
     > 
     > 
6. Använd cmdleten Test-Anslutning för att kontrollera att du har anslutning till det externa nätverket. Mer information finns i [Felsöka med cmdleten Test-Anslutning](#troubleshoot-with-the-test-connection-cmdlet).
7. Kontrollera om det finns brandväggsstörningar. Om du har verifierat att de virtuella IP-inställningarna (VIP), undernät, gateway och DNS är korrekta och du fortfarande ser anslutningsproblem, är det möjligt att brandväggen blockerar kommunikationen mellan enheten och det externa nätverket. Du måste se till att portarna 80 och 443 är tillgängliga på din StorSimple-enhet för utgående kommunikation. Mer information finns i [Nätverkskrav för Din StorSimple-enhet](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Titta på stockarna. Gå till [Supportpaket och enhetsloggar som är tillgängliga för felsökning](#support-packages-and-device-logs-available-for-troubleshooting).
9. Om föregående steg inte löser problemet [kontaktar du Microsoft Support](storsimple-8000-contact-microsoft-support.md) för att få hjälp.

## <a name="next-steps"></a>Nästa steg
[Lär dig hur du använder diagnostikverktyget för att felsöka en StorSimple-enhet](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
