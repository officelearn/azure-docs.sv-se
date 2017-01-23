---
title: "Självstudie om NoSQL C++ för DocumentDB | Microsoft Docs"
description: "En självstudie om NoSQL C++ där du skapar en C++-databas och ett konsolprogram med DocumentDB C++ SDK. Document DB är en större version av NoSQL-databastjänsten."
services: documentdb
documentationcenter: cpp
author: asthana86
manager: jhubbard
editor: 
ms.assetid: b8756b60-8d41-4231-ba4f-6cfcfe3b4bab
ms.service: documentdb
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: cpp
ms.topic: hero-article
ms.date: 12/25/2016
ms.author: aasthan
translationtype: Human Translation
ms.sourcegitcommit: 16bff1b5708652a75ea603f596c864901b12a88d
ms.openlocfilehash: f622b9a35c370148a3472fa6924a50933d59601e


---
# <a name="nosql-c-tutorial-documentdb-c-console-application"></a>Självstudiekurs om NoSQL C++: DocumentDB C++-konsolapp
> [!div class="op_single_selector"]
> * [NET](documentdb-get-started.md)
> * [.NET Core](documentdb-dotnetcore-get-started.md)
> * [Java](documentdb-java-get-started.md)
> * [Node.js](documentdb-nodejs-get-started.md)
> * [C++](documentdb-cpp-get-started.md)
>  
> 
 

Välkommen till självstudien om C++ för Azure DocumentDB Node.js SDK för C++! När du har följt den här självstudien har du ett konsolprogram som skapar och skickar frågor till DocumentDB-resurser, inklusive en C++-databas.

Vi tar upp följande:

* Skapa och ansluta till ett DocumentDB-konto
* Konfigurera ditt program
* Skapa en C++ DocumentDB-databas
* Skapa en samling
* Skapa JSON-dokument
* Skicka frågor till samlingen
* Ersätta ett dokument
* Ta bort ett dokument
* Ta bort en C++ DocumentDB-databas

Har du inte tid? Oroa dig inte! Den kompletta lösningen finns på [GitHub](https://github.com/stalker314314/DocumentDBCpp). En snabbguide finns i [Hämta den kompletta lösningen](#GetSolution).

När du har genomfört självstudien om C++ får du gärna ge oss feedback med röstningsknapparna längst ned på den här sidan. 

Om du vill att vi ska kontakta dig direkt kan du skriva din e-postadress i kommentaren eller [kontakta oss här](https://www.research.net/r/8BKRJ3Z). 

Nu sätter vi igång!

## <a name="prerequisites-for-the-c-tutorial"></a>Förutsättningar för självstudien om C++
Se till att du har följande:

* Ett aktivt Azure-konto. Om du inte har ett kan du registrera dig för en [kostnadsfri utvärderingsversion av Azure](https://azure.microsoft.com/pricing/free-trial/).
* [Visual Studio](https://www.visualstudio.com/downloads/), med språkkomponenter för C++ installeras.

## <a name="step-1-create-a-documentdb-account"></a>Steg 1: Skapa ett DocumentDB-konto
Börja med att skapa ett DocumentDB-konto. Om du redan har ett konto som du vill använda kan du gå vidare till [Konfigurera C++-programmet](#SetupNode).

[!INCLUDE [documentdb-create-dbaccount](../../includes/documentdb-create-dbaccount.md)]

## <a name="a-idsetupcastep-2-set-up-your-c-application"></a><a id="SetupC++"></a>Steg 2: Konfigurera din app
1. Öppna Visual Studio och klicka på **Nytt** i menyn **Arkiv** och sedan på **Projekt**. 
2. I fönstret **Nytt projekt** på panelen **Installerade** expanderar du **Visual C++**. Klicka sedan på **Win32** och klicka sedan på **Win32-konsolprogrammet**. Namnge project hellodocumentdb och klicka sedan på **OK**. 
   
    ![Skärmdump som visar fönstret Nytt projekt](media/documentdb-cpp-get-started/hellodocumentdb.png)
3. När guiden Win32-program startas, klickar du på **Slutför**.
4. När projektet har skapats öppar du pakethanteraren NuGet genom att högerklicka på projektet **hellodocumentdb** i **Solution Explorer** och därefter på **Hantera NuGet-paketet**. 
   
    ![Skärmbild som visar Hantera NuGet-paketet på menyn Projekt](media/documentdb-cpp-get-started/nuget.png)
5. På fliken **NuGet: hellodocumentdb** klickar du på **Bläddra**, och sedan söker du efter *documentdbcpp*. Välj DocumentDbCPP bland resultaten, enligt följande skärmbild. Paketet installerar referenser till C++ REST SDK, som är ett beroende för DocumentDbCPP.  
   
    ![Skärmbild som visar DocumentDbCpp-paketet](media/documentdb-cpp-get-started/documentdbcpp.png)
   
    När paketen har lagts till i projektet, kan vi börja skriva kod.   

## <a name="a-idconfigastep-3-copy-connection-details-from-azure-portal-for-your-documentdb-database"></a><a id="Config"></a>Steg 3: Kopiera anslutningsinformation från Azure-portalen för DocumentDB-databasen
Ta fram [Azure-portalen](https://portal.azure.com) och bläddra till NoSQL-databaskontot (DocumentDB) du har skapat. Vi behöver URI och den primära nyckeln från Azure-portalen i nästa steg för att upprätta en anslutning från vårt C++-kodfragment. 

![DocumentDB-URI och nycklar i Azure-portalen](media/documentdb-cpp-get-started/nosql-tutorial-keys.png)

## <a name="a-idconnectastep-4-connect-to-a-documentdb-account"></a><a id="Connect"></a>Steg 4: Anslut till ett DocumentDB-konto
1. Lägg till följande rubriker och namnområden i källkoden efter `#include "stdafx.h"`.
   
        #include <cpprest/json.h>
        #include <documentdbcpp\DocumentClient.h>
        #include <documentdbcpp\exceptions.h>
        #include <documentdbcpp\TriggerOperation.h>
        #include <documentdbcpp\TriggerType.h>
        using namespace documentdb;
        using namespace std;
        using namespace web::json;
2. Lägg sedan till följande kod i din huvudfunktion och ersätt kontokonfigurationen och den primära nyckeln så att de matchar DocumentDB-inställningarna från steg 3. 
   
        DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
        DocumentClient client (conf);
   
    Nu har du koden för att initiera DocumentDB-klienten och det är dags att gå vidare till arbete med DocumentDB-resurser.

## <a name="a-idcreatedbcollastep-5-create-a-c-database-and-collection"></a><a id="CreateDBColl"></a>Steg 5: Skapa en C++-databas och -samling
Innan vi utför det här steget ska vi gå igenom hur en databas, samling och dokument interagerar för dig som precis har börjat med DocumentDB. En [databas](documentdb-resources.md#databases) är en logisk behållare för dokumentlagring, partitionerad över samlingarna. En [samling](documentdb-resources.md#collections) är en behållare för JSON-dokument och associerad JavaScript-applogik. Du kan lära dig mer om den hierarkiska resursmodellen för DocumentDB och begrepp i [Hierarkisk resursmodell och begrepp i DocumentDB](documentdb-resources.md).

Om du vill skapa en databas och en motsvarande samling ska du lägga till följande kod i slutet av din huvudfunktion. Detta skapar en databas som heter "FamilyRegistry" och en samling som heter "FamilyCollection' med klientkonfigurationen du deklarerade i föregående steg.

    try {
      shared_ptr<Database> db = client.CreateDatabase(L"FamilyRegistry");
      shared_ptr<Collection> coll = db->CreateCollection(L"FamilyCollection");
    } catch (DocumentDBRuntimeException ex) {
      wcout << ex.message();
    }


## <a name="a-idcreatedocastep-6-create-a-document"></a><a id="CreateDoc"></a>Steg 6: Skapa ett dokument
[Dokument](documentdb-resources.md#documents) är användardefinierat (godtyckligt) JSON-innehåll. Du kan nu infoga ett dokument i DocumentDB. Du kan skapa ett dokument genom att kopiera följande kod till slutet av huvudfunktionen. 

    try {
      value document_family;
      document_family[L"id"] = value::string(L"AndersenFamily");
      document_family[L"FirstName"] = value::string(L"Thomas");
      document_family[L"LastName"] = value::string(L"Andersen");
      shared_ptr<Document> doc = coll->CreateDocumentAsync(document_family).get();

      document_family[L"id"] = value::string(L"WakefieldFamily");
      document_family[L"FirstName"] = value::string(L"Lucy");
      document_family[L"LastName"] = value::string(L"Wakefield");
      doc = coll->CreateDocumentAsync(document_family).get();
    } catch (ResourceAlreadyExistsException ex) {
      wcout << ex.message();
    }

Sammanfattningsvis skapar den här koden en DocumentDB-databas, -samling och -dokument som du kan skicka frågor till i dokumentutforskaren i Azure Portal. 

![Självstudie om C++ – Diagram som illustrerar den hierarkiska relationen mellan kontot, databasen, samlingen och dokumenten](media/documentdb-cpp-get-started/documentdbdocs.png)

## <a name="a-idquerydbastep-7-query-documentdb-resources"></a><a id="QueryDB"></a>Steg 7: Skicka frågor till DocumentDB-resurser
DocumentDB stöder [komplexa frågor](documentdb-sql-query.md) mot JSON-dokument som lagras i varje samling. Nedanstående exempelkod visar olika frågor med både DocumentDB SQL-syntax som du kan köra mot dokumenten som infogades i föregående steg.

Funktionen tar som argument den unika identifieraren eller resurs-id för databasen och samlingen tillsammans med dokumentklienten. Lägg till den här koden före huvudfunktionen.

    void executesimplequery(const DocumentClient &client,
                            const wstring dbresourceid,
                            const wstring collresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        wstring coll_name = coll->id();
        shared_ptr<DocumentIterator> iter =
            coll->QueryDocumentsAsync(wstring(L"SELECT * FROM " + coll_name)).get();
        wcout << "\n\nQuerying collection:";
        while (iter->HasMore()) {
          shared_ptr<Document> doc = iter->Next();
          wstring doc_name = doc->id();
          wcout << "\n\t" << doc_name << "\n";
          wcout << "\t"
                << "[{\"FirstName\":"
                << doc->payload().at(U("FirstName")).as_string()
                << ",\"LastName\":" << doc->payload().at(U("LastName")).as_string()
                << "}]";
        }
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idreplaceastep-8-replace-a-document"></a><a id="Replace"></a>Steg 8: Ersätta ett dokument
DocumentDB stöder ersättning av JSON-dokument, som visas i följande kod. Lägg till den här koden efter funktionen executesimplequery.

    void replacedocument(const DocumentClient &client, const wstring dbresourceid,
                         const wstring collresourceid,
                         const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        value newdoc;
        newdoc[L"id"] = value::string(L"WakefieldFamily");
        newdoc[L"FirstName"] = value::string(L"Lucy");
        newdoc[L"LastName"] = value::string(L"Smith Wakefield");
        coll->ReplaceDocument(docresourceid, newdoc);
      } catch (DocumentDBRuntimeException ex) {
        throw;
      }
    }

## <a name="a-iddeleteastep-9-delete-a-document"></a><a id="Delete"></a>Steg 9: Ta bort ett dokument
DocumentDB har stöd för att ta bort JSON-dokument. Kopiera och klistra in följande kod efter replacedocument-funktionen. 

    void deletedocument(const DocumentClient &client, const wstring dbresourceid,
                        const wstring collresourceid, const wstring docresourceid) {
      try {
        client.GetDatabase(dbresourceid).get();
        shared_ptr<Database> db = client.GetDatabase(dbresourceid);
        shared_ptr<Collection> coll = db->GetCollection(collresourceid);
        coll->DeleteDocumentAsync(docresourceid).get();
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-iddeletedbastep-10-delete-a-database"></a><a id="DeleteDB"></a>Steg 10: Ta bort en databas
Om du tar bort databasen du skapade försvinner databasen och alla underordnade resurser (t.ex. samlingar och dokument).

Kopiera och klistra in följande kodutdrag (funktionen cleanup) efter funktionen deletedocument för att ta bort databasen och alla underordnade resurser.

    void deletedb(const DocumentClient &client, const wstring dbresourceid) {
      try {
        client.DeleteDatabase(dbresourceid);
      } catch (DocumentDBRuntimeException ex) {
        wcout << ex.message();
      }
    }

## <a name="a-idrunastep-11-run-your-c-application-all-together"></a><a id="Run"></a>Steg 11: Kör C++-appen i sin helhet!
Nu har vi lagt till kod för att skapa, läsa, ändra och ta bort olika DocumentDB-resurser.  Nu är det dags att samla detta genom att lägga till anrop till de olika funktionerna från vår huvudfunktion i hellodocumentdb.cpp, tillsammans med vissa diagnostiska meddelanden.

Du kan göra det genom att ersätta huvudfunktionen i ditt program med följande kod. Detta skriver över account_configuration_uri och primary_key som du kopierade till koden i steg 3, så spara raden eller kopiera värdena igen från portalen. 

    int main() {
        try {
            // Start by defining your account's configuration
            DocumentDBConfiguration conf (L"<account_configuration_uri>", L"<primary_key>");
            // Create your client
            DocumentClient client(conf);
            // Create a new database
            try {
                shared_ptr<Database> db = client.CreateDatabase(L"FamilyDB");
                wcout << "\nCreating database:\n" << db->id();
                // Create a collection inside database
                shared_ptr<Collection> coll = db->CreateCollection(L"FamilyColl");
                wcout << "\n\nCreating collection:\n" << coll->id();
                value document_family;
                document_family[L"id"] = value::string(L"AndersenFamily");
                document_family[L"FirstName"] = value::string(L"Thomas");
                document_family[L"LastName"] = value::string(L"Andersen");
                shared_ptr<Document> doc =
                    coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                document_family[L"id"] = value::string(L"WakefieldFamily");
                document_family[L"FirstName"] = value::string(L"Lucy");
                document_family[L"LastName"] = value::string(L"Wakefield");
                doc = coll->CreateDocumentAsync(document_family).get();
                wcout << "\n\nCreating document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                replacedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nReplaced document:\n" << doc->id();
                executesimplequery(client, db->resource_id(), coll->resource_id());
                deletedocument(client, db->resource_id(), coll->resource_id(),
                    doc->resource_id());
                wcout << "\n\nDeleted document:\n" << doc->id();
                deletedb(client, db->resource_id());
                wcout << "\n\nDeleted db:\n" << db->id();
                cin.get();
            }
            catch (ResourceAlreadyExistsException ex) {
                wcout << ex.message();
            }
        }
        catch (DocumentDBRuntimeException ex) {
            wcout << ex.message();
        }
        cin.get();
    }

Du bör nu kunna skapa och köra din kod i Visual Studio genom att trycka på F5 eller alternativt i terminalfönstret genom att hitta programmet och köra den körbara filen. 

Du bör nu se utdata från din kom-igång-app. Utdata bör matcha följande skärmbild.

![Utdata för DocumentDB C++-program](media/documentdb-cpp-get-started/docdbconsole.png)

Grattis! Du har slutfört självstudien om C++ och skapat ditt första DocumentDB-konsolprogram!

## <a name="a-idgetsolutionaget-the-complete-c-tutorial-solution"></a><a id="GetSolution"></a>Hämta den fullständiga lösningen till C++-självstudiekursen
För att bygga GetStarted-lösningen med alla exempel i den här artikeln behöver du följande:

* [DocumentDB-konto][documentdb-create-account].
* [GetStarted](https://github.com/stalker314314/DocumentDBCpp)-lösningen som finns på GitHub.

## <a name="next-steps"></a>Nästa steg
* Mer information om hur du [övervakar ett DocumentDB-konto](documentdb-monitor-accounts.md).
* Kör frågor mot vår exempeldatauppsättning i [Query Playground](https://www.documentdb.com/sql/demo).
* Mer information om programmeringsmiljön finns i avsnittet Utveckla på [dokumentationssidan för DocumentDB](https://azure.microsoft.com/documentation/services/documentdb/).

[documentdb-create-account]: documentdb-create-account.md





<!--HONumber=Jan17_HO1-->


