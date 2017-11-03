---
title: "Felsöka en distribuerad virtuell StorSimple-enhet | Microsoft Docs"
description: "Beskriver hur du diagnostisera och åtgärda fel som uppstår på en StorSimple-enhet som är för tillfället distribuerade och fungerar."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 05/16/2016
ms.author: v-sharos
ms.openlocfilehash: 8d1b4905d0a24c8df9eb2c986459286909fd20dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 10/11/2017
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Felsöka en operativa StorSimple-enhet
## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsinformation för att lösa konfigurationsproblem som kan uppstå när din StorSimple-enhet är distribuerade och fungerar. Här beskrivs vanliga problem, möjliga orsaker och rekommenderade steg för att hjälpa dig att lösa problem som kan uppstå när du kör Microsoft Azure StorSimple. Den här informationen gäller både den lokala fysiska StorSimple-enheten och den virtuella StorSimple-enheten.

I slutet av den här artikeln som du hittar en lista över felkoder som kan uppstå under Microsoft Azure StorSimple åtgärden samt steg kan du vidta för att åtgärda felen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Installationsguiden för enheter
Du kan använda installationsguiden ([Invoke-HcsSetupWizard][1]) att kontrollera enhetskonfigurationen och vidta åtgärder om det behövs.

När du kör guiden på en tidigare konfigurerad och fungerar enhet är processflödet olika. Du kan ändra följande uppgifter:

* IP-adress, nätmask och gateway
* Primär DNS-server
* Primär NTP-server
* Valfritt webbproxykonfigurationen

Installationsguiden inte att utföra de åtgärder som rör insamling och enheter för registrering av lösenord.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fel som uppstår under efterföljande körningar av installationsguiden
I följande tabell beskrivs de fel som du kan stöta på när du kör guiden på en operativa enhet, möjliga orsaker till fel och rekommenderade åtgärder kan lösas. 

| Nej. | Felmeddelande eller villkor | Möjliga orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1 |Fel 350032: Den här enheten har redan inaktiverats. |Det här felet visas om du kör guiden på en enhet som har inaktiverats. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. En inaktiverad enhet kan inte placeras i tjänsten. En fabriksåterställning kan krävas innan enheten kan aktiveras igen. |
| 2 |Anropa HcsSetupWizard: ERROR_INVALID_FUNCTION (undantag från HRESULT: 0x80070001) |DNS-serveruppdateringen misslyckas. DNS-inställningarna är globala inställningar som tillämpas på alla aktiverade nätverksgränssnitt. |Aktivera gränssnittet och använda DNS-inställningarna igen. Eftersom de här inställningarna är globala kan det störa nätverket för andra aktiverade gränssnitt. |
| 3 |Enheten verkar vara online i StorSimple Manager-tjänstportalen, men när du försöker slutföra minsta installationen och spara konfigurationen misslyckas åtgärden. |Under installationen, har webbproxy inte konfigurerats, även om det har en verklig proxyserver. |Använd den [Test-HcsmConnection cmdlet] [ 2] att hitta felet. [Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) om det inte går att åtgärda problemet. |
| 4 |Anropa HcsSetupWizard: Värdet hamnar inte inom det förväntade intervallet. |En felaktig nätmask genererar det här felet. Möjliga orsaker är: <ul><li> Nätmask är tomt eller saknas.</li><li>Format för Ipv6-prefix är felaktigt.</li><li>Gränssnittet är moln-aktiverat, men gatewayen är saknas eller är felaktig.</li></ul>Observera att DATA 0 är automatiskt moln-aktiverat om konfigureras via guiden. |Använda undernätet 0.0.0.0 eller 256.256.256.256 för att fastställa problemet och sedan tittar på utdata. Ange rätt värden för nätmask, gateway och Ipv6-prefix efter behov. |

## <a name="error-codes"></a>Felkoder
Fel visas i numerisk ordning.

| Felnummer | Feltext eller beskrivning | Rekommenderade användaråtgärd |
|:--- |:--- |:--- |
| 10502 |Ett fel uppstod vid åtkomst till ditt lagringskonto. |Vänta några minuter och försök sedan igen. Om problemet kvarstår bör du kontakta Microsoft Support för nästa steg. |
| 40017 |Säkerhetskopieringen misslyckades eftersom en volym som angetts i principen för säkerhetskopiering inte hittades på enheten. |Försök att säkerhetskopiera igen om problemet kvarstår kontaktar du Microsoft Support. Nästa steg. |
| 40018 |Säkerhetskopieringen misslyckades eftersom ingen av de volymer som angetts i principen för säkerhetskopiering hittades på enheten. |Försök att säkerhetskopiera igen om problemet kvarstår kontaktar du Microsoft Support. Nästa steg. |
| 390061 |Systemet är upptaget eller inte tillgänglig. |Vänta några minuter och försök sedan igen. Om problemet kvarstår bör du kontakta Microsoft Support för nästa steg. |
| 390143 |Ett fel har uppstått med felkoden 390143. (Okänt fel). |Om felet kvarstår, kontakta Microsoft Support för nästa steg. |

## <a name="next-steps"></a>Nästa steg
Om det inte går att lösa problemet och [kontaktar Microsoft Support](storsimple-contact-microsoft-support.md) om du behöver hjälp. 

[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
