---
title: Felsöka en distribuerad StorSimple-enhet | Microsoft-dokument
description: Beskriver hur du diagnostiserar och åtgärdar fel som uppstår på en StorSimple-enhet som för närvarande distribueras och fungerar.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933301"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Felsöka en fungerande StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Om du har frågor om flytten kan du läsa [Vanliga frågor och svar: Flytta till Azure Portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsvägledning för att lösa konfigurationsproblem som kan uppstå när Din StorSimple-enhet har distribuerats och fungerar. Den beskriver vanliga problem, möjliga orsaker och rekommenderade steg som hjälper dig att lösa problem som kan uppstå när du kör Microsoft Azure StorSimple. Den här informationen gäller både den lokala storsimpleenheten och den virtuella StorSimple-enheten.

I slutet av den här artikeln hittar du en lista över felkoder som kan uppstå under Microsoft Azure StorSimple-åtgärden, samt åtgärder som du kan vidta för att lösa felen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Installationsprogrammets guideprocess för driftenheter
Du använder installationsguiden ([Invoke-HcsSetupWizard][1]) för att kontrollera enhetens konfiguration och vidta korrigerande åtgärder om det behövs.

När du kör installationsguiden på en tidigare konfigurerad och operativ enhet är processflödet annorlunda. Du kan bara ändra följande poster:

* IP-adress, nätmask och gateway
* Primär DNS-server
* Primär NTP-server
* Valfri webbproxykonfiguration

Installationsguiden utför inte de åtgärder som är relaterade till lösenordsinsamling och enhetsregistrering.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fel som uppstår under efterföljande körningar av installationsguiden
I följande tabell beskrivs de fel som kan uppstå när du kör installationsguiden på en fungerande enhet, möjliga orsaker till felen och rekommenderade åtgärder för att lösa dem. 

| Nej. | Felmeddelande eller villkor | Möjliga orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1 |Fel 350032: Den här enheten har redan inaktiverats. |Det här felet visas om du kör installationsguiden på en enhet som är inaktiverad. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) för nästa steg. Det går inte att ta drift en avaktiverad enhet. En fabriksåterställning kan krävas innan enheten kan aktiveras igen. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Undantag från HRESULT: 0x80070001) |DNS-serveruppdateringen misslyckas. DNS-inställningar är globala inställningar och tillämpas i alla aktiverade nätverksgränssnitt. |Aktivera gränssnittet och tillämpa DNS-inställningarna igen. Detta kan störa nätverket för andra aktiverade gränssnitt eftersom dessa inställningar är globala. |
| 3 |Enheten verkar vara online i StorSimple Manager-tjänstportalen, men när du försöker slutföra den minsta installationen och spara konfigurationen misslyckas åtgärden. |Under den första installationen konfigurerades inte webbproxynen, även om det fanns en faktisk proxyserver på plats. |Använd [cmdleten Test-HcsmConnection][2] för att hitta felet. [Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) om du inte kan åtgärda problemet. |
| 4 |Invoke-HcsSetupWizard: Värdet faller inte inom det förväntade intervallet. |En felaktig nätmask ger det här felet. Möjliga orsaker är: <ul><li> Nätmasken saknas eller är tom.</li><li>Prefixformatet Ipv6 är felaktigt.</li><li>Gränssnittet är molnaktiverat, men gatewayen saknas eller är felaktig.</li></ul>Observera att DATA 0 automatiskt är molnaktiverat om det konfigureras via installationsguiden. |Om du vill ta reda på problemet använder du undernät 0.0.0.0 eller 256.256.256.256 och tittar sedan på utdata. Ange korrekta värden för prefixet undernätsmask, gateway och Ipv6 efter behov. |

## <a name="error-codes"></a>Felkoder
Fel visas i numerisk ordning.

| Felnummer | Feltext eller beskrivning | Rekommenderad användaråtgärd |
|:--- |:--- |:--- |
| 10502 |Ett fel uppstod när du kom åt ditt lagringskonto. |Vänta några minuter och försök sedan igen. Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |
| 40017 |Säkerhetskopieringen misslyckades eftersom en volym som angavs i säkerhetskopieringsprincipen inte hittades på enheten. |Försök med säkerhetskopiering, om felet kvarstår, kontakta Microsoft Support. för nästa steg. |
| 40018 |Säkerhetskopieringen misslyckades eftersom ingen av de volymer som angavs i säkerhetskopieringsprincipen hittades på enheten. |Försök med säkerhetskopiering, om felet kvarstår, kontakta Microsoft Support. för nästa steg. |
| 390061 |Systemet är upptaget eller inte tillgängligt. |Vänta några minuter och försök sedan igen. Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |
| 390143 |Ett fel uppstod med felkod 390143. (Okänt fel.) |Om felet kvarstår kontaktar du Microsoft Support för nästa steg. |

## <a name="next-steps"></a>Nästa steg
Om du inte kan lösa problemet [kontaktar du Microsoft Support](storsimple-contact-microsoft-support.md) för att få hjälp. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
