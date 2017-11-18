---
title: "Lär dig mer om de senaste versionerna av Azure gäst-OS | Microsoft Docs"
description: "Den senaste versionen nyheterna och SDK-kompatibilitet för Azure Cloud Services Gästoperativsystem."
services: cloud-services
documentationcenter: na
author: raiye
manager: timlt
editor: 
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 11/16/2017
ms.author: raiye
ms.openlocfilehash: e19bb15be29fefbfbc94f7396bb2b68f8236f66a
ms.sourcegitcommit: a036a565bca3e47187eefcaf3cc54e3b5af5b369
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/17/2017
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure gäst-OS-versioner och SDK-kompatibilitetsmatris
Ger dig med uppdaterad information om den senaste Azure-Gästoperativsystem släpper för molntjänster. Den här informationen hjälper dig att planera din uppgradering innan ett gäst-OS är inaktiverad. Om du konfigurerar dina roller att använda *automatisk* Gästoperativsystem uppdateras enligt beskrivningen i [Azure gäst-OS uppdateringsinställningar][Azure Guest OS Update Settings], inte är det viktigt att du läser den här sidan.

> [!IMPORTANT]
> Den här sidan gäller molntjänster webb- och arbetsroller roller som körs ovanpå ett Gästoperativsystem. Det gör **inte** till IaaS-virtuella datorer.
>
>


> [!TIP]
>  Prenumerera på den [gäst OS uppdatera RSS-Feed] ska få tid meddelandet på alla ändringar som Gästoperativsystem.
>
>

> [!IMPORTANT]
> Från November driftsättningen blir endast under de senaste 2 versionerna av gäst-OS tillgängliga i Azure-portalen och som stöds.
>
>

Osäker på om vilka Gästoperativsystem är eller hur gäst-OS släpper arbete? Läs [detta](#how-it-works) avsnitt.

## <a name="news-updates"></a>Nyheter

###### <a name="november-8-2017"></a>**8 november 2017**
Oktober Gästoperativsystem har publicerat.

###### <a name="october-6-2017"></a>**6 oktober 2017**
September Gästoperativsystem har publicerat. Netfx3 är aktiverad som standard för Windows Server 2016 September-versionen. Kunder bör lägga till ' dism / online/Disable-Feature /featurename:netfx3 ”i sina OnStart om arbetsflödet kräver dem att köra en .NET 2.x-app med en 4.x runtime eller om de körde en .NET-2.x app hanteras ett fel och körde en 4.x .NET-app.

###### <a name="september-14-2017"></a>**14 september 2017**
September gäst-OS-distributionen startar 14 September och har en planerade versionen av den 9 oktober.

###### <a name="august-24-2017"></a>**24 augusti 2017**
Augusti Gästoperativsystem har publicerat.

###### <a name="august-3-2017"></a>**3 augusti 2017**
Juli Gästoperativsystem har publicerat.

###### <a name="july-19-2017"></a>**19 juli 2017**
Juli gäst-OS-distributionen startar juli 19 och har en planerade version av 8 augusti.

###### <a name="july-7-2017"></a>**7 juli 2017**
Juni Gästoperativsystem har publicerat.

###### <a name="june-16-2017"></a>**16 juni 2017**
Juni gäst-OS-distributionen startar 16 juni och har en planerade version av 11 juli.

###### <a name="june-5-2017"></a>**5 juni 2017**
Gästoperativsystem kan har publicerat.

###### <a name="may-17-2017"></a>**Den 17 maj 2017**
På grund av ett programfel för säkerhet kan vi inaktiverar följande December 2016 och januari 2017 OS-versioner som inte har den [åtgärda] från portalen: WA-GÄST-OS-5.4_201612-01, WA-GÄST-OS-4.39_201612-01, WA-GUEST-OS-3.46_201612-01, WA-GUEST-OS-2.59_201701-01


## <a name="releases"></a>Versioner
## <a name="family-5-releases"></a>Familj 5 versioner
**Windows Server 2016**

.NET framework har installerats: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2

> [!NOTE]
> Datum med ett * kan komma att ändras.
>
> RDP-lösenordet för OS-familjen 5 måste vara minst 10 tecken.
>

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum | Utgångna datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-5.12_201710-02 |8 november 2017 |Bokför 5.14 |TBD |
| WA-GUEST-OS-5.11_201709-01 |6 oktober 2017 |Post 5.13 |TBD |
| WA-GUEST-OS-5.10_201708-01 |24 augusti 2017 |Post 5.12 |TBD |
|~~WA-GUEST-OS-5.9_201707-01~~ |3 augusti 2017 |8 november 2017 |TBD |
|~~WA-GUEST-OS-5.8_201706-01~~ |7 juli 2017 |6 oktober 2017 |TBD |
|~~WA-GUEST-OS-5.7_201705-01~~ |5 juni 2017 |24 augusti 2017 |TBD |
|~~WA-GUEST-OS-5.6_201704-01~~ |9 kan 2017 |3 augusti 2017 |TBD |
|~~WA-GUEST-OS-5.5_201703-01~~ |10 april 2017 |7 juli 2017 |TBD |
|~~WA-GUEST-OS-5.4_201612-01~~ |10 januari 2017 |5 juni 2017|TBD |
|~~WA-GUEST-OS-5.3_201611-01~~ |14 december 2016 |9 kan 2017 |TBD |

## <a name="family-4-releases"></a>Familj 4 versioner
**Windows Server 2012 R2**

.NET framework har installerats: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Datum med ett * kan komma att ändras
>
>

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum | Utgångna datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-4.47_201710-02 |8 november 2017 |Bokför 4.49 |TBD |
| WA-GUEST-OS-4.46_201709-01 |6 oktober 2017 |Bokför 4.48 |TBD |
| WA-GUEST-OS-4.45_201708-01 |24 augusti 2017 |Bokför 4.47 |TBD |
|~~WA-GUEST-OS-4.44_201707-01~~ |3 augusti 2017 |8 november 2017 |TBD |
|~~WA-GUEST-OS-4.43_201706-01~~ |7 juli 2017 |6 oktober 2017 |TBD |
|~~WA-GUEST-OS-4.42_201705-01~~ |5 juni 2017 |24 augusti 2017 |TBD |
|~~WA-GUEST-OS-4.41_201704-01~~ |9 kan 2017 |3 augusti 2017 |TBD |
|~~WA-GUEST-OS-4.40_201703-01~~ |10 april 2017 |7 juli 2017 |TBD |
|~~WA-GUEST-OS-4.39_201612-01~~ |10 januari 2017 |5 juni 2017 |TBD |
|~~WA-GUEST-OS-4.38_201611-01~~ |14 december 2016 |9 kan 2017 |TBD |

## <a name="family-3-releases"></a>Familj 3-versioner
**Windows Server 2012**

.NET framework har installerats: 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Datum med ett * kan komma att ändras
>
>

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum | Utgångna datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-3.54_201710-02 |8 november 2017 |Post 3.56 |TBD |
| WA-GUEST-OS-3.53_201709-01 |6 oktober 2017 |Bokför 3.55 |TBD |
| WA-GUEST-OS-3.52_201708-01 |24 augusti 2017 |Bokför 3.54 |TBD |
|~~WA-GUEST-OS-3.51_201707-01~~ |3 augusti 2017 |8 november 2017 |TBD |
|~~WA-GUEST-OS-3.50_201706-01~~ |7 juli 2017 |6 oktober 2017 |TBD |
|~~WA-GUEST-OS-3.49_201705-01~~ |5 juni 2017 |24 augusti 2017 |TBD |
|~~WA-GUEST-OS-3.48_201704-01~~ |9 kan 2017 |3 augusti 2017 |TBD |
|~~WA-GUEST-OS-3.47_201703-01~~ |10 april 2017 |7 juli 2017 |TBD |
|~~WA-GUEST-OS-3.46_201612-01~~ |10 januari 2017 |5 juni 2017 |TBD |
|~~WA-GUEST-OS-3.45_201611-01~~ |14 december 2016 |9 kan 2017 |TBD |

## <a name="family-2-releases"></a>Familj 2 versioner
**Windows Server 2008 R2 SP1**

.NET framework har installerats: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

> [!NOTE]
> Datum med ett * kan komma att ändras
>
>

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum | Utgångna datum |
| --- | --- | --- | --- |
| WA-GUEST-OS-2.67_201710-02 |8 november 2017 |Post 2.69 |TBD |
| WA-GUEST-OS-2.66_201709-01 |6 oktober 2017 |Bokför 2.68 |TBD |
| WA-GUEST-OS-2.65_201708-01 |24 augusti 2017 |Bokför 2.67 |TBD |
|~~WA-GUEST-OS-2.64_201707-01~~ |3 augusti 2017 |8 november 2017 |TBD |
|~~WA-GUEST-OS-2.63_201706-01~~ |7 juli 2017 |6 oktober 2017 |TBD |
|~~WA-GUEST-OS-2.62_201705-01~~ |5 juni 2017 |24 augusti 2017 |TBD |
|~~WA-GUEST-OS-2.61_201704-01~~ |9 kan 2017 |3 augusti 2017 |TBD |
|~~WA-GUEST-OS-2.60_201703-01~~ |10 april 2017 |7 juli 2017 |TBD |
|~~WA-GUEST-OS-2.59_201701-01~~ |10 januari 2017 |5 juni 2017 |TBD |
|~~WA-GUEST-OS-2.58_201612-01~~ |10 januari 2017 |9 kan 2017|TBD |
|~~WA-GUEST-OS-2.57_201611-01~~ |14 december 2016 |10 april 2017 |TBD |


## <a name="msrc-patch-updates"></a>MSRC patch-uppdateringar
Lista över korrigeringsprogram som ingår i varje månad gäst-OS-version är tillgänglig [här][patches].

## <a name="sdk-support"></a>Stöd för SDK
Även om den [pensionering princip för Azure SDK] [ retire policy sdk] anger att endast versioner ovan 2.2 är stöds, specifik gäst-OS-familjer kan du använda tidigare versioner. Du bör alltid använda den senaste SDK som stöds.

| OS-Gästfamiljen | Kompatibel SDK-versioner |
| --- | --- |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1 + |
| 3 |Version 1.8 + |
| 2 |Version 1.3 + |
| 1 |Version 1.0 + |

## <a name="guest-os-release-information"></a>Information för gäst-OS-versionen
Det finns tre datum som är viktiga för gäst-OS-versioner: **släpper** datum, **inaktiverad** datum och **giltighetstid** datum. Ett gäst-OS anses vara tillgänglig när den är i portalen och kan väljas som mål Gästoperativsystem. När ett gäst-OS når den **inaktiverad** datum, den tas bort från Azure. Alla Molntjänsten som mål som Gästoperativsystem fortfarande fungerar dock som vanligt.

Fönstret mellan den **inaktiverad** datum och **giltighetstid** datum ger dig en buffert till enkelt övergång från ett Gästoperativsystem till en nyare. Om du använder *automatisk* som din gäst-OS du alltid att den senaste versionen och du inte behöver bry dig om det upphör att gälla.

När den **giltighetstid** datum kan en tjänst i molnet fortfarande använder den Gästoperativsystem som ska stoppas, ta bort eller tvingas att uppgradera. Du kan läsa mer om avyttring principen [här][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Förklaring för gäst-OS-familjen-Version
Gäst-OS-familjer baseras på versioner av Microsoft Windows Server. Gäst-OS är det underliggande operativsystemet som Azure Cloud Services körs på. Varje Gästoperativsystem har en familj, version och utgåva nummer.

* **Gäst-OS-familjen**  
  En Windows Server operating system-versionen som en gäst-OS baseras på. Till exempel *familj 3* baseras på Windows Server 2012.
* **Gäst-OS-version**  
  Specifika för gäst-OS-familjen avbildningar plus relevanta [Microsoft Security Response Center (MSRC)] [ msrc] korrigeringsprogram som är tillgängliga på det datum som produceras av den nya gäst-OS-versionen. Inte alla korrigeringsfiler kan ingå.

    Siffror som börjar på 0 och öka med 1 varje gång en ny uppsättning uppdateringar har lagts till. Efterföljande nollor visas bara om det är viktigt. Version 2.10 är en annan, mycket senare version än version 2.1.
* **Gäst-OS-versionen**  
  En ny utgåva av en gäst-OS-version. En ny utgåva uppstår om Microsoft hittar problem under testningen; kräver ändringar. Den senaste versionen alltid ersätter alla tidigare versioner, offentlig eller inte. Azure-portalen kan användaren att välja den senaste versionen för en viss version. Distributioner som körs på en tidigare version är vanligtvis inte kraft uppgraderas beroende på allvarlighetsgraden för felet.

2 är familjen i exemplet nedan och 12 är versionen ”rel2” är versionen.

**Gäst-OS-versionen** - 2,12 rel2

**Konfigurationssträngen för den här versionen** -WA-GUEST-OS-2.12_201208-02

Konfigurationssträngen för ett gäst-OS har samma information inbäddat i den, tillsammans med ett datum som visar vilka MSRC säkerhetskorrigeringar ansågs för uppdateringen. I det här exemplet ansågs MSRC korrigeringsfiler produceras för Windows Server 2008 R2 upp till och med augusti 2012 för införande. Endast de korrigeringar som gäller specifikt för den versionen av Windows Server ingår. Till exempel om en MSRC korrigeringsfilen avser Microsoft Office, tas den inte med eftersom produkten inte är en del av basavbildningen för Windows Server.

## <a name="guest-os-system-update-process"></a>Uppdateringsprocessen för gäst-OS-System
Den här sidan innehåller information om kommande versioner för gästoperativsystem. Kunder har meddelat att de vill veta när en Versionspost inträffar eftersom deras molntjänstroller startas om de har angetts till ”automatisk” update. Gäst-OS-versioner sker vanligtvis minst fem (5) dagar efter MSRC uppdatering som uppstår på den andra tisdagen varje månad. Nya versioner innehåller alla relevanta MSRC korrigeringar för varje gäst-OS-familjen.

Microsoft Azure lanserar ständigt uppdateringar. Gäst-OS är endast en sådan uppdatering i pipelinen. En version som kan påverkas av flera faktorer för många att lista här. Dessutom körs Azure på hundratals med tusentals datorer. Det innebär att det är omöjligt att ge ett exakt datum och tid när dina roller kommer att startas om. Vi arbetar på en plan för att begränsa eller tid omstarter.

När en ny version av gäst-OS publiceras kan ta det tid att fullständigt huvudkonfigurationsfil via Azure. När tjänster uppdateras till den nya gäst-OS, de startas om respektera update domäner. Tjänster som anges för att använda ”automatisk” uppdateringar får en Versionspost första. Efter uppdateringen måste se du den nya gäst-OS-version som anges för tjänsten i Azure-portalen. Här återutgivningarna kan uppstå under denna tid. Vissa versioner kan distribueras under längre tid och automatisk uppgradering omstarter inträffar för många veckor efter den officiella versionen. När ett gäst-OS är tillgänglig, kan du sedan väljer den här versionen från portalen eller i konfigurationsfilen.

En massa värdefull information på startas om och länkar till mer information teknisk information om uppdateringar med gästen och värd-OS, finns i MSDN-blogg post med titeln [roll-instansen startas om på grund av OS-uppgraderingar] [ restarts].

Om du manuellt uppdatera gäst-OS finns det [Gästoperativsystem pensionering princip] [ retirepolicy] för ytterligare information.

## <a name="guest-os-supportability-and-retirement-policy"></a>Support för gäst-OS och tillbakadragning princip
Principen för Gästoperativsystem support och tillbakadragning förklaras [här][retirepolicy].

[gäst OS uppdatera RSS-Feed]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/en-us/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: http://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: http://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: http://azure.microsoft.com/support/options/
[net install pkg]: http://www.microsoft.com/download/details.aspx?id=42643
[msrc]: http://www.microsoft.com/security/msrc/default.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[åtgärda]: https://technet.microsoft.com/en-us/library/security/ms17-010.aspx
