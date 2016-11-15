---
title: "Så här migrerar du logikappar till förhandsschemaversionen från 2015-08-01 | Microsoft Docs"
description: "Du kan enkelt migrera dina logikappar till den senaste schemaversionen. Följ bara de här stegen."
services: logic-apps
documentationcenter: 
author: MSFTMAN
manager: erikre
editor: 
tags: connectors
ms.assetid: 3e177e49-fd69-43e9-9b9b-218abb250c31
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/23/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ab22e0369781f9f9afb9b3df9e7fdd54660a959d


---
# <a name="how-to-migrate-logic-apps-to-schema-version-20150801preview"></a>Så här migrerar du logikappar till förhandsschemaversionen från 2015-08-01
Flytta dina befintliga logikappar till det nya schemat genom att göra följande:  

1. Öppna logikappen i Azure-portalen  
2. Klicka på Uppdatera schema:
   
   ![API-ikon][step1]   
   Sidan Uppdatera schema visas och där finns en länk till ett dokument som innehåller detaljerad information om förbättringarna i det nya schemat: ![API-ikon][step2]

> [!NOTE]
> När du väljer **Uppdatera schema**kör vi automatiskt migreringsstegen och visar dig kodens utdata. Du kan använda dessa för att uppdatera din definition, men var noga med att följa bra kodningsmetoder, t.ex. de som beskrivs i avsnittet **Bästa metoder** nedan.
> 
> 

## <a name="best-practices-when-migrating-your-logic-apps-to-the-latest-schema-version"></a>Bästa metoder när du migrerar dina logikappar till den senaste schemaversionen:
* Kopiera det migrerade skriptet till en ny logikapp – skriv inte över den gamla förrän du har slutfört testerna och bekräftat att den migrerade appen fungerar som förväntat.
* Testa logikappen **innan** du använder den
* När migreringen har slutförts kan du börja uppdatera logikapparna och använda de [hanterade API:erna](apis-list.md) där det är möjligt. Du kan till exempel börja använda Dropbox v2 på samma sätt som DropBox v1.

## <a name="whats-next"></a>Nästa steg
* [Lär dig att migrera dina logikappar manuellt](../app-service-logic/app-service-logic-schema-2015-08-01.md)

<!--Icon references-->
[step1]: ./media/connectors-schema-migration/migrateschema1.png
[step2]: ./media/connectors-schema-migration/migrateschema2.png









<!--HONumber=Nov16_HO2-->


