---
title: Tids zoner för Azure SQL Database hanterade instanser | Microsoft Docs "
description: Läs mer om tids zons information för Azure SQL Database Hanterad instans
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
ms.date: 08/12/2019
ms.openlocfilehash: 515e971214244cdd14955cc269a5f005cb93734f
ms.sourcegitcommit: 5d6c8231eba03b78277328619b027d6852d57520
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 08/13/2019
ms.locfileid: "68967927"
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
  > Vi rekommenderar starkt att du använder samma tidszon för den primära och sekundära instansen i en grupp för växling vid fel. På grund av vissa sällsynta scenarier tillämpas inte samma tidszon på primära och sekundära instanser. Det är viktigt att förstå att om du använder manuell eller automatisk redundans behåller den sekundära instansen den ursprungliga tids zonen.

## <a name="limitations"></a>Begränsningar

- Det går inte att ändra tids zonen för den befintliga hanterade instansen.
- Externa processer som startas från SQL Server Agent jobben observerar inte tids zonen för instansen.

## <a name="known-issues"></a>Kända problem

När en PITR-åtgärd (Point-in-Time Restore) utförs tolkas tiden för att återställa till enligt tids zonen på den hanterade instansen där automatiska databas säkerhets kopieringar tas från, även om Portal sidan för PITR antyder att tiden tolkas som UTC.

Exempel:

Anta att den här instansen där automatiska säkerhets kopieringar tas från har standard tids zon uppsättningen (UTC-5).
Portal sida för återställning vid tidpunkt föreslår att tiden du väljer att återställa till är UTC-tid:

![PITR med lokal tid med hjälp av portalen](media/sql-database-managed-instance-timezone/02-pitr-with-nonutc-timezone.png)

Tiden för att återställa till tolkas dock som Eastern, normal tid, och i det här exemplet kommer databasen att återställas till tillstånds nivån 9 AM Eastern, normal tid och inte UTC-tid.

Om du vill göra en tidpunkts återställning till en viss tidpunkt i UTC-tid, beräknar du först motsvarande tid i tids zonen för käll instansen och använder den tiden i portalen eller PowerShell/CLI-skriptet.

## <a name="list-of-supported-time-zones"></a>Lista över tids zoner som stöds

| **Tidszons-ID** | **Visnings namn för tidszon** |
| --- | --- |
| Datumgränsen, normaltid | (UTC-12:00) Internationella datumlinjen, väst |
| UTC-11 | (UTC-11:00) Coordinated Universal Time-11 |
| Aleuterna, normaltid | (UTC-10:00) Aleuterna |
| Hawaii, normaltid | (UTC-10:00) Hawaii |
| Marquesas, normaltid | (UTC-09:30) Marquesasöarna |
| Alaska, normaltid | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Koordinerad universell tid-09 |
| Pacific, normaltid (Mexiko) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Koordinerad universaltid-08 |
| Pacific, normaltid | (UTC-08:00) Pacific Time (USA och Kanada) |
| US Mountain, normaltid | (UTC-07:00) Arizona |
| Mountain, normaltid (Mexiko) | (UTC-07:00) Chihuahua, La Paz, Mazatlan |
| Mountain, normaltid | (UTC-07:00) Mountain Time (USA och Kanada) |
| Centralamerika, normaltid | (UTC-06:00) Centralamerika |
| Central, normaltid | (UTC-06:00) Central Time (USA och Kanada) |
| Påskön, normaltid | (UTC-06:00) Påskön |
| Central normaltid (Mexiko) | (UTC-06:00) Guadalajara, Mexico City, Monterrey |
| Kanada, centrala, normaltid | (UTC-06:00) Saskatchewan |
| SA Pacific, normaltid | (UTC-05:00) Bogota, Lima, Quito, Rio Branco |
| Normaltid, östra (Mexiko) | (UTC-05:00) Chetumal |
| Eastern, normaltid | (UTC-05:00) Eastern Time (USA och Kanada) |
| Haiti, normaltid | (UTC-05:00) Haiti |
| Kuba, normaltid | (UTC-05:00) Havanna |
| US Eastern, normaltid | (UTC-05:00) Indiana (östra) |
| Turks-och Caicosöarna, normal tid | (UTC – 05:00) Turks- och Caicosöarna |
| Paraguay, normaltid | (UTC-04:00) Asunción |
| Atlantic, normaltid | (UTC-04:00) Atlantic Time (Kanada) |
| Venezuela, normaltid | (UTC-04:00) Caracas |
| Centrala Brasilien, normaltid | (UTC-04:00) Cuiaba |
| Västra Sydamerika, normaltid | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA, normaltid | (UTC-04:00) Santiago |
| Newfoundland, normaltid | (UTC-03:30) Newfoundland |
| Tocantins, normaltid | (UTC-03:00) Araguaina |
| E. Södra Amerika, normal tid | (UTC-03:00) Brasilia |
| Sydamerika (östra), normaltid | (UTC-03:00) Cayenne, Fortaleza |
| Argentina, normaltid | (UTC-03:00) Buenos Aires |
| Grönland, normaltid | (UTC-03:00) Grönland |
| Montevideo, normaltid | (UTC-03:00) Montevideo |
| Magallanes, normaltid | (UTC-03:00) Punta Arenas |
| Saint Pierre, normaltid | (UTC-03:00) Saint-Pierre and Miquelon |
| Bahia, normaltid | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Coordinated Universal Time-02 |
| Mid-Atlantic, normaltid | (UTC-02:00) Mid-Atlantic - gammal |
| Azorerna, normaltid | (UTC-01:00) Azorerna |
| Kap Verde, normal tid | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| GMT, normaltid | (UTC+00:00) Dublin, Edinburgh, Lissabon, London |
| Greenwich, normaltid | (UTC+00:00) Monrovia, Reykjavik |
| W. Europa, normal tid | (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Centraleuropa, normaltid | (UTC+01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Paris, Madrid, normaltid | (UTC+01:00) Bryssel, Köpenhamn, Madrid, Paris |
| Marocko, normaltid | (UTC+01:00) Casablanca |
| São Tomé, normaltid | (UTC+01:00) São Tomé |
| Centraleuropeisk normaltid | (UTC+01:00) Sarajevo, Skopje, Warszawa, Zagreb |
| W. Central Afrika, normal tid | (UTC+01:00) Västra Centralafrika |
| Jordanien, normaltid | (UTC+02:00) Amman |
| GTB, normaltid | (UTC+02:00) Aten, Bukarest |
| Mellanöstern, normaltid | (UTC+02:00) Beirut |
| Egypten, normaltid | (UTC+02:00) Kairo |
| E. Europa, normal tid | (UTC+02:00) Chisinau |
| Syrien, normaltid | (UTC+02:00) Damaskus |
| Västra bankens normal tid | (UTC+02:00) Gaza, Hebron |
| Södra Afrika, normaltid | (UTC+02:00) Harare, Pretoria |
| FLE, normaltid | (UTC+02:00) Helsingfors, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel, normal tid | (UTC+02:00) Jerusalem |
| Kaliningrad normal tid | (UTC+02:00) Kaliningrad |
| Sudan, normaltid | (UTC + 02:00) Khartoum |
| Libyen, normaltid | (UTC+02:00) Tripoli |
| Namibia, normaltid | (UTC+02:00) Windhoek |
| Irak, normaltid | (UTC+03:00) Bagdad |
| Turkiet, normaltid | (UTC+03:00) Istanbul |
| Arabien, normaltid | (UTC+03:00) Kuwait, Riyad |
| Vitryssland, normaltid | (UTC+03:00) Minsk |
| Ryssland, normal tid | (UTC+03:00) Moskva, St. Petersburg |
| E. Afrika, normal tid | (UTC+03:00) Nairobi |
| Iran, normaltid | (UTC+03:30) Teheran |
| Arabisk normaltid | (UTC+04:00) Abu Dhabi, Muskat |
| Astrachan, normaltid | (UTC+04:00) Astrakhan, Ulyanovsk |
| Azerbajdzjan, normaltid | (UTC+04:00) Baku |
| Ryssland-tid Zon 3 | (UTC+04:00) Izhevsk, Samara |
| Mauritius, normaltid | (UTC+04:00) Port Louis |
| Saratov, normaltid | (UTC+04:00) Saratov |
| Georgien, normaltid | (UTC+04:00) Tbilisi |
| Volgograd standardtid | (UTC+04:00) Volgograd |
| Kaukasus, normaltid | (UTC+04:00) Jerevan |
| Afghanistan, normaltid | (UTC+04:30) Kabul |
| Västra Asien, normaltid | (UTC+05:00) Asjchabad, Tasjkent |
| Jekaterinburg normal tid | (UTC+05:00) Ekaterinburg |
| Pakistan, normaltid | (UTC+05:00) Islamabad, Karachi |
| Indien, normaltid | (UTC+05:30) Chennai, Kolkata, Mumbai, New Delhi |
| Sri Lanka, normaltid | (UTC+05:30) Sri Jayawardenepura |
| Nepal, normaltid | (UTC+05:45) Katmandu |
| Centralasien, normaltid | (UTC+06:00) Astana |
| Bangladesh, normaltid | (UTC+06:00) Dhaka |
| Omsk, normaltid | (UTC+06:00) Omsk |
| Unionen Myanmar, normaltid | (UTC+06:30) Yangon |
| Sydostasien, normaltid | (UTC+07:00) Bangkok, Hanoi, Jakarta |
| Altaj, normaltid | (UTC+07:00) Barnaul, Gorno-Altaysk |
| W. Mongoliet, normal tid | (UTC+07:00) Hovd |
| Nord Asien, normal tid | (UTC+07:00) Krasnoyarsk |
| N. Centralasien, normaltid | (UTC+07:00) Novosibirsk |
| Tomsk, normaltid | (UTC+07:00) Tomsk |
| Kina, normaltid | (UTC+08:00) Beijing, Chongqing, Hongkong, Urumqi |
| Östra Nord Asien, normal tid | (UTC+08:00) Irkutsk |
| Singapore, normal tid | (UTC+08:00) Kuala Lumpur, Singapore |
| W. Australien, normal tid | (UTC+08:00) Perth |
| Taipei, normaltid | (UTC+08:00) Taipei |
| Ulan Bator, normaltid | (UTC+08:00) Ulan Bator |
| Centr. v. Australien, normaltid | (UTC+08:45) Eucla |
| Transbajkal, normaltid | (UTC+09:00) Chita |
| Tokyo, normaltid | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Nordkorea, normaltid | (UTC + 09:00) Pyongyang |
| Korea, normaltid | (UTC+09:00) Söul |
| Jakutsk normal tid | (UTC+09:00) Yakutsk |
| Cent. Australien, normal tid | (UTC+09:30) Adelaide |
| Centrala Australien, normaltid | (UTC+09:30) Darwin |
| E. Australien, normal tid | (UTC+10:00) Brisbane |
| Östra Australien, normaltid | (UTC+10:00) Canberra, Melbourne, Sydney |
| West Pacific, normaltid | (UTC+10:00) Guam, Port Moresby |
| Tasmanien, normaltid | (UTC+10:00) Hobart |
| Vladivostok, normal tid | (UTC+10:00) Vladivostok |
| Lord Howe, normaltid | (UTC+10:30) Lord Howeön |
| Bougainville, normaltid | (UTC+11:00) Bougainville |
| Ryssland-tidszon 10 | (UTC+11:00) Chokurdakh |
| Magadan, normaltid | (UTC+11:00) Magadan |
| Norfolk, normaltid | (UTC+11:00) Norfolköarna |
| Sachalin, normaltid | (UTC+11:00) Sakhalin |
| Central Pacific, normaltid | (UTC+11:00) Salomonöarna, Nya Kaledonien |
| Ryssland-tidszon 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
| Nya Zeeland, normaltid | (UTC+12:00) Auckland, Wellington |
| UTC+12 | (UTC+12:00) Coordinated Universal Time+12 |
| Fidji, normaltid | (UTC+12:00) Fiji |
| Kamtjatka, normaltid | (UTC+12:00) Petropavlovsk-Kamtjatskij - gammal |
| Chathamöarna, normaltid | (UTC+12:45) Chathamöarna |
| UTC+13 | (UTC+13:00) Coordinated Universal Time+13 |
| Tonga, normaltid | (UTC+13:00) Nuku'alofa |
| Samoa, normaltid | (UTC+13:00) Samoa |
| Linjeöarna, normaltid | (UTC+14:00) Kiritimati (Julön) |

## <a name="see-also"></a>Se också 

- [CURRENT_TIMEZONE (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql)
- [I tidszon (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)
