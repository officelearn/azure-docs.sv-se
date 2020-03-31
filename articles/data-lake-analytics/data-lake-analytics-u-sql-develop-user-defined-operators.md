---
title: Utveckla U-SQL-användardefinierade operatorer - Azure Data Lake Analytics
description: Lär dig hur du utvecklar användardefinierade operatorer som ska användas och återanvändas i Azure Data Lake Analytics-jobb.
services: data-lake-analytics
ms.service: data-lake-analytics
author: saveenr
ms.author: saveenr
ms.reviewer: jasonwhowell
ms.assetid: e5189e4e-9438-46d1-8686-ed4836bf3356
ms.topic: conceptual
ms.date: 12/05/2016
ms.openlocfilehash: b2d1293b06b4d8791138ed666bc3cb4abe3adf40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: sv-SE
ms.lasthandoff: 03/27/2020
ms.locfileid: "71316546"
---
# <a name="develop-u-sql-user-defined-operators-udos"></a>Utveckla U-SQL-användardefinierade operatorer (UDOs)
I den här artikeln beskrivs hur du utvecklar användardefinierade operatorer för att bearbeta data i ett U-SQL-jobb.

## <a name="define-and-use-a-user-defined-operator-in-u-sql"></a>Definiera och använda en användardefinierad operator i U-SQL
**Så här skapar och skickar du ett U-SQL-jobb**

1. Välj **Fil > Nytt >-projekt > U-SQL-projekt i**Visual Studio .
2. Klicka på **OK**. Visual Studio skapar en lösning med en Script.usql-fil.
3. Expandera Script.usql från **Solution Explorer**och dubbelklicka sedan på **Script.usql.cs**.
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
6. Öppna **Script.usql**och klistra in följande U-SQL-skript:

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
10. Om du inte har anslutit till din Azure-prenumeration uppmanas du att ange dina Azure-kontouppgifter.
11. Klicka på **Skicka**. Inlämningsresultat och jobblänk är tillgängliga i fönstret Resultat när inlämningen är klar.
12. Klicka på knappen **Uppdatera** om du vill se den senaste jobbstatusen och uppdatera skärmen.

**Så här ser du utdata**

1. Expandera **DataSjöAnalys**från **Server Explorer**, expandera ditt DataSjöanalyskonto, expandera **Azure** **Lagringskonton,** högerklicka på Standardlagring och klicka sedan på **Explorer**.
2. Expandera exempel, expandera utdata och dubbelklicka sedan på **Drivers.csv**.

## <a name="see-also"></a>Se även
* [Utöka U-SQL-uttryck med användarkod](/u-sql/concepts/extending-u-sql-expressions-with-user-code)
* [Använda Data Lake Tools för Visual Studio för att utveckla U-SQL-program](data-lake-analytics-data-lake-tools-get-started.md)
