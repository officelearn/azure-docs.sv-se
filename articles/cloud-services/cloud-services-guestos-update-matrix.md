---
title: Lär dig mer om de senaste Azure-Gästoperativsystemversioner | Microsoft Docs
description: Den senaste versionen nyheterna och SDK-kompatibilitet för Gästoperativsystemet för Azure Cloud Services.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2019
ms.author: raiye
ms.openlocfilehash: 955ba0aae41d7e555c1248cf04073b1b6e3f2d4f
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/23/2019
ms.locfileid: "60622974"
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

###### <a name="march-26-2019"></a>**26 mars 2019**
Gäst-OS mars har publicerat.

###### <a name="march-12-2019"></a>**Den 12 mars 2019**
Gäst-OS februari har publicerat.

###### <a name="february-5-2019"></a>**Den 5 februari 2019**
Gäst-OS januari har publicerat.

###### <a name="january-24-2019"></a>**24 januari 2019**
Familj 6 gäst-OS (Windows Server 2019) har publicerat.

###### <a name="january-7-2019"></a>**7 januari 2019**
Gäst-OS December har publicerat.

###### <a name="december-14-2018"></a>**December 14 maj 2018**
Gäst-OS November har publicerat.

###### <a name="november-8-2018"></a>**8 november 2018**
Gäst-OS oktober har publicerat.

###### <a name="october-12-2018"></a>**12 oktober 2018**
Gästoperativsystemet i September har publicerat.

## <a name="releases"></a>Versioner

## <a name="family-6-releases"></a>Familj 6 versioner
**Windows Server 2019**

.NET framework installerat: 3.5, 4.7.2

> [!NOTE]
> Windows Azure SDK för .NET - 3.0 kan laddas ned [här][Windows Azure SDK].
>
>Installationssteg:
>1. Avinstallera alla äldre versioner av MicrosoftAzureAuthoringTools*.msi
>2. Installera den [Azure SDK för .NET - 3.0][Windows Azure SDK]
>3. Starta om datorn
>4. Skapa ett nytt Cloud Service-projekt och Lägg till en enda Worker-roll
>5. Ändra OS-familj på 6 och skapa ett paket
>6. Distribuera paketet till Azure med hjälp av Azure portal eller Visual Studio
>


| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-6.5_201903-01 |26 mars 2019 |Post 6.7 |
| WA-GUEST-OS-6.4_201902-01 |Den 12 mars 2019 |Post 6.6 |
|~~WA-GUEST-OS-6.3_201901-01~~ |Den 5 februari 2019 |26 mars 2019 |
|~~WA-GUEST-OS-6.2_201812-01~~ |24 januari 2019 |Den 12 mars 2019 |
|~~WA-GUEST-OS-6.1_201811-01~~ |24 januari 2019 |Den 5 februari 2019 |

## <a name="family-5-releases"></a>Familj 5 versioner
**Windows Server 2016**

.NET framework installerat: 3.5, 4.6.2

> [!NOTE]
> RDP-lösenord för OS-familj 5 måste vara minst 10 tecken.
>


| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-5.29_201903-01 |26 mars 2019 |Post 5.31 |
| WA-GUEST-OS-5.28_201902-01 |Den 12 mars 2019 |Post 5.30 |
|~~WA-GUEST-OS-5.27_201901-01~~ |Den 5 februari 2019 |26 mars 2019 |
|~~WA-GUEST-OS-5.26_201812-01~~ |7 januari 2019 |Den 12 mars 2019 |
|~~WA-GUEST-OS-5.25_201811-01~~ |December 14 maj 2018 |Den 5 februari 2019 |
|~~WA-GUEST-OS-5.24_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-5.23_201809-01~~ |12 oktober 2018 |December 14 maj 2018 |

## <a name="family-4-releases"></a>Gästoperativsystemfamilj 4 versioner
**Windows Server 2012 R2**

.NET framework installerat: 3.5, 4.5.1

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-4.64_201903-01 |26 mars 2019 |Post 4,66 |
| WA-GUEST-OS-4.63_201902-01 |Den 12 mars 2019 |Post 4.65 |
|~~WA-GUEST-OS-4.62_201901-01~~ |Den 5 februari 2019 |26 mars 2019 |
|~~WA-GUEST-OS-4.61_201812-01~~ |7 januari 2019 |Den 12 mars 2019 |
|~~WA-GUEST-OS-4.60_201811-01~~ |December 14 maj 2018 |Den 5 februari 2019 |
|~~WA-GUEST-OS-4.59_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-4.58_201809-01~~ |12 oktober 2018 |December 14 maj 2018 |

## <a name="family-3-releases"></a>Familj 3 versioner
**Windows Server 2012**

.NET framework installerat: 3.5, 4.5

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-3.71_201903-01 |26 mars 2019 |Post 3,73 |
| WA-GUEST-OS-3.70_201902-01 |Den 12 mars 2019 |Post 3,72 |
|~~WA-GUEST-OS-3.69_201901-01~~ |Den 5 februari 2019 |26 mars 2019 |
|~~WA-GUEST-OS-3.68_201812-01~~ |7 januari 2019 |Den 12 mars 2019 |
|~~WA-GUEST-OS-3.67_201811-01~~ |December 14 maj 2018 |Den 5 februari 2019 |
|~~WA-GUEST-OS-3.66_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-3.65_201809-01~~ |12 oktober 2018 |December 14 maj 2018 |

## <a name="family-2-releases"></a>Familj 2 versioner
**Windows Server 2008 R2 SP1**

.NET framework installerat: 3.5 (inklusive.NET 2.0 och 3.0)

| Konfigurationssträngen | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
| WA-GUEST-OS-2.84_201903-01 |26 mars 2019 |Post 2.86 |
| WA-GUEST-OS-2.83_201902-01 |Den 12 mars 2019 |Post 2,85 |
|~~WA-GUEST-OS-2.82_201901-01~~ |Den 5 februari 2019 |26 mars 2019 |
|~~WA-GUEST-OS-2.81_201812-01~~ |7 januari 2019 |Den 12 mars 2019 |
|~~WA-GUEST-OS-2.80_201811-01~~ |December 14 maj 2018 |Den 5 februari 2019 |
|~~WA-GUEST-OS-2.79_201810-01~~ |8 november 2018 |7 januari 2019 |
|~~WA-GUEST-OS-2.78_201809-01~~ |12 oktober 2018 |December 14 maj 2018 |

## <a name="msrc-patch-updates"></a>MSRC patch-uppdateringar
Lista över korrigeringsprogram som ingår med varje månatlig gäst-OS-version är tillgänglig [här][patches].

## <a name="sdk-support"></a>SDK-support
Även om den [indragningsprincip för Azure SDK] [ retire policy sdk] indikerar att det bara versioner ovan 2.2 stöds, specifika gästoperativsystemfamiljer kan du använda tidigare versioner. Du bör alltid använda den senaste SDK som stöds.

| Gästoperativsystemfamilj | Kompatibla SDK-versioner |
| --- | --- |
| 6 |Version 2.9.6+ |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1+ |
| 3 |Version 1.8+ |
| 2 |Version 1.3+ |
| 1 |Version 1.0+ |

## <a name="guest-os-release-information"></a>Versionsinformation för gäst-OS
Det finns tre datum som är viktiga för gäst-OS-versioner: **viktig** datum, **inaktiverad** datum, och **upphör att gälla** datum. Ett Gästoperativsystem betraktas som tillgänglig när den är i portalen och kan väljas som mål gäst-OS. När ett Gästoperativsystem når den **inaktiverad** datum, tas den bort från Azure. Dock valfri molntjänst som riktar in sig på att Gästoperativsystemet kommer fortfarande att fungera som vanligt.

Fönstret mellan den **inaktiverad** datum och **upphör att gälla** datum innehåller en buffert till enkelt övergång från ett Gästoperativsystem till en nyare. Om du använder *automatisk* som gäst-OS, kommer du alltid att den senaste versionen och du behöver inte bekymra dig om det upphör att gälla.

När den **upphör att gälla** datum kan valfri molntjänst fortfarande använder den gäst-OS ska stoppas, tas bort, eller tvingas att uppgradera. Du kan läsa mer om principen för tillbakadragande [här][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Förklaring av gäst-OS-familjen-version
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

## <a name="guest-os-system-update-process"></a>Uppdateringsprocessen för gäst-OS-system
Den här sidan innehåller information om kommande versioner för gästoperativsystem. Kunder har angett att de vill veta när en version beror på att deras molntjänstroller startas om de är inställd på ”automatisk” uppdatering. Gäst-OS-versioner inträffar normalt 2 – 3 veckor efter MSRC uppdatering som körs på den andra tisdagen varje månad. Nya versioner innehåller alla relevanta MSRC-korrigeringar för varje gästoperativsystemfamilj.

Microsoft Azure lanserar ständigt uppdateringar. Gäst-OS är bara en sådan uppdatering i pipelinen. En version som kan påverkas av många faktorer för många för att visa här. Dessutom körs Azure på hundratals med tusentals datorer. Det innebär att det är omöjligt att ge ett exakt datum och tid när dina roller startas om. Vi arbetar på en plan för att begränsa eller tid omstarter.

När en ny version av gäst-OS publiceras, kan det ta tid att sprida helt i Azure. När tjänster uppdateras till den nya gäst-OS, de startas om respektera uppdateringsdomäner. Tjänster som är konfigurerad att använda ”automatisk” uppdateringar kommer att få en version första. Efter uppdateringen visas den nya gäst-OS-versionen för din tjänst i Azure-portalen. Här återutgivningarna kan uppstå under denna period. Vissa versioner kan distribueras under längre tidsperioder och automatisk uppgradering omstarter kan inte ha många veckor efter officiella lanseringsdatumet. När en gäst-OS är tillgänglig, kan du sedan väljer den här versionen från portalen eller i konfigurationsfilen.

Mycket värdefull information på startas om och länkar till mer information teknisk information om uppdateringar av Gäst och värd-OS, finns i MSDN-blogg som heter efter [rollen instansen startas om på grund av OS-uppgraderingar] [ restarts].

Om du manuellt uppdaterar gäst-OS, finns i den [gäst-OS indragningsprincip] [ retirepolicy] för ytterligare information.

## <a name="guest-os-supportability-and-retirement-policy"></a>Princip för support och tillbakadragande av gäst-OS
Principen för gäst-OS support och tillbakadragande förklaras [här][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[Gäst-OS-uppdateringen RSS-Feed]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
[Install .NET on a Cloud Service Role]: https://azure.microsoft.com/documentation/articles/cloud-services-dotnet-install-dotnet/?WT.mc_id=azurebg_email_Trans_963_RevisedNET_Update
[Azure Guest OS Update Settings]: cloud-services-how-to-configure-portal.md
[ssl3 announcement]: https://azure.microsoft.com/blog/2014/12/09/azure-security-ssl-3-0-update/
[Microsoft Security Advisory 3009008]: https://technet.microsoft.com/library/security/3009008.aspx
[ssl3-fixit]: https://go.microsoft.com/?linkid=9863266
[MS14-066]: https://technet.microsoft.com/library/security/ms14-066.aspx
[MS14-046]: https://technet.microsoft.com/library/security/ms14-046.aspx
[retire policy sdk]: https://msdn.microsoft.com/library/dn479282.aspx
[server and gos]: https://msdn.microsoft.com/library/dn775043.aspx
[azuresupport]: https://azure.microsoft.com/support/options/
[net install pkg]: https://www.microsoft.com/download/details.aspx?id=42643
[msrc]: https://technet.microsoft.com/security/dn440717.aspx
[update guest os portal]: https://msdn.microsoft.com/library/gg433101.aspx
[update guest os svc]: https://msdn.microsoft.com/library/gg456324.aspx
[restarts]: https://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx
[patches]: cloud-services-guestos-msrc-releases.md
[retirepolicy]: cloud-services-guestos-retirement-policy.md
[fam1retire]: cloud-services-guestos-family1-retirement.md
[fix]: https://technet.microsoft.com/library/security/ms17-010.aspx
[Windows Azure SDK]: https://www.microsoft.com/en-us/download/details.aspx?id=54917
