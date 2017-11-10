---
title: "Skapa och återställa en säkerhetskopia i BizTalk-tjänst | Microsoft Docs"
description: "BizTalk-tjänster omfattar säkerhetskopiering och återställning. Lär dig hur du skapar och återställer en säkerhetskopia och Bestäm vad säkerhetskopieras. MABS WABS"
services: biztalk-services
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: 59f91173-4683-48df-abd5-41262bfce6df
ms.service: biztalk-services
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2016
ms.author: mandia
ms.openlocfilehash: 45365092f5bcd1a8d309c10404a7437c494a8967
ms.sourcegitcommit: dcf5f175454a5a6a26965482965ae1f2bf6dca0a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/10/2017
---
# <a name="biztalk-services-backup-and-restore"></a>BizTalk Services: Säkerhetskopiering och återställning

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Azure BizTalk-tjänster inkluderar funktioner för säkerhetskopiering och återställning. 

> [!INCLUDE [Use APIs to manage MABS](../../includes/biztalk-services-retirement-azure-classic-portal.md)]

> [!NOTE]
> Hybridanslutningar säkerhetskopieras inte, oavsett vilken version. Du måste återskapa din hybridanslutningar.


## <a name="before-you-begin"></a>Innan du börjar
* Säkerhetskopiering och återställning kanske inte tillgänglig för alla versioner. Se [BizTalk-tjänst: utgåvor diagram](biztalk-editions-feature-chart.md).
* Säkerhetskopiering innehåll kan återställas till samma BizTalk Service eller till en ny BizTalk Service. Om du vill återställa den BizTalk Service med samma namn, befintlig BizTalk Service måste tas bort och namnet måste vara tillgänglig. Det kan ta längre tid än vill om samma namn ska vara tillgängliga när du har tagit bort en BizTalk Service. Om du inte kan vänta på samma namn ska vara tillgängliga, återställer du sedan till ett nytt BizTalk-Service.
* BizTalk-tjänst kan återställas till samma version eller en senare version. Återställa BizTalk-tjänst till en lägre version stöds från när säkerhetskopian skapades, inte.
  
    Exempelvis kan du återställa en säkerhetskopia med Basic-versionen till Premium Edition. En säkerhetskopiering med Premium Edition kan inte återställas till Standard Edition.
* EDI-kontrollen siffror säkerhetskopieras upprätthålla kontinuitet av. Meddelanden som bearbetas efter den senaste säkerhetskopieringen, kan återställa säkerhetskopiering innehållet orsaka dubbla kontrollen siffror.
* Om en batch har aktiva meddelanden, bearbetar batchen **innan** köra en säkerhetskopiering. När du skapar en säkerhetskopia (som krävs eller är schemalagda) lagras aldrig meddelanden i batchar. 
  
    **Om en säkerhetskopia görs med aktiva meddelanden i en grupp, säkerhetskopieras inte dessa meddelanden och därför går förlorade.**
* Valfritt: I BizTalk-Services-portalen stoppa några hanteringsåtgärder.

## <a name="create-a-backup"></a>Skapa en säkerhetskopia
En säkerhetskopia kan vidtas när som helst och är helt styrs av du. Så här skapar du en säkerhetskopia av [REST API för hantering av BizTalk-tjänst på Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

## <a name="restore"></a>Återställ
Använd för att återställa en säkerhetskopia av [REST API för hantering av BizTalk-tjänst på Azure](https://msdn.microsoft.com/library/azure/dn232347.aspx).

### <a name="postrestore"></a>När du återställer en säkerhetskopia
BizTalk Service återställs alltid i en **pausad** tillstånd. I det här tillståndet kan du göra några konfigurationsändringar innan den nya miljön är fungerar, inklusive:

* Om du har skapat BizTalk Service program med Azure BizTalk Services SDK kan behöva du uppdatera autentiseringsuppgifterna med dessa program ska fungera med den återställda miljön Access Control (ACS).
* Du kan återställa en BizTalk Service för att replikera en befintlig BizTalk Service-miljö. I det här fallet om det finns avtal som konfigurerats i den ursprungliga BizTalk-Services-portalen och som använder en källmapp av FTP-kan du behöva uppdatera avtalen i den nyligen återställda miljön att använda en annan källa FTP-mapp. Annars kan finnas det två olika avtal som försöker hämta samma meddelande.
* Om du har återställt för miljöer med flera BizTalk Service, kontrollera att du inrikta dig på rätt miljön i Visual Studio-program, PowerShell-cmdlets, REST API: er eller handel Partner hantering OM API: er.
* Det är en bra idé att konfigurera automatisk säkerhetskopiering på den nyligen återställda BizTalk Service-miljön.

## <a name="what-gets-backed-up"></a>Vad säkerhetskopieras
När en säkerhetskopia skapas följande objekt som ska säkerhetskopieras:

<table border="1"> 
<tr bgcolor="FAF9F9">
<th> </th>
<TH>Objekt som har säkerhetskopierats</TH> 
</tr> 
<tr>
<td colspan="2">
 <strong>Azure BizTalk-Services-portalen</strong></td>
</tr> 
<tr>
<td>Konfiguration och körning</td> 
<td>
<ul>
<li>Information för partner och profil</li>
<li>Partner avtal</li>
<li>Anpassade sammansättningar som har distribuerats</li>
<li>Bryggor distribueras</li>
<li>Certifikat</li>
<li>Transformeringar distribueras</li>
<li>Pipelines</li>
<li>Mallar som har skapats och sparats i BizTalk-Services-portalen</li>
<li>X12 ST01 och GS01 mappningar</li>
<li>Kontrollen siffror (EDI)</li>
<li>AS2 meddelandet MIC värden</li>
</ul>
</td>
</tr> 

<tr>
<td colspan="2">
 <strong>Azure BizTalk-tjänst</strong></td>
</tr> 
<tr>
<td>SSL-certifikat</td> 
<td>
<ul>
<li>Data för SSL-certifikat</li>
<li>Lösenordet för SSL-certifikat</li>
</ul>
</td>
</tr> 
<tr>
<td>BizTalk-tjänst-inställningar</td> 
<td>
<ul>
<li>Skalningsantalet för enhet</li>
<li>Utgåva</li>
<li>Produktversion</li>
<li>Region/Datacenter</li>
<li>Access Control Service (ACS) namnområde och nyckel</li>
<li>Spåra databasanslutningssträng</li>
<li>Arkivering lagringsanslutningssträng för kontot</li>
<li>Övervaka lagringsanslutningssträng för kontot</li>
</ul>
</td>
</tr> 
<tr>
<td colspan="2">
 <strong>Ytterligare objekt</strong></td>
</tr> 
<tr>
<td>Spårning av databasen</td> 
<td>När BizTalk Service skapas anges spårning databasinformationen, inklusive Azure SQL-databasservern och spåra databasnamnet. Spårning av databasen säkerhetskopieras automatiskt inte.
<br/><br/>
<strong>Viktigt</strong><br/>
Om spårning av databasen tas bort och databas måste återställas, måste det finnas en tidigare säkerhetskopia. Om en säkerhetskopiering inte finns, är inte spårning-databasen och dess data återställas. I så fall kan du skapa en ny spårning databas med samma databasnamn. GEO-replikering rekommenderas.</td>
</tr> 
</table>

## <a name="next"></a>Nästa
Om du vill skapa Azure BizTalk-tjänst, gå till [BizTalk-tjänst: etablering](http://go.microsoft.com/fwlink/p/?LinkID=302280). Om du vill börja skapa program går du till [Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="see-also"></a>Se även
* [Säkerhetskopiera BizTalk-tjänst](http://go.microsoft.com/fwlink/p/?LinkID=325584)
* [Återställa BizTalk-tjänst från en säkerhetskopia](http://go.microsoft.com/fwlink/p/?LinkID=325582)
* [BizTalk-tjänst: Utvecklare, Basic, Standard och Premium-utgåvor diagram](http://go.microsoft.com/fwlink/p/?LinkID=302279)
* [BizTalk-tjänst: etablering](http://go.microsoft.com/fwlink/p/?LinkID=302280)
* [BizTalk Services: Etablering av statusdiagram](http://go.microsoft.com/fwlink/p/?LinkID=329870)
* [BizTalk Services: Flikarna Instrumentpanel, Övervakare och Skalning](http://go.microsoft.com/fwlink/p/?LinkID=302281)
* [BizTalk Services: Begränsning](http://go.microsoft.com/fwlink/p/?LinkID=302282)
* [BizTalk Services: Utfärdarens namn och nyckel](http://go.microsoft.com/fwlink/p/?LinkID=303941)
* [Hur gör jag för att börja använda Azure BizTalk Services SDK?](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[BackupStatus]: ./media/biztalk-backup-restore/status-last-backup.png
[Restore]: ./media/biztalk-backup-restore/restore-ui.png
[AutomaticBU]: ./media/biztalk-backup-restore/AutomaticBU.png
[RestoreBizTalkService]: ./media/biztalk-backup-restore/RestoreBizTalkServiceWindow.png

