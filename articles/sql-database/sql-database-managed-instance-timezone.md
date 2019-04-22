---
title: Azure SQL Database Managed Instance tidszon | Microsoft-Docs ”
description: Lär dig mer om tidszonen ärendets natur Azure SQL Database Managed Instance
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: MladjoA
ms.author: mlandzic
ms.reviewer: ''
manager: craigg
ms.date: 04/10/2019
ms.openlocfilehash: 23314e97051da95ab164baeab6e9d089f486351a
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/18/2019
ms.locfileid: "59489698"
---
# <a name="time-zone-in-azure-sql-database-managed-instance-preview"></a>Tidszonen i Azure SQL Database Managed Instance (förhandsversion)

Med hjälp av Coordinated Universal Time (UTC) är en rekommendation för datanivån för molnlösningar, Azure SQL Database Managed Instance kan du välja mellan för tidszonen att uppfylla behoven i de befintliga program som lagrar datum- och tidsvärden och Samtalsdatum och tidsfunktioner med en implicit kontexten för en viss tidszon.

T-SQL-funktioner som [GETDATE()](https://docs.microsoft.com/sql/t-sql/functions/getdate-transact-sql) och CLR-kod Observera tidszonen som angetts på instansen. SQL Agent-jobb kan du även följa schemat enligt tidszonen för instansen.

  >[!NOTE]
  > Managed Instance är alternativet endast distribution av Azure SQL-databas som har stöd för tidszon. Andra distributionsalternativ följer alltid UTC.
Använd [AT TIME ZONE](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql) i enkel och grupperade SQL-databaser om du behöver att tolka information datum och tid i icke-UTC-tidszonen.

## <a name="supported-time-zones"></a>Tidszoner som stöds

En uppsättning stöds tidszoner har ärvts från det underliggande operativsystemet för den hanterade instansen och ska uppdateras regelbundet för att hämta den nya tidszonsdefinitioner och förändringar i befintliga.

En lista med namnen på tidszonerna som stöds är tillgängliga via den [sys.time_zone_info](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql) systemvy.

## <a name="setting-time-zone"></a>Ange tidszon

En tidszonen för hanterad instans kan anges när instans skapas endast. The default time zone is Coordinated Universal Time (UTC).

  >[!NOTE]
  > Tidszonen för en befintlig hanterad instans kan inte ändras.

### <a name="setting-the-time-zone-through-azure-portal"></a>Tidszonen via Azure-portalen

När du skriver in parametrar för den nya instansen Välj en tidszon i listan över tidszoner som stöds:
  
![Ange tidszonen när instans skapas](media/sql-database-managed-instance-timezone/01-setting_timezone-during-instance-creation.png)

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-mall

Ange timezoneId-egenskapen i din [Resource Manager-mall](https://aka.ms/sql-mi-create-arm-posh) att ställa in den aktuella tidszonen när instans skapas.

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

Lista över värden som stöds för egenskapen timezoneId finns i slutet av den här artikeln.

Om inte anges, ställs tidszon till UTC.

## <a name="checking-the-time-zone-of-instance"></a>Kontrollera tidszonen för instans

[CURRENT_TIMEZONE](https://docs.microsoft.com/sql/t-sql/functions/current-timezone-transact-sql) returnerar funktionen ett namn på tidszonen för instansen.

## <a name="cross-feature-considerations"></a>Överväganden för Cross-funktionen

### <a name="restore-and-import"></a>Återställning och importera

Du kan återställa säkerhetskopian eller importera data till managed instance från en instans eller en server med olika tidszonsinställningar. Se dock till att göra det med försiktighet och för att analysera programmets beteende och resultatet av frågor och rapporter, precis som när du överför data mellan två SQL Server-instanser med olika tidszonsinställningar.

### <a name="point-in-time-restore"></a>Återställning från tidpunkt

När du utför point-in-time-återställning, tolkas det hög tid att återställa till som UTC-tid för att undvika eventuella tvetydighet på grund av sommartid och dess eventuella ändringar.

### <a name="auto-failover-groups"></a>Automatiska redundansgrupper

Med hjälp av samma tidszon mellan primära och sekundära instans i redundanskluster gruppen tillämpas inte, men rekommenderas starkt.
  >[!IMPORTANT]
  > Även om det finns giltigt scenarier för att använda en annan tidszon med geo-secondary instans används för att läsa skala, Observera att om en manuell eller automatisk redundans som sker till sekundär instans det behåller sin ursprungliga tidszon.

## <a name="limitations"></a>Begränsningar

- Tidszonen för den befintliga hanterade instansen kan inte ändras.
- Externa processer som startas från SQL Agent-jobb tar inte tjänstkvoterna tidszonen för instansen.
- Hanterad instans är inbyggda [New AzSqlInstance](https://docs.microsoft.com/powershell/module/az.sql/new-azsqlinstance) PowerShell-cmdlet inte stöd för skicka tidszonen parametern ännu. Använd PowerShell-omslutningen med [Resource Manager-mall](https://aka.ms/sql-mi-create-arm-posh) i stället.
- CLI-kommando [az sql mi skapa](https://docs.microsoft.com/cli/azure/sql/mi?view=azure-cli-latest#az-sql-mi-create) stöder inte tidszonsparametern ännu.

## <a name="list-of-supported-time-zones"></a>Lista med tidszoner som stöds

| **Tidszons-ID:** | **Visningsnamn för tidszonen** |
| --- | --- |
| Datumgränsen, normaltid | (UTC-12:00) Internationella datumlinjen, väst |
| UTC-11 | (UTC-11:00) Coordinated Universal Time-11 |
| Aleuterna, normaltid | (UTC-10:00) Aleuterna |
| Hawaii, normaltid | (UTC-10:00) Hawaii |
| Marquesas, normaltid | (UTC-09:30) Marquesasöarna |
| Alaska, normaltid | (UTC-09:00) Alaska |
| UTC-09 | (UTC-09:00) Coordinated Universal Time-09 |
| Pacific, normaltid (Mexiko) | (UTC-08:00) Baja California |
| UTC-08 | (UTC-08:00) Coordinated Universal Time-08 |
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
| Turks- och Caicosöarna, normaltid | (UTC – 05:00) Turks- och Caicosöarna |
| Paraguay, normaltid | (UTC-04:00) Asunción |
| Atlantic, normaltid | (UTC-04:00) Atlantic Time (Kanada) |
| Venezuela, normaltid | (UTC-04:00) Caracas |
| Centrala Brasilien, normaltid | (UTC-04:00) Cuiaba |
| Västra Sydamerika, normaltid | (UTC-04:00) Georgetown, La Paz, Manaus, San Juan |
| Pacific SA, normaltid | (UTC-04:00) Santiago |
| Newfoundland, normaltid | (UTC-03:30) Newfoundland |
| Tocantins, normaltid | (UTC-03:00) Araguaina |
| E. Sydamerika, normaltid | (UTC-03:00) Brasilia |
| Sydamerika (östra), normaltid | (UTC-03:00) Cayenne, Fortaleza |
| Argentina, normaltid | (UTC-03:00) Buenos Aires |
| Grönland, normaltid | (UTC-03:00) Grönland |
| Montevideo, normaltid | (UTC-03:00) Montevideo |
| Magallanes, normaltid | (UTC-03:00) Punta Arenas |
| Saint Pierre, normaltid | (UTC-03:00) Saint Pierre och Miquelon |
| Bahia, normaltid | (UTC-03:00) Salvador |
| UTC-02 | (UTC-02:00) Coordinated Universal Time-02 |
| Mid-Atlantic, normaltid | (UTC-02:00) Mid-Atlantic - gammal |
| Azorerna, normaltid | (UTC-01:00) Azorerna |
| Cabo Verde, normaltid | (UTC-01:00) Cabo Verde |
| UTC | (UTC) Coordinated Universal Time |
| GMT, normaltid | (UTC+00:00) Dublin, Edinburgh, Lissabon, London |
| Greenwich, normaltid | (UTC+00:00) Monrovia, Reykjavik |
| W. Europa, normaltid | (UTC+01:00) Amsterdam, Berlin, Bern, Rom, Stockholm, Wien |
| Centraleuropa, normaltid | (UTC+01:00) Belgrad, Bratislava, Budapest, Ljubljana, Prag |
| Paris, Madrid, normaltid | (UTC+01:00) Bryssel, Köpenhamn, Madrid, Paris |
| Marocko, normaltid | (UTC+01:00) Casablanca |
| São Tomé, normaltid | (UTC+01:00) São Tomé |
| Centraleuropeisk normaltid | (UTC+01:00) Sarajevo, Skopje, Warszawa, Zagreb |
| W. Centralafrika, normaltid | (UTC+01:00) Västra Centralafrika |
| Jordanien, normaltid | (UTC+02:00) Amman |
| GTB, normaltid | (UTC+02:00) Aten, Bukarest |
| Mellanöstern, normaltid | (UTC+02:00) Beirut |
| Egypten, normaltid | (UTC+02:00) Kairo |
| E. Europa, normaltid | (UTC+02:00) Chisinau |
| Syrien, normaltid | (UTC+02:00) Damaskus |
| Västbanken, normaltid | (UTC+02:00) Gaza, Hebron |
| Södra Afrika, normaltid | (UTC+02:00) Harare, Pretoria |
| FLE, normaltid | (UTC+02:00) Helsingfors, Kiev, Riga, Sofia, Tallinn, Vilnius |
| Israel Standard Time | (UTC+02:00) Jerusalem |
| Kaliningrad, normaltid | (UTC+02:00) Kaliningrad |
| Sudan, normaltid | (UTC + 02:00) Khartoum |
| Libyen, normaltid | (UTC+02:00) Tripoli |
| Namibia, normaltid | (UTC + 02:00) Windhoek |
| Irak, normaltid | (UTC+03:00) Bagdad |
| Turkiet, normaltid | (UTC+03.00) Istanbul |
| Arabien, normaltid | (UTC+03:00) Kuwait, Riyad |
| Vitryssland, normaltid | (UTC+03:00) Minsk |
| Ryssland, normaltid | (UTC+03:00) Moskva, St. Petersburg |
| E. Afrika, normaltid | (UTC+03:00) Nairobi |
| Iran, normaltid | (UTC+03:30) Teheran |
| Arabisk normaltid | (UTC+04:00) Abu Dhabi, Muskat |
| Astrachan, normaltid | (UTC+04:00) Astrakhan, Ulyanovsk |
| Azerbajdzjan, normaltid | (UTC+04:00) Baku |
| Ryssland tidszon 3 | (UTC+04:00) Izhevsk, Samara |
| Mauritius, normaltid | (UTC+04:00) Port Louis |
| Saratov, normaltid | (UTC+04:00) Saratov |
| Georgien, normaltid | (UTC+04:00) Tbilisi |
| Volgograd standardtid | (UTC+04:00) Volgograd |
| Kaukasus, normaltid | (UTC+04:00) Jerevan |
| Afghanistan, normaltid | (UTC+04:30) Kabul |
| Västra Asien, normaltid | (UTC+05:00) Asjchabad, Tasjkent |
| Ekaterinburg, normaltid | (UTC+05:00) Ekaterinburg |
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
| W. Mongoliet, normaltid | (UTC+07:00) Hovd |
| Nordasien, normaltid | (UTC+07:00) Krasnoyarsk |
| N. Centralasien, normaltid | (UTC+07:00) Novosibirsk |
| Tomsk, normaltid | (UTC+07:00) Tomsk |
| Kina, normaltid | (UTC+08:00) Beijing, Chongqing, Hongkong SAR, Urumqi |
| Östra Nordasien, normaltid | (UTC+08:00) Irkutsk |
| Singapore, normaltid | (UTC+08:00) Kuala Lumpur, Singapore |
| W. Australien, normaltid | (UTC+08:00) Perth |
| Taipei, normaltid | (UTC+08:00) Taipei |
| Ulan Bator, normaltid | (UTC+08:00) Ulan Bator |
| Centr. v. Australien, normaltid | (UTC+08:45) Eucla |
| Transbajkal, normaltid | (UTC+09:00) Chita |
| Tokyo, normaltid | (UTC+09:00) Osaka, Sapporo, Tokyo |
| Nordkorea, normaltid | (UTC + 09:00) Pyongyang |
| Korea, normaltid | (UTC+09:00) Söul |
| Yakutsk, normaltid | (UTC+09:00) Yakutsk |
| Cen. Australien, normaltid | (UTC+09:30) Adelaide |
| Centrala Australien, normaltid | (UTC+09:30) Darwin |
| E. Australien, normaltid | (UTC+10:00) Brisbane |
| Östra Australien, normaltid | (UTC+10:00) Canberra, Melbourne, Sydney |
| West Pacific, normaltid | (UTC+10:00) Guam, Port Moresby |
| Tasmanien, normaltid | (UTC+10:00) Hobart |
| Vladivostok, normaltid | (UTC+10:00) Vladivostok |
| Lord Howe, normaltid | (UTC+10:30) Lord Howeön |
| Bougainville, normaltid | (UTC+11:00) Bougainville |
| Ryssland tidszon 10 | (UTC+11:00) Chokurdakh |
| Magadan, normaltid | (UTC+11:00) Magadan |
| Norfolk, normaltid | (UTC+11:00) Norfolkön |
| Sachalin, normaltid | (UTC+11:00) Sakhalin |
| Central Pacific, normaltid | (UTC+11:00) Salomonöarna, Nya Kaledonien |
| Ryssland tidszon 11 | (UTC+12:00) Anadyr, Petropavlovsk-Kamchatsky |
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
- [VID TIDSZON (Transact-SQL)](https://docs.microsoft.com/sql/t-sql/queries/at-time-zone-transact-sql)
- [sys.time_zone_info (Transact-SQL)](https://docs.microsoft.com/sql/relational-databases/system-catalog-views/sys-time-zone-info-transact-sql)