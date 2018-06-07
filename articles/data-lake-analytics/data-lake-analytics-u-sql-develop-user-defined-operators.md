---
title: Utveckla U-SQL-användardefinierade operatorer (UDO) i Azure Data Lake Analytics
description: Lär dig mer om att utveckla användardefinierade operatorer som ska användas och återanvändas i Azure Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
manager: kfile
editor: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: 8d8937a6132f770e989d7595883b2c5cf804c44f
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 06/01/2018
ms.locfileid: "34623863"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Utveckla U-SQL-användardefinierade operatorer (UDO)
Den här artikeln beskriver hur du utvecklar användardefinierade operatorer för att bearbeta data i ett U-SQL-jobb.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiera och använder en användardefinierad operator i U-SQL
**Skapa och skicka ett U-SQL-jobb**

1. Visual Studio väljer **Arkiv > Nytt > Projekt > U-SQL-projekt**.
2. Klicka på **OK**. Visual Studio skapar en lösning med en Script.usql-fil.
3. Från **Solution Explorer**, expandera Script.usql och dubbelklicka sedan på **Script.usql.cs**.
4. Klistra in följande kod i filen:

        using Microsoft.Analytics.Interfaces;
        using System.Collections.Generic;

        namespace USQL_UDO
        {
            public class CountryName : IProcessor
            {
                private static IDictionary<string, string> CountryTranslation = new Dictionary<string, string>
                {
                    {
                        "Deutschland", "Germany"
                    },
                    {
                        "Suisse", "Switzerland"
                    },
                    {
                        "UK", "United Kingdom"
                    },
                    {
                        "USA", "United States of America"
                    },
                    {
                        "中国", "PR China"
                    }
                };

                public override IRow Process(IRow input, IUpdatableRow output)
                {

                    string UserID = input.Get<string>("UserID");
                    string Name = input.Get<string>("Name");
                    string Address = input.Get<string>("Address");
                    string City = input.Get<string>("City");
                    string State = input.Get<string>("State");
                    string PostalCode = input.Get<string>("PostalCode");
                    string Country = input.Get<string>("Country");
                    string Phone = input.Get<string>("Phone");

                    if (CountryTranslation.Keys.Contains(Country))
                    {
                        Country = CountryTranslation[Country];
                    }
                    output.Set<string>(0, UserID);
                    output.Set<string>(1, Name);
                    output.Set<string>(2, Address);
                    output.Set<string>(3, City);
                    output.Set<string>(4, State);
                    output.Set<string>(5, PostalCode);
                    output.Set<string>(6, Country);
                    output.Set<string>(7, Phone);

                    return output.AsReadOnly();
                }
            }
        }
6. Öppna **Script.usql**, och klistra in följande U-SQL-skript:

        @drivers =
            EXTRACT UserID      string,
                    Name        string,
                    Address     string,
                    City        string,
                    State       string,
                    PostalCode  string,
                    Country     string,
                    Phone       string
            FROM "/Samples/Data/AmbulanceData/Drivers.txt"
            USING Extractors.Tsv(Encoding.Unicode);

        @drivers_CountryName =
            PROCESS @drivers
            PRODUCE UserID string,
                    Name string,
                    Address string,
                    City string,
                    State string,
                    PostalCode string,
                    Country string,
                    Phone string
            USING new USQL_UDO.CountryName();    

        OUTPUT @drivers_CountryName
            TO "/Samples/Outputs/Drivers.csv"
            USING Outputters.Csv(Encoding.Unicode);
7. Ange Data Lake Analytics-kontot, databas och schema.
8. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skapa skript**.
9. Från **Solution Explorer**, högerklicka på **Script.usql** och klicka sedan på **Skicka skript**.
10. Om du inte har anslutit till din Azure-prenumeration uppmanas du att ange dina autentiseringsuppgifter för Azure-konto.
11. Klicka på **skicka**. Resultat för skicka och jobblänk är tillgängliga i resultatfönstret när överföringen är klar.
12. Klicka på den **uppdatera** för att visa senast jobbstatusen och uppdatera skärmen.

**Se utdata**

1. Från **Server Explorer**, expandera **Azure**, expandera **Datasjöanalys**, expandera Data Lake Analytics-kontot, expandera **Lagringskonton**högerklickar du lagringsplatsen standard och klicka sedan på **Explorer**.
2. Expandera exempel utdata, och dubbelklicka sedan på **Drivers.csv**.

## <a name="see-also"></a>Se också
* [Utöka U-SQL-uttryck med användar-kod](https://msdn.microsoft.com/library/azure/mt621316.aspx)
* [Använd Data Lake-verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
