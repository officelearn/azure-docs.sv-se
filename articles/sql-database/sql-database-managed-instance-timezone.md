---
title: Azure SQL Database hanterade instans tids zoner "
description: Läs mer om tids zons information för Azure SQL Database Hanterad instans
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 09/03/2019
ms.openlocfilehash: a578c626cd1edd4a3f7474e59c2e795be2e3c121
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 11/06/2019
ms.locfileid: "73687847"
---
# <a name="time-zones-in-azure-sql-database-managed-instance"></a>Tids zoner i Azure SQL Database Hanterad instans

UTC (Coordinated Universal Time) är den rekommenderade tids zonen för data nivån för moln lösningar. Azure SQL Database Hanterad instans erbjuder också ett urval av tids zoner för att uppfylla behoven hos befintliga program som lagrar datum-och tids värden och anropar datum-och tids funktioner med en implicit kontext för en speciell tidszon.

T-SQL-funktioner som [getDate ()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) eller CLR-kod studerar tids zonen på instans nivå. SQL Server Agents jobb följer också scheman enligt tids zonen för instansen.

  >[!NOTE]
  > Hanterad instans är det enda distributions alternativet för Azure SQL Database som stöder tids zons inställningen. Andra distributions alternativ följer alltid UTC.
Använd i [tids zonen](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) i enskilda och pooler SQL-databaser om du behöver tolka datum-och tidsinformation i en tids zon som inte är UTC-tid.

## <a name="supported-time-zones"></a>Tids zoner som stöds

En uppsättning tids zoner som stöds ärvs från det underliggande operativ systemet för den hanterade instansen. Den uppdateras regelbundet för att hämta nya definitioner för tids zoner och återspegla ändringar av befintliga.

[Princip för sommar tid/tids zons ändringar](https://aka.ms/time) garanterar historisk noggrannhet från 2010 forward.

En lista med namn på de tids zoner som stöds exponeras i system visningen [sys. time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) .

## <a name="set-a-time-zone"></a>Ange en tidszon

En tids zon för en hanterad instans kan bara anges när en instans skapas. Standard tids zonen är UTC.

  >[!NOTE]
  > Det går inte att ändra tids zonen för en befintlig hanterad instans.

### <a name="set-the-time-zone-through-the-azure-portal"></a>Ange tids zonen via Azure Portal

När du anger parametrar för en ny instans väljer du en tidszon i listan över tids zoner som stöds.
  
![Ange en tidszon när en instans skapas](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Ange timezoneId-egenskapen i [Resource Manager-mallen](https://aka.ms/sql-mi-create-arm-posh) för att ställa in tids zonen under skapandet av instansen.

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

En lista med värden som stöds för egenskapen timezoneId finns i slutet av den här artikeln.

Om detta inte anges anges tids zonen till UTC.

## <a name="check-the-time-zone-of-an-instance"></a>Kontrol lera tids zonen för en instans

Funktionen [CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) returnerar ett visnings namn för instansens tidszon.

## <a name="cross-feature-considerations"></a>Överväganden för olika funktioner

### <a name="restore-and-import"></a>Återställa och importera

Du kan återställa en säkerhets kopia eller importera data till en hanterad instans från en instans eller en server med olika tids zons inställningar. Se till att göra det med försiktighet. Analysera program beteendet och resultaten av frågorna och rapporterna, precis som när du överför data mellan två SQL Server instanser med olika tids zons inställningar.

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

När du utför en tidpunkts återställning, tolkas tiden för att återställa till UTC-tid. På så sätt kan eventuella tvetydigheter på grund av sommar tid och eventuella ändringar undvikas.

### <a name="auto-failover-groups"></a>Automatiska redundansgrupper

Att använda samma tidszon i en primär och sekundär instans i en grupp för växling vid fel är inte tvingande, men vi rekommenderar starkt.

  >[!WARNING]
  > Vi rekommenderar starkt att du använder samma tidszon för den primära och sekundära instansen i en grupp för växling vid fel. På grund av vissa ovanliga användnings fall är det inte tvingande att behålla samma tidszon mellan primära och sekundära instanser. Det är viktigt att förstå att om du använder manuell eller automatisk redundans behåller den sekundära instansen den ursprungliga tids zonen.

## <a name="limitations"></a>Begränsningar

- Det går inte att ändra tids zonen för den befintliga hanterade instansen.
- Externa processer som startas från SQL Server Agent jobben observerar inte tids zonen för instansen.

## <a name="list-of-supported-time-zones"></a>Lista över tids zoner som stöds

| **Tidszons-ID** | **Visnings namn för tidszon** |
| --- | --- |
| Datum gränsen, normal tid | (UTC-12:00) Internationella datumlinjen, väst |
| UTC-11 | (UTC-11:00) Coordinated Universal Time-11 |
| Aleuterna normal tid | (UTC-10:00) Aleuterna |
| Hawaii, normal tid | (UTC-10:00) Hawaii |
| Marquesas, normal tid | (UTC-09:30) Marquesasöarna |
| Alaska, normal tid | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Coordinated Universal Time-09 |
| Pacific, normal tid (Mexiko) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Coordinated Universal Time-08 |
| Pacific, normal tid | (UTC-08:00) Pacific Time (USA och Kanada) |
| US Mountain, normal tid | (UTC-07:00) Arizona |
| Mountain, normal tid (Mexiko) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Mountain, normal tid | (UTC-07:00) Mountain Time (USA och Kanada) |
| Central Amerika, normal tid | (UTC-06:00) Centralamerika |
| Central, normal tid | (UTC-06:00) Central Time (USA och Kanada) |
| Påskön, normal tid | (UTC-06:00) Påskön |
| Central normal tid (Mexiko) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Kanada, Central, normal tid | (UTC-06:00) Saskatchewan |
| SA Pacific, normal tid | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Eastern, normal tid (Mexiko) | (UTC-05:00) Chetumal |
| Eastern, normal tid | (UTC-05:00) Eastern Time (USA och Kanada) |
| Haiti, normal tid | (UTC-05:00) Haiti |
| Kuba, normal tid | (UTC-05:00) Havanna |
| Östra USA, normal tid | (UTC-05:00) Indiana (östra) |
| Turks-och Caicosöarna, normal tid | (UTC-05:00) Turks-och Caicosöarna |
| Paraguay, normal tid | (UTC-04:00) Asunción |
| Atlantic, normal tid | (UTC-04:00) Atlantic Time (Kanada) |
| Venezuela, normal tid | (UTC-04:00) Caracas |
| Centrala Brasilien, normal tid | (UTC-04:00) Cuiaba |
| Västra Sydamerika, normal tid | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA, normal tid | (UTC-04:00) Santiago |
| Newfoundland, normal tid | (UTC-03:30) Newfoundland |
| Tocantins, normal tid | (UTC-03:00) Araguaina |
| E. Södra Amerika, normal tid | (UTC-03:00) Brasilia |
| SA Eastern, normal tid | (UTC-03:00) Cayenne, Fortaleza |
| Argentina, normal tid | (UTC-03:00) Buenos Aires |
| Grönland, normal tid | (UTC-03:00) Grönland |
| Montevideo, normal tid | (UTC-03:00) Montevideo |
| Magallanes normal tid | (UTC-03:00) Punta Arenas |
| Saint Pierre, normal tid | (UTC-03:00) Saint Pierre och Miquelon |
| Bahia, normal tid | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Coordinated Universal Time-02 |
| Mid-Atlantic, normal tid | (UTC-02:00) Mid-Atlantic - gammal |
| Azorerna, normal tid | (UTC-01:00) Azorerna |
| Kap Verde, normal tid | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| GMT, normal tid | (UTC+00:00) Dublin, Edinburgh, Lissabon, London |
| Greenwich, normal tid | (UTC+00:00) Monrovia, Reykjavik |
| A. Europa, normal tid | (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Central Europa, normal tid | (UTC+01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Romantik, normal tid | (UTC+01:00) Bryssel, Köpenhamn, Madrid, Paris |
| Marocko, normal tid | (UTC + 01:00) Casablanca |
| Sao Tome, normal tid | (UTC + 01:00) Sao Tome |
| Central europeisk normal tid | (UTC+01:00) Sarajevo, Skopje, Warszawa, Zagreb |
| A. Central Afrika, normal tid | (UTC+01:00) Västra Centralafrika |
| Jordanien, normal tid | (UTC+02:00) Amman |
| GTB, normal tid | (UTC+02:00) Aten, Bukarest |
| Mellanöstern, normal tid | (UTC+02:00) Beirut |
| Egypten, normal tid | (UTC+02:00) Kairo |
| E. Europa, normal tid | (UTC+02:00) Chisinau |
| Syrien, normal tid | (UTC+02:00) Damaskus |
| Västra bankens normal tid | (UTC+02:00) Gaza, Hebron |
| Södra Afrika, normal tid | (UTC+02:00) Harare, Pretoria |
| FLE, normal tid | (UTC+02:00) Helsingfors, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel, normal tid | (UTC+02:00) Jerusalem |
| Kaliningrad normal tid | (UTC+02:00) Kaliningrad |
| Sudan, normal tid | (UTC + 02:00) Khartoum |
| Libyen, normal tid | (UTC+02:00) Tripoli |
| Namibia, normal tid | (UTC + 02:00) Windhoek |
| Irak, normal tid | (UTC+03:00) Bagdad |
| Turkiet, normal tid | (UTC + 03:00) Istanbul |
| Arabemiraten, normal tid | (UTC+03:00) Kuwait, Riyad |
| Vitryssland, normal tid | (UTC+03:00) Minsk |
| Ryssland, normal tid | (UTC + 03:00) Moskva, St. Petersburg |
| E. Afrika, normal tid | (UTC+03:00) Nairobi |
| Iran, normal tid | (UTC+03:30) Teheran |
| Arabisk normal tid | (UTC+04:00) Abu Dhabi, Muskat |
| Astrachan, normal tid | (UTC+04:00) Astrakhan, Ulyanovsk |
| Azerbajdzjan, normal tid | (UTC+04:00) Baku |
| Ryssland-tid Zon 3 | (UTC+04:00) Izhevsk, Samara |
| Mauritius, normal tid | (UTC+04:00) Port Louis |
| Saratov normal tid | (UTC + 04:00) Saratov |
| Georgien, normal tid | (UTC+04:00) Tbilisi |
| Volgograd, normal tid | (UTC + 04:00) Volgograd |
| Kaukasus, normal tid | (UTC+04:00) Jerevan |
| Afghanistan, normal tid | (UTC+04:30) Kabul |
| Västra Asien, normal tid | (UTC+05:00) Asjchabad, Tasjkent |
| Jekaterinburg normal tid | (UTC+05:00) Ekaterinburg |
| Pakistan, normal tid | (UTC+05:00) Islamabad, Karachi |
| Indien, normal tid | (UTC+05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lanka, normal tid | (UTC+05:30) Sri Jayawardenepura |
| Nepal, normal tid | (UTC+05:45) Katmandu |
| Central Asien, normal tid | (UTC+06:00) Astana |
| Bangladesh, normal tid | (UTC+06:00) Dhaka |
| Omsk normal tid | (UTC + 06:00) Omsk |
| Myanmar, normal tid | (UTC+06:30) Yangon |
| Sydostasien, normal tid | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altai normal tid | (UTC+07:00) Barnaul, Gorno-Altaysk |
| A. Mongoliet, normal tid | (UTC+07:00) Hovd |
| Nord Asien, normal tid | (UTC+07:00) Krasnoyarsk |
| M. Central Asien, normal tid | (UTC + 07:00) Novosibirsk |
| Tomsk, normal tid | (UTC+07:00) Tomsk |
| Kina, normal tid | (UTC+08:00) Beijing, Chongqing, Hongkong SAR, Urumqi |
| Östra Nord Asien, normal tid | (UTC+08:00) Irkutsk |
| Singapore, normal tid | (UTC+08:00) Kuala Lumpur, Singapore |
| A. Australien, normal tid | (UTC+08:00) Perth |
| Taipei, normal tid | (UTC+08:00) Taipei |
| Ulan Bator normal tid | (UTC+08:00) Ulan Bator |
| Centrala Australien, normal tid | (UTC+08:45) Eucla |
| Transbajkal, normal tid | (UTC+09:00) Chita |
| Tokyo, normal tid | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Nord Korea, normal tid | (UTC + 09:00) Pyongyang |
| Korea, normal tid | (UTC+09:00) Söul |
| Jakutsk normal tid | (UTC+09:00) Yakutsk |
| Cent. Australien, normal tid | (UTC+09:30) Adelaide |
| Centrala Australien, normal tid | (UTC+09:30) Darwin |
| E. Australien, normal tid | (UTC+10:00) Brisbane |
| Östra Australien, normal tid | (UTC+10:00) Canberra, Melbourne, Sydney |
| Västra Stilla havs området, normal tid | (UTC+10:00) Guam, Port Moresby |
| Tasmanien, normal tid | (UTC+10:00) Hobart |
| Vladivostok, normal tid | (UTC+10:00) Vladivostok |
| Herren Howe normal tid | (UTC+10:30) Lord Howeön |
| Bougainville normal tid | (UTC+11:00) Bougainville |
| Ryssland-tidszon 10 | (UTC+11:00) Chokurdakh |
| Magadan, normal tid | (UTC+11:00) Magadan |
| Norfolk, normal tid | (UTC+11:00) Norfolkön |
| Sachalin, normal tid | (UTC+11:00) Sakhalin |
| Central Pacific, normal tid | (UTC+11:00) Salomonöarna, Nya Kaledonien |
| Ryssland-tidszon 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nya Zeeland, normal tid | (UTC+12:00) Auckland, Wellington |
| UTC + 12 | (UTC+12:00) Coordinated Universal Time+12 |
| Fidji, normal tid | (UTC+12:00) Fiji |
| Kamtjatka, normal tid | (UTC+12:00) Petropavlovsk-Kamtjatskij - gammal |
| Chathamöarna Islands, normal tid | (UTC+12:45) Chathamöarna |
| UTC + 13 | (UTC + 13:00) Koordinerad universell tid + 13 |
| Tonga, normal tid | (UTC+13:00) Nuku'alofa |
| Samoa, normal tid | (UTC+13:00) Samoa |
| Linje öarna, normal tid | (UTC+14:00) Kiritimati (Julön) |

## <a name="see-also"></a>Se även 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [I tidszon (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys. time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
