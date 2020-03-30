---
title: Läs mer om de senaste Azure Guest OS-versionerna | Microsoft-dokument
description: De senaste nyheterna och SDK-kompatibiliteten för Azure Cloud Services Guest OS.
services: cloud-services
documentationcenter: na
author: raiye
editor: ''
ms.assetid: 6306cafe-1153-44c7-8554-623b03d59a34
ms.service: cloud-services
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: tbd
ms.date: 3/26/2020
ms.author: raiye
ms.openlocfilehash: f265faef4e6520f5c74fce9a13b3d81118707025
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371129"
---
# <a name="azure-guest-os-releases-and-sdk-compatibility-matrix"></a>Azure Guest OS-versioner och SDK-kompatibilitetsmatris
Ger dig aktuell information om de senaste Azure Guest OS-versionerna för Molntjänster. Den här informationen hjälper dig att planera uppgraderingsvägen innan ett gästoperativsystem inaktiveras. Om du konfigurerar dina roller för att använda *automatiska* gästoperativsystemuppdateringar enligt beskrivningen i [Azure Guest OS Update Settings][Azure Guest OS Update Settings]är det inte viktigt att du läser den här sidan.

> [!IMPORTANT]
> Den här sidan gäller webb- och arbetarroller för Molntjänster som körs ovanpå ett gästoperativsystem. Det **gäller inte** för IaaS virtuella datorer.
>
>


> [!TIP]
>  Prenumerera på [RSS-feed för gäst-OS-uppdatering] för att få den senaste informationen om alla ändringar i gästoperativsystemet.
>
>

> [!IMPORTANT]
> Endast de senaste 2 versionerna av gästoperativsystemet stöds och är tillgängliga i Azure-portalen.
>
>

Osäker på hur du uppdaterar ditt gäst-OS? Kolla in [det här.][cloud updates]

## <a name="news-updates"></a>Nyhetsuppdateringar

###### <a name="march-5-2020"></a>**Den 5 mars 2020**
Februari Guest OS har släppt. 

###### <a name="january-24-2020"></a>**den 24 januari 2020**
Januari Guest OS har släppts. 

###### <a name="january-8-2020"></a>**den 8 januari 2020**
December Guest OS har släppt.

###### <a name="december-5-2019"></a>**den 5 december 2019**
November Guest OS har släppt.

###### <a name="november-1-2019"></a>**Den 1 november 2019**
Oktober Guest OS har släppt.

###### <a name="october-7-2019"></a>**den 7 oktober 2019**
September Guest OS har släppt.

###### <a name="september-4-2019"></a>**den 4 september 2019**
August Guest OS har släppts.

###### <a name="july-26-2019"></a>**den 26 juli 2019**
Juli Guest OS har släppt.

###### <a name="july-8-2019"></a>**den 8 juli 2019**
June Guest OS har släppts.

###### <a name="june-6-2019"></a>**den 6 juni 2019**
May Guest OS har släppts.

###### <a name="may-7-2019"></a>**den 7 maj 2019**
April Guest OS har släppt.

###### <a name="march-26-2019"></a>**den 26 mars 2019**
March Guest OS har släppts.

###### <a name="march-12-2019"></a>**den 12 mars 2019**
Februari Guest OS har släppt.

###### <a name="february-5-2019"></a>**den 5 februari 2019**
Januari Guest OS har släppts.

###### <a name="january-24-2019"></a>**den 24 januari 2019**
Family 6 Guest OS (Windows Server 2019) har släppts.

###### <a name="january-7-2019"></a>**den 7 januari 2019**
December Guest OS har släppt.

###### <a name="december-14-2018"></a>**den 14 december 2018**
November Guest OS har släppt.

###### <a name="november-8-2018"></a>**8 november 2018**
Oktober Guest OS har släppt.

###### <a name="october-12-2018"></a>**den 12 oktober 2018**
September Guest OS har släppt.

## <a name="releases"></a>Versioner

## <a name="family-6-releases"></a>Familj 6 utgåvor
**Windows Server 2019**

.NET Framework installerat: 3.5, 4.7.2

> [!NOTE]
> Windows Azure SDK för .NET - 3.0 kan hämtas [här][Windows Azure SDK].
>
>Installationssteg:
>1. Avinstallera alla äldre versioner av MicrosoftAzureAuthoringTools*.msi
>2. Installera [Azure SDK för .NET - 3.0][Windows Azure SDK]
>3. Starta om datorn
>4. Skapa ett nytt molntjänstprojekt och lägg till en enda arbetarroll
>5. Ändra OS-familjen till 6 och skapa ett paket
>6. Distribuera paketet till Azure med Azure-portalen eller Visual Studio
>
>Gäst OS Family 6-versionen tillämpar TLS 1.2 genom att uttryckligen inaktivera TLS 1.0 och 1.1 och definiera en specifik uppsättning chiffersviter. Läs [mer].


| Konfigurationssträng | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
|  WA-GÄST-OS-6.16_202002-01  |  Den 5 mars 2020  |  Inlägg 6,18  |  
|  WA-GÄST-OS-6.15_202001-01  |  den 24 januari 2020  |  Inlägg 6,17  |  
|~~WA-GÄST-OS-6.14_201912-01~~| den 8 januari 2020 | Den 5 mars 2020 |  
|~~WA-GÄST-OS-6.13_201911-01~~| den 5 december 2019 | den 24 januari 2020 |  
|~~WA-GÄST-OS-6.12_201910-01~~| Den 1 november 2019 | den 8 januari 2020 |  
|~~WA-GÄST-OS-6.11_201909-01~~| den 7 oktober 2019 | den 5 december 2019 |  
|~~WA-GÄST-OS-6.10_201908-01~~| den 4 augusti 2019 | Den 1 november 2019  |  
|~~WA-GÄST-OS-6.9_201907-0~~|den 26 juli 2019 | den 7 oktober 2019 |
|~~WA-GÄST-OS-6.8_201906-01~~|den 8 juli 2019 |den 4 augusti 2019 |
|~~WA-GÄST-OS-6.7_201905-01~~ |den 6 juni 2019 |den 26 juli 2019 |
|~~WA-GÄST-OS-6.6_201904-01~~ |den 7 maj 2019 |den 8 juli 2019 |
|~~WA-GÄST-OS-6.5_201903-01~~ |den 26 mars 2019 |den 6 juni 2019 |
|~~WA-GÄST-OS-6.4_201902-01~~ |den 12 mars 2019 |den 7 maj 2019 |
|~~WA-GÄST-OS-6.3_201901-01~~ |den 5 februari 2019 |den 26 mars 2019 |
|~~WA-GÄST-OS-6.2_201812-01~~ |den 24 januari 2019 |den 12 mars 2019 |
|~~WA-GÄST-OS-6.1_201811-01~~ |den 24 januari 2019 |den 5 februari 2019 |

## <a name="family-5-releases"></a>Familj 5 utgåvor
**Windows Server 2016**

.NET Framework installerat: 3.5, 4.6

> [!NOTE]
> RDP-lösenordet för OS-familjen 5 måste vara minst 10 tecken.
>


| Konfigurationssträng | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
|  WA-GÄST-OS-5.40_202002-01  |  Den 5 mars 2020  |  Inlägg 5,42  |  
|  WA-GÄST-OS-5.39_202001-01  |  den 24 januari 2020  |  Inlägg 5,41  |  
|~~WA-GÄST-OS-5.38_201912-01~~| den 8 januari 2020 | Den 5 mars 2020 |  
|~~WA-GÄST-OS-5.37_201911-01~~| den 5 december 2019 | den 24 januari 2020 |  
|~~WA-GÄST-OS-5.36_201910-01~~| Den 1 november 2019 | den 8 januari 2020 |  
|~~WA-GÄST-OS-5.35_201909-01~~| den 7 oktober 2019 | den 5 december 2019 |  
|~~WA-GÄST-OS-5.34_201908-01~~|  den 4 augusti 2019  | Den 1 november 2019 |  
|~~WA-GÄST-OS-5.33_201907-01~~| den 26 juli 2019 | den 7 oktober 2019 |  
|~~WA-GÄST-OS-5.32_201906-01~~|den 8 juli 2019 |den 4 augusti 2019 |
|~~WA-GÄST-OS-5.31_201905-01~~ |den 6 juni 2019 |den 26 juli 2019 |
|~~WA-GÄST-OS-5.30_201904-01~~ |den 7 maj 2019 |den 8 juli 2019 |
|~~WA-GÄST-OS-5.29_201903-01~~ |den 26 mars 2019 |den 6 juni 2019 |
|~~WA-GÄST-OS-5.28_201902-01~~ |den 12 mars 2019 |den 7 maj 2019 |
|~~WA-GÄST-OS-5.27_201901-01~~ |den 5 februari 2019 |den 26 mars 2019 |
|~~WA-GÄST-OS-5.26_201812-01~~ |den 7 januari 2019 |den 12 mars 2019 |
|~~WA-GÄST-OS-5.25_201811-01~~ |den 14 december 2018 |den 5 februari 2019 |
|~~WA-GÄST-OS-5.24_201810-01~~ |8 november 2018 |den 7 januari 2019 |
|~~WA-GÄST-OS-5.23_201809-01~~ |den 12 oktober 2018 |den 14 december 2018 |

## <a name="family-4-releases"></a>Familj 4 utgåvor
**Windows Server 2012 R2**

.NET Framework installerat: 3.5, 4.5.1, 4.5.2

| Konfigurationssträng | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
|  WA-GÄST-OS-4.75_202002-01  |  Den 5 mars 2020  |  Inlägg 4,77  |  
|  WA-GÄST-OS-4.74_202001-01  |  den 24 januari 2020  |  Inlägg 4,76  |  
|~~WA-GÄST-OS-4.73_201912-01~~| den 8 januari 2020 | Den 5 mars 2020 |  
|~~WA-GÄST-OS-4.72_201911-01~~| den 5 december 2019 | den 24 januari 2020 |  
|~~WA-GÄST-OS-4.71_201910-01~~| Den 1 november 2019 | den 8 januari 2020 |  
|~~WA-GÄST-OS-4.70_201909-01~~| den 7 oktober 2019 | den 5 december 2019 |  
|~~WA-GÄST-OS-4.69_201908-01~~| den 4 augusti 2019 | Den 1 november 2019 |  
|~~WA-GÄST-OS-4.68_201907-01~~| den 26 juli 2019  | den 7 oktober 2019 |
|~~WA-GÄST-OS-4.67_201906-01~~| den 8 juli 2019 |den 4 augusti 2019 |
|~~WA-GÄST-OS-4.66_201905-01~~ |den 6 juni 2019 |den 26 juli 2019 |
|~~WA-GÄST-OS-4.65_201904-01~~ |den 7 maj 2019 |den 8 juli 2019 |
|~~WA-GÄST-OS-4.64_201903-01~~ |den 26 mars 2019 |den 6 juni 2019 |
|~~WA-GÄST-OS-4.63_201902-01~~ |den 12 mars 2019 |den 7 maj 2019 |
|~~WA-GÄST-OS-4.62_201901-01~~ |den 5 februari 2019 |den 26 mars 2019 |
|~~WA-GÄST-OS-4.61_201812-01~~ |den 7 januari 2019 |den 12 mars 2019 |
|~~WA-GÄST-OS-4.60_201811-01~~ |den 14 december 2018 |den 5 februari 2019 |
|~~WA-GÄST-OS-4.59_201810-01~~ |8 november 2018 |den 7 januari 2019 |
|~~WA-GÄST-OS-4.58_201809-01~~ |den 12 oktober 2018 |den 14 december 2018 |

## <a name="family-3-releases"></a>Familj 3 utgåvor
**Windows Server 2012**

.NET Framework installerat: 3.5, 4.5

| Konfigurationssträng | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
|  WA-GÄST-OS-3.82_202002-01  |  Den 5 mars 2020  |  Inlägg 3,84  |  
|  WA-GÄST-OS-3.81_202001-01  |  den 24 januari 2020  |  Inlägg 3,83  |  
|~~WA-GÄST-OS-3.80_201912-01~~| den 8 januari 2020 | Den 5 mars 2020 |  
|~~WA-GÄST-OS-3.79_201911-01~~| den 5 december 2019 | den 24 januari 2020 |  
|~~WA-GÄST-OS-3.78_201910-01~~| Den 1 november 2019 | den 8 januari 2020 |  
|~~WA-GÄST-OS-3.77_201909-01~~| den 7 oktober 2019 | den 5 december 2019 |  
|~~WA-GÄST-OS-3.76_201908-01~~|  den 4 augusti 2019  |  Den 1 november 2019  |  
|~~WA-GÄST-OS-3.75_201907-01~~| den 26 juli 2019 | den 7 oktober 2019 |
|~~WA-GÄST-OS-3.74_201906-01~~| den 8 juli 2019 |den 4 augusti 2019 |
|~~WA-GÄST-OS-3.73_201905-01~~ |den 6 juni 2019 |den 26 juli 2019 |
|~~WA-GÄST-OS-3.72_201904-01~~ |den 7 maj 2019 |den 8 juli 2019 |
|~~WA-GÄST-OS-3.71_201903-01~~ |den 26 mars 2019 |den 6 juni 2019 |
|~~WA-GÄST-OS-3.70_201902-01~~ |den 12 mars 2019 |den 7 maj 2019 |
|~~WA-GÄST-OS-3.69_201901-01~~ |den 5 februari 2019 |den 26 mars 2019 |
|~~WA-GÄST-OS-3.68_201812-01~~ |den 7 januari 2019 |den 12 mars 2019 |
|~~WA-GÄST-OS-3.67_201811-01~~ |den 14 december 2018 |den 5 februari 2019 |
|~~WA-GÄST-OS-3.66_201810-01~~ |8 november 2018 |den 7 januari 2019 |
|~~WA-GÄST-OS-3.65_201809-01~~ |den 12 oktober 2018 |den 14 december 2018 |

## <a name="family-2-releases"></a>Familj 2 utgåvor
**Windows Server 2008 R2 SP1**

.NET Framework installerat: 3,5 (omfattar 2,0 och 3,0), 4,5

| Konfigurationssträng | Utgivningsdatum | Inaktivera datum |
| --- | --- | --- |
|  WA-GÄST-OS-2.95_202002-01  |  Den 5 mars 2020  |  Inlägg 2,97  |  
|  WA-GÄST-OS-2.94_202001-01  |  den 24 januari 2020  |  Inlägg 2,96  |  
|~~WA-GÄST-OS-2.93_201912-01~~| den 8 januari 2020 | Den 5 mars 2020 |  
|~~WA-GÄST-OS-2.92_201911-01~~| den 5 december 2019 | den 24 januari 2020 |  
|~~WA-GÄST-OS-2.91_201910-01~~| Den 1 november 2019 | den 8 januari 2020 |  
|~~WA-GÄST-OS-2.90_201909-01~~| den 7 oktober 2019 | den 5 december 2019 |  
|~~WA-GÄST-OS-2.89_201908-01~~| den 4 augusti 2019 | Den 1 november 2019 |  
|~~WA-GÄST-OS-2.88_201907-01~~| den 26 juli 2019 | den 7 oktober 2019 |
|~~WA-GÄST-OS-2.87_201906-01~~|den 8 juli 2019 | den 4 augusti 2019 |
|~~WA-GÄST-OS-2.86_201905-01~~ |den 6 juni 2019 |den 26 juli 2019 |
|~~WA-GÄST-OS-2.85_201904-01~~ |den 7 maj 2019 |den 8 juli 2019 |
|~~WA-GÄST-OS-2.84_201903-01~~ |den 26 mars 2019 |den 6 juni 2019 |
|~~WA-GÄST-OS-2.83_201902-01~~ |den 12 mars 2019 |den 7 maj 2019 |
|~~WA-GÄST-OS-2.82_201901-01~~ |den 5 februari 2019 |den 26 mars 2019 |
|~~WA-GÄST-OS-2.81_201812-01~~ |den 7 januari 2019 |den 12 mars 2019 |
|~~WA-GÄST-OS-2.80_201811-01~~ |den 14 december 2018 |den 5 februari 2019 |
|~~WA-GÄST-OS-2.79_201810-01~~ |8 november 2018 |den 7 januari 2019 |
|~~WA-GÄST-OS-2.78_201809-01~~ |den 12 oktober 2018 |den 14 december 2018 |

## <a name="msrc-patch-updates"></a>Uppdateringar av MSRC-korrigering
Listan över patchar som ingår i varje månatlig Gäst OS-utgåva finns [här][patches].

## <a name="sdk-support"></a>SDK-support
Även om [pensionsprincipen för Azure SDK][retire policy sdk] anger att endast versioner över 2.2 stöds, kan du använda tidigare versioner med specifika gästoperativsystem. Du bör alltid använda den senaste SDK som stöds.

| Gäst OS-familj | Kompatibla SDK-versioner |
| --- | --- |
| 6 |Version 2.9.6+ |
| 5 |Version 2.9.5.1+ |
| 4 |Version 2.1+ |
| 3 |Version 1.8+ |
| 2 |Version 1.3+ |
| 1 |Version 1.0+ |

## <a name="guest-os-release-information"></a>Information om utgivning av gästoperativsystem
Det finns tre datum som är viktiga för Gästoperativsystem-versioner: **utgivningsdatum,** **inaktiverat** datum och **utgångsdatum.** Ett gäst-OS anses vara tillgängligt när det finns i portalen och kan väljas som målgäst-OS. När ett gästoperativsystem når det **inaktiverade** datumet tas det bort från Azure. Alla molntjänstinriktning som Gästoperativsystemet fortfarande fungerar som vanligt.

Fönstret mellan det **inaktiverade** datumet och **utgångsdatumet** ger dig en buffert för att enkelt övergå från ett gästoperativsystem till ett nyare. Om du använder *automatiskt* som gäst-OS kommer du alltid att vara på den senaste versionen och du behöver inte oroa dig för att det ska gå ut.

När **utgångsdatumet** går igenom stoppas, tas alla molntjänster som fortfarande använder det gästoperativsystemet bort eller tvingas uppgradera. Du kan läsa mer om pensionspolitiken [här][retirepolicy].

## <a name="guest-os-family-version-explanation"></a>Förklaring av gäst-OS-familjeversion
Gästoperativsystemets familjer baseras på utgivna versioner av Microsoft Windows Server. Gästoperativsystemet är det underliggande operativsystemet som Azure Cloud Services körs på. Varje gästoperativsystem har en familj, version och versionsnummer.

* **Gäst OS-familj**  
  En Windows Server-operativsystemversion som ett gästoperativsystem baseras på. *Familj 3* baseras till exempel på Windows Server 2012.
* **Gäst OS-version**  
  Specifika för en gäst-OS-familjeavbildning plus relevanta [MSRC-korrigeringar (Microsoft Security Response Center)][msrc] som är tillgängliga vid det datum då den nya Gästoperativsystemet-versionen produceras. Alla plåster får inte ingå.

    Siffrorna börjar vid 0 och öka med 1 varje gång en ny uppsättning uppdateringar läggs till. Avslutande nollor visas bara om det är viktigt. Det vill än version 2.10 är en annan, mycket senare version än version 2.1.
* **Släpp gäst-OS**  
  En återuppstättning av en Gäst OS-version. En återleasing inträffar om Microsoft hittar problem under testningen. kräver ändringar. Den senaste versionen ersätter alltid alla tidigare utgåvor, offentliga eller inte. Azure-portalen tillåter bara användare att välja den senaste versionen för en viss version. Distributioner som körs på en tidigare version är vanligtvis inte tvinga uppgraderas beroende på hur allvarlig felet är.

I exemplet nedan är 2 familjen, 12 är den version och "rel2" är utgåvan.

**Gäst OS release** - 2,12 rel2

**Konfigurationssträng för den här versionen** - WA-GUEST-OS-2.12_201208-02

Konfigurationssträngen för ett gästoperativsystem har samma information inbäddad i den, tillsammans med ett datum som visar vilka MSRC-korrigeringar som övervägdes för den versionen. I det här exemplet övervägdes MSRC-korrigeringar som producerades för Windows Server 2008 R2 till och med augusti 2012 för inkludering. Endast korrigeringar som specifikt gäller för den versionen av Windows Server ingår. Om till exempel en MSRC-korrigering gäller för Microsoft Office inkluderas den inte eftersom produkten inte ingår i Windows Server-basavbildningen.

## <a name="guest-os-system-update-process"></a>Uppdatering av gäst-OS-system
Den här sidan innehåller information om kommande gäst-OS-versioner. Kunder har angett att de vill veta när en version inträffar eftersom deras molntjänstroller startas om om de är inställda på "Automatisk" uppdatering. Gäst OS-versioner inträffar vanligtvis 2-3 veckor efter MSRC-uppdateringen som inträffar den andra tisdagen i varje månad. Nya versioner inkluderar alla relevanta MSRC-patchar för varje Gäst OS-familj.

Microsoft Azure släpper ständigt uppdateringar. Guest OS är bara en sådan uppdatering på gång. En release kan påverkas av många faktorer för många för att lista här. Dessutom körs Azure på bokstavligen hundratusentals datorer. Detta innebär att det är omöjligt att ge ett exakt datum och tid när din roll (er) kommer att starta om. Vi arbetar på en plan för att begränsa eller tid omstarter.

När en ny version av gästoperativsystemet publiceras kan det ta tid att sprida sig helt i Azure. När tjänsterna uppdateras till det nya gästoperativsystemet startas de om för att hedra uppdateringsdomäner. Tjänster som är inställda på att använda "Automatiska" uppdateringar får en version först. Efter uppdateringen visas den nya gästoperativsystemets version som anges för din tjänst i Azure-portalen. Återseleaser kan förekomma under denna period. Vissa versioner kan distribueras under längre tidsperioder och automatiska omstarter av uppgraderingar kanske inte sker på många veckor efter det officiella utgivningsdatumet. När ett gästoperativsystem är tillgängligt kan du sedan uttryckligen välja den versionen från portalen eller i konfigurationsfilen.

En hel del värdefull information om omstarter och pekare till mer information om tekniska detaljer för uppdateringar av gäst- och värd-OS finns i MSDN-blogginlägget med titeln [Rollinstansstarter på grund av OS-uppgraderingar][restarts].

Om du uppdaterar gästoperativsystemet manuellt läser du [pensionsprincipen gästoperativsystem][retirepolicy] för ytterligare information.

## <a name="guest-os-supportability-and-retirement-policy"></a>Gäst OS support och pensionspolicy
Gäst os support och pension politik förklaras [här][retirepolicy].

[cloud updates]: https://docs.microsoft.com/azure/cloud-services/cloud-services-update-azure-service
[RSS-feed för operativsystemets uppdatering]: https://raw.githubusercontent.com/MicrosoftDocs/azure-cloud-services-files/master/GuestOS/GuestOSFeed.xml
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
[Mer]: https://docs.microsoft.com/azure/cloud-services/applications-dont-support-tls-1-2  
