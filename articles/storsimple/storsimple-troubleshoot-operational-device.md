---
title: Felsöka en distribuerad StorSimple-enhet | Microsoft Docs
description: Beskriver hur du diagnostiserar och korrigerar fel som uppstår på en StorSimple-enhet som för närvarande har distribuerats och fungerar.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 8ad3f09bf46caf426b2008b583ebd2ff78522462
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60302525"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Felsöka en operational StorSimple-enhet
> [!NOTE]
> Den klassiska portalen för StorSimple är inaktuell. Dina StorSimple-enhetshanterare flyttas automatiskt till nya Azure Portal enligt utfasningsschemat. Du kommer att få ett e-postmeddelande och ett portalmeddelande om flytten. Det här dokumentet kommer också att dras tillbaka snart. Läs några frågor om flytten [vanliga frågor och svar: Flytta till Azure portal](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Översikt
Den här artikeln innehåller användbar felsökningsinformation för att lösa konfigurationsproblem som du kan stöta på när din StorSimple-enhet har distribuerats och fungerar. Den beskriver vanliga problem, möjliga orsaker och rekommenderade åtgärder för att hjälpa dig att lösa problem som kan uppstå när du kör Microsoft Azure StorSimple. Den här informationen gäller för både den lokala fysiska StorSimple-enheten och den virtuella StorSimple-enheten.

I slutet av den här artikeln som du hittar en lista över felkoder som kan uppstå under Microsoft Azure StorSimple samt steg kan du vidta för att åtgärda felen. 

## <a name="setup-wizard-process-for-operational-devices"></a>Installationsguiden för enheter
Du använder installationsguiden ([Invoke-HcsSetupWizard][1]) att kontrollera enhetskonfigurationen och vidta åtgärder om det behövs.

När du kör guiden på en tidigare konfigurerad och fungerar enhet skiljer sig processflödet. Du kan ändra följande poster:

* IP-adress, nätmask och gateway
* Primär DNS-server
* Primär NTP-server
* Valfritt webbproxykonfigurationen

Installationsguiden utför åtgärder som rör registrering av lösenord insamling och enheten.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Fel som uppstår under efterföljande körningar av installationsguiden
I följande tabell beskrivs de fel som du kan stöta på när du kör guiden på en operational enhet, möjliga orsaker till felen och rekommenderade åtgärder du kan åtgärda detta. 

| Nej. | Felmeddelande eller ett villkor | Möjliga orsaker | Rekommenderad åtgärd |
|:--- |:--- |:--- |:--- |
| 1 |Fel 350032: Enheten har redan inaktiverats. |Det här felet visas om du kör guiden på en enhet som har inaktiverats. |[Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) om du vill ha hjälp. En inaktiverad enhet kan inte placeras i tjänsten. En fabriksåterställning kan krävas innan enheten kan aktiveras igen. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (undantag från HRESULT: 0x80070001) |DNS-serveruppdateringen misslyckas. DNS-inställningarna är globala inställningar som används för alla aktiverade nätverksgränssnitt. |Aktivera gränssnittet och tillämpa DNS-inställningarna igen. Nätverk för andra aktiverade gränssnitt kan det störa eftersom de här inställningarna är globala. |
| 3 |Enheten verkar vara online i StorSimple Manager-tjänstportalen, men när du försöker spara konfigurationen och slutföra minsta installationen misslyckas. |Under installationen, har webbproxy inte konfigurerats, även om det uppstod ett faktiska proxyserver på plats. |Använd den [cmdlet Test-HcsmConnection] [ 2] att hitta felet. [Kontakta Microsoft Support](storsimple-contact-microsoft-support.md) om det inte går att åtgärda problemet. |
| 4 |Invoke-HcsSetupWizard: Värdet faller inte inom det förväntade intervallet. |En felaktig nätmask genererar det här felet. Möjliga orsaker är: <ul><li> Nätmask är tom eller saknas.</li><li>Format för Ipv6-prefix är felaktigt.</li><li>Gränssnittet är moln-aktiverat, men gatewayen är saknas eller är felaktig.</li></ul>Observera att DATA 0 är automatiskt moln-aktiverat om konfigureras via installationsguiden. |Använd undernät 0.0.0.0 eller 256.256.256.256 för att fastställa problemet och titta sedan på utdata. Ange rätt värden för nätmask, gatewayen och Ipv6-prefix, efter behov. |

## <a name="error-codes"></a>Felkoder
Fel visas i numerisk ordning.

| Felnummer | Feltext eller beskrivning | Rekommenderade användaråtgärder |
|:--- |:--- |:--- |
| 10502 |Ett fel uppstod vid åtkomst till ditt lagringskonto. |Vänta några minuter och försök sedan igen. Om felet kvarstår kan du kontakta Microsoft Support angående nästa steg. |
| 40017 |Säkerhetskopieringen misslyckades eftersom en volym som angetts i principen för säkerhetskopiering inte hittades på enheten. |Försök att säkerhetskopiera igen, om felet kvarstår, kontakta Microsoft Support. Nästa steg. |
| 40018 |Säkerhetskopieringen misslyckades eftersom ingen av de volymer som angetts i principen för säkerhetskopiering hittades på enheten. |Försök att säkerhetskopiera igen, om felet kvarstår, kontakta Microsoft Support. Nästa steg. |
| 390061 |Systemet är upptagen eller otillgänglig. |Vänta några minuter och försök sedan igen. Om felet kvarstår kan du kontakta Microsoft Support angående nästa steg. |
| 390143 |Ett fel har uppstått med felkoden 390143. (Okänt fel.) |Om felet kvarstår, kontakta Microsoft Support angående nästa steg. |

## <a name="next-steps"></a>Nästa steg
Om du inte kan lösa problemet, [kontakta Microsoft Support](storsimple-contact-microsoft-support.md) för att få hjälp. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
