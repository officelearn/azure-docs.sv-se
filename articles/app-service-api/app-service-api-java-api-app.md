---
title: Skapa och distribuera en Java API-app i Azure Apptjänst
description: Lär dig hur du skapar ett Java API app-paket och distribuerar det till Azure Apptjänst.
services: app-service\api
documentationcenter: java
author: bradygaster
manager: mohisri
editor: tdykstra

ms.service: app-service-api
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: get-started-article
ms.date: 08/31/2016
ms.author: rachelap

---
# Skapa och distribuera en Java API-app i Azure Apptjänst
[!INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

I de här självstudierna får du veta hur du skapar en Java-applikation och distribuerar den till API Apps i Azure Apptjänst med hjälp av [Git]. Anvisningarna i den här kursen kan tillämpas på alla operativsystem som kan köra Java. Koden i självstudierna byggs med [Maven]. [Jax RS] används för att skapa tjänsten RESTful och genereras baserat på [Swagger]-metadataspecifikationerna med hjälp av [Swagger Editor].

## Krav
1. [Java Developer's Kit 8] \(eller senare)
2. [Maven] installerat på utvecklingsdatorn
3. [Git] installerat på utvecklingsdatorn
4. En betald eller [kostnadsfri prov]prenumeration på [Microsoft Azure]
5. Ett program för HTTP-test som [Postman]

## Autogenerera API:et med Swagger.IO
Med redigeraren swagger.io online kan du ange Swagger JSON- eller YAML-kod som representerar strukturen för din API. När du har utformat API-ytan kan du exportera kod för olika plattformar och ramverk. Den autogenererade koden kommer att ändras till att inkludera fingerade funktioner i nästa avsnitt. 

Den här demonstrationen börjar med en Swagger JSON-text som du ska klistra in i redigeraren swagger.io. Texten används sedan för att generera kod genom att utnyttja JAX-RS för att komma åt en REST API-slutpunkt. Sedan redigerar du den autogenererade koden för att returnera fingerade data genom att simulera en REST-API som byggts ovanpå en mekanism för datapersistence.  

1. Kopiera följande Swagger JSON-kod till Urklipp:
   
        {
            "swagger": "2.0",
            "info": {
                "version": "v1",
                "title": "Contact List",
                "description": "A Contact list API based on Swagger and built using Java"
            },
            "host": "localhost",
            "schemes": [
                "http",
                "https"
            ],
            "basePath": "/api",
            "paths": {
                "/contacts": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_get",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                },
                "/contacts/{id}": {
                    "get": {
                        "tags": [
                            "Contact"
                        ],
                        "operationId": "contacts_getById",
                        "consumes": [],
                        "produces": [
                            "application/json",
                            "text/json"
                        ],
                        "parameters": [
                            {
                                "name": "id",
                                "in": "path",
                                "required": true,
                                "type": "integer",
                                "format": "int32"
                            }
                        ],
                        "responses": {
                            "200": {
                                "description": "OK",
                                "schema": {
                                    "type": "array",
                                    "items": {
                                        "$ref": "#/definitions/Contact"
                                    }
                                }
                            }
                        },
                        "deprecated": false
                    }
                }
            },
            "definitions": {
                "Contact": {
                    "type": "object",
                    "properties": {
                        "Id": {
                            "format": "int32",
                            "type": "integer"
                        },
                        "Name": {
                            "type": "string"
                        },
                        "EmailAddress": {
                            "type": "string"
                        }
                    }
                }
            }
        }
2. Navigera till [Swagger-redigeraren online]. När du är där klickar du på menyalternativet **Arkiv > Klistra in JSON**.
   
    ![Menyalternativet Klistra in JSON][paste-json]
3. Klistra in den Swagger JSON-kod som du kopierade tidigare för kontaktlistans API. 
   
    ![Klistra in JSON-kod i Swagger][pasted-swagger]
4. Visa dokumentationssidorna och API-översikten som visas i redigeraren. 
   
    ![Visa Swagger-genererade dokument][view-swagger-generated-docs]
5. Välj menyalternativet **Generera Server -> JAX-RS** och autogenerera den serverkod som du ska redigera senare för att lägga till fingerad implementering. 
   
    ![Menyalternativet Generera kod][generate-code-menu-item]
   
    När koden har genererats får du en ZIP-fil att hämta. Den här filen innehåller koden som skapats av kodgeneratorn Swagger och alla associerade byggnadsskript. Packa upp hela biblioteket till en katalog på utvecklingsdatorn. 

## Redigera koden för att lägga till API-implementering
I det här avsnittet ska du ersätta den genererade Swagger-kodens implementering på serversidan med din anpassade kod. Den nya koden returnerar en ArrayList med kontaktenheter till den anropande klienten. 

1. Öppna modellfilen *Contact.java* som finns i mappen *src/gen/java/io/swagger/model* med [Visual Studio-koden] eller ditt favoritprogram för textredigering. 
   
    ![Öppna modellfilen för kontakter][open-contact-model-file]
2. Lägg till följande konstruktor för klassen **Kontakt**. 
   
        public Contact(Integer id, String name, String email) 
        {
            this.id = id;
            this.name = name;
            this.emailAddress = email;
        }
3. Öppna tjänstimplementeringsfilen *Contact.java* som finns i mappen *src/main/java/io/swagger/api/impl* med [Visual Studio-koden] eller ditt favoritprogram för textredigering.
   
    ![Öppna tjänstkodfilen för kontakter][open-contact-service-code-file]
4. Skriv över kod i filen med den här nya koden för att lägga till en fingerad implementering till tjänstkoden. 
   
        package io.swagger.api.impl;
   
        import io.swagger.api.*;
        import io.swagger.model.*;
        import com.sun.jersey.multipart.FormDataParam;
        import io.swagger.model.Contact;
        import java.util.*;
        import io.swagger.api.NotFoundException;
        import java.io.InputStream;
        import com.sun.jersey.core.header.FormDataContentDisposition;
        import com.sun.jersey.multipart.FormDataParam;
        import javax.ws.rs.core.Response;
        import javax.ws.rs.core.SecurityContext;
   
        @javax.annotation.Generated(value = "class io.swagger.codegen.languages.JaxRSServerCodegen", date = "2015-11-24T21:54:11.648Z")
        public class ContactsApiServiceImpl extends ContactsApiService {
   
            private ArrayList<Contact> loadContacts()
            {
                ArrayList<Contact> list = new ArrayList<Contact>();
                list.add(new Contact(1, "Barney Poland", "barney@contoso.com"));
                list.add(new Contact(2, "Lacy Barrera", "lacy@contoso.com"));
                list.add(new Contact(3, "Lora Riggs", "lora@contoso.com"));
                return list;
            }
   
            @Override
            public Response contactsGet(SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                return Response.ok().entity(list).build();
                }
   
            @Override
            public Response contactsGetById(Integer id, SecurityContext securityContext)
            throws NotFoundException {
                ArrayList<Contact> list = loadContacts();
                Contact ret = null;
   
                for(int i=0; i<list.size(); i++)
                {
                    if(list.get(i).getId() == id)
                        {
                            ret = list.get(i);
                        }
                }
                return Response.ok().entity(ret).build();
            }
        }
5. Öppna en kommandotolk och ändra katalogen till rotmappen för ditt program.
6. Kör följande Maven-kommando för att skapa koden och kör den med hjälp av Jetty-appservern lokalt. 
   
        mvn package jetty:run
7. Du bör se i kommandofönstret att Jetty har startat koden på port 8080. 
   
    ![Öppna tjänstkodfilen för kontakter][run-jetty-war]
8. Använd [Postman] och gör en begäran för API-metoden "hämta alla kontakter" på http://localhost:8080/api/contacts.
   
    ![Anropa API för kontakter][calling-contacts-api]
9. Använd [Postman] och gör en begäran för API-metoden "hämta specifika kontakter" på http://localhost:8080/api/contacts/2.
   
    ![Anropa API för kontakter][calling-specific-contact-api]
10. Slutligen skapar du Java WAR-filen (webbarkiv) genom att köra följande Maven-kommando i konsolen. 
    
         mvn package war:war
11. När WAR-filen har skapats placeras den i **mål**mappen. Navigera till **mål**mappen och byt namn på WAR-filen till **ROOT.war**. (Kontrollera att skiftläget matchar detta format).
    
          rename swagger-jaxrs-server-1.0.0.war ROOT.war
12. Kör slutligen följande kommandon från rotmappen för programmet och skapa en mapp som heter **Distribuera** och som ska användas för att distribuera WAR-filen till Azure. 
    
          mkdir deploy
          mkdir deploy\webapps
          copy target\ROOT.war deploy\webapps
          cd deploy

## Publicera utdata i Azure Apptjänst
I det här avsnittet får du information om hur du skapar en ny API-App i Azure-portalen, förbereder API-appen för Java-program och distribuerar den nyligen skapade WAR-filen till Azure Apptjänst för att köra din nya API-app. 

1. Skapa en ny API-app i [Azure-portalen] genom att klicka på menyalternativet **Ny -> Webb + Mobil > API-app**, ange information om dina appar och sedan klicka på **Skapa**.
   
    ![Skapa en ny API-app][create-api-app]
2. När din API-app har skapats kan du öppna appens blad **Inställningar** och klicka på menyalternativet **Programinställningar**. Välj de senaste Java-versionerna från de tillgängliga alternativen och sedan den senaste Tomcat från menyn **Webbehållaren**. Klicka sedan på **Spara**.
   
    ![Ställa in Java i bladet API-app][set-up-java]
3. Klicka på menyalternativet för inställningar till **Autentiseringsuppgifter för distribution** och ange ett användarnamn och lösenord som du vill använda för att publicera filer till API-appen. 
   
    ![Ange autentiseringsuppgifter för distribution][deployment-credentials]
4. Klicka på menyalternativet för inställningar till **Distributionskälla**. När du är där klickar du på knappen **Välj källa**. Välj alternativet **Lokal Git-lagringsplats** och klicka sedan på **OK**. Detta skapar en Git-lagringsplats som körs i Azure och som är kopplad till din API-app. Varje gång du kopplar kod till *huvud* grenen av Git-lagringsplatsen publiceras koden till den instans för API-appen som är live. 
   
    ![Skapa en ny lokal Git-lagringsplats][select-git-repo]
5. Kopiera URL:en till den nya Git-lagringsplatsen till Urklipp. Spara den eftersom den behövs om en stund. 
   
    ![Skapa en ny Git-lagringsplats för din app][copy-git-repo-url]
6. Git skickar WAR-filen till online-lagringsplatsen. Gör detta genom att navigera till mappen **Distribuera** som du skapade tidigare så att du kan enkelt kan koppla koden upp till den lagringsplats som körs i din Apptjänst. När du är i konsolfönstret och har navigerat till mappen där webbappens mapp finns ska du utfärda följande Git-kommandon för att starta processen och utlösa en distribution. 
   
         git init
         git add .
         git commit -m "initial commit"
         git remote add azure [YOUR GIT URL]
         git push azure master
   
    När du skickar en **push**-begäran uppmanas du att ange lösenordet som du tidigare skapade för distribution av autentiseringsuppgifter. När du har angett dina autentiseringsuppgifter ska portalen visa att uppdateringen har distribuerats.
7. Om du använder Postman igen för att träffa den nyligen distribuerade API-appen som körs i Azure Apptjänst, ser du att beteendet är konsekvent, att kontaktdata returneras som förväntat och att det nu används enkla kodändringar i Swagger.io som är autogenererad Java-kod. 
   
    ![Använda REST API för Java kontakter live i Azure][postman-calling-azure-contacts]

## Nästa steg
I den här artikeln kunde du börja med en Swagger JSON-fil och autogenererad Java-kod som hämtats från redigeraren Swagger.io. Därifrån gjorde dina enkla ändringar och en Git-distributionsprocess att du skapade en fungerande API-app skriven i Java. Nästa självstudiekurser visar hur du [använder API Apps från JavaScript-klienter med hjälp av CORS][Apptjänst API CORS]. Senare självstudiekurser i serien visar hur du implementerar autentisering och auktorisering.

För att bygga på det här exemplet kan du läsa mer om [Lagrings-SDK för Java] för att bevara JSON-blobbar. Eller så kan du använda [Dokumentet DB Java SDK] för att spara kontaktinformation i Azure-dokumentet DB. 

Mer information om hur du använder Java i Azure finns i [Java-utvecklingscenter].

<!-- URL List -->

[Apptjänst API CORS]: app-service-api-cors-consume-javascript.md
[Azure-portalen]: https://portal.azure.com/
[Dokumentet DB Java SDK]: ../documentdb/documentdb-java-application.md
[kostnadsfri prov]: https://azure.microsoft.com/pricing/free-trial/
[Git]: http://www.git-scm.com/
[Java-utvecklingscenter]: /develop/java/
[Java Developer's Kit 8]: http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html
[Jax RS]: https://jax-rs-spec.java.net/
[Maven]: https://maven.apache.org/
[Microsoft Azure]: https://azure.microsoft.com/
[Swagger-redigeraren online]: http://editor.swagger.io/
[Postman]: https://www.getpostman.com/
[Lagrings-SDK för Java]: ../storage/storage-java-how-to-use-blob-storage.md
[Swagger]: http://swagger.io/
[Swagger Editor]: http://editor.swagger.io/
[Visual Studio-koden]: https://code.visualstudio.com

<!-- IMG List -->

[paste-json]: ./media/app-service-api-java-api-app/paste-json.png
[pasted-swagger]: ./media/app-service-api-java-api-app/pasted-swagger.png
[view-swagger-generated-docs]: ./media/app-service-api-java-api-app/view-swagger-generated-docs.png
[generate-code-menu-item]: ./media/app-service-api-java-api-app/generate-code-menu-item.png
[open-contact-model-file]: ./media/app-service-api-java-api-app/open-contact-model-file.png
[open-contact-service-code-file]: ./media/app-service-api-java-api-app/open-contact-service-code-file.png
[run-jetty-war]: ./media/app-service-api-java-api-app/run-jetty-war.png
[calling-contacts-api]: ./media/app-service-api-java-api-app/calling-contacts-api.png
[calling-specific-contact-api]: ./media/app-service-api-java-api-app/calling-specific-contact-api.png
[create-api-app]: ./media/app-service-api-java-api-app/create-api-app.png
[set-up-java]: ./media/app-service-api-java-api-app/set-up-java.png
[deployment-credentials]: ./media/app-service-api-java-api-app/deployment-credentials.png
[select-git-repo]: ./media/app-service-api-java-api-app/select-git-repo.png
[copy-git-repo-url]: ./media/app-service-api-java-api-app/copy-git-repo-url.png
[postman-calling-azure-contacts]: ./media/app-service-api-java-api-app/postman-calling-azure-contacts.png



<!--HONumber=Oct16_HO1-->


