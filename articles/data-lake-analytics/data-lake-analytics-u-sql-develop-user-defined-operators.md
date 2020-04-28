---
title: Utveckla användardefinierade U-SQL-operatörer – Azure Data Lake Analytics
description: Lär dig hur du utvecklar användardefinierade operatörer som ska användas och återanvändas i Azure Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 04/28/2020
ms.locfileid: "71316546"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Utveckla användardefinierade U-SQL-operatörer (Katalogentiteter)
Den här artikeln beskriver hur du utvecklar användardefinierade operatorer för att bearbeta data i ett U-SQL-jobb.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiera och använda en användardefinierad operator i U-SQL
**Skapa och skicka ett U-SQL-jobb**

1. Från Visual Studio väljer du **fil > nytt > projekt > U-SQL-projekt**.
2. Klicka på **OK**. Visual Studio skapar en lösning med en script. usql-fil.
3. Från **Solution Explorer**expanderar du script. usql och dubbelklickar sedan på **script.usql.cs**.
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
6. Öppna **script. usql**och klistra in följande U-SQL-skript:

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
10. Om du inte har anslutit till din Azure-prenumeration uppmanas du att ange dina autentiseringsuppgifter för Azure-kontot.
11. Klicka på **Skicka**. Resultatet av överföringen och jobb länken är tillgängliga i resultat fönstret när överföringen är klar.
12. Klicka på **Uppdatera** om du vill se senaste jobb status och uppdatera skärmen.

**Visa utdata**

1. Från **Server Explorer**, expandera **Azure**, expandera **data Lake Analytics**, expandera ditt data Lake Analytics-konto, expandera **lagrings konton**, högerklicka på standard lagringen och klicka sedan på **Utforskaren**.
2. Expandera exempel, expandera utdata och dubbelklicka sedan på **driv rutiner. csv**.

## <a name="see-also"></a>Se även
* [Utöka U-SQL-uttryck med användar kod](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Använd Data Lake verktyg för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
