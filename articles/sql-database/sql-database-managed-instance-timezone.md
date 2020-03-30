---
title: Tidszoner för hanterad instans
description: Lär dig mer om tidszonsdetaldigerna i Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: 0dbed3db8e106b9bfe1b48ff2b9bc52840fc4c3a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256099"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Tidszoner i Azure SQL Database Managed Instance

Coordinated Universal Time (UTC) är den rekommenderade tidszonen för datanivån för molnlösningar. Azure SQL Database Managed Instance erbjuder också ett urval av tidszoner för att uppfylla behoven hos befintliga program som lagrar datum- och tidsvärden och samtalsdatum- och tidsfunktioner med en implicit kontext för en viss tidszon.

T-SQL-funktioner som [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) eller CLR-kod observerar tidszonen som angetts på instansnivå. SQL Server Agent-jobb följer också scheman enligt tidszonen för instansen.

  >[!NOTE]
  > Hanterad instans är det enda distributionsalternativet för Azure SQL Database som stöder tidszonsinställning. Andra distributionsalternativ följer alltid UTC.
Använd [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) i enkla och poolade SQL-databaser om du behöver tolka datum- och tidsinformation i en icke-UTC-tidszon.

## <a name="supported-time-zones"></a>Tidszoner som stöds

En uppsättning tidszoner som stöds ärvs från det underliggande operativsystemet för den hanterade instansen. Den uppdateras regelbundet för att få nya tidszonsdefinitioner och återspegla ändringar i befintliga.

[Sommartid/tidszonsändringar garanterar historisk](https://aka.ms/time) noggrannhet från 2010 framåt.

En lista med namn på tidszonerna som stöds visas via [systemvyn sys.time_zone_info.](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)

## <a name="set-a-time-zone"></a>Ange en tidszon

En tidszon för en hanterad instans kan endast ställas in när instans skapas. Standardtidszonen är UTC.

  >[!NOTE]
  > Tidszonen för en befintlig hanterad instans kan inte ändras.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ange tidszonen via Azure-portalen

När du anger parametrar för en ny instans väljer du en tidszon i listan över tidszoner som stöds.
  
![Ställa in en tidszon när instans skapas](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Ange egenskapen timezoneId i [resurshanteraren-mallen](https://aka.ms/sql-mi-create-arm-posh) för att ange tidszonen när instansen skapas.

```json
"properties": {
                "administratorLogin": "[parameters('user')]",
                "administratorLoginPassword": "[parameters('pwd')]",
                "subnetId": "[parameters('subnetId')]",
                "storageSizeInGB": 256,
                "vCores": 8,
                "licenseType": "LicenseIncluded",
                "hardwareFamily": "Gen5",
                "collation": "Serbian_Cyrillic_100_CS_AS",
                "timezoneId": "Central European Standard Time"
            },

```

En lista över värden som stöds för egenskapen timezoneId finns i slutet av den här artikeln.

Om inget anges är tidszonen inställd på UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Kontrollera tidszonen för en instans

Funktionen [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) returnerar ett visningsnamn för tidszonen för förekomsten.

## <a name="cross-feature-considerations"></a>Överväganden över flera funktioner

### <a name="restore-and-import"></a>Återställa och importera

Du kan återställa en säkerhetskopia eller importera data till en hanterad instans från en instans eller en server med olika tidszonsinställningar. Se till att göra det med försiktighet. Analysera programbeteendet och resultaten av frågorna och rapporterna, precis som när du överför data mellan två SQL Server-instanser med olika tidszonsinställningar.

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

När du utför en point-in-time-återställning tolkas tiden för återställning till som UTC-tid. På så sätt undviks eventuella oklarheter på grund av sommartid och dess potentiella förändringar.

### <a name="auto-failover-groups"></a>Automatiska redundansgrupper

Att använda samma tidszon över en primär och sekundär instans i en redundansgrupp tillämpas inte, men vi rekommenderar det starkt.

  >[!WARNING]
  > Vi rekommenderar starkt att du använder samma tidszon för den primära och sekundära instansen i en redundansgrupp. På grund av vissa sällsynta användningsfall som håller samma tidszon över primära och sekundära instanser tillämpas inte. Det är viktigt att förstå att när det gäller manuell eller automatisk redundans behåller den sekundära instansen sin ursprungliga tidszon.

## <a name="limitations"></a>Begränsningar

- Tidszonen för den befintliga hanterade instansen kan inte ändras.
- Externa processer som startas från SQL Server Agent-jobben observerar inte tidszonen för instansen.

## <a name="list-of-supported-time-zones"></a>Lista över tidszoner som stöds

| **Tidszons-ID** | **Visningsnamn för tidszon** |
| --- | --- |
| Standardtid för datumrad | (UTC-12:00) Internationella datumlinjen väst |
| UTC-11 | (UTC-11:00) Samordnad universell tid-11 |
| Aleutian normaltid | (UTC-10:00) Aleutiska öarna |
| Hawaiian normaltid | (UTC-10:00) Hawaii |
| Marquesas normaltid | (UTC-09:30) Marquesasöarna |
| Alaskan normaltid | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Samordnad universell tid-09 |
| Stilla havstid (Mexiko) | (UTC-08:00) Baja Kalifornien |
| UTC-08 | (UTC-08:00) Samordnad universell tid-08 |
| Stilla havstid | (UTC-08:00) Stillahavstid (USA & Kanada) |
| Us Mountain normaltid | (UTC-07:00) Arizona |
| Normaltid för berg (Mexiko) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Mountain normaltid | (UTC-07:00) Mountain Time (USA & Kanada) |
| Centralamerika normaltid | (UTC-06:00) Centralamerika |
| Central standardtid | (UTC-06:00) Centraltid (USA & Kanada) |
| Påskön standardtid | (UTC-06:00) Påskön |
| Central normaltid (Mexiko) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Kanada Central standardtid | (UTC-06:00) Saskatchewan |
| SA Pacific normaltid | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Östlig normaltid (Mexiko) | (UTC-05:00) Chetumal |
| Östra normaltid | (UTC-05:00) Östlig tid (US-& Kanada) |
| Haiti normaltid | (UTC-05:00) Haiti |
| Kuba normaltid | (UTC-05:00) Havanna |
| Usa östra normaltid | (UTC-05:00) Indiana (öst) |
| Turkar och Caicos normaltid | (UTC-05:00) Turkar och Caicos |
| Paraguay normaltid | (UTC-04:00) Asuncion |
| Atlantens standardtid | (UTC-04:00) Atlantic Time (Kanada) |
| Venezuela normaltid | (UTC-04:00) Caracas |
| Central brasiliansk normaltid | (UTC-04:00) Cuiabá |
| SA Västra normaltid | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA normaltid | (UTC-04:00) Santiago |
| Newfoundland normaltid | (UTC-03:30) Newfoundland |
| Tocantins normaltid | (UTC-03:00) Araguaina (på andra sätt) |
| E. Sydamerika normaltid | (UTC-03:00) Brasilia |
| SA Östra standardtid | (UTC-03:00) Cayenne , Fortaleza |
| Argentina normaltid | (UTC-03:00) Staden Buenos Aires |
| Grönland normaltid | (UTC-03:00) Grönland |
| Montevideo normaltid | (UTC-03:00) Montevideo |
| Magallanes normaltid | (UTC-03:00) Punta arenor |
| Saint Pierre normaltid | (UTC-03:00) Saint Pierre och Miquelon |
| Bahia normaltid | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Samordnad universell tid-02 |
| Mid-Atlantic normaltid | (UTC-02:00) Mid-Atlantic - Gamla |
| Azorerna normaltid | (UTC-01:00) Azorerna |
| Kap Verde normaltid | (UTC-01:00) Cabo Verde är. |
| UTC | (UTC) Koordinerad universell tid |
| GMT-standardtid | (UTC+00:00) Dublin, Edinburgh, Lissabon, London |
| Greenwich normaltid | (UTC+00:00) Monrovia, Reykjavik |
| W. Europa normaltid | (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Centraleuropa normaltid | (UTC+01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Romantik Standard tid | (UTC+01:00) Bryssel, Köpenhamn, Madrid, Paris |
| Marocko Normaltid | (UTC+01:00) Casablanca |
| Sao Tome normaltid | (UTC+01:00) Sao Tome |
| Centraleuropeisk normaltid | (UTC+01:00) Sarajevo, Skopje, Warszawa, Zagreb |
| W. Centralafrika normaltid | (UTC+01:00) Västra Centralafrika |
| Jordanien normaltid | (UTC+02:00) Amman |
| GTB-standardtid | (UTC+02:00) Aten, Bukarest |
| Tid i Mellanöstern | (UTC+02:00) Beirut |
| Egypten Normaltid | (UTC+02:00) Kairo |
| E. Europa normaltid | (UTC+02:00) Chişinău |
| Syrien normaltid | (UTC+02:00) Damascus |
| Västbankens standardtid | (UTC+02:00) Gaza, Hebron |
| Sydafrikas standardtid | (UTC+02:00) Harare, Pretoria |
| FLE Normaltid | (UTC+02:00) Helsingfors, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel normaltid | (UTC+02:00) Jerusalem |
| Kaliningrad normaltid | (UTC+02:00) Kaliningrad |
| Sudan normaltid | (UTC+02:00) Khartoum |
| Libyens standardtid | (UTC+02:00) Tripoli |
| Namibia normaltid | (UTC+02:00) Windhoek |
| Arabisk standardtid | (UTC+03:00) Bagdad |
| Turkiet Normaltid | (UTC+03:00) Istanbul |
| Arabisk normaltid | (UTC+03:00) Kuwait, Riyadh |
| Vitryssland normaltid | (UTC+03:00) Minsk |
| Rysk normaltid | (UTC+03:00) Moskva, S:t Petersburg |
| E. Afrika normaltid | (UTC+03:00) Nairobi |
| Iran normaltid | (UTC+03:30) Teheran |
| Arabisk standardtid | (UTC+04:00) Abu Dhabi , Muscat |
| Astrakhan normaltid | (UTC+04:00) Astrakhan, Storbritannien |
| Azerbajdzjan normaltid | (UTC+04:00) Baku |
| Ryssland Tidszon 3 | (UTC+04:00) Izhevsk , Samara |
| Mauritius normaltid | (UTC+04:00) Port Louis |
| Saratov normaltid | (UTC+04:00) Saratov |
| Georgisk normaltid | (UTC+04:00) Tbilisi |
| Volgograd normaltid | (UTC+04:00) Volgograd |
| Kaukasus normaltid | (UTC+04:00) Yerevan |
| Afghanistan normaltid | (UTC+04:30) Kabul |
| Västra Asien normaltid | (UTC+05:00) Ashgabat, Tasjkent |
| Ekaterinburg normaltid | (UTC+05:00) Ekaterinburg |
| Pakistan normaltid | (UTC+05:00) Islamabad , Karachi |
| Indien normaltid | (UTC+05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lankas standardtid | (UTC+05:30) Sri Jayawardenepura |
| Nepal normaltid | (UTC+05:45) Kathmandu |
| Standardtid i Centralasien | (UTC+06:00) Astana |
| Bangladesh normaltid | (UTC+06:00) Dhaka |
| Omsk normaltid | (UTC+06:00) Omsk |
| Myanmar normaltid | (UTC+06:30) Yangon (Rangoon) |
| SE Asien normaltid | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altai normaltid | (UTC+07:00) Barnaul , Gorno-Altaysk |
| W. Mongoliet normaltid | (UTC+07:00) Hovd (olika) |
| Norra Asien normaltid | (UTC+07:00) Krasnoyarsk |
| N. Standardtid i Centralasien | (UTC+07:00) Novosibirsk |
| Tomsk normaltid | (UTC+07:00) Tomsk |
| Kina normaltid | (UTC+08:00) Peking, Chongqing, Hongkong, Urumqi |
| Nordasien östlig standardtid | (UTC+08:00) Irkutsk |
| Singapore normaltid | (UTC+08:00) Kuala Lumpur, Singapore |
| W. Australien normaltid | (UTC+08:00) Perth |
| Taipei normaltid | (UTC+08:00) Taipei |
| Ulaanbaatar normaltid | (UTC+08:00) Ulaanbaatar |
| Aus Central W. Normaltid | (UTC+08:45) Eucla (av eucla) |
| Transbaikal normaltid | (UTC+09:00) Chita |
| Tokyo normaltid | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Nordkorea normaltid | (UTC+09:00) Pyongyang |
| Korea normaltid | (UTC+09:00) Seoul |
| Jakutsk standardtid | (UTC+09:00) Yakutsk |
| Cen. Australien normaltid | (UTC+09:30) Adelaide |
| AUS Central standardtid | (UTC+09:30) Darwin |
| E. Australien normaltid | (UTC+10:00) Brisbane |
| AUS Östra normaltid | (UTC+10:00) Canberra, Melbourne, Sydney |
| Västra Stilla havet normaltid | (UTC+10:00) Guam, Port Moresby |
| Tasmanien normaltid | (UTC+10:00) Hobart |
| Vladivostok normaltid | (UTC+10:00) Vladivostok |
| Lord Howe standardtid | (UTC+10:30) Lord Howe ön |
| Bougainville normaltid | (UTC+11:00) Bougainville ön |
| Ryssland Tidszon 10 | (UTC+11:00) Chokurdakh (på andra sätt) |
| Magadan normaltid | (UTC+11:00) Magadan |
| Norfolk normaltid | (UTC+11:00) Norfolkön |
| Sachalin normaltid | (UTC+11:00) Sakhalin |
| Standardtid för Centrala Stilla havet | (UTC+11:00) Solomon Är., Nya Kaledonien |
| Ryssland Tidszon 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nya Zeelands standardtid | (UTC+12:00) Auckland , Wellington |
| UTC+12 | (UTC+12:00) Samordnad universell tid+12 |
| Fijis standardtid | (UTC+12:00) Fiji |
| Kamtjatka normaltid | (UTC+12:00) Petropavlovsk-Kamchatsky - Gammal |
| Chatham Islands normaltid | (UTC+12:45) Chathamöarna |
| UTC+13 | (UTC+13:00) Samordnad universell tid+13 |
| Tonga normaltid | (UTC+13:00) Nuku'alofa |
| Samoa normaltid | (UTC+13:00) Samoa |
| Linjeöarnas standardtid | (UTC+14:00) Kiritimati ön |

## <a name="see-also"></a>Se även 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [TIDSZON (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
