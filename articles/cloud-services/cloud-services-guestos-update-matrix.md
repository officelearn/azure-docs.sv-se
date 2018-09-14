---
title: Lär dig mer om de senaste Azure-Gästoperativsystemversioner | Microsoft Docs
description: Den senaste versionen nyheterna och SDK-kompatibilitet för Gästoperativsystemet för Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: RichardScheel
manager: timlt
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 9/12/2018
ms.author: RichardScheel
ms.openlocfilehash: 8d49742e90e09a8836309db165b07074645df00a
ms.sourcegitcommit: e2ea404126bdd990570b4417794d63367a417856
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 09/14/2018
ms.locfileid: "45576178"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure gäst-OS-versioner och SDK-kompatibilitetsöversikten
Ger dig med uppdaterad information om den senaste Azure-Gästoperativsystem släpper för molntjänster. Den här informationen hjälper dig att planera din uppgraderingsväg innan en gäst-OS är inaktiverad. Om du konfigurerar dina roller att använda *automatisk* gäst-OS uppdateras enligt beskrivningen i [Azure Guest OS uppdateringsinställningar][Azure Guest OS Update Settings], inte är det viktigt att du läser den här sidan.

> [!IMPORTANT]
> Den här sidan gäller för Cloud Services webb- och arbetsroller roller som körs ovanpå ett Gästoperativsystem. Den gör **gäller inte** till IaaS-datorer.
>
>


> [!TIP]
>  Prenumerera på den [Gäst-OS-uppdateringen RSS-Feed] ska få mest aktuella meddelandet på alla gästoperativsystemändringar.
>
>

> [!IMPORTANT]
> Endast de senaste 2 versionerna av gäst-OS kommer stöds som är tillgänglig i Azure-portalen.
>
>

Osäker på om hur du uppdaterar din gäst-OS? Kontrollera [detta] [ cloud updates] ut.

## <a name="news-updates"></a>Nyheter

###### <a name="september-12-2018"></a>**Den 12 september 2018**
Gäst-OS augusti har publicerat.

###### <a name="august-3-2018"></a>**Den 3 augusti 2018**
Gäst-OS juli har publicerat.

###### <a name="july-3-2018"></a>**3 juli 2018**
Gäst-OS juni har publicerat.

###### <a name="june-1-2018"></a>**Den 1 juni 2018**
Gästoperativsystemet kan har publicerat.

###### <a name="may-4-2018"></a>**Den 4 maj 2018**
Gäst-OS April har publicerat.

###### <a name="april-6-2018"></a>**6 april 2018**
Gäst-OS mars har publicerat.

###### <a name="march-19-2018"></a>**19 mars 2018**
Gäst-OS februari har publicerat.

###### <a name="january-29-2018"></a>**Den 29 januari 2018**
Gäst-OS januari har släppts för OS-familjer 2 (WA-GÄST-OS-2.70_201801-01) & 3 (WA-GUEST-OS-3.57_201801-01)

###### <a name="january-4-2018"></a>**Den 4 januari 2018**
Gäst-OS januari har släppts för OS-familjer 4 (WA-GÄST-OS-4.50_201801-01) & 5 (WA-GUEST-OS-5.15_201801-01) och innehåller viktiga säkerhetsuppdateringar.  

###### <a name="january-4-2018"></a>**Den 4 januari 2018**
December Gästoperativsystem har publicerat.

###### <a name="december-14-2017"></a>**14 december 2017**
November Gästoperativsystem har publicerat.

###### <a name="november-8-2017"></a>**8 november 2017**
Oktober Gästoperativsystem har publicerat.



## <a name="releases"></a>Versioner
## <a name="family-5-releases"></a>Familj 5 versioner
**Windows Server 2016**

.NET framework installerat: 4.0, 4.5, 4.5.1, 4.5.2, 4.6, 4.6.1, 4.6.2 eller senare

> [!NOTE]
> RDP-lösenord för OS-familj 5 måste vara minst 10 tecken.
>

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-5.22_201808-01 |Den 12 september 2018 |Post 5,24 |
| WA-GUEST-OS-5.21_201807-02 |Den 3 augusti 2018 |Post 5.23 |
|~~WA-GUEST-OS-5.20_201806-01~~ |3 juli 2018 |Den 12 september 2018 |
|~~WA-GUEST-OS-5.19_201805-01~~ |Den 1 juni 2018 |Den 3 augusti 2018 |
|~~WA-GUEST-OS-5.18_201804-01~~ |Den 4 maj 2018 |3 juli 2018 |
|~~WA-GUEST-OS-5.17_201803-01~~ |6 april 2018 |Den 1 juni 2018|
|~~WA-GUEST-OS-5.16_201802-01~~ |Den 12 mars 2018 |Den 4 maj 2018 |
|~~WA-GUEST-OS-5.15_201801-01~~ |Den 4 januari 2018 |6 april 2018 |
|~~WA-GUEST-OS-5.14_201712-01~~ |Den 4 januari 2018 |Den 12 mars 2018 |
|~~WA-GUEST-OS-5.13_201711-01~~ |14 december 2017 |Den 4 januari 2018|
|~~WA-GUEST-OS-5.12_201710-02~~ |8 november 2017 |Den 4 januari 2018 |


## <a name="family-4-releases"></a>Gästoperativsystemfamilj 4 versioner
**Windows Server 2012 R2**

.NET framework installerat: 4.0, 4.5, 4.5.1, 4.5.2

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-4.57_201808-01 |Den 12 september 2018 |Post 4.59 |
| WA-GUEST-OS-4.56_201807-02 |Den 3 augusti 2018 |Post 4.58 |
|~~WA-GUEST-OS-4.55_201806-01~~ |3 juli 2018 |Den 12 september 2018 |
|~~WA-GUEST-OS-4.54_201805-01~~ |Den 1 juni 2018 |Den 3 augusti 2018 |
|~~WA-GUEST-OS-4.53_201804-01~~ |Den 4 maj 2018 |3 juli 2018 |
|~~WA-GUEST-OS-4.52_201803-01~~ |6 april 2018 |Den 1 juni 2018 |
|~~WA-GUEST-OS-4.51_201802-01~~ |Den 12 mars 2018 |Den 4 maj 2018 |
|~~WA-GUEST-OS-4.50_201801-01~~ |Den 4 januari 2018 |6 april 2018 |
|~~WA-GUEST-OS-4.49_201712-01~~ |Den 4 januari 2018 |Den 12 mars 2018 |
|~~WA-GUEST-OS-4.48_201711-01~~ |14 december 2017 |Den 4 januari 2018 |
|~~WA-GUEST-OS-4.47_201710-02~~ |8 november 2017 |Den 4 januari 2018 |


## <a name="family-3-releases"></a>Familj 3 versioner
**Windows Server 2012**

.NET framework installerat: 4.0, 4.5, 4.5.1, 4.5.2

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-3.64_201808-01 |Den 12 september 2018 |Post 3.66 |
| WA-GUEST-OS-3.63_201807-02 |Den 3 augusti 2018 |Post 3.65 |
|~~WA-GUEST-OS-3.62_201806-01~~ |3 juli 2018 |Den 12 september 2018 |
|~~WA-GUEST-OS-3.61_201805-01~~ |Den 1 juni 2018 |Den 3 augusti 2018 |
|~~WA-GUEST-OS-3.60_201804-01~~ |Den 4 maj 2018 |3 juli 2018 |
|~~WA-GUEST-OS-3.59_201803-01~~ |6 april 2018 |Den 1 juni 2018 |
|~~WA-GUEST-OS-3.58_201802-01~~ |19 mars 2018 |Den 4 maj 2018 |
|~~WA-GUEST-OS-3.57_201801-01~~ |Den 29 januari 2018 |6 april 2018 |
|~~WA-GUEST-OS-3.56_201712-01~~ |Den 4 januari 2018 |19 mars 2018 |
|~~WA-GUEST-OS-3.55_201711-01~~ |14 december 2017 |Den 29 januari 2018 |
|~~WA-GUEST-OS-3.54_201710-02~~ |8 november 2017 |Den 4 januari 2018 |


## <a name="family-2-releases"></a>Familj 2 versioner
**Windows Server 2008 R2 SP1**

.NET framework installerat: 3.5, 4.0, 4.5, 4.5.1, 4.5.2

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-2.77_201808-01 |Den 12 september 2018 |Post 2.79 |
| WA-GUEST-OS-2.76_201807-02 |Den 3 augusti 2018 |Post 2.78 |
|~~WA-GUEST-OS-2.75_201806-01~~ |3 juli 2018 |Den 12 september 2018 |
|~~WA-GUEST-OS-2.74_201805-01~~ |Den 1 juni 2018 |Den 3 augusti 2018|
|~~WA-GUEST-OS-2.73_201804-01~~ |Den 4 maj 2018 |3 juli 2018 |
|~~WA-GUEST-OS-2.72_201803-01~~ |6 april 2018 |Den 1 juni 2018 |
|~~WA-GUEST-OS-2.71_201802-01~~ |Den 12 mars 2018 |Den 4 maj 2018 |
|~~WA-GUEST-OS-2.70_201801-01~~ |Den 29 januari 2018 |6 april 2018 |
|~~WA-GUEST-OS-2.69_201712-01~~ |Den 4 januari 2018 |Den 12 mars 2018 |
|~~WA-GUEST-OS-2.68_201711-01~~ |14 december 2017 |Den 29 januari 2018 |
|~~WA-GUEST-OS-2.67_201710-02~~ |8 november 2017 |Den 4 januari 2018 |
|~~WA-GUEST-OS-2.66_201709-01~~ |6 oktober 2017 |14 december 2017 |
|~~WA-GUEST-OS-2.65_201708-01~~ |24 augusti 2017 |14 december 2017 |


## <a name="msrc-patch-updates"></a>MSRC patch-uppdateringar
Lista över korrigeringsprogram som ingår med varje månatlig gäst-OS-version är tillgänglig [här][patches].

## <a name="sdk-support"></a>SDK-support
Även om den [indragningsprincip för Azure SDK] [ retire policy sdk] indikerar att det bara versioner ovan 2.2 stöds, specifika gästoperativsystemfamiljer kan du använda tidigare versioner. Du bör alltid använda den senaste SDK som stöds.

| OS-Gästfamiljen | Kompatibla SDK-versioner |
| --- | --- |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1+ |
| 3 |Version 1.8 + |
| 2 |Version 1.3 + |
| 1 |Version 1.0 + |

## <a name="guest-os-release-information"></a>Information för gäst-OS-version
Det finns tre datum som är viktiga för gäst-OS-versioner: **viktig** datum, **inaktiverad** datum, och **upphör att gälla** datum. Ett Gästoperativsystem betraktas som tillgänglig när den är i portalen och kan väljas som mål gäst-OS. När ett Gästoperativsystem når den **inaktiverad** datum, tas den bort från Azure. Dock valfri molntjänst som riktar in sig på att Gästoperativsystemet kommer fortfarande att fungera som vanligt.

Fönstret mellan den **inaktiverad** datum och **upphör att gälla** datum innehåller en buffert till enkelt övergång från ett Gästoperativsystem till en nyare. Om du använder *automatisk* som gäst-OS, kommer du alltid att den senaste versionen och du behöver inte bekymra dig om det upphör att gälla.

När den **upphör att gälla** datum kan valfri molntjänst fortfarande använder den gäst-OS ska stoppas, tas bort, eller tvingas att uppgradera. Du kan läsa mer om principen för tillbakadragande [här][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Förklaring för gäst-OS-familjen-Version
Gäst-OS-familjer baseras på Microsoft Windows Server-versioner. Gäst-OS är det underliggande operativsystemet som Azure Cloud Services körs på. Varje Gästoperativsystem har en familj, version och version tal.

* **Gästoperativsystemfamilj**  
  En Windows Server version av operativsystemet som en gäst-OS baseras på. Till exempel *familj 3* baseras på Windows Server 2012.
* **Gäst-OS-version**  
  Specifika för gäst-OS-familjen avbildningar plus relevanta [Microsoft Security Response Center (MSRC)] [ msrc] korrigeringsprogram som är tillgängliga på det datum då den nya gäst-OS-versionen skapas. Inte alla korrigeringsfiler kan ingå.

    Siffror som börjar på 0 och öka med 1 varje gång en ny uppsättning uppdateringar har lagts till. Efterföljande nollor visas endast om det är viktigt. Version 2.10 är en annan, mycket senare version än version 2.1.
* **Gäst-OS-version**  
  En ny utgåva av en gäst-OS-version. En ny utgåva inträffar om Microsoft hittar problem under testningen; kräver att ändringar. Den senaste versionen alltid ersätter alla tidigare versioner, offentlig eller inte. Azure-portalen kan användaren att välja den senaste versionen för en viss version. Distributioner som körs på en tidigare version är vanligtvis inte kraft uppgraderas beroende på allvarlighetsgraden för buggen.

I exemplet nedan 2 är familjen, 12 är versionen och ”rel2” är versionen.

**Gäst-OS-versionen** – 2,12 rel2

**Konfigurationssträngen för den här versionen** -WA-GUEST-OS-2.12_201208-02

Konfigurationssträngen för ett Gästoperativsystem har samma information inbäddat i sig, tillsammans med ett datum som visar vilka MSRC-korrigeringar ansågs för uppdateringen. I det här exemplet ansågs MSRC korrigeringar produceras för Windows Server 2008 R2 upp till och med augusti 2012 ska ingå. Endast de korrigeringar som gäller specifikt för den versionen av Windows Server ingår. Till exempel om en MSRC korrigeringsfilen avser Microsoft Office, tas den inte med eftersom den här produkten inte är en del av Windows Server-basavbildningen.

## <a name="guest-os-system-update-process"></a>Uppdateringsprocessen för gäst-OS-System
Den här sidan innehåller information om kommande versioner för gästoperativsystem. Kunder har angett att de vill veta när en version beror på att deras molntjänstroller startas om de är inställd på ”automatisk” uppdatering. Gäst-OS-versioner inträffar normalt 2 – 3 veckor efter MSRC uppdatering som körs på den andra tisdagen varje månad. Nya versioner innehåller alla relevanta MSRC-korrigeringar för varje gästoperativsystemfamilj.

Microsoft Azure lanserar ständigt uppdateringar. Gäst-OS är bara en sådan uppdatering i pipelinen. En version som kan påverkas av många faktorer för många för att visa här. Dessutom körs Azure på hundratals med tusentals datorer. Det innebär att det är omöjligt att ge ett exakt datum och tid när dina roller startas om. Vi arbetar på en plan för att begränsa eller tid omstarter.

När en ny version av gäst-OS publiceras, kan det ta tid att sprida helt i Azure. När tjänster uppdateras till den nya gäst-OS, de startas om respektera uppdateringsdomäner. Tjänster som är konfigurerad att använda ”automatisk” uppdateringar kommer att få en version första. Efter uppdateringen visas den nya gäst-OS-versionen för din tjänst i Azure-portalen. Här återutgivningarna kan uppstå under denna period. Vissa versioner kan distribueras under längre tidsperioder och automatisk uppgradering omstarter kan inte ha många veckor efter officiella lanseringsdatumet. När en gäst-OS är tillgänglig, kan du sedan väljer den här versionen från portalen eller i konfigurationsfilen.

Mycket värdefull information på startas om och länkar till mer information teknisk information om uppdateringar av Gäst och värd-OS, finns i MSDN-blogg som heter efter [rollen instansen startas om på grund av OS-uppgraderingar] [ restarts].

Om du manuellt uppdaterar gäst-OS, finns i den [gäst-OS indragningsprincip] [ retirepolicy] för ytterligare information.

## <a name="guest-os-supportability-and-retirement-policy"></a>Support för gäst-OS och Indragningsprincip
Principen för gäst-OS support och tillbakadragande förklaras [här][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Gäst-OS-uppdateringen RSS-Feed]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
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
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
