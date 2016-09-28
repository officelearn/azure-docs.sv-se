<properties
    pageTitle="Så här migrerar du logikappar till förhandsschemaversionen från 2015-08-01 | Microsoft Azure Apptjänst"
    description="Du kan enkelt migrera dina logikappar till den senaste schemaversionen. Följ bara de här stegen."
    services="logic-apps"
    documentationCenter=""
    authors="MSFTMAN"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/23/2016"
    ms.author="deonhe"/>


# Så här migrerar du logikappar till förhandsschemaversionen från 2015-08-01

Flytta dina befintliga logikappar till det nya schemat genom att göra följande:  
1. Öppna logikappen i Azure-portalen  
2. Klicka på Uppdatera schema:

 ![API-ikon][step1]   
Sidan Uppdatera schema visas och där finns en länk till ett dokument som innehåller detaljerad information om förbättringarna i det nya schemat: ![API-ikon][step2]

>[AZURE.NOTE] När du väljer **Uppdatera schema**kör vi automatiskt migreringsstegen och visar dig kodens utdata. Du kan använda dessa för att uppdatera din definition, men var noga med att följa bra kodningsmetoder, t.ex. de som beskrivs i avsnittet **Bästa metoder** nedan.

## Bästa metoder när du migrerar dina logikappar till den senaste schemaversionen:  

- Kopiera det migrerade skriptet till en ny logikapp – skriv inte över den gamla förrän du har slutfört testerna och bekräftat att den migrerade appen fungerar som förväntat.
- Testa logikappen **innan** du använder den
- När migreringen har slutförts kan du börja uppdatera logikapparna och använda de [hanterade API:erna](./apis-list.md) där det är möjligt. Du kan till exempel börja använda Dropbox v2 på samma sätt som DropBox v1.


## Nästa steg
-  [Lär dig att migrera dina logikappar manuellt](../app-service-logic/app-service-logic-schema-2015-08-01.md)


<!--Icon references-->
[steg 1]: ./media/connectors-schema-migration/migrateschema1.png
[steg 2]: ./media/connectors-schema-migration/migrateschema2.png









<!--HONumber=Sep16_HO3-->


